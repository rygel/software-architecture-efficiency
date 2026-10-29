All numbers below come from the **synthetic supplied snapshots**. They are not live observations, reproduced benchmarks or current hosting quotes. “Measured” identifies the supplied measurement’s provenance; “estimated” identifies calculations or projections. No workloads, installations or production changes were performed.

**Case 01 — Server track**

Retain static deployment through Caddy. Node is build tooling here; there is no deployed Node process to remove. The current server architecture already consists of serving files.

Compare retaining React, optimizing its delivered assets, and replacing the small catalog with static HTML plus modest browser JavaScript for search/filtering. The last option appears behaviorally plausible, but its memory, transfer and responsiveness benefits are **unknown**. Adding an application server or database would introduce responsibilities absent from this product.

The smallest useful verification is one representative catalog/search journey: transferred bytes, JavaScript execution, full browser memory and input responsiveness on the same device. Preserve accessible descriptions, keyboard operation and filtering behavior. Agree budgets before ranking candidates.

Hosting savings are **unknown**: reducing this site’s footprint does not reduce a shared VM bill unless the allocation or billed usage changes. Stage any frontend change as a replaceable static release, with the prior asset bundle available for rollback.

**Case 02 — Server track**

The simplified trial is the stronger candidate, but recovery remains a release gate.

| Dimension | Current stack | Trial stack |
| --- | --- | --- |
| Behavior | Supplied shared suite passed | Same suite passed, including permissions and job retry/idempotency |
| Sampled whole-host maximum memory | 1,400 MiB; exceeds 700 MiB budget | 310 MiB; provisionally meets budget |
| Successful rate/errors | 40/sec; 0% | 40/sec; 0% |
| p95 latency | 210 ms; exceeds 200 ms budget | 120 ms; provisionally meets budget |
| Recovery | Unverified against requirement | Restore, shutdown and restart unverified |
| Scenario monthly infrastructure | USD 24 compute + 4 backups = **28** | USD 6 compute + 2 backups = **8** |

These are preliminary **supplied measured** results: identical stated VM class, dataset, durability and load; five-minute warm-up, fifteen-minute sample and one-second sampling. One run each and missing toolchain versions limit confidence. “Maximum” means maximum sampled during that window.

**Estimated arithmetic:** memory reduction is 1,090 MiB, **77.9%**; p95 reduction is 90 ms, **42.9%**. Neither establishes the same improvement under other workloads or a smaller production VM.

Scenario infrastructure savings are **USD 20/month**, **71.4%**, or **USD 240/year**, before taxes. With unchanged operator hours and the supplied USD 120 migration estimate, payback is **six months**; first-year savings after migration are **USD 120**. Tax-inclusive totals are unknown.

Retain Caddy and the trial’s simple service structure conditionally. Before cutover, verify the actual Linux x64 artifact, SQLite driver/linking, contention under representative bursts, consistent independent backups and restoration within **30 minutes**, with at most **24 hours** of lost data. Exercise graceful shutdown, restart and job recovery.

Rehearse PostgreSQL-to-SQLite conversion with data/constraint checks. A future SQLite-to-PostgreSQL path remains **planned**, not demonstrated. Revisit SQLite if sustained writer contention or multi-host requirements emerge. Define a write freeze and reconciliation strategy at cutover; retaining the old PostgreSQL database alone cannot recover subsequent SQLite writes losslessly.

**Case 03 — Server track, including its browser client**

Retain the React editor and Go/PostgreSQL backend. There is no installed desktop client in the snapshot.

The supplied trace points to repeated processing of the 40,000-object scene, while the affected workflow has no network dependency. Moving interaction to server-rendered requests would not preserve airplane-mode editing, immediate dragging, undo/redo and local drafts without retaining substantial client behavior.

Prioritize scene-processing changes: avoid recomputing unchanged objects, bound work to the affected region, and examine scheduling or worker execution where appropriate. These are candidates to profile, not proven fixes. A focused compute replacement is worth considering only if optimization leaves a measured bottleneck and transfer/serialization costs are included.

Reuse the offline editing/synchronization behavior as the acceptance contract. Measure drag input-to-visible response, frame stalls and both total browser memory and JavaScript heap on the same scene/device. Check draft recovery and later synchronization. No numeric speedup, memory saving or hosting saving is supported.

Stage algorithm changes independently and retain the previous implementation until output and editing behavior match.

**Case 04 — Server track**

Reject the shared-network-file SQLite proposal. It does not satisfy the stated architecture requirements.

