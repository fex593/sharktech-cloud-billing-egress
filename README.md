# managed cloud web hosting: Sharktech's plan prices, billing rules and egress math explained, with a Cloudways comparison

Most people searching for managed cloud web hosting want two things: someone else keeps the platform running, and the bill at the end of the month looks like the bill they expected. The short version from our research: **Sharktech's Public Cloud starts at $39/month** for a full OpenStack resource pool with DDoS mitigation and a generous outbound traffic allowance, while **Cloudways' published pricing starts at $14/month** (Micro tier) if all you need is one managed app on someone else's cloud. These are different products wearing similar labels, and the details below should tell you which one fits your situation.

> **Quick pick:** Running multiple VMs, a small production stack, or anything that attracts DDoS traffic? The **Public Cloud Small plan at $39/month** is the sensible entry point — in the official cost calculator's example configuration (1 VM, 2 vCPU, 4 GB RAM, 100 GB storage), it bills at exactly $39 with zero overage. Just hosting one site and wanting zero server work? A cheaper app-level host will do. 👉 [Configure the Public Cloud Small plan on Sharktech](https://portal.sharktech.net/cart.php?a=add&pid=602&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626)

## What "managed" actually covers on Sharktech

Sharktech manages the platform layer; you manage what runs inside your VMs. That's the honest framing before any credit card comes out.

What Sharktech handles: the OpenStack cloud platform itself, networking (virtual routers with NAT, floating IPs, fine-grained firewalls and security groups, load balancers), automatic failover for high availability, weekly updated Linux images, free native VPN integration for hybrid setups, and 24/7 technical support. The infrastructure spans five data centers — Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam — interconnected by multiple 40G and 100G links, with DDoS protection built into the network rather than bolted on as a paid add-on. The product page advertises 99.999% uptime on this fully redundant setup.

What stays on your side: installing and operating your applications. This is infrastructure-level cloud hosting with full compute, network, and storage control through the portal or the OpenStack API (Nova, Cinder, Swift, Neutron, Keystone). API access is standard on both billing models, restricted to approved source IPs. If "managed" to you means "patch my WordPress plugins automatically," that's a different category — the comparison section covers it.

One genuine advantage worth flagging: because the platform is standard OpenStack, you can upload your own qcow2 images or ISOs and download disk images for offsite backup or migration. Sharktech positions this as no vendor lock-in, and the exit path is real — something proprietary managed hosts rarely offer.

## Sharktech Public Cloud: the full plan lineup

The official cloud cost calculator currently lists four Public Cloud tiers, all priced monthly in USD. The product page also quotes hourly equivalents, so short-lived workloads can be measured per hour:

