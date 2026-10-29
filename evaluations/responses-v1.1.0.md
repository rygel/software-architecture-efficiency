# Architecture-efficiency responses

Method: Independently read skill v1.1.0, its four relevant references and evaluations/cases.md; answered each synthetic snapshot without running workloads or inspecting live systems. No rubric, prior evaluation or repository changes were used. “Measured (supplied)” below means reported observation inside the synthetic fixture, never reproduced evidence. Official compatibility documentation was consulted on 2026-10-29; documentation establishes capabilities, not speedups. Monetary calculations are estimates from scenario assumptions, not provider quotes.

SKILL.md SHA256 actually loaded: `4A61E9BF3373777A351697DDB3AD50BFDD5E7C15A3A16901F4BFB136B79CF462`.

## Case 01

Retain Caddy/static hosting and first investigate browser costs. The supplied deployment already has no persistent Node process: Node builds `dist/`; Caddy serves it; React executes in the browser. There is no server runtime to remove by replacing React.

| Option | Compatibility and evidence | Cost, effort and decision |
| --- | --- | --- |
| Current static React | Supplied: supports bundled catalog and search/filter. Browser transfer, CPU, responsiveness and total memory unknown. | Existing shared VM bill/capacity unknown; retain baseline. |
| Tune assets or use prebuilt HTML plus small client search/filter | Conditional: preserve filtering, accessibility and navigation. Lower browser overhead is unmeasured. | Tuning first; HTML conversion costs effort but needs no API/database or new dynamic server. |

Measure one representative load-and-filter journey on a fixed device/browser, recording transferred bytes, JS execution, responsiveness and consistently accounted browser memory. Set acceptance budgets from that baseline before choosing a conversion. Server utilization is a separate measurement. No cash saving is established without an actual bill/capacity change.

If tuning misses agreed budgets, trial the simpler frontend against the same behavior cases; retain versioned static output for immediate rollback. Revisit when content scale or measured browser costs justify it; a database migration is irrelevant here.

## Case 02

Prefer the Go/templates/htmx/SQLite/database-jobs trial conditionally, pending recovery and service lifecycle checks. The supplied common behavior suite covers permissions and retry/idempotency; single-host CRUD fits the trial better than an assumed multi-host deployment.

| Option | Requirements and supplied measurements | Recurring scenario cost and effort |
| --- | --- | --- |
| Current Node/Python/PostgreSQL/Redis/Caddy | 1,400 MiB whole-host maximum and p95 210 ms: fails 700 MiB/200 ms budgets. 40 successes/sec, 0% errors meet load/error budgets. | USD 24 compute + 4 independent backup = 28/month before tax. No migration. |
| Tune current | Possible smaller change, but no supplied result establishes budget compliance. | Cost reduction unknown until an equivalent smaller plan passes. |
| Trial Go/htmx/SQLite/jobs/Caddy | 310 MiB and p95 120 ms meet sampled memory/latency budgets; 40 successes/sec, 0% errors. Recovery and shutdown/restart unknown. | USD 6 + 2 independent backup = 8/month before tax; estimated migration USD 120; operator hours unchanged. |

Measured (supplied `trial-summary.csv`): same 2-core VM class, dataset/durability and offered 40/sec; five-minute warm-up, fifteen-minute window, one-second whole-host samples including OS/proxy/data services; one run each. The observed maximum falls 1,090 MiB (77.9%); p95 falls 90 ms (42.9%). These are preliminary window-specific comparisons, not lifetime peaks or proven repeatable capacity. Versions, exact OS/CPU/sharing, production profiles, request mix, latency boundary, cache/swap treatment and run variability remain unknown.

Estimated savings: USD 20/month (71.4%), USD 240/year at unchanged rates; simple payback 120/20 = 6 months after cutover; first-year net USD 120. Taxes are unknown; unchanged operator hours add no labor difference. The smaller offer's capacity/recovery must still qualify.

Before cutover, inventory actual Go/htmx/SQLite driver and Linux x64 dependencies; verify consistent independent backups restore within 30 minutes with at most 24 hours' loss. Test graceful stop/restart and job recovery; only repeat bounded load checks where uncertainty changes acceptance. Provide dedicated service identities, local durable data, protected secrets, resource limits, supervision/logs, Caddy routing/health checks and versioned updates.

