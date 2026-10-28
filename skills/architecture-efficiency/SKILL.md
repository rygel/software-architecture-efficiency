---
name: architecture-efficiency
metadata:
  version: "1.0.0"
description: Compare existing resource-heavy architectures with efficient alternatives, including native services, lean frontends, compiled backends, SQLite, Caddy and hosting costs. Use for architecture efficiency reviews and side-by-side migration comparisons, not for implementing a rewrite by default.
---

# Architecture efficiency

Review the actual system and recommend a simpler, faster, lower-memory architecture. Favor native host services over Docker, compiled or native software over interpreted runtimes, SQLite first with a viable PostgreSQL path, and Caddy as reverse proxy. These are preferences to investigate, not proof that a rewrite or replacement will improve a specific workload.

The primary deliverable is a direct comparison of the existing heavier solution and practical efficient alternatives. A catalog of technologies or hosting providers alone does not complete the review.

For a substantive architecture comparison, use [references/decision-guide.md](references/decision-guide.md) to establish requirements, evaluate language and operational fit, and define evidence for a decision. It includes a selective synthesis of Project-Foundations with pinned source links; it does not depend on installing that plugin or its workflow.

## Review

1. Read the owning source, deployment configuration, dependencies and available operational documentation. Map components, runtimes, data stores, traffic, external integrations, deployment boundaries and the responsibilities Docker currently provides. Identify constraints such as OS, hardware, availability, security, concurrency, team skills and compatibility. Distinguish observed facts from assumptions and unknowns.
2. Establish an affordable baseline using existing measurements where possible: idle and representative peak memory (including child processes and containers), CPU, throughput, latency, startup, storage and operational complexity. Record workload, environment and measurement method. Do not run expensive benchmarks without a clear need and bounded resource use. If evidence is missing, propose a focused measurement; never invent savings or claim a language alone guarantees them.
3. Identify the largest resource and complexity costs first. Compare keeping and tuning each component with replacing it; account for runtime overhead, dependencies, actual workload, correctness, operational burden and migration cost. Include a focused replacement of an expensive component when that is a useful middle option. Check current official documentation when version-specific capabilities or compatibility matter.
4. Present a prioritized list of favorable alternatives appropriate to the system. For each, explain what it replaces, why it fits, expected benefits as hypotheses or measured results, drawbacks, dependencies, migration effort and how to validate it. Include a baseline/keep option when useful. Avoid recommending a wholesale rewrite without evidence that its benefits justify the risk and cost.
5. Provide a target architecture and a staged migration plan with dependencies, reversible checkpoints, data migration, rollback and acceptance measurements. Separate quick wins from major rewrites. Analysis and recommendations do not authorize modifying production or implementing changes.

For a researched catalog of frontend and infrastructure candidates, read [references/alternatives.md](references/alternatives.md) when comparing alternatives.

When evaluating deployment or operating costs, read [references/hosting.md](references/hosting.md). Check existing capacity first, then compare suitable hosting models and current official prices for the required region and workload. Include resource specifications, availability, commitment and renewal terms, taxes, IPv4, storage, backups, bandwidth and operational effort. Refresh the dated price snapshot before making a current recommendation; distinguish verified prices from estimates and incomplete quotes. Research does not authorize purchases or provisioning.

## Required comparisons

Start with an overview table: existing component or stack, proposed alternative, source of potential savings, functionality or operational tradeoffs, evidence status, and recommendation. Show enough information in each row to compare without opening separate entries. When no project is supplied, produce illustrative comparisons from the catalog and label them as scenarios rather than observed architectures.

Compare relevant pairs such as:

| Heavier solution to investigate | Efficient candidate to compare | What the comparison must resolve |
| --- | --- | --- |
| React/Next.js or another hydrated SPA with a persistent Node server | Static output or Go-rendered HTML with htmx and small local scripts | Actual need for client state, browser JS and memory, server runtime, round trips, offline support and feature parity. A static SPA has no persistent Node server merely because its build uses Node. |
| Python/Node API and worker processes | Go or Rust services; compatible .NET Native AOT application | Profiled bottlenecks, complete process memory, throughput, latency, dependency compatibility and rewrite cost. |
| Docker Compose deployment on one host | Native services supervised by the OS | Container processes and any VM overhead actually present, deployment reproducibility, isolation, limits, maintenance and recovery. Do not assume Linux containers each carry a separate OS or large fixed RAM penalty. |
| PostgreSQL plus separate cache/queue for a modest application | SQLite with suitable in-process caching and durable database-backed jobs | Write concurrency, durability, job semantics, process count, resources and a tested PostgreSQL migration path. |
| Separate proxy, certificate tooling and management panel | Caddy with required native services | Feature parity, certificate renewal, configuration complexity and measured resource use. Existing proxies are not inherently heavy. |
| Multiple managed app/database services or oversized VMs | Appropriately sized native VPS; static hosting for static content | Complete recurring bill, operator effort, availability, backup/restore and scaling requirements. |

