# Why this skill exists

Software Architecture Efficiency helps answer a practical question: **can this application deliver its required behavior with less memory, less wasted computation and a simpler architecture?**

Applications accumulate costs through decisions that are reasonable individually: a browser engine for a desktop shell, another worker process, a cache, a database server, a deployment platform or a framework that brings more machinery than the product uses. Together, those choices can consume resources and create operating responsibilities that are hard to see from a dependency list.

The skill makes those costs visible and compares alternatives. Its output is a decision supported by evidence, including when to keep or tune an existing design. A replacement is useful when its benefits justify its migration and maintenance costs while preserving the behavior people rely on.

This document explains the choices behind the skill. The [skill instructions](../skills/architecture-efficiency/SKILL.md) and their references define the review procedure.

## The priorities are deliberate

The skill is opinionated about the architectures it investigates. It favors native software, low resource use, SQLite where suitable and straightforward operation. Desktop and server applications have different objectives and constraints:

| Application | Main objective | Architectural direction |
| --- | --- | --- |
| Installed desktop application | Minimize full-application RAM while preserving required behavior and usability | No embedded browser or WebView UI; compare suitable non-browser toolkits and optimize data/resource lifetimes. |
| Server or browser-delivered application | Meet workload and reliability requirements with efficient execution, controlled resources and realistic operating costs | Investigate native services, lean browser frontends, appropriate data stores and simple deployment. |
| Desktop client with remote services | Improve each component without breaking the product across their boundary | Apply desktop rules to the client and server rules to remote services; preserve offline use, synchronization and version compatibility. |

These priorities define what the skill looks for. They do not establish that a particular language or framework will outperform an inspected application.

## Why desktop reviews focus on RAM and exclude WebViews

An installed application consumes resources on the user's device alongside other applications. This project's desktop goal is to minimize that memory demand and use a UI architecture without an embedded browser engine.

That makes the no-WebView rule an **eligibility requirement**, not a prediction about every benchmark. Electron, Tauri and other embedded-browser designs can be inspected as existing baselines or retained temporarily during migration, but they are not compliant final desktop targets under the current rules.

