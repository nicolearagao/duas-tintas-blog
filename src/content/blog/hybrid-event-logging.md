---
title: "Designing for Auditability Without Full Event Sourcing"
description: "How I arrived at a middle ground between CRUD and event sourcing - an append-only event log alongside traditional mutable state, same database, same transaction - while investigating session state for an open source legal aid portal."
pubDate: 2026-06-28
category: "professional"
lang: "en"
tags: ["architecture", "django", "event-sourcing", "python"]
---

I had about five hours to dig into an open source codebase I'd never seen before, understand its state model, and propose something coherent. The project was a legal aid portal - a Django application where users interact with AI agents to understand their legal situation, extract case facts, and prepare for court. An [open issue](https://github.com/freelawproject/litigant-portal/issues/177) asked for a single container per legal issue where everything goes in and every screen reads from.

Five hours isn't a lot. You can't go deep on every rabbit hole - you have to read the codebase, identify the structural problem, and propose something that works with what's already there. Somewhere in hour two, I had the architecture equivalent of standing at a fork in the road with bad signage in both directions. One path said CRUD, the other said event sourcing, and neither felt right.

This article is about the pattern underneath that work, not the legal aid system itself.

## The Problem That Started It

The way the system worked: a user describes their situation in a chat, AI agents extract structured facts from the conversation - court dates, parties involved, possible defenses - and the sidebar updates with that information. There's also a guided flow (think step-by-step questionnaire) for users who'd rather answer specific questions than have an open conversation. Both paths are supposed to build the same picture of the user's legal situation.

The problem was that each feature had grown its own storage. Chat messages lived in one set of tables. Case facts lived in another, written by AI tools into an unversioned JSON blob. User answers from the guided flow lived only in the Django session, not even in the database - gone when the session expired. And the identity system tied it all together loosely, more persistence layer than state model.

None of this was designed as a system. Each piece was built to serve its own feature, and it worked well enough in isolation. But the issue asked for a unified container, and the current architecture couldn't support that without addressing something deeper. Three things were missing:

**No audit trail.** When an AI tool extracted a court date from a conversation, the original user input that produced it wasn't linked to the extracted fact. If the AI got it wrong - and it does - there was no way to trace back to what the user actually said.

**No replay capability.** The AI pipeline would evolve. New models, new prompts, different tool logic. There was no way to test a new version against real inputs.

**No history of user decisions.** When a user corrected something the AI got wrong, the corrected value just overwrote the old one. No record of what changed, from what, or why.

These are exactly the problems event sourcing was designed to solve. So I went there first.

## The Detour Through Event Sourcing

In pure event sourcing, you store only events. Current state is always derived - either by replaying the event stream on every read, or by maintaining materialized projections that update when new events arrive.

I started sketching what that would look like for this system, and the scope kept growing. The sidebar reads `CaseInfo`, `Deadline`, and `ActionItem` rows directly. In event sourcing, those tables become projections rebuilt from events. Every existing view needs a new read path. Then you need projection infrastructure - something to listen for events and update read models. In Django, that's either synchronous (too slow) or asynchronous (new infrastructure to build and monitor). And if projections update asynchronously, there's a window where the read model hasn't caught up. In a legal tool where someone is preparing for a court date tomorrow, "your deadline will appear in a few seconds" is not an answer.

Event sourcing has a strong track record in financial and legal systems. But the infrastructure overhead is justified when you have dedicated teams, high event volumes, and complex derived state. A small team shipping a beta on a single server is not that situation.

## Why "Just Add a Field" Wasn't Enough Either

The opposite extreme was tempting. Add a `topic` field to the case info table, key the session by topic, fix the immediate isolation problem. Smallest possible diff.

This fixes the symptoms but not the structure. Case facts are still overwritten in place, inputs are still not captured separately from outputs, user corrections still vanish. And the next time a new integration arrives - a document upload flow, an external API, a new AI tool - the same gaps resurface because the architecture doesn't have a place for input history.

I was running out of hours. Then something clicked.

## The Click

I was framing this as a binary: either state is mutable or state is derived from events. But nothing stops you from having both - in the same database, in the same transaction - as long as you're honest about what each one is for.

The system has two truths, each answering a different question. Working state answers: *what is true right now?* The event log answers: *how did we get here?*

Views never read the event log. The write path changes: every write goes to both places.

## The Pattern

Two representations of session state, maintained in lockstep:

```
                         ┌─────────────────────────────────┐
                         │     transaction.atomic()         │
                         │                                  │
  User input             │  ┌────────────────────────────┐  │
  (chat, upload,    ───────>│  Append to event log       │  │
   guided flow,          │  │  (what happened, verbatim) │  │
   user correction)      │  └────────────────────────────┘  │
                         │                                  │
                         │  ┌────────────────────────────┐  │
                    ───────>│  Update working state       │  │
                         │  │  (what the UI shows)       │  │
                         │  └────────────────────────────┘  │
                         │                                  │
                         └─────────────────────────────────┘
                                       │
                         Same Postgres instance. ACID.
                         No broker. No relay.
```