| Plan | Included resources | Base price | Hourly start | Order link |
| --- | --- | --- | --- | --- |
| Small | 4 vCPU / 8 GB RAM / 300 GB SSD / 20 TB outbound | $39.00/mo | ~$0.0609/hr | [ Configure Small](https://portal.sharktech.net/cart.php?a=add&pid=602&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626) |
| Medium | 8 vCPU / 16 GB RAM / 800 GB SSD / 20 TB outbound | $79.00/mo | ~$0.1289/hr | [ Configure Medium](https://portal.sharktech.net/cart.php?a=add&pid=603&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626) |
| Large | 32 vCPU / 64 GB RAM / 1,500 GB SSD / 20 TB outbound | $249.00/mo | ~$0.3989/hr | [ Configure Large](https://portal.sharktech.net/cart.php?a=add&pid=604&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626) |
| Enterprise | 64 vCPU / 128 GB RAM / 5,000 GB SSD / 20 TB outbound | $499.00/mo | ~$0.7412/hr | [ Configure Enterprise](https://portal.sharktech.net/cart.php?a=add&pid=605&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626) |

Every plan includes one free public IPv4. Beyond the four fixed tiers, Sharktech also offers a Custom configuration (higher specs, more IPv4 addresses, doubled bandwidth allowance) priced by quote — the product page lists it starting around $51.82/month, and you'd request pricing through their sales team.

The spec column above matches Sharktech's own product page and its published calculator pricing. One note on billing flexibility: the current calculator prices these as monthly commitments; third-party roundups report that quarterly, semi-annual, and annual cycles on the fixed tiers historically carried 5–15% discounts. Treat that as a bonus if it shows up at checkout, not a promise.

## How the billing actually works (the part most roundups skip)

Public Cloud bills as a base commitment plus metered usage above the plan's included pool. Officially: "Base commitment + usage above the plan's included pool." Compute is metered by selected hours, storage by a 730-hour estimated month, transfer and IP charges monthly. The published overage rates:

- **CPU:** $0.0025 per core, per hour (Enterprise $0.0020)
- **RAM:** $0.0035 per GB, per hour (Enterprise $0.0030)
- **NVMe storage:** $0.00009 per GB, per hour (Enterprise $0.00008)
- **SSD storage:** $0.00006 per GB, per hour (Enterprise $0.000045)
- **HDD storage:** $0.00002 per GB, per hour (Enterprise $0.000015)
- **Extra public IPv4:** $1.50/month each
- **Outbound bandwidth:** $0.002/GB beyond the included pool (Enterprise $0.0015)

Two details from the official FAQ deserve more attention than they usually get.

First, Small, Medium, and Large plans have **resource ceilings**; Enterprise has no preset ceiling. Sharktech is explicit about what that means: "Resource ceilings are not monetary spending caps." A ceiling bounds how far you can burst beyond your included pool, which prevents runaway usage — but on Enterprise, a traffic spike can keep metering upward with no preset limit. If bill predictability outranks headroom, that's a real consideration.

Second, and this one catches people: **storage stays billed while a VM is stopped.** The calculator says it plainly: "Storage stays allocated for the estimated month, even when a VM is stopped." Shutting a machine down saves compute charges; the disks keep accruing until you delete them. Anyone who has run a test environment on metered cloud has paid this tax at least once.

The resource-pool model itself is where the value sits. Resources are accounted for across your entire cloud rather than by fixed VM shapes — a Medium pool of 8 vCPU / 16 GB / 800 GB can run as one machine or eight smaller ones, reshuffled anytime. Storage comes in three tiers you assign per volume, and the published IO figures make the trade-offs concrete: NVMe at roughly 1.2 GB/s and 18,000 IOPS, SSD at ~350 MB/s and 6,000 IOPS, HDD at ~120 MB/s and 3,000 IOPS. A database belongs on NVMe; backups belong on HDD — same cloud, same bill, different volume types. 👉 [Open Sharktech's Public Cloud configuration and size a pool around your own numbers](https://portal.sharktech.net/cart.php?a=add&pid=603&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626)

## Public Cloud vs Dedicated Cloud: same platform, different bills

Sharktech runs two billing models on identical OpenStack infrastructure, so the choice is really about how you want to be billed:

|  | Public Cloud | Dedicated Cloud |
| --- | --- | --- |
| Billing | Monthly base plus metered usage above included resources | Fixed price for your chosen allocation |
| Growth | Burst into extra resources at published hourly rates | Upgrade your allocation through the billing platform |
| Limits | Small/Medium/Large have ceilings; Enterprise has none | Your purchased allocation; no automatic overages |
| Best for | Changing resource demand | Steady workloads and predictable budgets |

Dedicated Cloud is Sharktech's answer to bill anxiety: "If you pay for 8 cores, you get 8 cores. No more, no less." No hourly resource charges, no automatic overages — a fixed monthly price for a fixed allocation you can still split across as many VMs as you like. If your workload is steady and you'd rather never think about metering, this removes the only genuinely variable part of the Public Cloud model.

One naming trap to clear up: Dedicated Cloud is a fixed allocation on the shared Sharktech platform. Private Cloud is a separate product — exclusive physical infrastructure for one organization. Similar words, different products.

## The egress math nobody shows you

Data transfer is where hyperscaler cloud bills quietly triple, and Sharktech's approach here is its sharpest cost argument.

Inbound traffic is free — the company states explicitly that there are no ingress charges, precisely because egress fees are what lock clients into big clouds. Outbound traffic beyond the included allowance bills at $0.002/GB, and every standard tier ships with 20 TB of outbound per month per the product page.

Put numbers on it: pushing 10 TB of outbound traffic monthly at the roughly $0.09/GB rate Sharktech's own comparison cites for hyperscalers runs about $900/month in transfer alone. On Sharktech, that same 10 TB sits inside the included pool of any standard tier — $0 in overage — and even fully above the allowance it would bill around $20. That gap widens as traffic grows.

Underneath it all sits the protection layer. Sharktech's published specs bundle always-on DDoS mitigation rated at **60 Gbps per IPv4 address** on every cloud plan — network-level filtering that's active continuously rather than a scrubbing service that shows up after your site goes dark. The company has run DDoS-protected hosting for over two decades, and for anything that attracts attacks (game servers, trading tools, high-visibility sites), buying equivalent mitigation separately usually costs more than this entire plan. If that describes your project, the math tilts hard. 👉 [Pick a data center and deploy a DDoS-protected cloud VM](https://portal.sharktech.net/cart.php?a=add&pid=602&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626)

## When Sharktech is the wrong pick

Honest caveats, because no single host fits every search.

If you're running one blog or small business site and want the application layer handled — updates, caching, staging — **Cloudways fits better**. Its published pricing lists self-select cloud plans from $14/month (Micro: 1 GB RAM, 1 vCPU, 25 GB NVMe, 1 TB bandwidth), with managed Node.js plans from $20/month; those Node.js tiers include DDoS and malware protection, and you can deploy on DigitalOcean, Vultr, Linode, AWS, or Google Cloud. You trade infrastructure control for simplicity, and for one site the trade is usually correct. Just note that backup storage beyond the platform bills extra ($0.033/GB) and CDN overages run $0.02/GB — for a traffic-heavy site, the gap between the entry prices narrows faster than the front page suggests.

Sharktech also isn't the cheapest way to get one small virtual server. The company runs a separate Proxmox-based Smart VPS line with pre-packaged plans starting at $7.95/month, which undercuts its own cloud entry point for single-server needs. The cloud earns its price once you want the OpenStack primitives: resource pools, multiple VMs, custom networking, API automation, or mixed storage tiers.

Where Sharktech clearly wins: multi-VM production stacks, heavy outbound traffic, serious DDoS exposure, teams wanting OpenStack compatibility and standard REST APIs instead of a proprietary console, and migrations off hyperscalers where egress fees dominate the bill.

## FAQ

**Can my Sharktech bill spiral out of control?**
On Small, Medium, and Large, resource ceilings bound how far you can burst beyond your included pool. Enterprise has no preset ceiling, so watch the portal's usage monitoring on that tier. And remember: stopped VMs stop compute billing but keep storage billing until the disks are deleted.

**Which plan fits one website?**
Sharktech's own calculator answers this: a 1-VM configuration with 2 vCPU, 4 GB RAM, and 100 GB storage bills at exactly **$39/month on Small** with $0.00 in additional compute, storage, transfer, or IP charges. That comfortably runs a busy WordPress or small business site, with the included DDoS protection and 20 TB outbound allowance along for the ride.

**Can I run Windows VMs?**
Yes. The platform supports both Linux and Windows virtual machines, Linux images update weekly, and you can upload your own qcow2 images or ISOs if you need a specific OS build.

**Will I get locked in?**
The platform is standard OpenStack with a documented REST API covering compute, storage, networking, and identity. You can download your disk images and move them to any OpenStack-compatible environment — an exit path most proprietary managed hosts don't provide.

## Before you order

Three rules to close with. Running one small site and wanting zero server work? An app-level managed host from around $14/month is the right shape, and a full OpenStack cloud would be overkill. Running a steady production workload and allergic to variable bills? Configure a Dedicated Cloud allocation instead of Public Cloud and skip metering entirely. Need a resource pool, multiple VMs, or real always-on DDoS protection without scrubbing-fee surprises? Public Cloud Small at $39/month is where the value starts, and the calculator shows your exact number before you commit anything. 👉 [Configure and deploy your Sharktech cloud now](https://portal.sharktech.net/cart.php?a=add&pid=602&carttpl=public_cloud_cart&configoption%5B1677%5D=11563&language=english&aff=1626)