The distinction matters because a native executable does not necessarily imply a non-browser UI. [Electron uses Chromium's multi-process model](https://www.electronjs.org/docs/latest/tutorial/process-model). [Tauri combines a native core with platform WebViews](https://v2.tauri.app/concept/architecture/). Replacing a bundled browser with a system WebView changes the packaging and runtime arrangement; it does not remove the browser-rendered UI from the architecture.

Eligible designs can use OS-native controls or a non-browser rendering toolkit. Neither category automatically wins on RAM. The selected controls, data model, image handling, caches, background services and native dependencies still matter. Cross-platform support, accessibility and distribution requirements can also determine whether an otherwise small implementation is usable.

The skill therefore asks for the complete application's memory use during representative work, including relevant helper processes. Installer size, the main process alone and a JavaScript heap figure answer different questions. A smaller download cannot establish a smaller working footprint.

Idle memory and active peaks can favor different implementations. The review must identify which workload and memory budget matter rather than select whichever metric makes a candidate look better. Repeated open/edit/close cycles can also expose retained data that a short startup measurement misses.

Required behavior remains a constraint: reducing memory by losing accessibility, dropping unsaved data or making interaction unusable is not an acceptable optimization. Moving the main UI into an external browser is not a workaround for this desktop requirement. Opening the system browser for a separate authentication or documentation task is a different boundary.

The [desktop track](../skills/architecture-efficiency/references/desktop.md) owns the detailed comparisons and checks.

## Why server reviews have different rules

A server must handle a workload over time, often for multiple users or other services. Its efficiency cannot be assessed from one process's idle memory alone. Request and job concurrency, successful throughput, errors, latency, persistence and recovery determine how much useful work the system actually delivers.

For example, a service with lower latency among successful requests may still be a worse design if it rejects or times out more work. A smaller database arrangement may also fail an existing durability or failover requirement. Those tradeoffs must be visible before a recommendation is ranked.

The desktop restriction does not apply to browser-delivered applications. The browser is their intended client environment. The server review instead asks how much client-side code and server infrastructure the actual product needs. Static output, server-rendered HTML and small interactive components are useful candidates when they preserve the required experience. Rich offline editing may justify retaining substantial browser-side state and computation.

Build tooling, deployed services and delivered browser code are separate costs. A project that uses Node to build static files does not necessarily run a Node server in production. Removing a dependency from the build is not evidence that deployed memory use decreased.

The [server track](../skills/architecture-efficiency/references/server.md) owns these workload and deployment comparisons.

## Why investigate native services and compiled code

Native deployment can make the running system easier to account for: executables, persistent data, configuration and explicit service supervision. It can remove deployment machinery that a particular application does not need. Compiled implementations can also be candidates for reducing runtime overhead or replacing an expensive computation.

These are hypotheses to investigate. If a request spends most of its time waiting on a poorly indexed query, changing the application language may leave the main problem intact. A focused query change or native component may offer a better return than rewriting the entire product.

Compilation, packaging and memory management are different properties. Go has a [garbage collector and associated CPU/memory tradeoffs](https://go.dev/doc/gc-guide). [.NET Native AOT](https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/) compiles code at publish time and targets a specific runtime environment; application features and dependencies still need to support that deployment model. A native binary is not proof of a negligible runtime footprint or a faster workload.

The same reasoning applies to Docker. [Containers are isolated processes that share a kernel](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/), not a separate operating system for every service. Removing containers does not justify inventing a fixed RAM saving. Any benefit must come from the actual deployment being reviewed, including any VM or management overhead present there.

Native deployment also inherits responsibilities previously supplied by the container or platform: isolation, identities, resource bounds, configuration, logs, restarts, health checks and updates. Simplification is worthwhile when the resulting system meets those needs with less unnecessary machinery. It is incomplete if those responsibilities simply disappear from the proposal.

## Why SQLite first, with a PostgreSQL path

For suitable local data, SQLite keeps persistence inside the application rather than requiring a separately operated database server. That fits the aim of a small desktop application and can also fit a modest single-host server. SQLite's own [usage guidance](https://www.sqlite.org/whentouse.html) distinguishes local storage needs from the shared-access responsibilities of client/server databases.

The preference has limits. A design requiring shared access from multiple hosts, sustained overlapping writes or a particular failover arrangement may need PostgreSQL or another server database. SQLite [WAL improves reader/writer concurrency but still has one writer at a time and same-host constraints](https://www.sqlite.org/wal.html). It is not distributed replication or a way to turn a network-shared file into a failover database service.

Keeping a PostgreSQL path open means making database semantics and migration work explicit. It does not mean building an elaborate abstraction layer before it is needed. Existing data-access boundaries, deliberate types, migration scripts and shared behavior cases may be enough. An ORM does not prove that constraints, transactions, queries or concurrency behave identically on both engines.

The skill distinguishes a planned path from one exercised on representative data and the destination engine. That distinction prevents future flexibility from being presented as a completed engineering result.

## Why Caddy is the preferred server proxy

Caddy is a concrete default to evaluate for server routing and TLS because its [automatic HTTPS support](https://caddyserver.com/docs/automatic-https) includes certificate provisioning and renewal where the deployment meets its requirements. The rationale is a simpler operating arrangement for those responsibilities.

This is not a claim that Caddy always uses less memory or serves requests faster than an existing proxy. Required routing, authentication, integrations and certificate behavior still need checking. A functioning proxy should not be replaced solely to make a diagram match a preferred stack. A local-only desktop app does not need a proxy merely because Caddy is favored for servers.

## Why migration cost and evidence are part of efficiency

The skill compares keeping the existing design, tuning it, replacing a costly component and simplifying the complete stack where relevant. This exposes smaller interventions and makes a rewrite compete with practical alternatives.

Migration has costs beyond code: behavior parity, data conversion, packaging, operations, support and recovery. Rolling an executable back may not undo newly written data or a schema change. Hybrid systems also have clients that update at different times, so replacing a desktop UI cannot silently change the remote API or synchronization contract.

Recommendations consequently separate measured evidence, published capabilities or prices, estimates and unknowns. A framework manual can establish a supported feature. It cannot establish the memory use of an application that has not been built and measured.

The [measurement and report reference](../skills/architecture-efficiency/references/report.md) gives those labels consistent meanings. It also requires equivalent workloads and resource boundaries, so a whole application's memory is not compared with one process from another architecture.

Resource savings and monetary savings are distinct. A smaller application does not reduce a fixed hosting bill until it permits an actual capacity or usage change. Hosting comparisons need equivalent persistence, traffic, recovery and availability, with taxes, commitments and renewal terms made explicit. Published prices also age, which is why [hosting research](../skills/architecture-efficiency/references/hosting.md) is dated and must be refreshed for a current decision.

## Why one skill has separate reference files

The project currently provides one skill with desktop, server and hybrid routes. Shared evidence standards, compatibility reasoning and report structure stay consistent. Application-specific references avoid applying hosting advice to a local desktop program or treating request latency as a complete measure of UI responsiveness.

This structure also lets an agent load details only when they affect the review. More sub-skills would be justified by independently useful workflows, not by the number of Markdown files. Scripts are appropriate for repeated deterministic work; adding a framework or collector does not by itself improve architectural judgment.

The [Project-Foundations synthesis](../skills/architecture-efficiency/references/decision-guide.md#project-foundations-useful-engineering-guidance) contributes language-specific build, dependency and verification considerations with pinned source attribution. Its guidance helps assess whether a replacement is practical to maintain. The skill does not require that plugin, its wider workflow or every tool listed by its language guides.

## What validation can establish

Metadata and link validation check whether the skill package is structurally usable. Independent synthetic evaluations check how an analyst applies the instructions to particular supplied cases. They do not measure an actual application's performance, prove migration success or establish reliability on every project.

The repository retains [case inputs](../evaluations/cases.md), a [reviewer rubric](../evaluations/rubric.md), actual responses and [version-specific outcomes](../evaluations/v1.3.0.md). Historical outcomes apply to the rules evaluated at that version; release tags preserve their original context. Author assessments of those responses are not human approval.

A useful review ultimately leaves the reader with a supported choice, clear tradeoffs and the next smallest check needed to resolve uncertainty. It can recommend retaining the current architecture or report that no compliant replacement has yet been demonstrated. That is more actionable than an unsupported promise that a preferred technology will be faster, smaller or cheaper.
