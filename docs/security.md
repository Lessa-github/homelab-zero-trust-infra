# 🛡️ Segurança & Hardening de Infraestrutura

A postura defensiva do laboratório foi desenhada seguindo as diretrizes do **NIST Cybersecurity Framework (CSF 2.0)** e práticas de **Zero-Trust Architecture (ZTA)**.

---

## 🔑 1. Authentik SSO + Forward-Auth com WebAuthn / Passkeys

Em vez de confiar em senhas fracas ou credenciais estáticas espalhadas por cada aplicação, a camada de acesso foi unificada:

```mermaid
sequenceDiagram
    autonumber
    participant Client as Navegador do Usuário
    participant NPM as Nginx Proxy Manager (CT 103)
    participant Auth as Authentik SSO (CT 111)
    participant App as Serviço Interno (ex: Komodo)

    Client->>NPM: Acessa https://komodo.mesh.internal
    NPM->>Auth: Valida sessão via Forward-Auth (/outpost.goauthentik.io/auth/nginx)
    alt Sessão Não Autenticada
        Auth-->>Client: Redireciona para tela de login do Authentik
        Client->>Auth: Desafio biométrico FIDO2 (WebAuthn Passkey)
        Auth-->>Client: Autenticação bem-sucedida + Cookie de Sessão
    end
    NPM->>App: Encaminha tráfego com Headers Sanitizados (X-authentik-username)
    App-->>Client: Responde painel autenticado
```

### Vantagens de Segurança:
* **Fim das Forças-Brutas:** Zero formulários de login expostos diretamente; o Nginx bloqueia antes mesmo do tráfego tocar a aplicação alvo.
* **MFA Físico/Biométrico:** O Authentik exige toque de chave de segurança ou biometria (TouchID/Windows Hello).
* **Auditoria Centralizada:** Cada acesso gera evento auditável com geolocalização e identidade.

---

## 🌐 2. Wildcard TLS sem Exposição via DNS Challenge

Certificados SSL válidos (`Let's Encrypt`) são gerados automaticamente pelo Certbot integrado no Nginx Proxy Manager utilizando **DNS-01 Challenge**:
* Nenhuma porta 80 precisa ser aberta para verificação de HTTP.
* A validação ocorre criando um registro TXT temporário no provedor de DNS.
* Todos os serviços internos usam TLS criptografado ponto a ponto com cabeçalhos HSTS rígidos (`Strict-Transport-Security: max-age=31536000; includeSubDomains`).
