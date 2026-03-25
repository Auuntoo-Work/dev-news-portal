---
title: "In Edison's Revenge, Data Centers Are Transitioning From AC to DC Power"
description: "Hyperscale cloud providers are quietly redesigning their data center power infrastructure, replacing traditional AC distribution with DC power buses to cut energy losses by up to 15%. Driven by the massive power demands of GPU-heavy AI training clusters, companies like Google, Microsoft, and Meta are embracing DC-native architectures — a fundamental shift that affects how developers think about cloud cost optimization, workload placement, and sustainable infrastructure."
pubDate: 2026-03-25T21:00:00Z
tags: ["data-centers", "infrastructure", "power-efficiency", "cloud-computing", "ai-infrastructure", "sustainability", "gpu-clusters"]
author: "AI Editor"
category: "DevOps"
---

## The War of Currents, Round Two

Thomas Edison lost the original war of currents in the 1880s. Alternating current won because it could be transmitted efficiently over long distances using transformers — something DC couldn't do at the time. For over a century, AC has been the undisputed standard for electrical distribution, including inside every data center on the planet.

That's changing. Hyperscale cloud providers are now redesigning their power infrastructure around **high-voltage direct current (HVDC)**, specifically 800V DC distribution. The shift is being driven by a simple reality: AI training clusters are pushing per-rack power demands toward 1 megawatt, and at that scale, the cascading inefficiencies of AC-to-DC conversion stages become too expensive to ignore.

## Why AC Distribution Is Breaking Down

Traditional data center power follows a long conversion chain. Utility power arrives as medium-voltage AC, gets stepped down to low-voltage AC (typically 480V), passes through an uninterruptible power supply (UPS), flows through power distribution units (PDUs), and finally hits individual server power supply units (PSUs) — where it's converted to DC to actually run the chips.

Each conversion stage introduces losses. A typical data center power path includes **three to four AC-DC and DC-AC conversion stages** before electricity reaches a GPU. Every stage burns energy as heat. At conventional rack densities of 10-20 kW, these losses were acceptable. At 100 kW to 1 MW per rack — the reality of modern AI training infrastructure — they represent millions of dollars in wasted electricity annually at gigawatt scale.

The math is straightforward: eliminating unnecessary conversion stages can improve end-to-end power delivery efficiency by **3% to 5%**. For a facility drawing hundreds of megawatts, that translates to tens of megawatts saved — enough to power additional GPU racks without pulling more from the grid.

## The Mt. Diablo Specification

The industry isn't improvising. In the fall of 2024, **Microsoft and Meta** jointly announced the Mt. Diablo project through the Open Compute Project (OCP). **Google joined in the spring of 2025**, and the specification was formally released in August 2025. Mt. Diablo standardizes on a **±400VDC three-conductor system** (effectively 800V between conductors) designed to power AI racks from 100 kW up to 1 MW.

The architecture centralizes the AC-to-DC conversion at the facility level, then distributes DC directly to the racks. Inside the rack, power is converted from 800V DC down to the voltages individual components need — but the multiple intermediate AC stages are gone. Fewer conversions means less heat, less copper, and less wasted energy.

**NVIDIA** is building its next-generation rack-scale systems around this architecture. The upcoming **Vera Rubin Ultra Kyber** platform is designed natively for 800V DC power delivery. Vertiv has announced that its 800V DC ecosystem — purpose-built for these NVIDIA platforms — will be commercially available in the second half of 2026.

## What This Means for Developers

Most developers will never touch a power bus. But the AC-to-DC transition has downstream effects that show up in cloud bills, instance availability, and sustainability metrics:

- **Cloud pricing pressure** — More efficient power delivery means lower operational costs per GPU-hour for cloud providers. As HVDC facilities come online, expect competitive pricing pressure on AI training and inference instances, particularly from providers who build new DC-native facilities versus those retrofitting legacy AC infrastructure.
- **Regional availability shifts** — DC-native facilities are being built from scratch in locations with abundant renewable energy and favorable grid connections. Meta's first multi-gigawatt data center, **Prometheus**, is coming online in 2026. New capacity won't be evenly distributed — workload placement decisions may need to account for where the most efficient infrastructure lives.
- **Sustainability reporting** — If your organization tracks Scope 3 emissions from cloud usage, the underlying power architecture of your provider's facilities matters. DC-native data centers will report lower PUE (Power Usage Effectiveness) numbers, which flows through to your carbon accounting.

## The Transition Won't Be Instant

Despite the momentum, a wholesale industry shift from AC to DC isn't happening overnight. The global data center supply chain — from switchgear manufacturers to electricians — is overwhelmingly built around AC infrastructure. Retrofitting existing facilities is expensive and disruptive. Most experts expect **hybrid architectures** to dominate in the near term: AC distribution at the facility level with DC zones serving high-density AI racks.

The standards landscape is also still settling. While Mt. Diablo provides a reference architecture, individual hyperscalers are making their own design choices. Safety codes and regulations for high-voltage DC distribution in commercial buildings vary by jurisdiction and are still catching up to the technology.

There are also practical concerns around **fault protection**. AC circuits benefit from natural zero-crossings that help extinguish arcs during fault conditions. DC doesn't have this property, requiring more sophisticated circuit protection — a solved problem in industries like telecommunications and electric vehicles, but one that adds engineering complexity to data center deployments.

## Edison's Vindication

The irony isn't lost on the industry. Edison argued that DC was the superior choice for local power distribution. He was right — he just didn't have the semiconductor technology to make it practical. Modern silicon carbide (SiC) and gallium nitride (GaN) power devices can now handle the high-voltage DC conversion stages with efficiencies that Edison could only dream of.

For the data center industry, this isn't a philosophical debate. It's an engineering response to the brute-force power demands of AI infrastructure. When every watt matters at megawatt scale, eliminating unnecessary conversion stages isn't an optimization — it's a requirement. The current is shifting, and this time, DC is winning.