Then rehearse data conversion and consistency, quiesce writes for cutover, and rerun the shared suite on the candidate tier. Retain old services/data; rollback after new writes requires reconciliation, not merely restarting the old database. A PostgreSQL return path remains planned until schema/data conversion and behavior are exercised on PostgreSQL. Revisit SQLite for sustained writer queues or multi-host requirements.

## Case 03

Keep the client editor and optimize scene processing first. The supplied CPU traces implicate repeated work across 40,000 objects during a disconnected workflow; server rendering cannot perform those immediate offline interactions.

| Option | Compatibility and performance evidence | Cost/decision |
| --- | --- | --- |
| React editor + Go/PostgreSQL | Preserves offline editing, undo/redo, drafts and synchronization. Trace identifies client computation; memory baseline incomplete. | Existing team fit; tune first. |
| Server-rendered replacement | Unsuitable as a wholesale replacement for required offline/local editing. Retaining a substantial client editor remains necessary. | No demonstrated gain or migration payback; introduces rewrite risk. |
| Focused scene-processing change | Conditional: avoid repeated full-scene work; investigate indexing, incremental updates or visible-region processing according to traces. | Smaller scope; worker offload only if profiling justifies transfer/coordination costs. |

Use a bounded, fixed-browser/device replay of drag/undo/save in airplane mode with the representative scene. Compare interaction timing, CPU work, JS heap and total browser-process memory separately; agree responsiveness/memory budgets before acceptance. Verify local draft recovery and later sync/conflicts using existing cases. Keep Go/PostgreSQL unless separate evidence identifies their cost.

Ship a reversible scene-processing change only after parity and agreed budgets pass; keep a previous frontend artifact and draft-format compatibility for rollback. Revisit broader replacement only if focused changes fail and an alternative demonstrably preserves the offline contract. Hosting savings and server benchmarks are presently irrelevant to the observed bottleneck.

## Case 04

Reject the proposed shared SQLite file. Retain PostgreSQL; evaluate container removal independently.

| Option | Compatibility verdict | Resource/cost and operations |
| --- | --- | --- |
| PostgreSQL with current containers | Supports central transactional access; actual 60-second failover and zero-loss compliance remains unverified. | RAM, bottleneck and bill unknown; containers supply limits/identities. |
| Native PostgreSQL and native app services | Conditional on preserving HA, identities, resource limits and operational controls. Team can operate it. | Can simplify packaging; no measured RAM or monetary saving. |
| Shared network SQLite WAL, no containers | Incompatible with proposed topology; provides no demonstrated failover solution. | Reject regardless of hypothetical RAM benefit. |

