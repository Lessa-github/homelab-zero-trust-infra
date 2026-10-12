# 🛠️ labctl: Controlador CLI de Infraestrutura

O `labctl.sh` é um utilitário de terminal em Bash construído para automatizar a manutenção rotineira do Proxmox VE e dos contêineres de serviços com saída sanitizada.

---

## 💻 Interface de Linha de Comando (`--help`)

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

## 🔒 Princípio de Sanitização de Saída

Quando a flag `-s` ou `--sanitized` é utilizada, qualquer IP de sub-rede doméstica ou endereço WAN é automaticamente mascarado para faixas de documentação corporativa (`172.28.40.x`), garantindo que capturas de tela ou logs de auditoria possam ser compartilhados sem risco de vazamento de informações do ambiente real.
