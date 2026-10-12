# 🧩 24/7 Production Services Matrix (12 LXC Containers)

The infrastructure architecture prioritizes physical resource frugality, long-term operational stability, and declarative maintenance.

---

## 📋 Comprehensive LXC Container Inventory

```
┌─────────┬──────────────────────┬───────────────┬────────────┬───────────────────────────────────────────┐
│ CT ID   │ Hostname             │ Synthetic IP  │ RAM (MB)   │ Production Tech Stack                     │
├─────────┼──────────────────────┼───────────────┼────────────┼───────────────────────────────────────────┤
│ CT 103  │ nginx-proxy-manager  │ 172.28.40.103 │ 180 MB     │ Nginx, OpenResty, Certbot DNS-Challenge   │
│ CT 104  │ vaultwarden          │ 172.28.40.104 │ 45 MB      │ Vaultwarden Rust, SQLite, WebSockets      │
│ CT 105  │ matrix-synapse       │ 172.28.40.105 │ 520 MB     │ Synapse Python, LiveKit WebRTC Go         │
│ CT 106  │ homepage-dashboard   │ 172.28.40.106 │ 110 MB     │ Next.js SSR Dashboard, Proxmox API        │
│ CT 107  │ uptime-kuma          │ 172.28.40.107 │ 160 MB     │ Uptime Kuma v2.5, SQLite, 51 Monitors     │
│ CT 108  │ dozzle-logs          │ 172.28.40.108 │ 95 MB      │ Dozzle Go, Beszel Hub Hardware Telemetry  │
│ CT 109  │ komodo               │ 172.28.40.109 │ 180 MB     │ Komodo Core Rust, Periphery Agent         │
│ CT 111  │ authentik-sso        │ 172.28.40.111 │ 980 MB     │ Authentik Server, Worker, Postgres, Redis │
│ CT 112  │ adguard-home         │ 172.28.40.112 │ 48 MB      │ AdGuard Home Go, DoH Upstreams            │
│ CT 113  │ simplelogin-alias    │ 172.28.40.113 │ 310 MB     │ SimpleLogin Python/Flask, Postfix Engine  │
│ CT 114  │ omniroute-ai         │ 172.28.40.114 │ 195 MB     │ OmniRoute Gateway, Redis Cache            │
│ CT 115  │ ai-workspace         │ 172.28.40.115 │ 850 MB     │ Hermes Agent, MCP Wrappers, SearXNG       │
└─────────┴──────────────────────┴───────────────┴────────────┴───────────────────────────────────────────┘
```

---

## 🛡️ Container Lifecycle Governance: Debian 12 Unprivileged LXC

All production containers adhere to strict containerization security standards:
1. **Unprivileged Mode (`unprivileged: 1`):** The `root` user within the container namespace maps to an unprivileged high UID (`100000+`) on the Proxmox host. Even in a container breakout scenario, the attacker holds zero privileges on the hypervisor kernel.
2. **Namespace Isolation:** Strict boundary enforcement across network, IPC, PID, UTS, and mount namespaces.
3. **Dedicated Volume Mounts:** State databases (PostgreSQL, SQLite) write to isolated ZFS subvolumes with strict POSIX permissions.
