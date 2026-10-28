# Hosting options and cost comparison

Research snapshot: 2026-10-28. Prices below were observed on official public pages, not confirmed in an account checkout. Refresh prices and stock before recommending a purchase. The reference storefront, currency and tax basis matter; do not assume the user's billing country from their timezone. Retain original currencies unless an exchange rate and date are supplied.

## Preferred deployment shape

For a modest dynamic application, evaluate one Linux VM running Caddy, a compiled application supervised by systemd, and SQLite on a persistent local filesystem. Build artifacts elsewhere and deploy them. Size from measured application use plus OS, proxy, filesystem cache, peak traffic and recovery headroom; no RAM tier guarantees capacity. A single VM is one failure domain, and reducing cost does not establish high availability.

First inspect existing authorized hosting capacity and its recurring cost, isolation and recovery needs. Reuse can avoid another bill, but spare resources and operational capacity must be verified. Consolidation can enlarge the failure domain. Do not assume access to, or permission to change, an existing server.

## Public price snapshot

The VPS rows are self-managed: application operation, updates and recovery remain the operator's responsibility. CPU counts across providers are not equivalent performance measurements. GB/GiB are retained as advertised.

| Option and reference market | Advertised monthly price | Included resources | Terms, availability and fit |
| --- | --- | --- | --- |
| OVHcloud VPS-1, Ireland storefront | From EUR 3.81 excluding VAT | 2 vCores, 4 GB RAM, 40 GB NVMe; daily backup; unlimited traffic at 500 Mbps outside stated regional exceptions | Product page labels the range “VPS 2027”. The entry configure link selects `upfront12`; treat this as an advertised annual-upfront starting price, not verified month-to-month or renewal pricing. Confirm region, IPv4, setup and final order terms. |
| OVHcloud VPS-2, same storefront | From EUR 7.21 excluding VAT | 4 vCores, 8 GB RAM, 75 GB NVMe; daily backup; unlimited traffic at 1 Gbps outside stated exceptions | Same starting-price and checkout qualifications; useful next tier to compare when measurements need more capacity. |
| Hetzner CX23, Germany/Finland | EUR 5.49 excluding VAT and IPv4 | 2 shared vCPUs, 4 GB RAM, 40 GB NVMe; 20 TB EU transfer | Cost-optimized page currently marks this plan unavailable. IPv4 adds EUR 0.50/month. Published rate is a price reference, not evidence it can be ordered. |
| Hetzner CAX11, Germany/Finland | EUR 5.99 excluding VAT and IPv4 | 2 shared Arm vCPUs, 4 GB RAM, 40 GB NVMe; 20 TB EU transfer | Also marked unavailable in the observed page. Check arm64 binaries, native dependencies and build support. IPv4 adds EUR 0.50/month. |
| DigitalOcean Basic Regular | USD 6 | 1 shared vCPU, 1 GiB RAM, 25 GiB SSD, 1,000 GiB transfer | Small native-app candidate subject to measured headroom. Backup charges are additional; confirm selected region, network and tax treatment. |
| DigitalOcean Basic Regular | USD 12 | 1 shared vCPU, 2 GiB RAM, 50 GiB SSD, 2,000 GiB transfer | More RAM and disk, same advertised CPU count. Region inventory must be checked. |
| AWS Lightsail Linux/Unix IPv4 bundle | USD 7 | 2 vCPUs, 1 GB RAM, 40 GB SSD, 2 TB transfer | Public IPv4 bundle. CPU burst characteristics and regional transfer exceptions matter; do not interpret vCPU count as sustained dedicated capacity. |
| AWS Lightsail Linux/Unix IPv4 bundle | USD 12 | 2 vCPUs, 2 GB RAM, 60 GB SSD, 3 TB transfer | Compare for AWS integration and regional fit. Snapshots, excess transfer and applicable taxes add to cost. |
| Cloudflare static assets | USD 0 for static asset storage and requests under documented service limits | Globally served static output; no user-managed VM | Good for Hugo or other prebuilt HTML/CSS/assets. Cloudflare recommends Workers for new projects; Pages remains documented. Static hosting does not run a persistent native backend, local SQLite process or user-managed Caddy. Dynamic Workers requests and data services have separate limits and pricing. |

