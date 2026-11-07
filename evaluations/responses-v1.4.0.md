# Version 1.4.0 retained responses

Claude Code answers (Claude Opus 5.5, see evaluations/v1.4.0.md) to Cases 08 and 10, two runs each, unedited apart from this heading structure. The OpenCode case runs are not retained because the prompt reached the model truncated (see v1.4.0.md).


## Case 08, run 1

# Invoice app: desktop alternatives and the Tauri RAM claim

**Short answer: no, the proposal does not prove a RAM improvement.** The two figures measure different things. 620 MiB is Electron's whole process tree. 45 MiB is only the Tauri app's Rust main process. Tauri on Windows draws its UI in WebView2, which runs its own browser, renderer and GPU processes. Those were left out, so no saving can be worked out yet.

## Scope and evidence

- **Type of app:** desktop. It's installed, works fully offline with local SQLite, and has no server or remote API. No server or hosting costs belong in this comparison.
- **Evidence:** only the numbers you supplied. The working directory holds no app source, build or logs, and I took no measurements myself.
- **Behavior that must keep working:** Windows, macOS and Linux; offline SQLite; keyboard navigation; the screen-reader workflow; file dialogs; printing; signed updates.

## What's wrong with the proposal's evidence

| Claim | Problem |
| --- | --- |
| 620 MiB vs 45 MiB | The scopes differ. Electron: full tree including GPU, renderer and helpers, private working set, 10-minute idle. Tauri: main process only, with WebView and GPU processes left out. The idle window and sampling for the Tauri figure aren't stated. Memory has been moved into uncounted processes, not removed. |
| 8 MB vs 120 MB installer | This is download size, not working memory. Part of the gap is that Tauri relies on the system's WebView instead of shipping Chromium. |
| No usable-state, interaction or feature-parity tests | There's no active or peak RAM figure for either app, and no proof the experiment keeps the required behavior working. |
| "Fully native-widget UI because its core uses Rust" | Incorrect. Tauri's docs ([architecture](https://v2.tauri.app/concept/architecture/)) say the UI renders in a WebView: WebView2 on Windows, WKWebView on macOS, WebKitGTK on Linux. A Rust core does not make the UI native widgets. |

Linux using WebKitGTK and macOS using WKWebView also means the Windows result wouldn't carry over to the other platforms, even once it's done properly.

## Comparison

