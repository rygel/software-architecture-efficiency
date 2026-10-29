# Requirements and decision guide

Use this guide for architecture comparisons. The requirements and comparison method below are this skill's synthesis for existing-system efficiency reviews. The language notes draw selectively on Project-Foundations; they are not upstream benchmark findings or a universal project template.

## What must be known before selecting alternatives

Read repository and deployment evidence before asking for information. Record unknowns and continue a conditional comparison when they do not block it. Ask only for decisions that materially change suitability.

| Area | Evidence or decision needed | Why it changes the comparison |
| --- | --- | --- |
| Product behavior | Main user journeys, authentication/authorization, uploads, search, background jobs, API consumers and integration contracts | A replacement must retain the behavior that matters. Fewer dependencies alone is insufficient. |
| Workload | Normal and peak request rates, active users, read/write mix, payload sizes, data growth, job duration, burst and sustained work | CPU-bound computation, IO wait and browser interaction need different remedies. |
| User experience | Offline/local editing, input responsiveness, startup, accessibility, supported devices and UI/browser memory | Desktop and remote interaction costs differ; preserve the user journey when moving work between them. |
| Resources and budget | Existing CPU/RAM/disk utilization, hosting bill, acceptable build resources and operator time | Runtime efficiency, engineering cost and actual bill savings are separate outcomes. |
| Reliability | Acceptable downtime and data loss, recovery time, backup retention, multi-host needs and isolation | A single native process or SQLite file does not replace replication or failover requirements. |
| Delivery constraints | OS/CPU architecture, deployment permissions, team skills, supported libraries and upgrade obligations | A theoretically efficient language may have an expensive or unsupported integration path. |

Create explicit acceptance budgets from existing targets or user requirements. If none exist, propose budgets labelled for agreement rather than inventing them as requirements. Cover the relevant metrics: memory at representative peak, p95 response time at a stated successful request rate, error rate, browser responsiveness, recovery time and monthly total cost. Name the condition that would trigger moving to PostgreSQL, increasing VM size, splitting a worker, or retaining client-side state.

## Diagnose before rewriting

Determine whether the dominant cost comes from algorithms, database queries/indexes, network calls, serialization, large payloads/assets, hydration, process replication, caches, concurrency or the runtime itself. A compiled replacement that repeats the same expensive work may not solve the problem.

Compare four choices when relevant: keep current design, tune it, replace the costly component, and simplify the full stack. A native worker or library can be a useful intermediate step, but count FFI, IPC, packaging and failure-handling costs. Prefer a modular application to distributed services when independent deployment and scaling are unnecessary; do not introduce layers or split projects merely to resemble a diagram.

Use the bounded measurement contract in [report.md](report.md) when an experiment is needed. Never weaken durability or remove required behavior to improve a benchmark.