Published: SQLite WAL requires processes on the same host and permits one writer at a time; it does not support this multi-host network-file proposal. SQLite transactions themselves are not the objection. [SQLite WAL documentation](https://www.sqlite.org/wal.html), checked 2026-10-29.

First inspect the existing PostgreSQL replication, acknowledgement durability, fencing, routing and failover configuration against sustained overlapping writes and account-balance invariants. Automatic failover alone does not prove no acknowledged loss. Define a bounded future fault/recovery check demonstrating recovery within 60 seconds and no acknowledged transaction loss.

If native deployment is justified, preserve service accounts, limits, isolation/security controls, persistent directories, secrets, logs, graceful stop/restart, health routing, backup/restore and versioned rollback. Linux containers share the host kernel; removing them does not remove a separate OS per container. Migrate one operational boundary at a time, retain database continuity, and block cutover until equivalent isolation/recovery passes. No engine migration is justified by the supplied evidence.

## Case 05

Use conventional .NET 10 hosting on Windows x64 as the initial deployment candidate. Reject the proposal to enable Native AOT while preserving every current feature unchanged.

| Option | Complete-stack verdict | Evidence/cost |
| --- | --- | --- |
| Framework-dependent .NET 10 MVC | Conditional: preserves the architectural feature model; requires installed compatible runtime and published-artifact integration checks. | No performance/bill baseline. Lowest proposed feature-change scope. |
| Self-contained win-x64 MVC | Conditional: bundles runtime; verify plugins, PostgreSQL driver and deployment dependencies. | Packaging convenience does not establish lower RAM; runtime security updates need republishing. |
| Native AOT unchanged | Incompatible: .NET 10 table marks MVC, sessions and non-JWT authentication unsupported; dynamic plugin loading is also a blocker. | Development build is not published-artifact proof. |
| Linux Arm migration | Conditional until OS/architecture, plugin/native libraries and PostgreSQL integration are checked. | “Cheaper” has no equivalent-service quote or measured capacity behind it. |

Published compatibility: [ASP.NET Core .NET 10 Native AOT support](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/native-aot?view=aspnetcore-10.0) and [Native AOT limitations](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/), checked 2026-10-29. AOT produces native code while retaining runtime services such as garbage collection; it is no memory guarantee.

Publish/test the selected Windows artifact before migration, exercising controllers/views, cookie login, sessions, runtime plugins and PostgreSQL. Measure representative whole-stack memory, successful throughput/errors, p95 and readiness; agree budgets because none were supplied.

Deploy under a dedicated Windows service identity with protected configuration/secrets, writable data paths, resource bounds, shutdown/restart/logging and Caddy-to-loopback health routing. Keep PostgreSQL/data protection and versioned artifacts; rollback application versions only across compatible schema changes. AOT APIs/workers would be a separate redesign needing justification. Consider Arm only after the dependency inventory and target-artifact checks pass; build elsewhere, not on the deployment host.

## Case 06

A Rust rewrite is not justified, and a RAM-saving percentage cannot be calculated from these figures.

| Option | Evidence and suitability | Decision/cost |
| --- | --- | --- |
| Current Python | Supplied production trace: 85% request time waiting on a missing-index query. 180 MiB RSS excludes two workers and database. | Establish complete baseline. |
| Python with index | Supplied local query improvement; representative end-to-end improvement unknown. | First candidate; inspect query plan and index storage/write costs. |
| Rust service | Unrelated development service: 240 MiB whole-host accounting, differing load/data/windows; 8% timeouts versus Python's 0.2% under another load. Integration parity absent. | Cannot rank speed, memory or reliability fairly; rewrite effort/payback unknown. |

Neither subtracting these memory numbers nor comparing success-only p95 establishes an improvement. Timeouts must accompany latency; dropped work is not faster successful service. Do not turn the 85% wait attribution into a promised overall speedup.

Next propose one bounded before/after index check using the same production-profile Python stack, representative dataset/request mix, hardware, cache state, warm-up and window. Include all workers/database and other relevant services under the same memory accounting, CPU, successful rate, p95 and failures. Set acceptance budgets from requirements first. Inspect end-to-end results before deciding further work.

If a measured application CPU bottleneck remains, compare a focused native component against rewriting, counting integration, Rust dependencies, release build, OS/CPU support and maintenance skills. Require the same behavior cases before ranking it. Stage index rollout with a compatible removal plan and query monitoring; no rewrite or production change is authorized here. Cash savings remain unknown without a qualified plan reduction and migration estimate.

## Case 07

No defensible fastest stack or monetary saving can be selected from dependency names. The 2024 EUR 3 advertisement is historical, not a purchasable equivalent-service quote.

| Option | Conditional fit | Evidence/cost |
| --- | --- | --- |
| Keep/tune current | Inspect whether Next.js is static or server-rendered, actual Python workers, Redis roles and Docker topology. | Utilization, bills and bottlenecks unknown. |
| Focused replacement | Optimize a measured query, browser payload or costly worker before replacing everything. | Effort/savings unknown. |
| Illustrative native stack | Linux VM, Go service, HTML/htmx, local SQLite, Caddy; only if behavior, single-host writer load, recovery and team fit permit. | No compatibility qualification or numeric resource/cost prediction. |

Offline editing may require retaining a client app; multi-host writes or availability may require PostgreSQL; Redis may supply essential queue semantics. Native deployment must replace supervision, identities, limits, secrets, logging, isolation and rollback responsibilities.

Request the deployment map, critical workflows, existing utilization/bills, data/write profile, recovery/availability targets, team skills and migration budget. First check existing authorized capacity. Then define acceptance budgets and one representative bounded comparison; no speculative full-stack benchmark is warranted.

Shortlist hosting only afterward, refreshing official region/stock, CPU/OS, persistent disk, bandwidth, addresses, independent backup/recovery, tax, commitment/renewal and operational costs. Compare equivalent requirements in the same currency and period. Savings = current total minus qualified candidate total; payback needs one-time cost and positive net monthly savings. Both are currently unknown. Stage any future replacement behind behavior and recovery gates with data-aware rollback; a PostgreSQL path is only planned until exercised.