Sources: [OVH VPS plans](https://www.ovhcloud.com/en-ie/vps/), [OVH entry configurator](https://www.ovhcloud.com/en-ie/vps/configurator/?brick=VPS%2BModel%2B1&planCode=vps-2027-model1&pricing=upfront12&processor=+&storage=40__SSD__NVMe&vcore=2__vCore), [Hetzner published price adjustment](https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/), [Hetzner specifications and stock](https://www.hetzner.com/cloud/cost-optimized/), [Hetzner IPv4 prices](https://docs.hetzner.com/cloud/servers/primary-ips/overview/), [DigitalOcean Droplets](https://www.digitalocean.com/pricing/droplets), [Lightsail bundles](https://aws.amazon.com/lightsail/pricing/), [Cloudflare static asset billing](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/), [Cloudflare platform guidance](https://developers.cloudflare.com/pages/).

### Other options investigated

- [netcup VPS](https://www.netcup.com/en/server/vps): VPS 500 G12.5 advertises 2 x86 vCores, 4 GB RAM and 64 GB SSD with a 12-month term. The extracted page displays EUR 8.26 alongside EUR 9.50 marked including 19% VAT; those figures do not reconcile as a simple net/gross pair. Treat the payable price as unresolved until country, network selection, discount and contract are verified. Do not rank it by an assumed net price.
- [Render](https://render.com/pricing): potentially less operational work, but the current price table was not readable in the retrieved page, so no verified compute quote is recorded. Its [disk documentation](https://render.com/docs/disks) says the default filesystem is ephemeral; persistent disks require a paid service and attach to only one instance. Account for disk pricing and deployment/scaling restrictions before considering SQLite there. “Native runtime” on a platform does not establish a container-free host or control over its proxy.
- [Vultr](https://www.vultr.com/pricing/): official price page returned HTTP 403 during this research; no current quote verified.
- Dedicated servers: evaluate only for measured sustained CPU, disk or consolidation requirements. Obtain a current regional quote including setup, storage redundancy, recovery and remote administration. Avoid a guessed starting price or comparing dedicated cores directly with shared vCPUs.

## Total cost and recovery

Monthly recurring cost = compute + addresses + persistent storage + backup storage/operations + excess transfer + required platform/database charges + applicable taxes. Show one-time setup and migration separately, and state annual upfront payment and renewal terms. Budget operational time explicitly when comparing a self-managed VPS with a managed service. Domain registration and transactional email may be separate costs.

- Hetzner backups cost 20% of the server price; primary IPv4 is EUR 0.50/month excluding VAT. Thus CX23 plus IPv4 and provider backups is approximately EUR 7.09/month excluding VAT: 5.49 + 0.50 + 1.098. This calculated example excludes independent backup storage and requires stock. [Billing](https://docs.hetzner.com/cloud/billing/faq/).
- DigitalOcean percentage-based weekly backups add 20%, daily backups 30%. The USD 6 tier with daily backups calculates to USD 7.80/month before other charges and taxes. Usage-based plans have separate rules. [Backup pricing](https://docs.digitalocean.com/products/backups/details/pricing/).
- OVH's advertised daily backup does not establish independent geographic recovery or the required retention. Check the chosen option and attached disk coverage. Hetzner server backups exclude attached volumes. [OVH backup terms](https://www.ovhcloud.com/en-ie/vps/), [Hetzner coverage](https://docs.hetzner.com/cloud/servers/backups-snapshots/overview/).

Use database-consistent backups and verify restoration; a whole-server snapshot alone is not a demonstrated SQLite recovery procedure. Determine acceptable data loss and downtime, retention, restore time and independent storage costs. Do not call a partial infrastructure subtotal a complete production budget.

## How to recommend

For each shortlisted host, show price basis, region, CPU architecture and sharing model, RAM, disk persistence and capacity, traffic limits, public connectivity, backup coverage, contract, availability and responsibility for operations together. Prefer the smallest measured configuration that meets requirements with headroom. Compare total cost and representative latency, disk behavior and sustained CPU rather than EUR per GB alone. For EU users, European regions are a sensible latency candidate to measure, not a guarantee.

Keep the PostgreSQL path open: first compare running PostgreSQL on the same adequately sized VM with a separate or managed database when concurrency, recovery, multi-host access or availability requirements warrant it. Obtain current database, storage and network quotes at that point; do not assume an entry VPS price covers a production PostgreSQL service.

Check pricing from official pages at recommendation time, record the date and selected market, and label unresolved quotes. Research never authorizes creating accounts, provisioning resources, committing to contracts or changing exposure.
