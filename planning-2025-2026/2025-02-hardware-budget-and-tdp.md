# Hardware Sizing Analysis & Thermal Design Power (TDP) Budget

**Date:** February 2025  
**Context:** Initial planning phase for bare-metal node acquisition in Vancouver  
**Objective:** Operate a 24/7 Proxmox VE hypervisor with minimal electricity consumption and maximum LXC container density.

## 1. The Architectural Dilemma: Used Enterprise Rack Server vs. Energy-Efficient Mini-PC

During 2025 capacity planning, three hardware form factors were benchmarked:

| Category | Representative Hardware | RAM | Typical Power Draw (Idle / Load) | Noise / Footprint | Estimated Annual Power Cost |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1U/2U Rack Server** | Dell PowerEdge R620 (Xeon E5) | 64 GB DDR3 | 120W / 240W | Extreme turbine noise (>55 dB) | ~$180 CAD |
| **ATX Desktop Tower** | Core i5 8th Gen Desktop | 32 GB DDR4 | 45W / 95W | Moderate footprint | ~$70 CAD |
| **Dedicated Mini-PC** | Intel 12th Gen Mini-PC | 32 GB DDR4 | **12W / 28W** | **Near-silent (<22 dB)** | **~$20 CAD** |

### Engineering Verdict
In residential and urban apartment deployments, surplus enterprise rack servers represent financial and operational liabilities. Cumulative power expenses, thermal heat dissipation, and acoustic noise prevent sustainable 24/7 operation. Selecting an **energy-efficient Mini-PC paired with 32 GB of DDR4 RAM and high-endurance NVMe storage** delivers the optimal IOPS-per-watt efficiency, comfortably accommodating 15+ concurrent LXC containers with 25 GB of idle RAM headroom.