| Option | Fits requirements? | RAM evidence | Migration cost and risks | Decision and next check |
| --- | --- | --- | --- | --- |
| **Current Electron** | Meets them (it's the working product) | **Measured (supplied):** 620 MiB idle, full tree, private working set, 10 min, same Windows device. Active or peak: unknown. | None | Keep as the baseline. Measure active and peak RAM over a scripted invoice workflow. |
| **Tuned Electron** | Meets them if the tuning keeps behavior intact | Unknown until the baseline is profiled (hidden windows, retained data or caches, resident helpers, unbounded lists) | Low to moderate | Cheap first step. Re-measure the full tree with the same method. |
| **Tauri (the proposal)** | Not acceptable as the end state: it still renders the UI in a WebView. Parity is unverified for the screen reader, printing, dialogs and updates on 3 browser engines. | Full-app RAM: **unknown** until WebView2, renderer and GPU processes are measured with the same accounting. The 45 MiB covers only part of the app. | UI port, Rust backend, testing on 3 different WebView engines | Don't approve on current evidence. It could only be a stopgap, and only if a correct measurement shows a gain. |
| **C#/.NET + [Avalonia](https://docs.avaloniaui.net/docs/welcome)** | Possible (it draws its own UI, no browser). Not yet verified: screen-reader support on all 3 OSes, printing, file dialogs, the SQLite driver, and the signed-update path for the chosen publishing model. | Unknown: no prototype has been measured | Full UI rewrite into C#, new packaging and signing per OS | Candidate long-term target. Needs a small prototype checked for RAM and parity. |
| **C++ [Qt Widgets](https://doc.qt.io/qt-6/qtwidgets-index.html), without Qt WebEngine** | Possible (native widgets). Not yet verified: accessibility on each OS, printing (Qt has a print module), licensing terms, packaging. | Unknown: no prototype has been measured | Full rewrite in C++, which brings memory-safety risk, team-skill and maintenance costs | Candidate target if the team has C++ or Qt skills. Same prototype check. |
| Windows-only toolkits (WinUI, WPF, Win32) | Ruled out: macOS and Linux are required | — | — | Not considered |

I can't give any RAM ranking between Avalonia and Qt without measuring both. Neither one is guaranteed to use less memory than a tuned Electron app.

## Recommendation

1. **Reject the RAM claim as stated.** Ask for the Tauri figure to be redone the same way as Electron's:
   - every process in the app's tree, including the `msedgewebview2.exe` processes that belong to it;
   - private working set, same device, same data, same 10-minute idle window and sampling interval;
   - GPU memory reported separately.
2. **Measure active RAM for both apps:** run one scripted workflow (open the invoice list, filter and scroll, edit, save, print preview, close and reopen) and record the peak.
3. **Run feature-parity checks before comparing efficiency.** On all 3 OSes: screen-reader workflow, keyboard focus, file dialogs, printing, signed-update install and rollback, and SQLite durability.
4. **Profile and tune the Electron baseline.** It's the lowest-cost path to lower RAM and needs no migration.
5. **If the long-term aim is minimal RAM with no embedded browser,** build one small vertical slice in Avalonia or Qt Widgets: invoice list, edit form, save to the existing SQLite schema, print. Run it through the same full-tree RAM and parity checks.

   Proposed acceptance rule (for you to agree, not a decision yet): the replacement must use less full-tree idle and active RAM than the tuned Electron app, keep every required behavior, and stay within the update and signing process.

   Make sure user data stays recoverable across the switch. Back up the SQLite file and avoid forward-only schema changes until the rollback point is past.

Until step 1 is done, the honest answer is that **the RAM difference between Electron and Tauri is unknown**, and Tauri wouldn't meet the no-embedded-browser target either way.


## Case 08, run 2

## Verdict: the proposal doesn't prove a RAM improvement

The two numbers measure different things, so the comparison doesn't hold.

| | Electron baseline | Tauri experiment |
|---|---|---|
| What was counted | Full process tree: main, renderer, GPU and helpers | **Rust main process only** |
| Metric and window | Private working set, 10-minute idle, same Windows device | 45 MiB. The metric and window aren't stated in the snapshot. |
| Left out | Nothing stated | **The WebView and GPU processes**, which do the actual UI rendering |
| Evidence label | Measured (supplied; I haven't re-run it) | Measured, but only part of the app is in scope |

- **620 MiB vs 45 MiB is not a saving.** On Windows, Tauri draws its UI with WebView2, which runs its own browser, renderer, GPU and utility processes. Those processes hold most of the memory a UI uses, and they were left out. The Tauri app's full-tree RAM is unknown until it's measured the same way as Electron: the whole process tree including the WebView2 children, private working set, 10-minute idle on the same device. Until then, no reduction in MiB or percent can be claimed.
- **8 MB vs 120 MB is a download-size result, not a RAM result.** Most of the gap is because Tauri uses the WebView already installed on the system instead of shipping Chromium. A smaller installer doesn't mean lower memory while running. The WebView runtime dependency needs to be counted separately.
- **Nothing else was tested.** There were no readiness, interaction, active or peak RAM, or feature-parity tests. Idle is only one of the memory budgets that matter, and no candidate has an active or peak figure yet.
- **"Fully native-widget UI" is wrong.** Tauri's core is Rust, but its interface is HTML/CSS/JS drawn by a system WebView: WebView2 on Windows, WKWebView on macOS and WebKitGTK on Linux ([Tauri architecture docs](https://v2.tauri.app/concept/architecture/), per the review skill's reference, checked 2026-10-29). The core's language says nothing about what draws the UI. That also means three different rendering engines, which adds parity risk for screen readers, keyboard focus and printing on each OS.

Under the desktop criteria used for this review, Tauri, like Electron, embeds a browser engine for its UI. So it's at most a possible stepping stone, not the final target.

## Alternatives compared

No source code was supplied (the project folder only holds the review skill). Everything below is based on your snapshot, and every RAM figure that hasn't been measured is marked unknown.

| Option | Fit with requirements | RAM evidence | Migration cost and risk | Decision and next check |
|---|---|---|---|---|
| **Current Electron** | Meets everything: offline SQLite, keyboard, screen reader, dialogs, printing, signed updates | 620 MiB idle full tree (measured, supplied). Active and peak: unknown | None. This is the reference for comparing everything else. | Keep as the baseline and as the fallback if a migration is rolled back |
| **Tuned Electron** | Keeps full parity | Savings: unknown until the 620 MiB is broken down by process (renderer count, hidden windows, GPU, preloaded data) | Low. No rewrite. | Do this first: if one process dominates, a targeted fix may be enough |
| **Tauri (the proposal)** | Not eligible as the final target because it embeds a WebView. Parity across three engines is unverified. Signed updates and printing need checking on each OS. | Full-tree RAM: unknown. The 45 MiB covers only part of the app. | Rewriting the backend in Rust; the UI is reused, but the Node-to-Rust bridge has to be moved over | Don't approve on the current evidence. Re-measure the full tree if it's kept as a stepping stone. |
| **C#/.NET + Avalonia** (draws its own UI, no browser) | Eligible. Conditional on screen-reader support on all three OSes (especially Linux), **printing** (verify it exists before committing), and a signed-update mechanism | Full-tree idle and active RAM: unknown. The .NET runtime and its garbage collector still use memory. | UI rewrite; SQLite through Microsoft.Data.Sqlite; new packaging and update path | Make a small prototype (a list view, the invoice editor and printing) and measure the full tree |
| **C++ Qt Widgets** (no Qt WebEngine) | Eligible. Qt has built-in print and file-dialog support and an accessibility layer on each OS. Updates, signing and the licence terms still need checking. | Full-tree idle and active RAM: unknown | Highest rewrite cost if the team doesn't already know C++. Packaging and ABI work. | Same prototype and measurement as Avalonia, if the team's skills fit |
| Windows-only toolkits (WinUI/WPF/Win32) | Not suitable: the app has to run on macOS and Linux | n/a | n/a | Excluded |

No server, hosting or PostgreSQL work is involved. The app is local-only and should stay that way. Moving the UI into a browser served from a local server would be the same problem in a different form. The benefit is on users' devices only; there's no hosting bill to cut.

## Recommendation and smallest useful check

1. **Re-measure Tauri fairly.** Use the same device, the same private-working-set metric and the same 10-minute idle window, summed over the full process tree including every WebView2 child process. This one step answers the RAM question.
2. **Add one active scenario for every candidate.** Launch until usable, open the invoice list, search, create, edit and save an invoice, open a file dialog, print preview, and repeat the open/edit/close cycle 5 times. Record peak and post-cycle memory to see whether usage grows, and run it 3 times. Include a short parity check alongside it: keyboard-only completion, screen-reader announcements (Narrator/NVDA, VoiceOver, Orca), printing, and a signed update plus rollback.
3. **Profile Electron's 620 MiB by process** before paying for a rewrite.
4. **Only then choose** between tuned Electron and an Avalonia or Qt prototype, picking whichever shows the lowest measured full-tree idle and active RAM *while passing the parity checks*. For any migration, keep SQLite migrations backward-recoverable, or back up user data before the first launch on the new version, so the old Electron build can still be rolled back to.

If you'd like this as a shareable page for the team, I can put it together.


## Case 10, run 1

# Architecture review: Tauri client, Go sync API and PostgreSQL

**Scope and evidence.** I based this on the snapshot in your request. The working directory holds only the skill files: no source code, deployment config, profiles, memory figures or bills. So every judgment below is a conclusion from the stated requirements. None of it was measured, and every resource or cost figure is **unknown**.

**Track:** hybrid. The installed client is judged against the desktop rules: lowest RAM, and no WebView in the final design. The Go API, Caddy and PostgreSQL are judged against the server rules.

## Hard requirements every option must keep

1. Six hours of offline use, with drafts stored locally.
2. Sync later, with conflict resolution as part of the sync contract.
3. OAuth user identity.
4. Shared account transactions with ACID guarantees, and automatic database failover.
5. The backend must keep working for clients on the previous release for one month.

## What runs on the device and what runs on the servers

| Responsibility | Where it belongs | Why |
| --- | --- | --- |
| Draft storage (SQLite), offline editing, sync queue, retries with idempotency keys, local schema migrations | Device (Rust core) | Offline use needs local writes. SQLite fits one user's data on one machine. |
| UI rendering | Device | Today this is a WebView. That works as a baseline but is **not an acceptable final design** under the desktop rules. |
| OAuth sign-in and token refresh | Device, using the system browser to sign in | Opening a system browser is allowed; embedding one is not. |
| Checking the user's token and permissions on every request | Go API | A client cannot be trusted to enforce its own permissions. |
| Deciding sync conflicts | Go API, using rules defined in the sync contract | Every client needs to see the same outcome. |
| Shared account transactions | PostgreSQL, accessed through the API | Many users write at the same time. Database credentials must never be on the device. |
| Failover, backups, TLS, routing | Two hosts, Caddy, PostgreSQL replication | These are availability requirements. |
| Support for the previous client release | API (versioned sync protocol) and PostgreSQL (schema changes that add before they remove) | The one-month window. |

## Comparison

| Option | Meets the requirements? | Resource and speed evidence | Recurring cost | Migration and operations | Decision |
| --- | --- | --- | --- | --- | --- |
| **Current setup** | Meets all functional requirements as described. The WebView client is not an acceptable final design. | Client RAM (all processes, idle and active), API latency and host usage: all unknown until measured | Unknown until a complete bill is supplied | No migration. Unverified: how failover is done with only two hosts (see note below). | Keep as the baseline |
| **Proposal B:** keep the topology, profile, then optimize one measured component | Meets the requirements if the change keeps the sync contract and old-client support | No profile exists yet, so the component and the benefit are both unknown | Changes only if fewer or smaller hosts are actually provisioned | Low risk. One change at a time, easy to roll back. | **Recommended for the backend**, starting with profiling |
| **Proposal A:** one SQLite file shared across machines, "no server" | **Unsuitable** (reasons below) | Not relevant, because it fails the requirements | It still needs a file server and an OAuth provider | Replaces both storage systems and removes the sync contract | **Reject** |
| **Non-WebView client** (required comparison) | Conditional. It must match current features, offline use, sync behavior and old-version handling. | RAM for all processes, idle and active: unknown for both today's Tauri client and any replacement | Device and support costs unknown | Rewrite only the UI and keep the Rust core and sync engine. List every WebView bridge command. Repackage. | **Conditional path for the client**, gated on a RAM and feature check |

### Why the shared SQLite file fails

- **Offline use:** a file on another machine can't be reached offline. Clients would still need a local copy plus sync, which is the system you already have.
- **Concurrency and correctness:** SQLite allows one writer at a time. WAL mode does not change that. SQLite's own docs warn that file locking is unreliable over network filesystems, so shared account transactions risk corruption. See [WAL](https://www.sqlite.org/wal.html) and [SQLite over a network](https://www.sqlite.org/useovernet.html).
- **Failover:** a single file has no replication and no automatic failover.
- **Identity and permissions:** anyone who can open the file has full access to everything in it. OAuth identity can't be enforced, and in effect the database credentials sit on every device.
- **Conflict resolution:** nothing would decide conflicts. Concurrent writes either overwrite each other or fail on locks.
- **Previous-release support:** clients would read the schema directly, with no versioned API in between. A schema change would break clients still on the previous release.
- **"No server":** this is false. The file share is a server, and OAuth needs an identity provider.

### Why Proposal B still needs one step first

"Optimize one measured component" only makes sense once something has been measured, and nothing has been yet. Proposal B also only covers the backend. It leaves the WebView client as it is.

### Note on failover with two hosts

Automatic PostgreSQL failover across just two hosts needs a witness node, fencing, or a managed failover service. Otherwise both hosts can end up acting as the primary at once. The snapshot doesn't say which mechanism you use. Check this before calling the backend "already minimal".

## Recommendation

- **Backend:** keep the current shape: two Linux hosts, Caddy, the Go API and PostgreSQL. It is already close to the minimum your requirements allow. Every part does a required job: Caddy handles TLS and routing, PostgreSQL handles shared transactions and failover, and the API handles identity and conflicts. I found nothing to remove without dropping a requirement. Use Proposal B's approach: profile first, then change one component.
- **Client:** keep the Rust core, the SQLite drafts and the sync engine. Treat the Tauri/WebView UI as the baseline and a stepping stone. Evaluate replacing it with a UI that doesn't use a browser, on the platforms you support. Candidates include Qt Widgets without Qt WebEngine, Avalonia, WinUI 3 or Win32 if you are Windows-only, or a native Rust toolkit. None of these is verified yet. Each needs an FFI and packaging check against the Rust core.
- **Confidence:** high for rejecting Proposal A, since that follows directly from the stated requirements. Low on any resource gain from either other option, because nothing has been measured.

## Measurement and migration plan

1. **Baseline measurements:**
   - **Client:** RAM for the whole Tauri process tree (main process plus WebView processes), idle and during a scripted session (start, open a draft, edit, go offline, reconnect and sync). Same device each time.
   - **API:** p50/p95 latency and error rate at a stated request rate, plus Go CPU and heap profiles (pprof), PostgreSQL slow queries, and memory for each process on each host.
2. **Backend:** optimize the single largest cost found in step 1. A change is accepted only if:
   - p95 latency and error rate are no worse;
   - sync behavior tests pass for both the current and the previous client release;
   - a forced failover drill passes.

   Rollback means redeploying the previous binary. Any PostgreSQL schema change must add before it removes, so the previous release keeps working for the full month.
3. **Client:** build a prototype UI without a browser that runs the same scripted session on the same Rust core. Continue only if both of these hold:
   - its whole-process RAM is lower than the baseline;
   - it matches the existing features: accessibility, keyboard support, high-DPI displays, offline use and sync.

   Then ship it as a new client release. The backend treats it the same as any other client, so the one-month compatibility rule still applies. Local SQLite migrations must stay readable by the previous release, or back up the data before migrating, so users can roll back.
4. **Revisit the backend topology** only if one of these happens:
   - measured host usage shows capacity can actually be cut;
   - the failover requirement changes.

I can turn this into a shareable page if others need to review it.


## Case 10, run 2

I've reviewed the application as a **hybrid**: an installed desktop client plus a remote service. My only source was the snapshot in your message. The working directory has no code, deployment config, profiles or bills (`ls` showed only `.claude/`), so I didn't measure anything and none of the figures below are numbers.

## Recommendation

**Reject the shared-SQLite proposal.** It can't meet several of the stated requirements, and its "no server" claim is wrong: it still needs a file server and an OAuth provider, and drops authorization and failover.

**Accept the profile-first proposal, on one condition.** It keeps every requirement. But nobody has profiled yet, so nobody knows which component to optimize. Measure first.

**Treat the client UI as a separate track.** Tauri draws its UI in a WebView. The review rules I'm applying don't accept a WebView desktop UI as the final design, so I've added a non-WebView client option below even though neither proposal raises it. You can drop that rule if you don't want it.

## Who should own what

Neither proposal changes this split. It's the yardstick for comparing them.

| Responsibility | Device (Tauri: Rust core + UI) | Service (Go API, Caddy, PostgreSQL) |
|---|---|---|
| UI, drafts, six-hour offline use | Local SQLite: crash-safe writes, schema migrations, outgoing changes queued while offline | None |
| OAuth identity | Sign-in in the system browser; tokens kept in the OS keychain. The refresh token has to stay valid through the offline period | Checks every token and decides what each user may do. A client can't be trusted with this |
| Shared account transactions | Sends changes, each with an ID so a retry can't apply it twice | PostgreSQL is the authoritative copy and enforces transactions |
| Conflict resolution | Shows conflicts to the user; can make a provisional local merge | Final decision rests with the server, which knows the full order of changes |
| Old clients for a month | Reports its sync-protocol version | Keeps serving the previous version; schema changes are made in an add-first, remove-later order |
| Availability | Survives the API being down, thanks to offline mode | Two hosts with automatic database failover |

The snapshot doesn't answer these, and they affect the review:
- Where PostgreSQL runs.
- How a two-node failover avoids both nodes acting as primary at once (a witness or quorum?).
- Whether Caddy load-balances across both hosts or traffic fails over another way.

## Comparison

| Option | Meets requirements? | Resource evidence | Recurring cost | Migration / ops | Decision |
|---|---|---|---|---|---|
| **Current setup** | Yes (per the snapshot) | Unknown. No client or backend memory baselines | Unknown until complete bills are supplied | Already running | Baseline. Measure it |
| **Shared SQLite file across machines** | **No.** (1) The client would need direct database access, so nothing on a server enforces OAuth permissions. (2) Many writers on many machines: SQLite has one writer at a time, and its own docs warn its locking over network filesystems is unreliable (I didn't re-fetch them this session), so shared transactions are at risk. (3) It's one file: no replication, no automatic failover. (4) A network share isn't reachable offline. A local copy means you still need sync and conflict resolution, the parts it claims to remove. (5) Old clients would read the schema directly, so any schema change breaks one-month compatibility | Not applicable | It still needs a file server, which is a server | Rewrite sync, auth and storage, and weaken recovery | **Unsuitable** |
| **Keep topology, optimize one profiled component** | Yes, if the change passes the sync, compatibility and failover checks | Unknown until profiled (client process tree; Go `pprof`; PostgreSQL slow-query stats) | Savings unknown. They only become real if you can cut host capacity or metered usage | Small, contained change | **Recommended**, starting with profiling |
| **Non-WebView client** (keep the Rust core) | Conditional. Sync, OAuth, local SQLite and the API stay the same. The UI must reach parity, including accessibility, high-DPI, and dialogs and the clipboard | Idle and active memory of the whole client: unknown until the current Tauri app's full process tree (including the WebView helper processes) is measured on the same scripted session | Device and support costs unknown. Backend cost unchanged | UI rewrite, list of WebView-to-Rust bridge calls, new packaging. Candidate toolkits (Rust-native ones, or Qt Widgets over the Rust core) are unverified | **Conditional target**. Decide after a memory and parity test on one real workflow |

Any real simplification on the server side is small. Two hosts and failover are requirements, and a single Go binary behind Caddy is already lean. Moving to managed PostgreSQL would hand off the failover work, but its cost is unknown until a provider's price page is checked against the same region and availability level.

## Staged plan and acceptance checks

1. **Baseline.** Measure the client's whole process tree, idle and during a scripted session, on each supported OS. On the backend, record memory per process, CPU per successful request, and p50/p95 latency with error rate at a stated request rate. Collect the bills.
2. **Contract tests before any change:**
   - six hours offline, then sync;
   - conflicting edits;
   - retried changes that must not apply twice;
   - previous-release client against the new server;
   - token refresh after the offline period;
   - a failover drill measuring how much data is lost and how long recovery takes.
3. **Optimize the one dominant cost** the profile shows. Success means the contract tests pass and the measured target improves under the same workload. Roll back by redeploying the previous binary, and keep schema changes backward-compatible so that works.
4. **Optional client test.** Build one workflow in a non-WebView UI on the unchanged Rust core and compare its memory against the Tauri baseline. Continue only if it uses less memory and reaches parity. Ship it behind the same sync API, so the backend and old clients aren't affected. Make the local SQLite migration reversible, or export drafts first, so users can go back to the Tauri build.

**When to revisit:** if profiling shows the cost is in the database tier, if the failover drill fails what you can tolerate, or if the bills show unused capacity worth consolidating.

I can publish this as a shareable page if it's going to a team.
