# Architectural Mandate: Why WAN Port-Forwarding Was Banned at Inception

**Date:** November 2025  

## The Architectural Flaws of Traditional Port-Forwarding

Most homelab tutorials advise opening ports 80 and 443 on consumer residential routers to expose services. This pattern introduces three critical security liabilities:
1. **Exposure to Mass Scanning Daemons:** Censys, Shodan, and automated botnets scan consumer ISP IP blocks within minutes of route registration.
2. **Dynamic DNS (DDNS) Leakage:** Public DDNS hostnames directly link administrative infrastructure back to the engineer's residential IP address.
3. **Loss of Cryptographic Secrecy in Free Proxy Tunnels (e.g. Cloudflare Tunnels):** Standard free-tier reverse-tunnel providers terminate TLS at their edge, inspecting plaintext traffic and undermining zero-knowledge privacy guarantees.

## The Architectural Mandate: Private WireGuard P2P Mesh

All administrative access and inter-service routing is enforced exclusively through a peer-to-peer encrypted WireGuard mesh, ensuring:
* The residential router maintains **exactly 0 open WAN ports**.
* Each authorized client device possesses a unique Ed25519 asymmetric cryptographic keypair.
* Packets lacking valid cryptographic signatures are dropped silently by kernel routing tables without generating ICMP error replies.
