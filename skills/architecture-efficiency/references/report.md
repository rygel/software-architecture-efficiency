# Measurement contract and comparison report

This is the canonical reporting and measurement format. Use only dimensions that affect the decision. A qualitative review does not require launching benchmarks or building replacements.

## Evidence labels

Label material values and claims: **measured** (traceable observation and method), **published** (dated official specification or price), **estimated** (calculation/model with assumptions), or **unknown**. Supplied logs can be measured evidence without being independently reproduced; state their provenance. Reasoned recommendations are conclusions drawn from those inputs, not additional measurements. Do not rank a hypothetical implementation as proven to meet a budget.

Attach each observation to a file/line, log/sample, bill, supplied artifact or direct source URL and date. Record what was inspected and what could not be verified. An unresolved quote or unavailable plan cannot establish purchasable savings.

## Measurement contract

Before comparing numbers, record the hardware/VM, CPU architecture and sharing model, OS, software versions, production build/profile, dataset, request mix, concurrency or offered rate, observation duration, sampling interval, warm-up and cache state. Use the same successful behavior and durability requirements across candidates. Keep load-generation overhead outside the measured scope or disclose its effect. Record run count and variability; treat one run as preliminary and repeat only when uncertainty could change the decision.

| Metric | Definition needed for a fair comparison |
| --- | --- |
| Process/host memory | State units and accounting: private bytes, working set, process RSS, proportional set size (PSS), cgroup accounting or whole-host use. Include child processes and relevant services. Define idle state and peak as the maximum observed over the stated window and sampling interval, not an assumed lifetime maximum. Record swap and cache treatment where material. Do not sum unsynchronized peaks or equate RSS sums with unique physical memory. |
| CPU | State CPU-seconds per successful operation or utilization with the core/capacity denominator and window. Separate burst results from sustained capacity and note throttling or contention. |
| Throughput and latency | State offered and successful work rates, error/rejection/timeout rate, client/server measurement boundary, units, sample count and p50/p95. Include failures alongside latency and explain timeout treatment; lower latency caused by dropping work is not a win. |
| UI/browser/WebView | Fix device, display, browser/renderer and user journey. Distinguish input-to-visible response, frame timing and startup from request latency. Report network transfer and JS execution separately when present. A JS heap or main-process figure is not full browser/desktop process-tree memory. Include the interaction/offline behavior being preserved; use the selected track for platform-specific measurements. |
| Startup and build | Separate cold/warm startup, readiness and build/publish time and peak resources. Development servers and unoptimized binaries are not production comparison baselines. |
| Storage and cost | Separate durable data, indexes, WAL/logs, backups and installed/deployed artifacts. Server cost must cover equivalent region, availability, traffic and recovery. Desktop comparisons include distribution/support and device costs when relevant; do not invent a hosting bill for a local app. Show billing period, currency, taxes and commitment for priced costs. |

The Linux kernel documents [RSS/PSS and process accounting](https://www.kernel.org/doc/html/latest/filesystems/proc.html). Use the target platform's equivalent metric rather than silently mixing definitions. Choose a bounded representative slice for a new experiment; avoid measuring every dimension by default.

## Compact report

1. **Scope and constraints:** inspected sources, desktop/server/hybrid track and component boundaries, workload, hard requirements, acceptance budgets and unknowns. Label proposed budgets as proposals. State whether evidence describes a live system, supplied snapshot or illustrative scenario. For hybrid systems, report device and backend costs separately before combining anything.
2. **Comparison:** use the table below for relevant options, keeping tradeoffs and evidence visible together. Cover complete-stack compatibility and feature parity before ranking efficiency.

| Option | Requirement/compatibility verdict | Resource and speed evidence | Recurring cost | Migration/operations cost and tradeoffs | Decision and next check |
| --- | --- | --- | --- | --- | --- |
| Current baseline | Meets / conditional / unsuitable, with reason | Values with evidence labels and scope | Itemized total or unknown | Existing responsibilities and constraints | Keep / investigate |
| Tuned baseline, if useful | Verdict and constraint affected | Same metrics and workload | Total or unknown | Change effort and behavior risks | Priority and acceptance check |
| Focused replacement, if useful | Verdict and compatibility gaps | Same metrics; estimates stay estimates | Total or unknown | Integration, cutover and maintenance | Priority and acceptance check |
| Simplified stack, if useful | Verdict for all combined components | Whole-stack accounting; avoid summed hypothetical savings | Total or unknown | Lost/changed capabilities, migration and operational responsibility | Priority and remaining evidence |

3. **Recommendation:** explain the strongest supported choice, what to retain, evidence confidence and the trigger for revisiting it. If data is insufficient, recommend a measurement or compatibility check rather than force a winner.
4. **Migration and acceptance:** ordered steps, shared behavior cases, required target-engine/published-artifact checks, success thresholds, downtime/data-loss implications and rollback boundaries. Identify owners or prerequisites when known; do not invent approval or implementation results.

For a narrow question, compress these items into a short answer or table. Avoid repeating the same caveats in every cell. This format should make the decision reviewable, not manufacture a lengthy report.

## Savings arithmetic

Use the same currency, tax basis and time period. Monthly infrastructure savings = current recurring infrastructure total minus candidate total. Keep one-time setup/migration costs separate. Annual recurring savings = monthly savings times 12 only for unchanged monthly rates; model promotions and renewal/commitments separately. Percentage reduction = savings / positive baseline times 100.

When operator time is priced, state hours and rate and include its monthly difference in **net** savings. Payback months = one-time migration cost / positive monthly net savings. Without those inputs, report payback as unknown or a labelled scenario; do not invent engineering rates. Smaller processes do not save a fixed hosting bill unless capacity can actually be reduced or metered usage falls.
