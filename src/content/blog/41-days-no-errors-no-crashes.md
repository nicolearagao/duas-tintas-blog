---
title: "41 Days, No Errors, No Crashes: A Study in Compound Failure"
description: "A background job hung for 41 days with no errors. The root cause took one calculation to find. The real story was discovering why seven safety nets all had holes."
pubDate: 2026-06-03
category: "professional"
lang: "en"
tags: ["debugging", "celery", "python", "systems"]
---

This article shares lessons from debugging a customer's background job that hung for 41 days - no errors, no crashes, no timeouts. Forensic log analysis and basic arithmetic revealed the root cause in about a day. But the root cause wasn't the interesting part. Six additional safeguards should have caught, mitigated, or reported the problem. All were broken or missing. This isn't a Celery tutorial. It's a postmortem where the interesting question isn't *what broke* but *why nothing else caught it*.

## The Problem

A customer reported that three scans on their instance had hung. The worst case: a network scan of a /21 subnet (~2,048 hosts) that ran for over 30 days, accumulating 1.6 GB of worker logs, producing no errors. The job sat in `running` state until someone killed it manually.

It took about 34 hours for the system to break, and 41 days for someone to notice.

The instance had been running continuously for about seven months. Other scans had completed successfully, so this wasn't a systemic infrastructure failure. Something about these specific scans was triggering the hang.

## The System: What You Need to Know

The architecture details don't matter, but the shape does. The system is built on Python, Django, and Celery, with Redis as both the message broker and result backend.

We run multi-phase network scans distributed through Celery. The workflow looks roughly like this:

```python
group(scan_task(host_batch) for host_batch in batches)
| fingerprint
| finalize
```

In Celery, `group | task` is a **chord**: a group of parallel tasks followed by a callback. The chord tracks completion by storing each member's result in Redis. When the last member finishes, Redis confirms all results are present, and the callback fires.

If the callback never fires, the job stays in `running` state permanently.

## The Investigation

The hung scan had no errors at all. It simply stopped producing output. So I started there, looking at what the scan *did* produce:

```
elapsed_time: 123s  - ansible_runner.run for 25 hosts
elapsed_time: 162s  - PERMISSION DENIED could not connect with cred [redacted]
elapsed_time: 407s  - Unexpected ansible status timeout. Continuing scan.
```

Other scans on the same instance told a different story. Across 426,000 lines of application logs, I found:

| Error | Count |
|-------|-------|
| `ForeignKeyViolation` on a task reference | 13 |
| `ScanTask.DoesNotExist` | 80 |
| `KeyError: 'connection_result'` | 1 |

These errors weren't on the hung scan. They were on scans that had been destroyed or re-queued while workers were still active. I noted them for later (they would turn out to be evidence of a separate bug) and returned to the hung scan.

### The Arithmetic

The gap from 162s to 407s in the scan log is 245 seconds, almost exactly the 250-second timeout configured for a batch of 25 hosts. The timeouts were working correctly. The scan wasn't stuck in a loop; it was processing hosts sequentially and timing out on unreachable ones.

Then I did the full calculation:

- A /21 subnet: ~2,048 hosts
- Batched into groups of 25: **82 groups**
- Each group tries 6 credentials, timing out at 250 seconds per attempt
- 82 x 6 x 250 = **123,000 seconds. About 34 hours.**

That's just the connect phase.

### The Root Cause

I checked the Celery configuration:

```python
CELERY_BROKER_URL = REDIS_URL
CELERY_RESULT_BACKEND = REDIS_URL
CELERY_TASK_ALWAYS_EAGER = env.bool("CELERY_TASK_ALWAYS_EAGER", False)
```

Three lines, and `CELERY_RESULT_EXPIRES` was not among them. Celery's default is 86,400 seconds: **24 hours**.

The connect phase takes 34 hours. Chord results expire after 24 hours. By the time the last batch finishes, the results from the first batches have been garbage collected from Redis. The chord can never confirm that all members completed. The callback never fires. The job hangs permanently.

To validate, I set `CELERY_RESULT_EXPIRES` to a ridiculously short value in a local environment and ran a scan that exceeded it. The chord callback never fired. The job hung, exactly as predicted. Restoring a sane expiration value allowed the scan to complete normally.

### But That Wasn't the Interesting Part

The chord expiration explained the hang. It didn't explain why no other mechanism in the system noticed, mitigated, or reported it. As I traced through the code, I found six additional safeguards, each of which should have caught the problem at a different layer, all broken or missing.

I wasn't familiar with this model until an AI agent surfaced it while helping me think through the investigation. James Reason's Swiss cheese model describes exactly this: each defensive layer has holes, but failures only become catastrophic when the holes in multiple layers align. It fit: every defensive layer we had was punctured in a different way.

## The Failed Safeguards

### Safeguard 1: The Cancel Callback (Dead Code)

Both the connect and inspect phases register a `cancel_callback` with the task runner:

```python
def cancel_callback(self):
    if self.stopped:
        return True
    return False
```

