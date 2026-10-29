# Server application efficiency

Use for hosted APIs, services, workers and browser-delivered applications. Static frontend assets belong here even when no application server is running. Keep shared feasibility in [decision-guide.md](decision-guide.md) and measurements/reporting in [report.md](report.md). Load [alternatives.md](alternatives.md) when comparing browser or infrastructure replacements and [hosting.md](hosting.md) when deployment cost matters.

## Workload and architecture

Map request and job paths, app/worker replicas, database connections, cache/queue roles, external dependencies and proxy boundaries. Record request mix, normal/peak concurrency, successful throughput, latency/errors, read/write contention, job duration/retries and data growth. Evaluate CPU, allocations, queries, network round trips, serialization and payload/hydration costs before choosing a language or removing services.

Compare keep/tune, focused replacement and a simpler complete stack where useful. For a suitable single-host workload, a candidate is Caddy, an OS-supervised compiled service and local SQLite with consistent independent backups. This is a candidate, not a default that overrides availability, write concurrency, isolation or integration requirements. Static content may need only file serving. A browser build using Node does not itself imply a deployed Node process.

## Feasibility and operations

- Preserve authentication/authorization, TLS/proxy behavior, API contracts, jobs, durability, tenant boundaries and observability. Removing a queue or cache requires checking the semantics it supplied, not just its memory use.
- Treat burst/shared CPU differently from sustained capacity. Include relevant replicas, workers, database and proxy in resource totals and state who owns each cost; avoid comparing a complete stack with one process.
- Distinguish single-host recovery from automatic failover, replication and multi-host service requirements. Define acceptable data loss/downtime and verify acknowledgement/durability and recovery behavior before claiming equivalence. WAL is not a distributed database or failover mechanism.
- Native deployment must supply the necessary service identity, privileges/isolation, resource limits, persistent directories, protected configuration/secrets, health/readiness checks, graceful shutdown, restart/logging, Caddy routing, updates and rollback. Build elsewhere and verify the published artifact for the actual OS/CPU and native dependencies. Do not add those deployment features to a client-only desktop application.
- The selected framework, driver, serializers and deployment mode must work together. For .NET 10, the [ASP.NET Core Native AOT table](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/native-aot?view=aspnetcore-10.0) lists MVC and Blazor Server as unsupported. Native AOT API/worker candidates are distinct from conventional Razor/MVC or Blazor apps. Check selected-version support, required features, publish warnings and published-app behavior. Checked 2026-10-29; refresh for the version reviewed. Do not apply this ASP.NET table to an unrelated desktop UI framework.

## Cost and migration

Compare complete recurring hosting cost for equivalent region, traffic, persistence, backup, availability and operational responsibility. Check existing capacity, current official price/stock, CPU architecture, commitments and renewal terms. Historical price tables are references rather than purchasable quotes; smaller processes save money only when provisioned capacity or metered usage changes.

Stage changes at service/data boundaries with equivalent behavior cases, consistency checks, accepted performance/error budgets and data-aware rollback. A PostgreSQL migration path is planned until verified against representative data on PostgreSQL itself. An operational proposal or benchmark plan does not authorize implementation, traffic generation or purchases.

For a desktop client backed by these services, use [desktop.md](desktop.md) for the device process and user journey. Account for offline/synchronization requirements, API/client version compatibility and device-versus-server resource attribution before recommending shifting work across the network.
