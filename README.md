# 🏛️ Zero-Trust High-Density Homelab Infrastructure

[![Documentation](https://img.shields.io/badge/Docs-Docsify_Live-06b6d4?style=for-the-badge&logo=googledocs&logoColor=white)](https://lessa-github.github.io/homelab-zero-trust-infra/)
[![Proxmox](https://img.shields.io/badge/Hypervisor-Proxmox_VE_8.x-E57000?style=for-the-badge&logo=proxmox&logoColor=white)](https://proxmox.com)
[![Architecture](https://img.shields.io/badge/Security-Zero--Trust_Mesh-10b981?style=for-the-badge&logo=wireguard&logoColor=white)](https://netbird.io)
[![Status](https://img.shields.io/badge/Production-24%2F7_Active-blue?style=for-the-badge)](https://lessa-github.github.io/gabriel-portfolio/)

Official repository documenting the production engineering, network architecture, automated orchestration, and SRE hardening of my **Zero-Trust Homelab Infrastructure** running on **Proxmox Virtual Environment (PVE)**.

Designed to operate **24/7 with high service density (12 persistent LXCs running concurrently in only 32 GB of RAM), under 25W power draw, and ZERO open WAN ports**.

---

## 🧭 Engineering Timeline (2025 – Present)

* **January/2025 to August/2026 (College Coursework & Theoretical Foundation):**
  Network architecture studies, hardware capacity planning (TDP targets of 15W to 35W, RAM allocation matrix), and topology simulations in Cisco Packet Tracer with documented corporate subnets (`172.28.40.0/24`). Established the strict architectural rule banning residential WAN port-forwarding.
* **August/2026 (Completed College Classes & Hardware Deployment):**
  Classes concluded; physical host assembly and Proxmox VE 8 initialization, NVMe ZFS storage partitioning, and Linux multi-bridge configuration (`vmbr0` and `vmbr1`).
* **September/2026 to October/2026 (Full Hands-on & Production Hardening):**
  NetBird WireGuard mesh deployment, AdGuard Home DNS with optimistic caching (0.2ms latency), Nginx Proxy Manager wildcard SSL via DNS Challenge, Authentik SSO with FIDO2 / WebAuthn passkeys, and declarative container GitOps migration with Komodo Orchestrator.

---

## 📐 Zero-Trust Network Topology

```mermaid
flowchart TD
    subgraph EXTERNAL_WAN ["External Boundary (Zero Open WAN Ports)"]
        AdminDevice["Administrator Laptop / Mobile Device<br>(WireGuard Peer: 10.240.50.2)"]
    end

    subgraph OVERLAY_MESH ["Encrypted WireGuard Mesh Overlay (NetBird)"]
        Tunnel["P2P Encrypted Tunnel (ChaCha20-Poly1305)"]
    end

    subgraph HYPERVISOR_NODE ["Proxmox VE Hypervisor Host (10.240.50.1)"]
        HostKernel["Linux Kernel Routing & cgroups v2 Governor"]
    end

    AdminDevice <-->|Cryptographic Handshake| Tunnel
    Tunnel <--> HostKernel

    HostKernel -->|"vmbr0 Production Bridge"| NPM["Nginx Proxy Manager (:443)<br>IP: 172.28.40.103"]
    HostKernel -.->|"vmbr1 Air-Gapped Bridge"| SANDBOX_HUB["Isolated L2 Virtual Switch<br>(No Internet Gateway)"]

    subgraph PROD_VLAN ["Production Service Zone (172.28.40.0/24)"]
        direction TB
        NPM["Nginx Proxy Manager (:443)<br>IP: 172.28.40.103"]
        AUTH["Authentik SSO (Passkeys / CT 111)"]
        ADG["AdGuard Home (0.2ms Cache / CT 112)"]
        KOM["Komodo GitOps Orchestrator (CT 109)"]
        OMNI["OmniRoute AI Gateway (CT 114)"]
        NPM --> AUTH & ADG
        AUTH & ADG --> KOM & OMNI
    end

    subgraph AIRGAPPED_VLAN ["Air-Gapped Sandbox Zone (192.168.199.0/24)"]
        VELOC["Velociraptor DFIR (67 MB RAM / CT 123)"]
        INET["INetSim Fake Internet (CT 202)"]
        SANDBOX_HUB -.-> VELOC & INET
    end
```

---

## 📊 24/7 Production Services Matrix (12 LXCs)

| CT ID | Hostname | Documentation IP | RAM Allocation | Primary Role | Technology Stack |
| :---: | :--- | :---: | :---: | :--- | :--- |
| **103** | `nginx-proxy-manager` | `172.28.40.103` | 180 MB | Wildcard SSL Reverse Proxy & Forward-Auth | OpenResty, Certbot DNS-01 |
| **104** | `vaultwarden` | `172.28.40.104` | 45 MB | Encrypted Password Vault | Rust Server, SQLite, WebSockets |
| **105** | `matrix-synapse` | `172.28.40.105` | 520 MB | Federated E2EE Messaging & LiveKit WebRTC | Python Synapse, LiveKit Go |
| **106** | `homepage-dashboard` | `172.28.40.106` | 110 MB | Real-time Hypervisor Telemetry Portal | Next.js SSR, Proxmox API |
| **107** | `uptime-kuma` | `172.28.40.107` | 160 MB | Continuous Health Monitoring (51 Monitors) | Node.js, SQLite, Push Notifications |
| **108** | `dozzle-logs` | `172.28.40.108` | 95 MB | Read-Only Streaming Logs & Beszel Hub | Go Binary, Beszel Telemetry |
| **109** | `komodo` | `172.28.40.109` | 180 MB | Declarative GitOps Container Orchestration | Rust Core, Periphery Agent |
| **111** | `authentik-sso` | `172.28.40.111` | 980 MB | Central Identity Provider, Passkeys & OIDC | Python, PostgreSQL 16, Redis |
| **112** | `adguard-home` | `172.28.40.112` | 48 MB | Hardened DNS Resolver (0.2ms Cache) | Go Static Binary, DoH Upstreams |
| **113** | `simplelogin-alias` | `172.28.40.113` | 310 MB | Anti-Spam Email Aliasing & Forwarding | Flask, PostgreSQL, Postfix Engine |
| **114** | `omniroute-ai` | `172.28.40.114` | 195 MB | Multi-Provider AI API Gateway & Compression | TypeScript, Redis Cache |
| **115** | `ai-workspace` | `172.28.40.115` | 850 MB | Hermes Autonomous Agent, MCP & SearXNG | Python 3.11, Debian 12 LXC |

---

## 🛠️ Infrastructure CLI Controller: labctl.sh

```text
$ ./cli-tools/labctl.sh --help
===================================================================
  LABCTL - Zero-Trust Homelab Infrastructure CLI Controller v1.4
===================================================================
Usage: ./labctl.sh [COMMAND] [OPTIONS]

Commands:
  status                   Display status of all managed LXC containers and host load
  backup <target>          Execute automated backup with Zero-Knowledge encryption
  dns-health               Validate AdGuard Home cache latency and upstream resolution
  netbird-mesh             Audit active WireGuard peers and subnet routes
  tune-kernel              Apply sysctl performance tunings and verify cgroups v2

Options:
  -v, --verbose            Enable detailed debug output
  -s, --sanitized          Force JSON output with fully sanitized network addresses
  -h, --help               Show this help message and exit
```

---

## 🌐 Live Technical Documentation

Explore the complete single-page interactive whitepaper with smooth section scrolling:

👉 **[https://lessa-github.github.io/homelab-zero-trust-infra/](https://lessa-github.github.io/homelab-zero-trust-infra/)**
