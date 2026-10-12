# 🧩 Matriz de Serviços em Produção (12 CTs 24/7)

A infraestrutura foi desenhada para priorizar eficiência de recursos, estabilidade a longo prazo e facilidade de manutenção.

---

## 📋 Inventário Detalhado dos Contêineres LXC

```
┌─────────┬──────────────────────┬───────────────┬────────────┬───────────────────────────────────────────┐
│ CT ID   │ Hostname             │ IP Sintético  │ RAM (MB)   │ Stack de Produção                         │
├─────────┼──────────────────────┼───────────────┼────────────┼───────────────────────────────────────────┤
│ CT 103  │ nginx-proxy-manager  │ 172.28.40.103 │ 180 MB     │ Nginx, OpenResty, Certbot DNS-Challenge   │
│ CT 104  │ vaultwarden          │ 172.28.40.104 │ 45 MB      │ Vaultwarden Rust, SQLite, WebSockets      │
│ CT 105  │ matrix-synapse       │ 172.28.40.105 │ 520 MB     │ Synapse Python, LiveKit WebRTC Go         │
│ CT 106  │ homepage-dashboard   │ 172.28.40.106 │ 110 MB     │ Next.js SSR Dashboard, Proxmox API        │
│ CT 107  │ uptime-kuma          │ 172.28.40.107 │ 160 MB     │ Uptime Kuma v2.5, SQLite, 51 Monitores    │
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

## 🛡️ Gestão de Ciclo de Vida: Debian 12 Unprivileged LXC

Todos os contêineres rodam com os seguintes padrões rígidos de segurança:
1. **Unprivileged Mode (`unprivileged: 1`):** O usuário `root` dentro do container é mapeado para um UID não privilegiado (`100000+`) no host. Mesmo que um invasor comprometa o container, ele não obtém privilégios no hypervisor.
2. **Isolamento de Namespaces:** Separação estrita de rede, IPC, PID e montagens de arquivos.
3. **Persistência em Volumes Montados:** Dados estaduais e bancos de dados (PostgreSQL, SQLite) gravam em diretórios dedicados com permissões restritas.