**Published capability:** SQLite WAL requires participating processes on the same host and permits only one writer at a time. WAL is not distributed replication or automatic failover. [SQLite WAL documentation](https://www.sqlite.org/wal.html).

Retain PostgreSQL for multi-host access and account transactions. Verify that its replication, acknowledgement and failover configuration actually delivers recovery within **60 seconds** without acknowledged transaction loss; the snapshot establishes requirements, not successful qualification.

Treat Docker removal as a separate comparison. Linux containers share the host kernel; removing them does not remove a separate OS per container. Native PostgreSQL/application services may fit the team, but must preserve per-service identities, resource limits, isolation, secrets, persistence, readiness, logging, restart and rollback.

Measure existing container/service overhead before predicting RAM benefits. Qualify a staged native-service transition against transaction integrity and failover requirements, with data-compatible rollback. No engine replacement or savings percentage is justified.

**Case 05 — Server track**

Reject `PublishAot=true` as an unchanged-feature deployment plan.

**Published compatibility:** the .NET 10 ASP.NET Core table lists MVC, sessions and authentication other than JWT as unsupported for Native AOT. Runtime-loaded reflection plugins add another incompatibility: Native AOT does not support dynamic assembly loading. [ASP.NET Core .NET 10 compatibility](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/native-aot?view=aspnetcore-10.0), [Native AOT limitations](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/).

Recommend evaluating a conventional .NET 10 deployment on the current Windows x64 host, supervised as a Windows service, with Caddy if appropriate. Framework-dependent and self-contained publishing are alternatives to compare; neither implies Native AOT. Preserve MVC/views, cookies, sessions, plugins and PostgreSQL.

A Linux Arm move is **conditional** on OS-specific integrations, plugin binaries, PostgreSQL driver, native libraries and target architecture. A development build proves none of that. Check the published artifact on its intended OS/CPU, including login, session persistence, plugin loading, database operations and restart behavior.

Keep performance and cost improvements **unknown** until comparable measurements and a complete hosting quote exist. Stage host migration separately from publishing changes. Native AOT is a possible future option for a separately justified compatible API/worker, not a requirement for service deployment.

**Case 06 — Server track**

Do not recommend a Rust rewrite or report a percentage RAM saving from this benchmark.

The memory scopes are incompatible: one Python process excludes two workers and the database; the Rust number covers a different whole host. Dataset, load, observation period and build conditions also differ. **RAM saving: unknown and not calculable from these values.**

The lower Rust p95 among successes is not a valid win with **8% timeouts**, against Python’s **0.2% under a different load**. Without matched traffic and timeout accounting, neither speed nor reliability can be ranked.

The supplied trace identifies database waiting as the dominant elapsed-time component. Prioritize the missing index and check its write/storage tradeoffs, then obtain one representative end-to-end run. An 85% waiting share is not an 85% promised improvement.

Use matching whole-stack memory scope, hardware, optimized builds, data, durability, offered/successful rates, latency and failures. Reuse API behavior cases. Consider Rust only if the tuned system still misses agreed budgets and profiling identifies a runtime or compute cost worth replacing. Roll back the focused index/query change if its measured costs outweigh benefits.

**Case 07 — Server track for an illustrative scenario; actual deployment boundary unconfirmed**

Dependencies alone do not establish deployed processes or even prove the application track. The following is conditional server analysis, not a definitive classification.

| Option | When suitable | Evidence and costs |
| --- | --- | --- |
| Tune the existing stack | Existing behavior and team fit remain suitable | First identify actual runtime processes, hot paths and utilization; savings unknown |
| Static output through an existing file server | Product consists of prebuilt content/client assets | Next.js build dependencies alone do not establish a running Node server; suitability unknown |
| Compiled service, server-rendered HTML, local SQLite and Caddy | Suitable single-host CRUD without incompatible concurrency/recovery requirements | Candidate only; driver, behavior, recovery, skills and migration cost unverified |
| Retain PostgreSQL/Redis or other dedicated services | Their shared-data, queue, availability or scale responsibilities require them | Removing them requires evidence that their semantics remain satisfied |

Request or inspect the deployment map, essential workflows, offline needs, workload/data size, bills, recovery requirements and team constraints. Those facts change the decision.

The pasted 2024 EUR 3 plan is historical advertising, not a current purchasable offer. Check existing capacity first; obtain current official market-specific pricing, stock, tax, commitments, renewal, traffic, storage and backup terms only once requirements establish a shortlist.

There is no defensible fastest stack, numeric savings or payback yet. Payback requires a positive monthly net saving and a migration estimate. Do not provision anything to resolve this comparison.

**Case 08 — Desktop track**

The proposed migration does **not** prove a RAM improvement.

The **supplied measured** Electron baseline includes its full process tree: 620 MiB private working set over ten minutes idle. Tauri’s 45 MiB counts only its Rust main process. No valid percentage reduction follows.

The installer figures show a separate download-size difference: **estimated 112 MB, or 93.3%, smaller** for the experiment. They do not establish installed footprint, runtime memory, feature parity or readiness. Tauri uses a Rust core plus platform WebViews, not an entirely native-widget UI. [Tauri process model](https://v2.tauri.app/concept/process-model/).

| Candidate | Fit and tradeoff | Decision |
| --- | --- | --- |
| Tune Electron | Preserves the established UI/integrations; investigate renderer/helper work and unnecessary background activity | Lowest disruption; measure first |
| Tauri | Can reuse web UI; platform WebViews and bridges need validation | Promising packaging candidate; RAM/parity unproven |
| Avalonia or Qt Widgets | Alternative cross-platform desktop approaches; require substantial UI, integration and distribution work | Consider only with a justified maintenance and migration budget |

Compare complete process trees on each supported OS using consistent accounting, idle and active workflows. Include cold/warm readiness, invoice editing, keyboard/focus, screen-reader operation, file dialogs, printing, signing and updates. Check WebView/runtime distribution and offline installation requirements.

Retain local SQLite and user-data separation. Stage an isolated migration with recoverable data conversion, signed distribution and an installer/update rollback boundary. Hosting is irrelevant; migration and support costs are unknown.

**Case 09 — Desktop track**

Retain WPF and local SQLite; fix the demonstrated query/UI-thread problem first.

The supplied trace identifies a six-second synchronous search wait and an unindexed scan. Changing toolkit would not inherently fix that query. Low idle CPU also provides no case for an idle-resource rewrite.

Prioritize an appropriate index/query plan, bounded or debounced search, cancellation of obsolete requests and asynchronous database work without unsafe UI-thread access. Background execution can improve responsiveness without reducing total computation; measure both effects.

Compare the tuned WPF application before funding Qt or a web-based rewrite. Qt adds unfamiliar C++/toolkit maintenance; a web architecture introduces new rendering or service responsibilities without a cross-platform requirement. Neither has measured superiority here.

Verify search correctness and stale-result handling, input responsiveness, file associations and accessibility on representative data. Establish full-process memory only if RAM remains a decision criterion. Agree performance budgets rather than inventing them.

Preserve the existing installer and user-data location. Keep any index/schema change recoverable and separate it from distribution changes. No data-format migration, hosting addition or RAM-saving claim is warranted.

**Case 10 — Hybrid track**

Retain the topology and optimize a measured component after profiling. Reject the shared SQLite-file proposal.

The client owns Rust/native integration, WebView interaction, local SQLite drafts and six-hour offline operation. The backend owns shared account transactions, authorization enforcement, synchronization and conflicts; PostgreSQL owns remote shared persistence and failover. Caddy owns proxy/TLS responsibilities. A shared file cannot remove these service responsibilities or supply offline synchronization.

Keep OAuth identity, authenticated sync, retries/idempotency, conflict handling and local draft recovery explicit. Preserve compatibility with previous-release clients for the required month. Use additive API/schema evolution and test the current and previous client against the changed backend.

Account separately for client core, WebView/GPU/helpers and device interactions; backend hosts, Go processes, Caddy and PostgreSQL; and deployment/backup bills. Comparable baselines and savings remain **unknown**. Do not combine main-process memory with backend totals.

Profile one representative offline-edit-and-resync journey plus its service transactions, then select the component that misses an agreed budget. Stage additive backend changes before dependent clients, exercise offline conflicts and database failover, and retain backward compatibility during rollback. Local and server data migrations both need recovery boundaries; older binaries may not understand newly written formats.

**Loaded skill**

Version: `1.2.0`
SHA256: `BF03F2A4EFD49C766D70AB80EAC87E1500A5CE79D2D771E8C5B26666629A9120`

No material conflict in the skill instructions was encountered. Material case ambiguities are Case 07’s unestablished deployment boundary and Case 05’s use of “native deployment,” which can mean OS-supervised deployment or Native AOT compilation; these require different compatibility decisions.
