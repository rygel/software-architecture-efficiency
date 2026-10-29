# Bounded architecture-efficiency evaluation

Method: independent, read-only application of skill version 1.3.0 to supplied Cases 01, 08, 09, 10 and 11. Read SKILL.md and references desktop.md, server.md, decision-guide.md, report.md and alternatives.md. No rubric, previous answers/outcomes, git diffs, external research, installs, workloads or production changes were used. No project implementation was inspected beyond these fixtures. This report contains recommendations, not completed migrations or verified artifacts.

Loaded SKILL.md SHA256: 29F3DEAA18008867128219FB391CFBC2EBC58DDAC459FCA8D9DD999405035F6B.

Evidence: all supplied numbers and traces are synthetic snapshot evidence, not real measurements reproduced here. “Supplied” below identifies that provenance; unmeasured benefits and costs remain unknown. Toolkit capability descriptions come from the loaded skill references, not newly verified current documentation.

## Case 01

**Track: server/browser-delivered application.** Caddy serves static assets; Node runs only during the build. There is no deployed Node service, database or API to remove. The desktop no-WebView restriction does not apply to this browser frontend.

| Choice | Fit, evidence and cost | Decision |
| --- | --- | --- |
| Retain React/Vite and Caddy | Meets the supplied search/filter workflow. Runtime utilization, browser memory, transfer size and responsiveness are unknown; existing shared hosting cost remains unchanged absent a capacity change. | Best-supported immediate choice; inspect the built asset output and profile the actual browser journey before optimization. |
| Tune the static frontend | Remove demonstrated unused assets/code or costly rendering. No savings have been measured. Low migration scope, but preserve keyboard/accessibility and search behavior. | Make only changes justified by the baseline. |
| Generate static HTML with small browser JavaScript for filtering | Plausible for 40 descriptions; can retain Caddy and avoid an application server. Lower browser costs are conditional; maintenance and migration costs are unknown. | Compare only if frontend costs justify replacement; dynamic server rendering adds no demonstrated value here. |

Smallest next check: on the same device/browser and content, compare initial transfer, startup, total browser/process memory and representative search/filter responsiveness using production assets, recording cache state and failures. Set acceptance limits before a trial. Preserve URLs and equivalent content/interaction; rollback can restore the previous static asset release. No hosting savings or numeric efficiency improvement can currently be claimed. Revisit hosting only if whole-VM evidence permits an actual plan reduction.

## Case 08

**Track: desktop, local-only.** Retain local SQLite and offline operation. Under the skill's desktop requirement, a final target must contain no embedded browser/WebView across all screens.

The supplied 620 MiB is Electron's full process tree over a ten-minute idle window; 45 MiB covers only Tauri's Rust process. These boundaries differ, so no RAM improvement or percentage saving is established. Tauri uses a WebView; a Rust core does not make its UI native widgets. The 120 MB versus 8 MB installer downloads establish only a supplied download-size difference, not working-memory, readiness or parity results.

| Choice | Eligibility and tradeoffs | Decision |
| --- | --- | --- |
| Retain/tune Electron | Baseline or interim only under the no-WebView requirement. Existing cross-platform workflows need preserving; savings unknown. | Use as the behavior and resource baseline. |
| Tauri experiment | WebView makes it ineligible as the final target. Omitted processes and missing parity prevent an efficiency verdict. | Do not approve the proposed claim or final design. |
| Avalonia/.NET | Non-browser rendering can qualify; Windows/macOS/Linux support, SQLite driver, accessibility, printing, dialogs and signed updates need artifact-level verification. Actual RAM and migration cost unknown. | Conditional candidate. |
| C++ Qt Widgets without Qt WebEngine | Non-browser widgets can qualify; verify the same integrations, packaging, licensing and team capability. Actual RAM and migration cost unknown. | Conditional candidate; no proven ranking against Avalonia. |

Before selecting, inventory bridges, dependencies and secondary screens. A bounded production-profile comparison should include cold/warm readiness, full-tree idle and representative active peak private working set, separate GPU accounting, stalls and repeated open/edit/print/close cycles on supported platforms. Reuse keyboard, screen-reader, file-dialog, printing, offline and recovery workflows; exercise signing/update rollback. Preserve writable user data outside installation files and retain recoverable SQLite backups before schema changes. Stage one workflow before full migration; stop if required behavior fails or the agreed RAM benefit is absent. No server or hosting purchase is warranted. Support/distribution effort and payback are unknown.

## Case 09

**Track: desktop, Windows-only.** Retain WPF and local SQLite as the best-supported direction. The supplied trace identifies a six-second synchronous UI-thread database wait on every search change and an unindexed scan. It does not establish that WPF or managed memory is the efficiency problem.

