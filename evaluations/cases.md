# Architecture comparison evaluation cases

These are synthetic supplied snapshots for evaluating the skill, not observed projects or verified hosting offers. Each case is independent. Use the skill to answer its request from the supplied facts, identifying unknowns and conditional conclusions. No deployment, package installation or workload execution is authorized. Do not treat fixture numbers as measurements you reproduced. Preserve each case ID in the response.

## Case 01

Request: Review the frontend and deployment for lower resource use.

Snapshot: `package.json` defines `vite build` producing `dist/`. The deployment copies `dist/` to `/srv/catalog/`; Caddy serves that directory. No Node process is deployed. React renders 40 product descriptions and search/filter controls; data is bundled with the static assets. No login, write API, offline editing or database exists. Build tooling is Node-based. There are no browser memory, network transfer, responsiveness or server utilization measurements. Existing hosting is a shared VM with other applications; no price or capacity-reduction opportunity is supplied.

## Case 02

Request: Compare the existing administration app and the trial alternative, including costs and migration payback.

Snapshot: 20 staff use ordinary CRUD forms on one Linux x64 host. No offline behavior or public API consumers. Current stack is Node-rendered pages, a Python API, PostgreSQL, Redis job queue and Caddy. Trial uses Go HTML templates, htmx, SQLite, database-backed jobs and Caddy as native services. The same supplied behavior suite passed on both, including permissions and job retry/idempotency. SQLite write contention was absent in the trial; restore verification and shutdown/restart behavior have not been exercised. No PostgreSQL migration rehearsal has run.

Supplied `trial-summary.csv`: same 2-core VM class, dataset, durability, 40 requests/sec, 5-minute warm-up then 15-minute sample, 1-second interval. Whole-host memory including OS, proxy and data services: current maximum 1,400 MiB; trial maximum 310 MiB. Successful throughput 40/sec, errors 0%, p95 current 210 ms and trial 120 ms. Only one run each; toolchain versions are missing. Required budget: p95 <=200 ms at 40 successful requests/sec, errors <1%, memory <=700 MiB, recover within 30 minutes and lose at most 24 hours of data. The recovery budget is not yet verified.

Fictional budget sheet: current compute USD 24/month plus independent backups USD 4/month; candidate compute USD 6/month plus equivalent independent backups USD 2/month. Both offers specify the same region, required disk/traffic and month-to-month term without promotions or setup fees; amounts exclude taxes. Capacity, availability and prices are scenario assumptions, not current provider quotes. Migration is estimated USD 120 one time; expected monthly operator hours are unchanged. No tax rate is provided.

## Case 03

Request: Assess whether replacing the JavaScript frontend with server-rendered HTML will improve this application.

Snapshot: A React drawing editor supports airplane-mode use, immediate drag interaction, undo/redo, local draft persistence and later synchronization. The server is a Go API with PostgreSQL. Client CPU traces attribute most slow interaction time to repeatedly processing a 40,000-object scene; network is absent during the affected workflow. Total browser memory and JS heap have not both been measured. The user requires existing offline and editing behavior to remain. Team skills include React and Go; no proposed replacement prototype exists.

## Case 04

Request: Compare PostgreSQL and Docker with a native SQLite alternative.

Snapshot: Three active Linux hosts issue writes to one PostgreSQL database. The app requires transactions across account balances, sustained overlapping writes from every host, automatic failover and recovery within 60 seconds with no acknowledged transaction loss. Containers also enforce resource limits and per-service identities. The proposal places a single shared SQLite file on network storage, enables WAL and removes the database server and containers. No workload, failover or restore test of that proposal exists. Team can operate native PostgreSQL services; no need for a database-engine switch was identified beyond RAM savings.

## Case 05

Request: Recommend a native deployment for a .NET web application.

Snapshot: Target .NET version is 10. The app uses MVC controllers/views, cookie authentication, sessions, reflection-based plugins loaded at runtime and PostgreSQL. A proposal says to set `PublishAot=true`, keep all existing features unchanged and place Caddy in front. Only a development build has run; no publish warnings or published-artifact test exists. Windows x64 is the current host; a Linux Arm VM is suggested as cheaper, without an integration or native-dependency inventory. No memory or throughput measurements exist.

## Case 06

Request: Decide whether rewriting the Python API in Rust is justified by the benchmark.

Snapshot: Supplied Python production trace: requests spend 85% of elapsed time waiting for a database query with a missing index. A local index experiment reduced query time; no representative end-to-end load run followed. The benchmark spreadsheet compares 180 MiB RSS for the Python process (excludes two workers and database) with 240 MiB whole-host used memory for an unrelated Rust development service. Observation periods, datasets and load differ. Rust requests report lower p95 among successes but 8% timeouts; Python reports 0.2% timeouts under its different load. No Rust integration parity checks exist. The owner asks for a percentage RAM saving and a rewrite recommendation.

## Case 07

Request: Choose a fast, low-memory stack and say how much money migrating will save.

Snapshot: Only a dependency list is available: Next.js, Python, Docker, PostgreSQL and Redis. Deployment topology, product workflows, utilization, bills, data size, availability and workload are unknown. A pasted hosting table from 2024 advertises a EUR 3/month plan with no current stock, contract, tax, backup or bandwidth evidence. Migration budget and team skills are unspecified.
