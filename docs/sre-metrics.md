# 📈 SRE & Métricas de Telemetria

Uma das maiores armadilhas em ambientes de Homelab é a criação de **loops de telemetria**: ferramentas de monitoramento pesadas que consomem mais CPU e RAM do que as próprias aplicações gerenciadas.

---

## ⚡ A Arquitetura de Observabilidade Frugal

Para monitorar mais de 12 serviços sem comprometer o hardware, estruturamos uma stack leve:

1. **Beszel Hub & Agent (CT 108):**
   * Agente em Go compilado estaticamente consumindo **menos de 15 MB de RAM** por nó.
   * Telemetria de CPU, uso de RAM, tráfego de interface de rede e integridade de disco NVMe (S.M.A.R.T.).
2. **Dozzle Log Viewer (CT 108):**
   * Visualizador de logs em streaming de alta performance em modo estritamente **read-only**.
   * Não grava índices redundantes de banco de dados no disco; lê diretamente dos sockets de log do Docker/LXC.
3. **Uptime Kuma v2 (CT 107):**
   * 51 monitores ativos com verificações TCP, HTTP(s), DNS e certificados TLS.
   * Notificações via webhooks autenticados.

---

## ⚙️ Calibragem de Kernel Linux (`sysctl.conf`)

No host Proxmox VE, as seguintes diretrizes de SRE foram aplicadas:

```ini
# Mitigação de exaustão de descritores de sockets em alta densidade
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 8192

# Otimização de reutilização de sockets TCP TIME_WAIT
net.ipv4.tcp_tw_reuse = 1

# Limites de governança de memória virtual (evita travamentos de swap)
vm.swappiness = 10
vm.dirty_ratio = 15
vm.dirty_background_ratio = 5

# Prevenção de loop de arp em interfaces bridge
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.all.arp_announce = 2
```