For shortlisted designs, give a side-by-side matrix with columns for current architecture, tuned current architecture where useful, and proposed architecture. Include:

- Idle and representative peak server RAM; browser RAM separately for frontend changes.
- CPU under the same load, throughput, p50/p95 latency and startup where relevant.
- Browser transfer and executed JS, interaction responsiveness and build/deploy resources where relevant.
- Runtime processes, storage, dependencies, deployment and maintenance effort.
- Monthly and annual hosting totals with equivalent region, availability, persistence, backup and traffic requirements; show migration and setup costs separately.
- Preserved features, lost or changed capabilities, migration effort, rollback and evidence confidence.
- Language-specific build, dependency, verification and maintenance needs; distinguish a deployment-native binary from an application with a managed runtime, a web view, or WebAssembly.

Use comparable hardware, datasets and workloads for performance measurements. Distinguish application, whole-host and browser resource accounting; avoid double-counting shared memory or adding component peaks that did not occur together. Label every quantitative value as measured, published price/specification, estimate with assumptions, or unknown. Documentation describing a design does not establish benchmark results. If measurement is unavailable, provide the qualitative comparison and the smallest useful verification plan.

Calculate savings only from compatible evidence: monthly cost reduction = current total minus alternative total; percentage reduction = reduction divided by current total times 100, when the baseline is positive. A faster or smaller application saves hosting money only if it permits an actual plan change or reduces metered usage. If relevant, estimate migration payback as one-time migration cost divided by monthly net savings, with assumptions and positive savings required.

Finish with a ranked recommendation stating which changes offer the best supported benefit for their migration cost, which require measurements, and which should be retained. Include a complete current-stack versus proposed-stack comparison so combined savings and operational tradeoffs are visible. Do not substitute arbitrary scoring or invented benchmark percentages for evidence.

State the workload, hard constraints, acceptance budgets and scale triggers behind the recommendation. Classify each option as meeting requirements, conditional on named evidence, or unsuitable for a stated reason. Distinguish a proposed migration path from one exercised on representative data and the target engine. Use the same behavior cases and measurement definitions across candidates; do not add implementation or benchmark tooling merely to complete an analysis.

## Preferred alternatives

Use these as candidates, not a mandatory stack. Verify suitability and current support for the target platform and workload.

| Current need | Favorable candidates | Decision points |
| --- | --- | --- |
| Docker-hosted service | Native executable or package managed by systemd on Linux, or an appropriate Windows service | Recreate required isolation, least privilege, dependency management, resource limits, health checks, logging, restart, updates and rollback. Retain containers where their benefits demonstrably outweigh the overhead or native support is impractical. |
| Python or JavaScript backend and workers | Go, Rust, C#/.NET (including Native AOT where compatible), or C/C++ where appropriate | Compare real workload and libraries, integration compatibility, development and maintenance costs. Native AOT and static linking have platform and library limitations; compiled runtimes are not automatically lower-memory. Retain or optimize existing components where justified. |
| Browser frontend | Server-rendered HTML with modest JavaScript; Go templates, ASP.NET, or another suitable server framework | Browsers still need JavaScript for client-side behavior; preserve accessibility, interactivity and required functionality. |
| Database | SQLite first for suitable single-host workloads; PostgreSQL for multi-host access, write concurrency or operational needs that justify it | Assess write contention, data size, durability, backup/restore and network filesystem constraints. Use supported SQLite concurrency settings when appropriate. Keep SQL and data access portable where practical; isolate dialect-specific SQL, migrations and drivers, and verify the PostgreSQL migration path rather than promising a drop-in switch. |
| Reverse proxy and TLS | Caddy | Verify deployment environment, certificate issuance and renewal, routing, headers, authentication and existing proxy features before migration. |
| Cache, queue or scheduler | In-process or database-backed implementation where the workload permits | Preserve durability, concurrency, recovery and multi-instance behavior; retain dedicated infrastructure when those requirements need it. |

Do not remove services solely because they use Python, JavaScript or containers. Evaluate total system resources and operational tradeoffs, including security and reliable recovery. Make recommendations specific to the evidence and the user's actual priorities.
