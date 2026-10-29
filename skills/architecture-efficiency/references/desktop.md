# Desktop application efficiency

Use for installed applications running on an end user's device. Keep evidence labels, comparison structure and measurement accounting in [report.md](report.md), and shared language/database feasibility in [decision-guide.md](decision-guide.md). A local desktop application does not inherently need Docker, Caddy, HTTP services, PostgreSQL or a paid host.

## Primary objective and eligibility

Minimize application RAM while preserving required functionality, correctness, responsiveness, accessibility and platform support. Treat no embedded browser/WebView UI as a desktop requirement unless the user explicitly changes it. Electron, Tauri, CEF, Qt WebEngine and any toolkit embedding an equivalent browser/WebView for application UI are ineligible final targets. A Rust/Go core, a smaller installer, or use of the system WebView does not meet this requirement.

Distinguish OS-native controls, non-browser self-rendered toolkits and browser-rendered UI. Both of the first two can qualify; neither guarantees the smallest RAM footprint. Inspect dependencies and secondary settings/help/plugin screens for hidden embedded browsers. Opening a system browser for authentication or documentation is distinct from embedding one, but do not move the main desktop UI into a browser as a workaround. Browser-delivered server applications are outside this desktop restriction.

An existing WebView application remains a measured baseline and a possible interim migration step, not a compliant final design. If required behavior cannot currently be preserved without a WebView, state that the constraint is unresolved; do not silently relax it or invent a working replacement.

## Inspect the actual desktop workload

Identify supported OS versions/CPU architectures, UI toolkit, renderer/WebView, main and helper processes, plugins/native libraries, local data and any remote service. Record user workflows: launch to usable state, open a representative document, scroll/filter, edit, save, close/reopen and resume from sleep where relevant. Preserve keyboard/focus, accessibility, high DPI, multi-monitor behavior, file dialogs/associations, clipboard/drag-and-drop, notifications and media/device integration that the product uses.

Distinguish startup overhead, UI-thread blocking, rendering/layout work, data preparation, IO and background activity. A synchronous database query, excessive layout or unbounded collection may be the bottleneck regardless of implementation language. Compare focused fixes such as cancellation, bounded work, batching or virtualization before recommending a new toolkit. Background execution can improve responsiveness without reducing total CPU; measure both. Keep heavy work off the UI thread without allowing unsafe cross-thread UI access.

## Desktop measurements

Apply the shared contract on the same device, OS, power mode, display configuration, data and scripted or described interaction sequence. Where they affect the decision, record:

- Cold/warm launch to usable UI and document-ready time; distinguish first-run setup.
- Idle and active full-process-tree memory, including renderer, GPU and helper processes. Keep private/working-set/RSS accounting consistent; separate GPU memory. An Electron main-process or WebView JS-heap figure is not total application memory.
- Input-to-visible-response and frame-time/jank or UI stalls during representative actions, with failures and sample windows. HTTP p95 alone cannot establish a responsive desktop UI.
- Idle/background CPU and wakeups; energy or battery drain only under controlled comparable conditions, not inferred from lower RAM.
- Installed/download size, runtime/WebView/shared-library dependencies and update size separately. A smaller executable does not establish lower working memory.

Use only metrics needed to resolve the current question; do not add a benchmarking framework by default.

Always compare relevant idle and representative active/peak full-application RAM. Establish which memory budget or workload matters most rather than choosing only the metric favoring a candidate. Watch retained data after repeated open/edit/close cycles and distinguish bounded caches from growth/leaks. Check allocations, copied datasets, image decoding, hidden windows, retained event handlers and unnecessarily resident helpers. Use bounded caches, lazy loading, virtualization/paging and resource release where supported by evidence. Do not claim a win by shifting memory to excluded processes, swap or GPU, or by dropping required data/behavior. CPU and startup remain acceptance/tradeoff checks, not substitutes for RAM evidence.

## Candidate comparisons

These are role-specific candidates, not measured rankings. Official documentation checked 2026-10-29; verify the selected versions and target platforms.

| Current design or need | Candidate | Fit and costs to verify |
| --- | --- | --- |
| Electron or other browser-based desktop shell | Compare non-WebView UI candidates below; retain/tune the current app only as baseline or interim migration | [Electron](https://www.electronjs.org/docs/latest/tutorial/process-model) includes Chromium processes; [Tauri](https://v2.tauri.app/concept/architecture/) still uses WebViews. Neither meets the desktop UI requirement. Measure the existing full process tree before estimating the replacement benefit. |
| Cross-platform business desktop UI | C#/.NET with [Avalonia](https://docs.avaloniaui.net/docs/welcome) | Uses its own rendering engine rather than wrapping native controls. Check OS/CPU support, UI behavior, data binding, graphics and dependencies. A native executable or an AOT option does not remove all runtime services; validate the actual publishing model. |
| Existing C++ code or widget-heavy cross-platform desktop app | C++ [Qt Widgets](https://doc.qt.io/qt-6/qtwidgets-index.html), without Qt WebEngine | Evaluate UI/platform integration, library packaging, ABI, team skills and distribution terms. A Python Qt binding still includes its Python runtime; moving to C++ changes the ownership and maintenance costs. |
| Windows-only desktop with deep Windows integration | Retain suitable WPF/WinForms code or compare [WinUI 3](https://learn.microsoft.com/en-us/windows/apps/winui/winui3/) | Preserve Windows integration and accessibility. Do not choose a cross-platform framework without a cross-platform requirement. Check native/managed dependencies and target Windows versions; no automatic performance ranking follows from the toolkit. |
| Windows-only UI with strict RAM goals and suitable team skills | C/C++ [Win32 controls and APIs](https://learn.microsoft.com/en-us/windows/win32/learnwin32/learn-to-program-for-windows) | Compare measured RAM against higher-level non-WebView toolkits; count implementation, accessibility, text/input and maintenance effort. Platform APIs alone are not a resource benchmark. |
| Measured expensive feature in a non-WebView UI | A focused native worker/library with the eligible current UI retained | Count FFI/IPC, serialization, copies, cancellation and packaging. A faster worker may increase RAM; compare the complete application. Keeping a WebView UI does not satisfy the final desktop constraint. |

Rank eligible candidates by comparable RAM evidence for the actual workflows, not framework reputation or language. If evidence is missing, present non-WebView candidates conditionally and identify the smallest useful memory/parity check; do not publish a universal MB ranking. A managed runtime or self-rendered UI can still be eligible when it does not embed a browser and meets the memory objective.

## Local data and distribution

Prefer SQLite for suitable local structured data; assess save/autosave durability, corruption/crash recovery, migrations, user exports and multiple local app instances. Store writable data in the appropriate user/application data location, separate from installed binaries. A remote PostgreSQL backend is a separate architecture responsibility; it is not a reason to expose database credentials to desktop clients or share a SQLite file across machines. Keep any future engine path explicit and proportional to actual requirements.

Evaluate installers/portable packaging, signing or notarization where required, updates and rollback, permissions, plugin compatibility and OS/runtime support on the shipped artifact. An executable rollback may fail after a forward-only user-data migration; preserve recoverability and exports/backups. Inventory old WebView bridges when migrating their functionality into non-browser UI; do not carry them into the target unnoticed. Do not add an always-running local server or administrator service unless required behavior justifies it.

The comparison should explain user-device resource savings and maintenance/distribution costs. Local-only apps may have no hosting bill to reduce; monetized hardware or battery savings need their own evidence. For a hybrid client, additionally check authentication, offline drafts, retries/idempotency, sync conflicts and client/backend version skew, and apply [server.md](server.md) only to the remote components.
