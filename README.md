# Software Architecture Efficiency

A reusable Codex skill for comparing resource-heavy architectures with practical, efficient alternatives.

Version: **1.3.0**. The skill version is recorded in `SKILL.md` under `metadata.version`; GitHub releases use matching tags such as `v1.3.0`.

The shared skill selects a **desktop**, **server** or **hybrid** review track from the actual application. Desktop guidance covers installed UIs, local data and distribution. Server guidance covers hosted services and browser applications, concurrency, recovery and hosting. Hybrid reviews apply each track to its own components while sharing evidence and reporting rules.

For desktop clients, the primary objective is **minimal RAM use with no embedded browser or WebView UI**. Electron and Tauri can be analyzed as existing baselines, but are not eligible final desktop targets. Compare non-WebView toolkits using full-application idle/active memory and required behavior; no framework is assumed to be the smallest. This restriction applies to the desktop part of hybrid systems, not browser-delivered server applications.

## Priorities

- Native host services instead of Docker where suitable.
- Compiled backends such as Go, Rust and compatible .NET Native AOT applications.
- Lean frontends: static output, server-rendered HTML and focused browser interaction.
- SQLite first, with an explicit migration path to PostgreSQL.
- Caddy for server reverse proxy and TLS where applicable.
- Lower CPU and RAM use, faster execution and realistic hosting costs.

Recommendations must preserve required behavior and distinguish measurements, estimates, published specifications and unknowns. Language or deployment preferences alone do not establish a performance improvement.

## Install

Copy the entire `skills/architecture-efficiency` directory into your Codex skills directory, normally `~/.codex/skills/`. On Windows, this is normally `%USERPROFILE%\.codex\skills\`.

Keep `SKILL.md`, `agents/` and `references/` together. If updating an existing installation, inspect and preserve local changes before replacing it. Start a new chat if the skill is not yet listed.

## Use

Example request:

> Use $architecture-efficiency to review this project. Compare the existing stack, tuning it, replacing expensive components and a simpler native stack. Include performance evidence, RAM, hosting costs, feature tradeoffs and a staged migration plan.

The skill can also produce illustrative comparisons when no application is supplied. An architecture review does not authorize a rewrite, production changes or hosting purchases.

## Contents

- [Skill instructions](skills/architecture-efficiency/SKILL.md)
- [Desktop application track](skills/architecture-efficiency/references/desktop.md)
- [Server application track](skills/architecture-efficiency/references/server.md)
- [Alternative solutions](skills/architecture-efficiency/references/alternatives.md)
- [Requirements and decision guide](skills/architecture-efficiency/references/decision-guide.md)
- [Measurement definitions and comparison report](skills/architecture-efficiency/references/report.md)
- [Hosting options and price research](skills/architecture-efficiency/references/hosting.md)

Hosting prices are dated research snapshots and must be refreshed before current recommendations or purchases. They are not checkout quotes.

## Sources

The references link to official product documentation. The decision guide includes a selective synthesis of [Project-Foundations](https://github.com/erophames/Project-Foundations) at a pinned commit, with file-level attribution. This skill does not require that plugin or its workflow.

## Evaluations

Version 1.3.0 has a [focused independent evaluation on five cases](evaluations/v1.3.0.md) covering the desktop/hybrid constraint, memory-based selection and a browser-app control. All five met the updated rubric in that run; [actual responses](evaluations/responses-v1.3.0.md) are retained. This does not claim eleven new passes or real application RAM benchmarks.

Historical evaluation outcomes apply to their version's rules; the corresponding release tag preserves the original cases and rubric. The 1.3.0 desktop policy supersedes earlier acceptance of WebView-based desktop targets.

Version 1.2.0 adds desktop/server/hybrid routing and a [recorded independent evaluation on ten cases](evaluations/v1.2.0.md), including three new desktop/hybrid cases. All ten met the rubric in that run. The [actual responses](evaluations/responses-v1.2.0.md), shared [case inputs](evaluations/cases.md) and [reviewer rubric](evaluations/rubric.md) are retained.

Version 1.1.0 includes a [recorded independent evaluation](evaluations/v1.1.0.md) on seven synthetic architecture cases, with [case inputs](evaluations/cases.md), a [reviewer rubric](evaluations/rubric.md) and [actual responses](evaluations/responses-v1.1.0.md). All seven met the rubric in that run. This checks analyst behavior on those scenarios; it is not an application benchmark or proof of general reliability.