**The event log** is append-only. It stores raw inputs: user messages, document uploads, user decisions (confirmed, dismissed, corrected), system context changes. Every event has a validated payload - not free-form JSON, but a discriminated union of Pydantic models that rejects malformed data at write time.

**Working state** is what the UI reads from. The existing tables remain exactly as they are. Views query them. Templates render them. Nothing breaks.

A service layer wraps both writes in a single transaction:

```python
class BriefcaseService:
    @staticmethod
    def record_and_update(briefcase, event_type, payload):
        with transaction.atomic():
            BriefcaseEvent.objects.create(
                briefcase=briefcase,
                event_type=event_type,
                payload=payload,
            )
            # Update mutable working state based on event type
            ...
```

This is not event sourcing - working state is written directly, not derived from events. And this is not CQRS - both writes hit the same Postgres instance in the same transaction, no eventual consistency. It's closer to what some people call [hybrid event sourcing](https://github.com/m-gris/effect-event-sourcing-poc/blob/main/hybrid-event-sourcing-approach.md) - your RDBMS stays authoritative for current state, and the event log provides audit and history alongside it.

The read paths make this concrete:

```
  Reads                       Event log
                                  ▲
  Sidebar ───────┐                │
  Chat ──────────┤            Audit
  Dashboard ─────┤            Replay
                 │            History
                 ▼
          Working State
```

Views never touch the event log. The sidebar queries case info. The dashboard queries deadlines. The event log exists for when you need to answer "what happened" - debugging, regression testing, compliance - not for rendering pages.

## What This Gets You

With the event log in place, the three gaps close.

Every input is preserved in its original form, linked to whatever the AI extracted from it. When the AI pipeline changes, you can feed the same inputs through the new version and diff the structured outputs - did the new pipeline extract the same court date? Did it miss a deadline? The assertions are on structured data, not free text - LLMs are non-deterministic, so you can't diff prose, but you can diff whether `UpdateCaseFacts` produced a deadline with label X from the same inputs.

User decisions get recorded too. When a user corrects a date the AI extracted wrong, the event log captures what changed, from what, to what, and why. Over time, these accumulate into a dataset for evaluating pipeline quality.

The data has to exist first, though. Retrofitting an event log onto a system that never captured raw inputs is far harder than capturing them from the start. And every existing view, query, and template continues to work unchanged. The event log is purely additive.

## The Risks

### Write discipline

Every write path must remember to do both: log the event AND update working state. If a developer adds a new AI tool that writes to case info but forgets the event, the log is incomplete. Nobody notices until somebody needs the audit trail and it isn't there.

The mitigations are real but none of them are bulletproof. A service layer centralizes writes - AI tools call the service instead of writing to models directly. If you bypass it, code review catches it - hopefully. If the discipline proves fragile over time, the service can evolve toward a synchronous projector: write to the event log first, derive working state from the event. This moves toward event sourcing incrementally, without the upfront infrastructure cost.

### Drift and schema evolution

Over time, the event log and working state could diverge through application-level bugs - wrong event type, missing field, update logic that doesn't match the event. The transaction boundary helps (both succeed or both fail), but the defense is testing the service layer directly: given this event, assert that working state changed in this specific way.

The event log also has a schema evolution problem. Old events stay forever with whatever shape they were written with. Pydantic's discriminated unions help - add new event types without breaking old ones. For changes within an event type: add optional fields, never remove required ones, or version the type itself.

## When This Pattern Fits

Not every application needs an event log alongside its mutable state. This pattern earns its complexity when your domain cares about audit trails (legal, financial, healthcare), when you have AI or business logic pipelines that will evolve and need regression testing against real inputs, when you have an existing CRUD app with working views that you don't want to rewrite, and when your team is small enough that a service-layer convention holds.

If you're starting from scratch, evaluate event sourcing properly - you don't have the migration cost to justify the compromise. If your team is large enough that convention won't hold, you need stronger enforcement.

## Where It Sits

| Approach | Write path | Read path | Consistency | Cost |
|----------|-----------|-----------|-------------|------|
| Pure CRUD | Update mutable rows | Query mutable rows | Immediate | None beyond your ORM |
| Hybrid event log | Append event + update rows (same tx) | Query mutable rows | Immediate | Service layer discipline |
| CQRS | Append event | Query projected read models | Eventual | Projection infrastructure |
| Event sourcing | Append event | Replay or query projections | Derived | Full event infrastructure |

I landed here because I had five hours and a codebase that already worked. Event sourcing would have been a rewrite. CRUD without the log would have left the same gaps that prompted the issue in the first place. The hybrid was the option that fit the actual constraints - team size, timeline, existing infrastructure - without pretending those constraints didn't exist.

*P.S. While writing this, I kept going back to something a project maintainer said during the discussion: "One briefcase per legal issue." Five words that collapsed hours of abstraction into a concrete model. Sometimes the best architectural insight isn't a pattern - it's a constraint that someone with domain knowledge hands you, and you have the good sense to accept it.*
