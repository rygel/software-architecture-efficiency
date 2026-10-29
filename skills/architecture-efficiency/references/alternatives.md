# Efficient alternatives catalog

Consult this catalog during an architecture review; select only candidates relevant to the workload. Official documentation linked below was checked 2026-10-28. Verify current versions, maintenance, platform support and integration fit before a recommendation. Candidate status is not evidence of lower total memory or faster execution for a particular application.

## Frontends: start with the least machinery that meets the need

| Candidate | Suitable for | What runs and tradeoffs |
| --- | --- | --- |
| Static HTML/CSS and native browser features | Content, documentation and simple forms | No frontend framework or application server for static pages. Dynamic content, authentication and form processing still need a suitable backend. |
| [Hugo](https://gohugo.io/about/introduction/) | Content-heavy sites and documentation | Go static-site generator runs at build time; resulting static files can be served by Caddy. Dynamic or personalized features need another service. Theme assets may still include JavaScript. |
| Server-rendered HTML and ordinary forms | CRUD, dashboards and data-driven sites | Go [html/template](https://pkg.go.dev/html/template), ASP.NET Razor or suitable Rust templates generate HTML on the server. Little or no browser JavaScript is necessary for basic navigation and forms. Full requests may feel less responsive for rich interactions; measure server work and round trips. |
| Server-rendered HTML + [htmx](https://htmx.org/docs/) | Partial page updates, forms, search and dashboards | Browser JavaScript library requests server-rendered HTML fragments; no Node server is required merely to use htmx. Handle loading, focus, history, errors, accessibility, CSRF and progressive enhancement. Latency and server load matter. |
| Server-rendered HTML + [Turbo](https://turbo.hotwired.dev/handbook/introduction) | Page navigation and server-driven partial updates | Browser JavaScript enhances HTML navigation and updates. Evaluate integration and the server-rendered HTML model; it is not a native-language frontend. |
| HTML + small vanilla JavaScript or [Alpine.js](https://alpinejs.dev/) | Local toggles, menus and simple client state | Runs in the browser; add behavior only where needed. As state and interactions grow, bespoke code and testing can become complex. |
| [Astro islands](https://docs.astro.build/en/concepts/islands/) | Content sites with a few rich interactive widgets | Build tooling uses a JavaScript ecosystem; static output needs no Node server, but on-demand rendering depends on its adapter/runtime. Explicit interactive islands send client JavaScript. Check dependencies, asset output and hosting before claiming savings. |
| ASP.NET Razor / [Blazor render modes](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/render-modes) | Teams already using .NET | Razor or static SSR produces HTML. Interactive Server keeps per-connection state and a live connection; WebAssembly downloads a .NET runtime and app bundle. These are distinct from a Native AOT API/worker; apply the complete-stack check in [decision-guide.md](decision-guide.md). Measure server memory, network resilience and initial download. |
| Existing SPA optimized in place | Rich offline editing, complex client state or extensive real-time interaction | Remove unused libraries, split code, reduce hydration and payloads, and measure before a rewrite. A client-heavy UI can be the right choice when its workload needs it. |

Separate build-time tools, deployed server runtime and browser-delivered code in every comparison. JavaScript in the browser is often necessary for interaction and is not the same resource cost as a persistent Node server. WebAssembly also has download, initialization and memory costs; do not assume it is inherently lighter.

## Other architecture layers

| Heavy or complex pattern | Candidates and conditions |
| --- | --- |
| Node/Python API or worker | Go or Rust executable; C#/.NET where team and library fit justify it, including Native AOT when compatible. First profile hot paths and account for native libraries already doing work. Compare total process memory, throughput and maintenance effort. |
| Containerized single-host deployment | Native packages/executables supervised by systemd or Windows Service Control Manager. Replace required sandboxing, privileges, limits, secrets, health checks, logging, dependencies, updates and rollback explicitly. |
| Database server for a modest single-host application | [SQLite](https://sqlite.org/whentouse.html) removes a separate database server. It supports one writer at a time per database file; assess contention, backup, durability and filesystem constraints. Keep SQL and migrations portable where practical, and validate migration to PostgreSQL for requirements such as multiple hosts or sustained concurrent writes. |
| Multiple proxy/TLS components | [Caddy](https://caddyserver.com/docs/automatic-https) can handle reverse proxy and automatic certificate management when its issuance and network requirements are met. Verify feature parity and actual exposure. |
| Separate cache, queue or scheduler | In-process cache or database-backed jobs for a single instance when durability and concurrency requirements permit. Explicitly test crash recovery, retention, retries, multiple workers and transaction boundaries; retain dedicated infrastructure if required. |
| Separate search service | Database indexes, SQLite FTS where appropriate, or PostgreSQL full-text search if already using it. Compare language handling, ranking, update behavior and scale against actual search requirements. |

## Evidence and comparison

Use [report.md](report.md) for shared measurement definitions, evidence labels and output. Check functionality, accessibility, security and recovery alongside resource use; candidate descriptions do not establish benchmark results.
