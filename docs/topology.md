# 📐 Network Topology & Trust Zones

Our network architecture categorically rejects the legacy perimeter model based on opening residential router ports (*WAN port-forwarding*).

---

## 🔒 The Zero-Trust Mesh Overlay

Instead of exposing ports 80/443 to the public internet, all nodes join a **peer-to-peer WireGuard encrypted overlay network managed via NetBird**:

```mermaid
flowchart TD
    subgraph EXTERNAL ["Remote Administrator Ingress"]
        AdminLaptop["Admin Laptop (WireGuard Peer: 10.240.50.2)"]
    end

    subgraph WIREGUARD_OVERLAY ["P2P Encrypted Mesh (ChaCha20-Poly1305)"]
        Tunnel["Direct P2P Encrypted WireGuard Tunnel"]
        AdminLaptop <-->|Encrypted Tunnel| Tunnel
    end

    subgraph PROXMOX_HOST ["Central Proxmox VE Host (10.240.50.1)"]
        Tunnel <--> PveHost["Host Kernel & Linux Bridge Routing"]
    end

    subgraph PRODUCTION_ZONE ["Production Service Zone (172.28.40.0/24)"]
        PveHost --> Bridge0["Linux Bridge vmbr0"]
        Bridge0 --> NPM["Nginx Proxy Manager (172.28.40.103)"]
        Bridge0 --> AUTH["Authentik SSO (172.28.40.111)"]
        Bridge0 --> ADG["AdGuard Home DNS (172.28.40.112)"]
        Bridge0 --> KOM["Komodo GitOps (172.28.40.109)"]
        Bridge0 --> APPS["Services & Gateways (172.28.40.104 - 115)"]
    end

    subgraph AIRGAPPED_ZONE ["Air-Gapped Detonation Zone (192.168.199.0/24)"]
        PveHost -.->|Isolated Bridge Without Gateway| Bridge1["Linux Bridge vmbr1"]
        Bridge1 --> MALWARE["Target VMs & Sandboxes (192.168.199.205)"]
        Bridge1 --> INET["INetSim Fake Services (192.168.199.202)"]
    end
```

---

## 🌐 Documented Synthetic Subnet Allocation

| Subnet CIDR | Functional Role | Isolation & Routing Policy |
| :--- | :--- | :--- |
| **`172.28.40.0/24`** | Production Services (`vmbr0`) | Accessible strictly over authenticated WireGuard tunnels; zero external exposure. |
| **`192.168.199.0/24`** | Air-Gapped Sandbox (`vmbr1`) | Complete air-gap; no default route or gateway; traffic contained at Layer 2. |
| **`10.240.50.0/24`** | NetBird Mesh Overlay | Direct peer-to-peer tunnels authenticated with Ed25519 public keys. |

---

## ⚡ 0.2ms DNS Resolution Architecture

Internal name resolution is handled by **AdGuard Home (CT 112)** running as a static Go binary:
1. **Optimistic Caching (`cache_optimistic: true`):** Serves cached queries instantly with 0.2ms latency and revalidates records upstream in the background.
2. **Encrypted Upstream Resolvers:** External queries route exclusively over **DNS-over-HTTPS (DoH)** with automatic failover across redundant privacy resolvers (`Quad9`, `Cloudflare`, `AdGuard`).
3. **Local Rewrites:** Internal domain queries resolve directly to Nginx Proxy Manager (`172.28.40.103`), never leaking internal infrastructure queries to the public internet.
