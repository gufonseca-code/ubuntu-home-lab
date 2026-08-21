# Ubuntu Home Lab – Visão Geral

## Objetivo

Este projeto documenta a construção de um laboratório doméstico (Home Lab) voltado ao estudo de infraestrutura de TI, virtualização, administração de servidores Linux, redes, containers e monitoramento.

O ambiente foi desenvolvido com foco em aprendizado prático e na aplicação de conceitos utilizados em ambientes corporativos, permitindo experimentar tecnologias em um ambiente controlado e reproduzível.

Além da implantação dos serviços, todo o processo de planejamento, configuração, migração e documentação é registrado para demonstrar a evolução da infraestrutura.

---

# Objetivos de Aprendizagem

O laboratório foi criado para desenvolver experiência prática em:

- Administração de servidores Linux
- Virtualização com Hyper-V
- Docker e Docker Compose
- Redes Docker
- Reverse Proxy
- DNS
- Monitoramento
- Infraestrutura como Código (IaC)
- Documentação técnica
- Troubleshooting

---

# Arquitetura Atual

## Host

- Windows 11 Pro
- Hyper-V

## Máquina Virtual

- Ubuntu Server
- IP estático: `192.168.0.50`

## Plataforma de Containers

- Docker
- Docker Compose

## Rede Docker

- `ubuntu-home-lab_proxy`

Todos os containers compartilham uma rede Docker dedicada e utilizam o DNS interno do Docker para comunicação entre si.

## DNS Local

- AdGuard Home (resolução de `*.lab.local` → `192.168.0.50`)

---

# Serviços

| Serviço | Função |
|----------|--------|
| Homarr | Dashboard central do laboratório |
| Uptime Kuma | Monitoramento dos serviços |
| Portainer | Gerenciamento dos containers |
| Nginx Proxy Manager | Reverse Proxy para acesso por domínios locais |
| AdGuard Home | Servidor DNS local |

---

# Estado Atual da Infraestrutura

Atualmente o laboratório possui:

- Docker Compose para gerenciamento da infraestrutura
- Rede Docker dedicada
- Comunicação entre containers utilizando DNS interno do Docker
- Reverse Proxy com Nginx Proxy Manager
- Monitoramento utilizando Uptime Kuma
- Dashboard centralizado com Homarr
- DNS local com AdGuard Home
- Volumes persistentes para todos os serviços

Toda a infraestrutura pode ser recriada utilizando um único comando:

```bash
docker compose up -d
```

---

# Marcos do Projeto

## Fase 1 — Monitoramento

- ✅ Implantação do Uptime Kuma
- ✅ Resolução do problema de DNS do container
- ✅ Migração dos monitores para utilização dos domínios locais

---

## Fase 2 — Organização da Infraestrutura

- ✅ Migração de todos os serviços para Docker Compose
- ✅ Criação da rede Docker dedicada (`ubuntu-home-lab_proxy`)
- ✅ Comunicação entre containers utilizando DNS interno do Docker
- ✅ Migração do Nginx Proxy Manager para utilizar nomes dos containers

---

## Fase 3 — Infraestrutura de Rede

- ✅ Implantação de DNS local (AdGuard Home)
- ✅ Remoção das entradas do arquivo `hosts`
- ✅ Remoção do `extra_hosts` do Uptime Kuma

---

## Próximos Marcos

### Fase 4 — HTTPS

- Certificados SSL
- Redirecionamento HTTP → HTTPS
- Renovação automática

### Fase 5 — Monitoramento

- Monitoramento do host Ubuntu
- Monitoramento de Docker
- Monitoramento da rede
- Monitoramento do gateway

### Fase 6 — Expansão

- Grafana
- Prometheus
- Gitea
- Vaultwarden
- Nextcloud
- Paperless-ngx

### Fase 7 — Documentação Final

- Diagramas da arquitetura
- Fluxo de DNS
- Fluxo do Reverse Proxy
- Estratégia de backup
- Procedimento de recuperação do ambiente

---

# Estrutura da Documentação

| Documento | Descrição |
|------------|-----------|
| 01-overview.md | Visão geral do projeto |
| 02-hardware-vm.md | Infraestrutura física e virtual |
| 03-network.md | Configuração de rede |
| 04-services.md | Serviços implantados |
| 08-lessons-learned.md | Problemas encontrados e soluções |

---

# Situação Atual

```
[x] Infraestrutura virtualizada
[x] Docker
[x] Docker Compose
[x] Rede Docker dedicada
[x] Reverse Proxy
[x] Monitoramento
[x] DNS Local
[ ] HTTPS
[ ] Monitoramento avançado
[ ] Expansão dos serviços
```
