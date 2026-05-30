---
title: "How Do You Debug What You Can't Reproduce? An IPv6 case study in minimal test environments"
description: "Debugging IPv6 connectivity issues without access to the customer's environment using minimal reproducible examples and fault isolation."
pubDate: 2025-05-30
category: "professional"
lang: "en"
tags: ["debugging", "devops", "linux", "IPv6"]
---

## The Problem

A customer reported that their Discovery scans were failing in a pure IPv6 environment. The error messages pointed to two different issues:

**Scenario A:** `LocationParseError: Failed to parse` - when using raw IPv6 addresses

**Scenario B:** `OSError: [Errno 101] Network is unreachable` - when using FQDNs

The tricky part? The customer was running a single-stack IPv6 OpenShift cluster - a rare configuration typically seen in telecommunications environments on bare-metal infrastructure. This wasn't a cluster we could just spin up on the fly. Setting one up would require specialized OpenShift expertise and dedicated resources, neither of which were readily available to us.

## The Challenge: You Can't Always Replicate the Customer's Environment

My first instinct was to provision an identical environment. I started researching options:

- Convert an existing cluster to IPv6?
- IPv6 on AWS? Complex, not available in standard templates.
- Request cluster time from internal infrastructure? Unclear if possible, and unknown timeline.

I was stuck. How do you debug a network connectivity issue if you can't reproduce the network?

## A Brief Detour: Debugging as a Discipline

Before diving into the solution, it's worth stepping back to consider what we're actually doing when we debug. Debugging isn't just trial and error - it's a systematic process with well-established principles.

### The Scientific Method in Debugging

Debugging, at its core, follows the scientific method:

1. **Observe:** Gather evidence (error messages, logs, customer reports)
2. **Hypothesize:** Form a theory about what's causing the issue
3. **Predict:** Determine what you'd expect to see if your hypothesis is correct
4. **Test:** Create an experiment to validate or invalidate the hypothesis
5. **Iterate:** Refine your hypothesis based on results

In our case, we had two hypotheses for Scenario B:

- **Hypothesis 1:** The Quadlet-managed container network lacks IPv6 routes
- **Hypothesis 2:** Python's socket library behaves differently than curl under certain conditions

Both needed testing - but we couldn't test them without a reproducible environment.

### The Minimal Reproducible Example (MRE)

The concept of a Minimal Reproducible Example (MRE) is fundamental in open source and debugging communities. An MRE is:

- **Minimal:** Contains only what's necessary to reproduce the issue
- **Reproducible:** Can be reliably repeated
- **Example:** Demonstrates the specific problem

The key insight is that you don't need to replicate the customer's entire environment. You need to replicate the conditions that cause the bug. Everything else is noise. For our IPv6 issue, the customer's OpenShift cluster had:

- Control plane nodes
- Worker nodes
- Operators and workloads
- Storage configurations
- Network policies
- ...and much more

But none of that mattered for our bug. We needed exactly two things:

1. An IPv6-only network
2. An HTTP endpoint to connect to

### Fault Isolation: Divide and Conquer

Fault isolation is the practice of systematically narrowing down where a problem occurs by testing components independently. Instead of asking "why doesn't this work?", you ask "at which layer does it stop working?"

The process:

1. Identify the layers/components in your system
2. Test each layer in isolation
3. Find the boundary where behavior changes
4. Focus investigation there

For container networking, the layers are:

```
Host System → Container Runtime → Container Network → Application
```

If something works at the host level but fails in a container, you've isolated the issue to container networking. This dramatically reduces the search space.

## The Insight: What Are We Actually Testing?

During a team discussion, my colleagues offered a critical insight: "You don't need OpenShift. You just need to prove that the scanner can connect to an IPv6 address. If the log says 'API returned 404,' that means connectivity worked - the scanner reached the server."

This is MRE thinking in action. We stripped away everything except the core question: can a container reach an IPv6 endpoint?

I didn't need a full OpenShift cluster with operators, nodes, and workloads. I needed:

1. A server listening on an IPv6 address
2. The ability to make HTTP requests to it
3. The same container environment the customer was using

A simple Python HTTP server would do.

## The Setup: Building a Minimal Reproduction Environment

I created the simplest possible IPv6-only environment on my Fedora laptop using libvirt.

### Step 1: Create an IPv6-Only Network

```xml
<network>
  <name>ipv6-only</name>
  <ip family='ipv6' address='fd00:dead:beef::1' prefix='64'>
    <dhcp>
      <range start='fd00:dead:beef::100' end='fd00:dead:beef::1ff'/>
    </dhcp>
  </ip>
  <nat ipv6='yes'>
    <port start='1024' end='65535'/>
  </nat>
</network>
```

I used a ULA (Unique Local Address) range - `fd00:dead:beef::/64` - which is perfect for local testing. No need for public IPv6 allocation.

### Step 2: Create a RHEL 9 VM

I installed a minimal RHEL 9 VM on this network, disabling IPv4 entirely during installation. The VM received the address `fd00:dead:beef::126` via DHCPv6.

### Step 3: Start a Mock Server

Instead of deploying OpenShift, I started a one-liner HTTP server:

```bash
python3 -m http.server --bind fd00:dead:beef::126 6443
```

That's it. A functioning "API endpoint" in 30 seconds. Total infrastructure cost: One VM, zero cloud resources, about 20 minutes of setup.

## The Validation: Applying Fault Isolation

With the environment ready, I applied fault isolation to test connectivity layer by layer. This systematic approach would prove crucial.

### Layer 1: Host to VM

**Hypothesis:** Basic IPv6 connectivity works from my laptop.

```bash
$ curl -v http://[fd00:dead:beef::126]:6443/
* Connected to fd00:dead:beef::126 port 6443
< HTTP/1.0 200 OK
```

**Result:** Confirmed. Moving to the next layer.

### Layer 2: Container with Host Networking

**Hypothesis:** Containers can reach IPv6 when sharing the host's network stack.

```bash
$ podman run --rm --network=host curlimages/curl \
  curl -s -o /dev/null -w '%{http_code}' http://[fd00:dead:beef::126]:6443/
200
```

**Result:** Confirmed. The issue isn't with containers in general.

### Layer 3: Container with Default Bridge

**Hypothesis:** Podman's default bridge network supports IPv6.

```bash
$ podman run --rm curlimages/curl \
  curl -s -o /dev/null -w '%{http_code}' http://[fd00:dead:beef::126]:6443/
200
```

**Result:** Confirmed. The default bridge works on my system.

### Layer 4: Application Container via Quadlet

**Hypothesis:** The Quadlet-managed environment behaves the same as manual container runs.

```bash
$ podman exec quipucords-server python3 -c "
import socket
socket.create_connection(('fd00:dead:beef::126', 6443), timeout=5)
"
FAILED: [Errno 101] Network is unreachable
```

**Result:** Failed. We found the boundary. The fault was isolated: something about the Quadlet-managed network is different from the default Podman bridge or host networking.

## The Discovery: Two Distinct Root Causes

Fault isolation pointed me to the Quadlet network. But as I investigated further, I discovered that Scenario A (URL parsing) was a completely separate issue.

### Scenario A: The URL Was Malformed

Testing the URL parsing directly:

```bash
$ podman run --rm --network=host python:3.12 python3 -c "
import requests
requests.get('http://fd00:dead:beef::126:6443/')
"
# Failed to parse: http://fd00:dead:beef::126:6443/
```

The problem is visible in the URL itself. IPv6 addresses contain colons, and so does the port separator. The parser sees `fd00:dead:beef::126:6443` and cannot determine where the host ends and the port begins.

Per RFC 2732, IPv6 addresses in URLs must be wrapped in square brackets:

- **Correct:** `http://[fd00:dead:beef::126]:6443/`
- **Incorrect:** `http://fd00:dead:beef::126:6443/`

Our code was building URLs by simple string concatenation:

```python
host_uri = f"{protocol}://{host}:{port}"
```

This works for IPv4 and hostnames, but fails for IPv6.

### Scenario B: The Container Network Had No IPv6

The Quadlet-managed containers use a custom Podman network. I inspected it:

```json
$ podman network inspect systemd-quipucords
{
  "ipv6_enabled": false,
  "subnets": [
    {"subnet": "10.89.0.0/24", "gateway": "10.89.0.1"}
  ]
}
```

There it was: `ipv6_enabled: false`. The network only had an IPv4 subnet. The containers literally had no path to reach IPv6 addresses.

The root cause was our installer's network configuration file:

```ini
[Network]
# An empty configuration. Podman defaults to IPv4-only.
```

## The Fix: One Line Each

### Fix for Scenario A (Application Code)

Create a utility function to wrap IPv6 addresses:

```python
import ipaddress

def format_host_for_url(host: str) -> str:
    """Wrap IPv6 addresses in brackets for URL construction."""
    try:
        ipaddress.IPv6Address(host)
        return f"[{host}]"
    except ipaddress.AddressValueError:
        return host

# Usage
host_uri = f"{protocol}://{format_host_for_url(host)}:{port}"
```

### Fix for Scenario B (Installer Configuration)

Enable IPv6 in the Quadlet network definition:

```ini
[Network]
IPv6=true
```

After applying this change and restarting the containers:

```bash
$ podman exec quipucords-server python3 -c "
import socket
socket.create_connection(('fd00:dead:beef::126', 6443), timeout=5)
print('SUCCESS')
"
# SUCCESS: Can reach IPv6 VM
```

The hypothesis was validated. The fix worked.

## Lessons Learned

### 1. Minimal Reproduction Beats Exact Replication

I didn't need a complex bare-metal IPv6 OpenShift cluster. I needed a VM with a Python one-liner. The key was applying MRE thinking: what is the minimum environment that reproduces the specific failure?

### 2. Test Each Layer Separately

Fault isolation works. By testing host → container → Quadlet progressively, I discovered that the issue only appeared in the Quadlet-managed environment. Without this layered approach, I might have spent hours debugging the wrong component.

### 3. Hypothesize, Then Test

I started with two hypotheses about Scenario B. The scientific method gave me a framework: form a hypothesis, predict what you'd see if it's true, test it, iterate. This prevents the aimless debugging that wastes hours.

### 4. Question Your Assumptions

I assumed "curl works in the container" meant Python would work too. It didn't - different network namespaces, different behavior. The customer's report that "curl works but the app fails" was a valid and important clue that I initially dismissed.

### 5. Two Bugs Can Look Like One

The customer reported "scans fail in IPv6." What they actually had was two unrelated bugs that both happened to affect IPv6:

- A URL formatting bug (would fail even with perfect networking)
- A network configuration bug (would fail even with perfect URLs)

Treating them as one problem would have led to incomplete fixes.

### 6. Collaboration Accelerates Everything

My teammates suggested the minimal reproduction approach. Without that conversation, I might still be waiting for infrastructure access. Sometimes the best debugging tool is a five-minute Standup discussion.

## Conclusion

Customer environments are often impossible to replicate exactly. But exact replication is rarely necessary. The principles of debugging — minimal reproducible examples, fault isolation, and the scientific method - provide a framework for making progress without perfect information.

By identifying the core behavior you need to test and building the simplest possible environment that exhibits it, you can debug effectively without access to production systems.

In this case, a RHEL VM, a Python HTTP server, and a Quadlet configuration file were enough to find and fix two bugs that would have been nearly impossible to debug in the customer's actual environment.

The total time from "I can't reproduce this" to "both fixes validated"? About four hours.

## Further Reading

- "Why Programs Fail" by Andreas Zeller — A systematic approach to debugging
- RFC 2732 - Format for Literal IPv6 Addresses in URLs
- Julia Evans' debugging zines - Accessible, visual explanations of debugging techniques
- Podman Quadlet documentation - Understanding systemd-managed containers
