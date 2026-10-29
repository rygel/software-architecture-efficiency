# Desktop application efficiency

Use for installed applications running on an end user's device. Keep evidence labels, comparison structure and measurement accounting in [report.md](report.md), and shared language/database feasibility in [decision-guide.md](decision-guide.md). A local desktop application does not inherently need Docker, Caddy, HTTP services, PostgreSQL or a paid host.

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

## Candidate comparisons

These are role-specific candidates, not measured rankings. Official documentation checked 2026-10-29; verify the selected versions and target platforms.

| Current design or need | Candidate | Fit and costs to verify |
| --- | --- | --- |
| Electron desktop shell | Tune the existing app; compare [Tauri](https://v2.tauri.app/concept/architecture/) when reusing web UI is useful | Tauri combines a Rust core with platform WebViews; it does not turn its HTML/JS UI into native widgets or eliminate web runtime costs. Check bridges, plugins, WebView distribution/version differences, accessibility and integration parity. Savings remain unmeasured until tested. |
| Cross-platform business desktop UI | C#/.NET with [Avalonia](https://docs.avaloniaui.net/docs/welcome) | Uses its own rendering engine rather than wrapping native controls. Check OS/CPU support, UI behavior, data binding, graphics and dependencies. A native executable or an AOT option does not remove all runtime services; validate the actual publishing model. |
| Existing C++ code or widget-heavy cross-platform desktop app | C++ [Qt Widgets](https://doc.qt.io/qt-6/qtwidgets-index.html) | Evaluate UI/platform integration, library packaging, ABI, team skills and distribution terms. A Python Qt binding still includes its Python runtime; moving to C++ changes the ownership and maintenance costs. |
| Windows-only desktop with deep Windows integration | Retain suitable WPF/WinForms code or compare [WinUI 3](https://learn.microsoft.com/en-us/windows/apps/winui/winui3/) | Preserve Windows integration and accessibility. Do not choose a cross-platform framework without a cross-platform requirement. Check native/managed dependencies and target Windows versions; no automatic performance ranking follows from the toolkit. |
| Measured compute-heavy desktop feature | A focused native worker/library with the current UI retained | Count FFI/IPC, serialization, copies, cancellation and packaging. Separate UI stalls from computation and verify output parity before a full rewrite. |

[Electron's process model](https://www.electronjs.org/docs/latest/tutorial/process-model) explains why measuring only one process misses costs. Optimizing or retaining Electron is a valid option when required browser/plugin capabilities and migration cost justify it.

## Local data and distribution

Prefer SQLite for suitable local structured data; assess save/autosave durability, corruption/crash recovery, migrations, user exports and multiple local app instances. Store writable data in the appropriate user/application data location, separate from installed binaries. A remote PostgreSQL backend is a separate architecture responsibility; it is not a reason to expose database credentials to desktop clients or share a SQLite file across machines. Keep any future engine path explicit and proportional to actual requirements.

Evaluate installers/portable packaging, signing or notarization where required, updates and rollback, permissions, plugin compatibility and OS/runtime support on the shipped artifact. An executable rollback may fail after a forward-only user-data migration; preserve recoverability and exports/backups. If using a WebView bridge, constrain exposed native capabilities and remote content appropriately. Do not add an always-running local server or administrator service unless required behavior justifies it.

The comparison should explain user-device resource savings and maintenance/distribution costs. Local-only apps may have no hosting bill to reduce; monetized hardware or battery savings need their own evidence. For a hybrid client, additionally check authentication, offline drafts, retries/idempotency, sync conflicts and client/backend version skew, and apply [server.md](server.md) only to the remote components.
