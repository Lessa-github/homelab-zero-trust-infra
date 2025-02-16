# Análise de Dimensionamento de Hardware e Orçamento Térmico (TDP)

**Data:** Fevereiro de 2025  
**Contexto:** Planejamento inicial para aquisição de nó bare-metal em Vancouver  
**Objetivo:** Rodar hypervisor Proxmox VE 24/7 com consumo elétrico mínimo e máxima densidade de containers.

## 1. O Dilema: Servidor Rack Usado vs. Mini-PC Eficiente

Ao planejar o laboratório em 2025, comparei três abordagens de hardware:

| Categoria | Hardware Exemplo | RAM | Consumo Típico (Idle / Load) | Ruído / Espaço | Custo de Energia Anual |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Servidor Rack 1U/2U** | Dell PowerEdge R620 (Xeon E5) | 64 GB DDR3 | 120W / 240W | Muito alto (turbina) | ~$180 CAD |
| **Desktop Torre ATX** | Core i5 8th Gen Desktop | 32 GB DDR4 | 45W / 95W | Médio | ~$70 CAD |
| **Mini-PC Dedicado** | Intel Core i3 / Core 12th Mini | 32 GB DDR4 | **12W / 28W** | **Inaudível (<22 dB)** | **~$20 CAD** |

### Conclusão de Engenharia
Em ambiente residencial, servidores enterprise descartados são armadilhas financeiras. O custo elétrico acumulado e o ruído inviabilizam a operação 24/7. A escolha por um **Mini-PC com 32 GB de RAM DDR4 e armazenamento NVMe de alta velocidade** oferece a melhor relação IOPS/Watt, permitindo rodar mais de 15 containers LXC simultâneos com 25 GB de folga em repouso.