`self.stopped` is never set to `True` anywhere in the codebase. The runner calls this callback periodically to check whether it should abort. It gets `False` every time and keeps running.

In some ways this is worse than having no callback at all. If the feature didn't exist, someone might have eventually built a real cancellation mechanism. But the callback's presence signals that cancellation is handled, so no one does. The team had discussed removing or reworking it months earlier, but it never made it to the priority board.

### Safeguard 2: The Exception Handler (Exception Hiding)

Every scan task is wrapped in an error handler:

```python
def wrapper(*args, **kwargs):
    try:
        return func(*args, **kwargs)
    except Exception:
        try:
            scan_task = ScanTask.objects.get(id=scan_task_id)
            scan_task.status_fail(...)
        except Exception:
            logger.exception(...)
        # implicit return None
```

When both the task *and* the error handler fail, the function falls through without a return statement, producing `None`. The Celery chain expects a `(success, task_id, status)` tuple. Receiving `None` breaks the chain silently: no exception, no retry, just undefined downstream behavior. The next task in the chain unpacks `None` as if it were a valid result, skips all processing, and propagates an empty success. A failure becomes a silent no-op that the system records as completed.

### Safeguard 3: The Cleanup Logic (Deletion Race Condition)

A code path that cleans up scan data deletes resources in this order:

1. Connection result rows (the foreign key target)
2. Task rows (which hold the foreign key reference)

If a Celery worker is mid-flight during cleanup, it holds an in-memory reference to a connection result that no longer exists. The worker crashes with a `ForeignKeyViolation`, the exception handler (Safeguard 2) swallows it, and the chain breaks silently.

The 80 `ScanTask.DoesNotExist` and 13 `ForeignKeyViolation` errors I noted earlier, the ones on *other* scans, were evidence of this race firing repeatedly over seven months.

### Safeguards 4, 5, and 6: The Missing Layers

Three additional safeguards simply didn't exist:

- **No mid-task cancellation checks.** Cancellation status was checked once at task entry. Once the actual work started, the task ran to completion or timeout regardless.
- **No task time limits.** Celery supports `time_limit` and `soft_time_limit` per task. Neither was configured. There was no enforced upper bound on execution time.
- **No watchdog.** There was no periodic task to detect jobs stuck in `running` state. No sweeper, no heartbeat, no alert. Once a job hung, it stayed hung until a human noticed.

### How They Aligned

| Defensive Layer | Intended Safety Net | The Hole |
|----------------|---------------------|----------|
| Result backend config | Chord results persist until callback fires | Redis TTL expired at 24h (unconfigured default) |
| Cancel callback | Stops runner when scan is canceled | `stopped` is never set; dead code |
| Exception handler | Surfaces task failures to the chain | Swallows exception, returns `None` |
| Cleanup logic | Deletes resources in FK-safe order | Deletes FK targets before FK holders |
| Cancellation check | Detects cancel during execution | Only checked at task entry |
| Task time limits | Bounds execution time | Not configured |
| Monitoring | Detects stuck jobs | Doesn't exist |

## The Fix

- Set `CELERY_RESULT_EXPIRES` to 90 days. The default 24-hour TTL is what killed the chord, but disabling expiration entirely would let orphaned keys accumulate in Redis indefinitely. Ninety days covers any realistic scan duration while still allowing Redis to self-clean.
- Reorder deletion logic to remove tasks before their foreign key targets.
- Add an explicit `return` to the exception wrapper so it produces a proper failure tuple instead of `None`.

The cancel callback (Safeguard 1) remains dead code. The team decided that fixing cancellation was scope creep for this effort. Scan cancellation had already been removed from the UI and CLI, so the broken callback has no active callers. We decided to buy the technical debt.

## Lessons Learned

### 1. Do the Arithmetic

The root cause became obvious once I multiplied three numbers: 82 groups x 6 credentials x 250 seconds = 34 hours. That single calculation, compared against the 24-hour default, explained the entire hang. Sometimes the most powerful debugging tool is basic arithmetic.

### 2. Defaults Are Invisible Design Decisions

Not configuring `CELERY_RESULT_EXPIRES` feels like not making a choice. Defaults are dangerous because they feel like not making a choice. But you are making a choice: you're accepting a value you've never tested against your actual conditions.

### 3. Dead Code Isn't Inert

The cancel callback's existence signaled that cancellation was handled, which meant no one built the real mechanism. Dead code blocks progress because it looks like a solution. Nobody builds the real thing when something already claims to handle it.

### 4. Exception Handling That Doesn't Re-Raise Is Exception Hiding

The wrapper caught the error, logged it, and returned `None`. From Celery's perspective, the task completed normally, and the error went into a log file that grew to 1.6 GB.

### 5. Compound Failures Are the Norm

None of these seven bugs were exotic: an unconfigured default, dead code, a missing return statement, a wrong deletion order. Each one would pass code review without much discussion. The system didn't fail because of a hard bug, but because every layer that should have caught the problem was itself broken or missing.

This changed how I think about writing and reviewing code. The question isn't just "does this work?" It's "what catches it when it doesn't?"

