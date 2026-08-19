# Ubuntu Home Lab

Um laboratório doméstico desenvolvido para estudar infraestrutura, Docker, redes, proxy reverso, monitoramento e administração de servidores Linux.

O objetivo deste projeto é construir um ambiente semelhante ao encontrado em ambientes corporativos, documentando toda a evolução da infraestrutura.

---

## Tecnologias

- Ubuntu Server
- Docker
- Docker Compose
- Nginx Proxy Manager
- Homarr
- Uptime Kuma
- Portainer

---

## Arquitetura

Internet / LAN
│
▼
Nginx Proxy Manager
│
▼
Docker Network (proxy)
├── Homarr
├── Uptime Kuma
└── Portainer

Os serviços se comunicam utilizando o DNS interno do Docker, eliminando dependência de endereços IP entre containers.

---

## Estrutura

docker/
├── compose.yml
├── .env
└── .env.example

docs/

README.md

---

## Serviços

| Serviço | Porta | Domínio |
|----------|------:|---------|
| Homarr | 7575 | homarr.lab.local |
| Uptime Kuma | 3001 | uptime.lab.local |
| Portainer | 9443 | portainer.lab.local |
| Nginx Proxy Manager | 81 | npm.lab.local |

---

## Roadmap

### Fase 1 — Monitoramento

- [x] Resolver DNS do Uptime Kuma
- [x] Utilizar domínios locais nos monitores

### Fase 2 — Organização do Docker

- [x] Migrar para Docker Compose
- [x] Criar rede Docker dedicada
- [x] Comunicação entre containers pelo DNS interno

### Fase 3 — Infraestrutura de Rede

- [ ] Implantar DNS local
- [ ] Remover arquivos hosts
- [ ] Configurar roteador para utilizar o DNS

### Fase 4 — HTTPS

- [ ] Certificados SSL
- [ ] Redirecionamento HTTP → HTTPS

### Fase 5 — Monitoramento

- [ ] Expandir monitoramento da infraestrutura

### Fase 6 — Expansão

- [ ] Grafana
- [ ] Prometheus
- [ ] Gitea
- [ ] Vaultwarden
- [ ] Nextcloud

### Fase 7 — Documentação Final

- [ ] Diagramas
- [ ] Fluxo de DNS
- [ ] Backup
- [ ] Recuperação de desastre

---

## Objetivos de Aprendizagem

- Docker Compose
- Redes Docker
- Reverse Proxy
- DNS
- Monitoramento
- Linux Server
- Infraestrutura como Código

---

## Status

Em desenvolvimento.