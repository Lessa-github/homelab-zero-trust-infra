# Por que o Port-Forwarding foi Banido na Concepção do Laboratório

**Data:** Novembro de 2025  

## O Problema do Port-Forwarding Tradicional
A maioria dos tutoriais de homelab recomenda abrir as portas 80 e 443 no roteador residencial para expor serviços. Isso apresenta três falhas inaceitáveis:
1. **Exposição a Bots e Scanners Automáticos:** Shodan, Censys e bots chineses/russos varrem a faixa do ISP em minutos.
2. **Dependência de DDNS Público:** Vaza o IP residencial do administrador.
3. **Quebra de Privacidade em Soluções Gratuitas (Cloudflare Tunnels):** Ao usar Cloudflare Tunnels, o tráfego TLS é descriptografado nos servidores da Cloudflare, quebrando o sigilo absoluto dos dados corporativos.

## A Decisão Arquitetural: Malha WireGuard Privada
Todo acesso administrativo remoto será feito via malha criptografada ponto-a-ponto com WireGuard, onde:
* O roteador doméstico tem **0 portas abertas**.
* Cada dispositivo autorizado possui sua chave assimétrica Ed25519.
* Se um pacote não for assinado criptograficamente, o firewall simplesmente descarta (drop silencioso sem resposta ICMP).
