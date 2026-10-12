# 📈 SRE & Telemetry Architecture

One of the most frequent traps in homelab engineering is falling into **telemetry feedback loops**: deploying heavyweight monitoring tools that consume more CPU cycles and RAM than the production workloads they observe.

---

## ⚡ The Frugal Observability Architecture

To monitor 12+ production services without taxing hardware limits, we established a lightweight, resource-bounded stack:

1. **Beszel Hub & Agent (CT 108):**
   * Statically compiled Go agent consuming **under 15 MB of physical RAM** per monitored host.
   * Collects CPU utilization, memory quotas, network interface throughput, and S.M.A.R.T. NVMe disk wear telemetry.
2. **Dozzle Log Viewer (CT 108):**
   * High-throughput log streaming daemon operating in strictly **read-only** mode.
   * Stores zero redundant database indices to disk; streams directly from LXC and Docker log sockets.
3. **Uptime Kuma v2 (CT 107):**
   * 51 continuous health probes executing TCP socket tests, HTTP(S) response status assertions, DNS resolution timings, and TLS certificate expiration monitoring.
   * Alerts routed through authenticated webhooks.

---

## ⚙️ Linux Kernel Tuning & SRE Calibration (`sysctl.conf`)

On the Proxmox VE host, the following kernel governance parameters were tuned to eliminate socket starvation and memory thrashing:

```ini
# Socket descriptor starvation prevention under high container concurrency
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 8192

# TCP TIME_WAIT socket reuse optimization
net.ipv4.tcp_tw_reuse = 1

# Virtual memory governance quotas (eliminates swap thrashing)
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

# ARP flux prevention across multi-bridge interfaces
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.all.arp_announce = 2
```
