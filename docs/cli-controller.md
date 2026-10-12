# 🛠️ labctl: Infrastructure CLI Controller

`labctl.sh` is an idempotent Bash terminal utility developed to automate routine Proxmox VE hypervisor maintenance and container lifecycle tasks with fully sanitized outputs.

---

## 💻 Command Line Interface (`--help`)

```text
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

## 🔒 Output Sanitization by Design

When the `-s` or `--sanitized` flag is passed, any internal domestic IP addresses, NetBird peer IDs, or hardware identifiers are dynamically mapped to standardized corporate documentation ranges (`172.28.40.x`). This ensures terminal outputs, audit logs, and diagnostic screenshots can be published publicly without exposing production infrastructure coordinates.
