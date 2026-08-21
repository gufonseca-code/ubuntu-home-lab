# Serviços

## Visão Geral

O Ubuntu Home Lab utiliza serviços executados em containers Docker para fornecer monitoramento, gerenciamento e acesso centralizado ao ambiente.

Toda a infraestrutura é gerenciada através do **Docker Compose**, permitindo que os serviços sejam recriados de forma consistente utilizando um único comando.

```bash
sudo docker compose up -d
```

Todos os containers compartilham uma rede Docker dedicada (`ubuntu-home-lab_proxy`) e utilizam o DNS interno do Docker para comunicação.

---

# Arquitetura dos Serviços

```text
Cliente
      │
      ▼
Nginx Proxy Manager
      │
      ▼
ubuntu-home-lab_proxy
      │
 ├── Homarr
 ├── Uptime Kuma
 ├── Portainer
 └── AdGuard Home
```

---

# Serviços Implantados

| Serviço              | Função                    | Porta Interna | Domínio / Acesso          |
|----------------------|---------------------------|---------------|---------------------------|
| Homarr               | Dashboard do laboratório  | 7575          | homarr.lab.local          |
| Uptime Kuma          | Monitoramento             | 3001          | uptime.lab.local          |
| Portainer            | Gerenciamento Docker      | 9443          | portainer.lab.local       |
| Nginx Proxy Manager  | Reverse Proxy             | 80 / 81 / 443 | npm.lab.local             |
| AdGuard Home         | DNS local                 | 53 / 80       | adguard.lab.local (:3000) |

---

# Docker Compose

Os serviços são definidos em um único arquivo:

```text
docker/compose.yml
```

A utilização do Docker Compose trouxe diversas vantagens:

- implantação reproduzível;
- configuração centralizada;
- gerenciamento simplificado;
- criação automática da rede Docker;
- criação automática dos volumes persistentes.

---

# Rede Docker

Todos os containers participam da rede:

```text
ubuntu-home-lab_proxy
```

A comunicação entre os serviços ocorre utilizando o DNS interno do Docker.

Exemplos:

```text
homarr:7575
uptime-kuma:3001
portainer:9443
```

Essa abordagem elimina a necessidade de utilizar o endereço IP da máquina virtual na comunicação entre containers.

---

# Volumes Persistentes

Os dados dos serviços são armazenados em volumes Docker.

Exemplos:

- homarr-appdata
- uptime-kuma
- npm_data
- npm_letsencrypt
- portainer_data
- adguard_work
- adguard_conf

Isso garante que os dados permaneçam preservados mesmo após recriar os containers.

---

# Reverse Proxy

O acesso aos serviços é realizado através do Nginx Proxy Manager.

Mapeamentos atuais:

| Domínio | Destino |
|----------|----------|
| homarr.lab.local | homarr:7575 |
| uptime.lab.local | uptime-kuma:3001 |
| portainer.lab.local | portainer:9443 |

O proxy encaminha as requisições utilizando o DNS interno da rede Docker.

---

# AdGuard Home

O AdGuard Home atua como servidor DNS local da rede do laboratório.

## Função

- Resolver os domínios `*.lab.local` para o IP da máquina virtual (`192.168.0.50`)
- Eliminar a dependência do arquivo `hosts`
- Permitir que qualquer dispositivo da rede resolva os serviços do laboratório automaticamente

## Implantação

O serviço é executado em container Docker e gerenciado pelo Docker Compose.

Portas publicadas:

| Porta do host | Destino no container | Uso                    |
|---------------|----------------------|------------------------|
| 53/tcp        | 53/tcp               | DNS                    |
| 53/udp        | 53/udp               | DNS                    |
| 3000/tcp      | 80/tcp               | Interface web          |

## Observação sobre a porta da interface web

Após o setup inicial, o AdGuard Home passa a servir a interface administrativa na porta **80** do container.

Como a porta 80 do host já é utilizada pelo Nginx Proxy Manager, a interface web foi mapeada para a porta **3000** do host:

```text
3000:80
```

## Volumes

- `adguard_work` → dados de trabalho
- `adguard_conf` → configuração

## Estado

- ✅ Implantado
- ✅ DNS Rewrites configurados
- ✅ Dependência do arquivo `hosts` removida

---

# Variáveis de Ambiente

As configurações sensíveis são armazenadas em um arquivo `.env`.

O repositório disponibiliza apenas:

```text
.env.example
```

Cada ambiente deve possuir seu próprio arquivo `.env`.

---

# Histórico da Implantação

A primeira versão do laboratório utilizava containers criados individualmente com `docker run`.

Com a evolução da infraestrutura, todos os serviços foram migrados para Docker Compose, tornando a implantação mais organizada, reproduzível e fácil de manter.

Essa migração também permitiu:

- criação automática da rede Docker;
- utilização do DNS interno do Docker;
- simplificação da manutenção;
- centralização da configuração da infraestrutura.

Posteriormente foi adicionado o AdGuard Home como servidor DNS local, eliminando a dependência do arquivo `hosts` e do `extra_hosts` no Uptime Kuma.

---

# Estado Atual

- ✅ Docker Compose
- ✅ Volumes persistentes
- ✅ Rede Docker dedicada
- ✅ Reverse Proxy
- ✅ DNS interno do Docker
- ✅ Comunicação entre containers
- ✅ DNS local (AdGuard Home)