| Choice | Requirement fit and evidence | Decision |
| --- | --- | --- |
| Tune WPF/SQLite search | Preserves team expertise, Windows association/accessibility and installer/data format. Index/query improvements address the demonstrated scan. Background work and cancellation can address stalls; RAM effects unknown. | First priority. |
| Migrate to Qt Widgets/C++ | Could meet desktop eligibility, but no comparative memory result exists. Requires new expertise and Windows integration/accessibility verification; migration cost unknown. | Unjustified by current evidence. |
| Embedded web UI or moving the main UI to a browser | Fails this skill's desktop target constraint. No remote requirement justifies introducing a service. | Reject as the proposed efficiency fix. |

Propose an appropriate index after checking query semantics and write/storage cost; debounce only as appropriate for expected behavior, cancel obsolete searches, bound results and keep database work off the UI thread with safe UI updates. These are proposed fixes, not performed work. Background execution alone need not reduce CPU or RAM.

Verify the representative query plan and search correctness, then input-to-visible response, cancellation and stale-result behavior with the existing accessibility/association cases. Capture full-process idle and active RAM before claiming memory savings; agree responsiveness and memory budgets. Preserve the existing installer and data location. Keep changes independently reversible, with a consistent database backup for any migration. Reconsider another toolkit only if measured residual costs justify its total migration/support burden.

## Case 10

**Track: hybrid.** Device and service responsibilities must stay explicit: the desktop owns six-hour offline drafts, local interaction and queued synchronization; the remote service owns authenticated shared account transactions, conflict resolution and availability. Client/backend compatibility must cover the previous client release for one month.

| Choice | Compatibility, evidence and tradeoffs | Decision |
| --- | --- | --- |
| Shared SQLite file across machines, remove servers | Cannot replace the supplied remote transaction/failover and offline sync contract. SQLite WAL does not provide distributed writers or automatic failover; disconnected devices cannot depend on a shared file. | Reject the complete proposal. |
| Retain topology and optimize a measured component | Retains appropriate local SQLite and remote Go/Caddy/PostgreSQL responsibilities. Resource baselines and bills are unknown. Tauri remains an existing WebView baseline/interim, not an eligible final desktop target. | Best near-term investigation; profiling precedes a specific optimization. |
| Retain service topology, replace only client UI with non-WebView toolkit | Avalonia or Qt Widgets are conditional candidates; local database and sync behavior can remain. Verify Rust integration, packaging, all screens, identity flow and platform requirements. | Appropriate final-target direction only after compatibility/parity and full-client RAM evidence. |

Do not expose database credentials or substitute file sharing for the API. Retain existing service supervision, identities, limits, secrets, TLS, health checks, shutdown/restart and updates; verify their current owners before simplifying deployment. PostgreSQL's actual failover/recovery configuration remains unverified here.

Measure client full-tree idle/active memory separately from API hosts, database and proxy; include representative offline/reconnect/conflict workloads and server successful throughput/errors. No combined RAM reduction, lower bill or payback is established. Stage changes behind backward-compatible API/data contracts, reuse auth/conflict/retry/idempotency and recovery cases, and test old/new clients together. Retain export/backup recovery and a data-aware rollback boundary; a previous executable alone cannot undo incompatible schema or synchronized writes. Revisit service capacity only on observed load/reliability evidence.

## Case 11

**Track: desktop, Windows-only.** Both supplied production-profile candidates pass the same workflow/accessibility/recovery suite and browser-free inventory. Their comparable sampling makes Qt Widgets C++ the **best-supported provisional choice for the stated primary metric**.

| Candidate | Supplied idle | Supplied observed active peak | Interpretation |
| --- | --- | --- | --- |
| Qt Widgets C++ | 160 MiB | 280 MiB | Lowest representative active peak in this trial. |
| Avalonia .NET | 110 MiB | 420 MiB | Lowest idle footprint; higher primary-metric peak. |

Qt's observed peak is 140 MiB lower, an estimated arithmetic reduction of 33.3% relative to Avalonia's 420 MiB. Avalonia's lower idle value does not establish it as the best choice when active peak is primary. This compares these implementations and workload, not C++ and .NET universally.

Confidence remains preliminary: one run each, fifteen-minute windows and one-second sampling establish observed peaks, not lifetime or subsecond maxima. Variability, retained-memory growth, exact versions and detailed cache/power/build settings remain unknown. Reuse the same bounded sequence for enough repetitions to assess whether the difference persists; inspect retained memory after repeated cycles. Preserve the existing parity suite and check responsiveness/startup as acceptance tradeoffs. No installation or benchmark is executed here. Keep migration conditional on stable RAM evidence and a concrete migration/support estimate; no total-cost winner, bill saving or payback can be claimed.
