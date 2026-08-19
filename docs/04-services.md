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
 └── Portainer
```

---

# Serviços Implantados

| Serviço | Função | Porta Interna | Domínio |
|----------|---------|--------------:|---------|
| Homarr | Dashboard do laboratório | 7575 | homarr.lab.local |
| Uptime Kuma | Monitoramento | 3001 | uptime.lab.local |
| Portainer | Gerenciamento Docker | 9443 | portainer.lab.local |
| Nginx Proxy Manager | Reverse Proxy | 80 / 81 / 443 | npm.lab.local |

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

---

# Estado Atual

- ✅ Docker Compose
- ✅ Volumes persistentes
- ✅ Rede Docker dedicada
- ✅ Reverse Proxy
- ✅ DNS interno do Docker
- ✅ Comunicação entre containers