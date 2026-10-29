---
name: architecture-efficiency
metadata:
  version: "1.2.0"
description: Compare resource-heavy desktop, server and hybrid architectures with efficient alternatives. Use for architecture reviews covering native applications and services, UI responsiveness, RAM, databases, deployment and hosting costs, not for implementing a rewrite by default.
---

# Architecture efficiency

Produce an evidence-backed comparison of the current architecture and practical alternatives. Favor efficient native software, local SQLite where suitable, lean UIs and simple deployment. For server components, consider native services, Caddy and an explicit PostgreSQL path. These preferences guide investigation; they do not prove a rewrite will improve the workload.

## Select the application track

- **Desktop:** an installed user-facing application. Load [desktop.md](references/desktop.md) for UI/event-loop responsiveness, process-tree resources, local data, platform integration and distribution. Do not introduce server infrastructure or hosting costs into a local-only application.
- **Server:** a hosted service, worker, API or browser-delivered application. Load [server.md](references/server.md) for request/job concurrency, reliability, deployment and hosting. A web frontend is part of this track even when its assets are static.
- **Hybrid:** a desktop client with a remote service. Apply both tracks only to their owning components; attribute device, browser/WebView and backend resources separately. Include the offline/synchronization contract and client/server version compatibility. Infer the track from inspected deployment evidence, not language or framework names; ask only if a consequential boundary remains unclear.

## Review workflow

1. Inspect owning source, deployment and operational evidence. Establish required behavior, workload, resource budgets, reliability and team/platform constraints. Record unknowns; ask only for information that changes the decision. When no system is supplied, label comparisons as illustrative scenarios.
2. Identify the dominant costs before choosing replacements. Compare keeping, tuning, replacing one costly component and simplifying the stack as relevant. Preserve required behavior, security, isolation and recovery. Include migration cost and operator effort.
3. Check the complete candidate stack with [decision-guide.md](references/decision-guide.md): framework, libraries, runtime, database driver, CPU/OS, persistence and deployment must work together. Reject an incompatible combination; label an unverified one conditional. A language name or successful development build is not compatibility proof.
4. Use the measurement definitions and compact report in [report.md](references/report.md). Reuse existing evidence and behavior cases. If measurements are missing, give a conditional comparison and the smallest useful verification plan; do not invent benchmark results or numeric savings.
5. Recommend the best-supported option for its cost and requirements, including what to retain, scale triggers and a staged migration with success criteria and rollback boundaries. A proposed PostgreSQL path remains planned until exercised on representative data and PostgreSQL itself.

## Load only relevant references

- [decision-guide.md](references/decision-guide.md): requirements, bottleneck diagnosis, compatibility, language engineering guidance, database and native deployment considerations. Includes attributed Project-Foundations guidance without requiring its plugin or tool suites.
- Desktop/server track references own application-specific checks and route to suitable alternatives; do not load the other track unless the system has those components.
- [alternatives.md](references/alternatives.md): browser/server replacement candidates, when relevant to the server track.
- [hosting.md](references/hosting.md): server deployment models, cost components and historical price research when hosting matters. Refresh official prices, market, tax basis, contract/renewal and stock before current recommendations. Check existing capacity first.
- [report.md](references/report.md): canonical evidence labels, measurement contract and comparison output. Scale it to the request; mark irrelevant dimensions once rather than filling tables with boilerplate.

## Decision rules

- Distinguish build-time tooling, server runtime and browser code. A static SPA does not require a running Node server. Retain client state or offline behavior where the product needs it.
- Count actual runtime and deployment overhead. Linux containers do not each carry a separate OS. Replacing Docker must preserve the responsibilities it supplied.
- Separate compilation, packaging and memory management. Go and .NET Native AOT can produce native executables while retaining runtime services such as garbage collection; native does not guarantee low RAM or high throughput.
- Evaluate the SQLite writer queue, locality, durability and recovery. WAL does not create multiple simultaneous writers. Do not treat an ORM as proof of PostgreSQL compatibility.
- Use measured, published, estimated and unknown evidence labels. Documentation establishes capabilities, not workload speedups. Cost savings require a real plan or usage change with equivalent service requirements.
- Keep analysis within scope: do not implement a rewrite, provision hosting or change production merely to complete a comparison. Bound any justified experiment and follow the repository's existing check policy.