Go's [diagnostics guide](https://go.dev/doc/diagnostics) provides profiling tools. Rust's [Cargo profiles](https://doc.rust-lang.org/cargo/reference/profiles.html) distinguish development and optimized builds. These are measurement aids, not evidence that a particular language wins.

## Project-Foundations: useful engineering guidance

Reviewed 2026-10-28 at commit `1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0`. [Repository snapshot](https://github.com/erophames/Project-Foundations/tree/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0).

The upstream [skill](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/SKILL.md) provides language conventions for project setup and verification. Its [index](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/index.md) distinguishes libraries, CLIs, services and data projects, and advises incremental modernization of existing layouts. Adopt that distinction to cost a migration realistically. Read only the references relevant to the options being compared.

| Candidate | Guidance used from Project-Foundations | How this skill applies it |
| --- | --- | --- |
| [Go](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/go.md) | Start with modules and standard tooling, use narrow dependencies and purposeful package boundaries, keep concurrency visible; format, vet, test and build, with race checks for concurrent paths. | Assess a simple service before adding frameworks. Investigate allocations, goroutine growth and IO cancellation when relevant; choose SQLite drivers and native linking requirements deliberately. |
| [Rust](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/rust.md) | Begin with one Cargo crate unless real boundaries justify more; make ownership/errors explicit, limit unsafe code and avoid unnecessary large dependency/async stacks. | Compare maintenance skills, allocation behavior, dependencies and build resources alongside runtime results. Existing Cargo formatting, lint, test and release-build checks usually establish the starting verification scope. |
| [C#/.NET](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/csharp.md) | Use SDK projects, nullable contracts, asynchronous IO and cancellation; keep project count proportional to actual boundaries. | Compare framework-dependent, self-contained and Native AOT deployment separately. Validate AOT compatibility and publish warnings for the actual framework, serializer and database driver; do not assume every ASP.NET application supports AOT. |
| [C](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/c.md) and [C++](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/cpp.md) | Define ownership and error contracts; keep public interfaces narrow; use compiler diagnostics, tests and relevant sanitizers. C++ favors RAII and value semantics. | Useful for existing native code, integrations or justified hot paths. Include memory-safety, ABI, dependency and maintenance costs before recommending a service rewrite. |
| [SQL](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/sql.md) | Choose dialect explicitly, reuse existing migration tooling, apply schema changes to fresh and representative data, inspect hot-query plans. SQLite does not prove another engine's behavior. | Separate SQLite suitability from PostgreSQL portability. Document dialect-sensitive types, queries, constraints and transactions, then identify what must be tested on the destination engine. |
| Existing [Python](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/python.md) / [JavaScript](https://github.com/erophames/Project-Foundations/blob/1b14661bca0d4f44a1aec067c51ff6dfbad6ebf0/skills/start-project-foundations/references/javascript.md) | Keep testable behavior separate from entrypoints and framework glue; use established package and verification conventions. | Reuse behavior cases as a migration contract. Count dependency/build tools separately from deployed services. Improving dependency installation speed is not proof of lower application RAM or faster requests. |

Upstream also prescribes extensive analyzer, audit, coverage, mutation and pre-commit tool suites in some languages and integrates with the superpowers workflow. These are upstream recommendations, not dependencies of this skill. Respect the owning repository's policy, reuse existing checks and add a tool only for a concrete unresolved risk; do not require installing the plugin, adopting another workflow, generating scaffolding or changing layouts during an audit. Consult [official Native AOT limitations](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/) for runtime-specific decisions rather than treating a language convention as compatibility proof.

## SQLite first, with an honest PostgreSQL path

Evaluate the real writer queue, transaction duration and data locality before choosing SQLite. Account for per-connection enforcement settings, busy handling, WAL/checkpoint behavior, backup and filesystem requirements. WAL can improve reader/writer concurrency but does not create multiple simultaneous writers. Check the actual deployed SQLite version and current documentation. [WAL](https://www.sqlite.org/wal.html).

For portability, document decisions for IDs, booleans, money/decimals, timestamps/timezones, JSON, nullability, constraints, collation/case handling, placeholders, upserts and transaction isolation. An ORM or shared interface does not establish equivalent semantics. SQLite's flexible typing and default foreign-key behavior require deliberate validation. [SQLite differences](https://www.sqlite.org/quirks.html).

Use one set of behavioral cases for the database operations, with engine-specific setup and assertions only where semantics require them. Describe the proposed PostgreSQL schema/data conversion, cutover window, consistency checks and rollback boundary. Data written after cutover needs an explicit recovery strategy; retaining the old database alone is not a lossless rollback. Mark the path as planned until representative migration and behavior checks have run on PostgreSQL. An analysis should specify that work rather than provisioning another database without need.

Specify a consistent backup mechanism, independent retention and a restore verification. Do not copy only a live main database file and assume it captures WAL data. SQLite documents its [online backup API and alternatives](https://www.sqlite.org/backup.html).

## Complete-stack compatibility

Check the proposed combination, not just its individual tools. Record framework/features, serialization/reflection, authentication/session model, native extensions/FFI, database driver, OS/CPU and shared-library requirements, persistence/replicas and proxy behavior. Name the evidence and any unresolved constraint. For a material unknown, propose one focused check of the published artifact or integration before declaring the design suitable; do not install a toolchain or create a prototype solely for an analysis.

Apply the framework-specific checks in the selected [desktop](desktop.md) or [server](server.md) track. A restriction on an ASP.NET feature does not establish the publishing support of a desktop UI framework, and a native core does not establish that a WebView UI uses native controls.

## Native deployment handoff

Identify what the current packaging/deployment supplies and how its responsibilities survive the change. Desktop distribution and local-data details belong to [desktop.md](desktop.md); hosted service operations belong to [server.md](server.md). Produce installers, configuration or services only if implementation is authorized.

Use [report.md](report.md) for the comparison and migration output; this guide owns feasibility and requirements, not a second reporting format.
