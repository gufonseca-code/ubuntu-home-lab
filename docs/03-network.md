# Rede e Conectividade

## Visão Geral

A infraestrutura de rede foi projetada para fornecer acesso estável aos serviços do laboratório tanto pela rede local quanto pela rede interna do Docker.

Atualmente, o ambiente utiliza:

- Hyper-V com External Switch
- Endereço IP estático para a máquina virtual
- Docker Compose
- Rede Docker dedicada
- Nginx Proxy Manager como Reverse Proxy
- Comunicação entre containers utilizando o DNS interno do Docker
- AdGuard Home como servidor DNS local

---

# Evolução da Rede

## Situação inicial

A VM foi criada utilizando o **Default Switch** do Hyper-V.

Esse switch utiliza NAT e DHCP interno, fazendo com que o endereço IP fosse alterado a cada reinicialização.

Como consequência, era necessário atualizar manualmente os endereços utilizados pelos serviços.

---

## Migração para External Switch

Foi criado um **External Switch** no Hyper-V para colocar a máquina virtual na mesma rede local do computador host.

### Configuração realizada

1. Abertura do Gerenciador de Switch Virtual
2. Criação de um switch do tipo **Externo**
3. Seleção da interface física (Wi-Fi)
4. Compartilhamento do adaptador com o host
5. Associação da VM ao novo switch

Após essa alteração, a VM passou a utilizar um endereço IP da rede local.

---

# Configuração de Rede

## Endereço IP

| Item | Valor |
|------|-------|
| Endereço IP | 192.168.0.50 |
| Gateway | 192.168.0.1 |
| Interface | eth0 |

A configuração foi realizada através do Netplan.

Arquivo:

```text
/etc/netplan/00-installer-config.yaml
```

Após alterações:

```bash
sudo netplan apply
```

---

# Resolução de Nomes

## Situação anterior

Durante o desenvolvimento inicial do laboratório, a resolução dos domínios locais era feita através do arquivo `hosts` em cada cliente.

Exemplo:

```text
192.168.0.50 homarr.lab.local
192.168.0.50 uptime.lab.local
192.168.0.50 portainer.lab.local
192.168.0.50 npm.lab.local
```

Essa abordagem permitiu validar o funcionamento do Reverse Proxy antes da implantação de um servidor DNS dedicado. Também foi necessário utilizar `extra_hosts` no container do Uptime Kuma para que ele conseguisse resolver os domínios locais.

## Situação atual — DNS Local

Foi implantado o **AdGuard Home** como servidor DNS local da rede.

Todos os domínios `*.lab.local` são resolvidos para o endereço da máquina virtual:

```text
192.168.0.50
```

### Registros configurados (DNS Rewrites)

| Domínio                 | Resposta       |
|-------------------------|----------------|
| homarr.lab.local        | 192.168.0.50   |
| uptime.lab.local        | 192.168.0.50   |
| portainer.lab.local     | 192.168.0.50   |
| npm.lab.local           | 192.168.0.50   |
| adguard.lab.local       | 192.168.0.50   |

Com essa configuração:

- Não é mais necessário editar o arquivo `hosts` nos clientes
- O bloco `extra_hosts` do Uptime Kuma foi removido
- Qualquer dispositivo que utilize o AdGuard Home como DNS passa a resolver os domínios do laboratório automaticamente

### Acesso à interface do AdGuard Home

- Endereço: `http://192.168.0.50:3000`
- (Opcional) Pode ser publicado via Nginx Proxy Manager em `adguard.lab.local`

### Configuração de clientes

Os dispositivos da rede devem utilizar o AdGuard Home como servidor DNS:

```text
DNS primário: 192.168.0.50
```

Isso pode ser feito:

- Manualmente em cada dispositivo, ou
- Centralmente no roteador (DHCP), apontando o DNS do gateway para `192.168.0.50`

---

# Reverse Proxy

O acesso aos serviços passou a ser centralizado pelo **Nginx Proxy Manager**.

Domínios configurados:

| Domínio | Serviço |
|----------|----------|
| homarr.lab.local | Homarr |
| uptime.lab.local | Uptime Kuma |
| portainer.lab.local | Portainer |
| npm.lab.local | Nginx Proxy Manager |
| adguard.lab.local | AdGuard Home (opcional) |

Com essa configuração, os usuários acessam os serviços utilizando nomes amigáveis, sem precisar informar portas específicas.

---

# Rede Docker

Todos os containers fazem parte da rede Docker criada automaticamente pelo Docker Compose.

Nome da rede:

```text
ubuntu-home-lab_proxy
```

Essa rede fornece um servidor DNS interno, permitindo que os containers se comuniquem utilizando seus nomes.

Exemplos:

```text
homarr:7575
uptime-kuma:3001
portainer:9443
```

O Nginx Proxy Manager utiliza esses nomes para encaminhar as requisições aos serviços, eliminando a dependência do endereço IP da máquina virtual na comunicação interna.

---

# Arquitetura Atual

```text
Cliente
      │
      ▼
homarr.lab.local  (resolvido pelo AdGuard Home → 192.168.0.50)
      │
      ▼
192.168.0.50
      │
      ▼
Nginx Proxy Manager
      │
      ▼
ubuntu-home-lab_proxy
      │
 ├── homarr
 ├── uptime-kuma
 ├── portainer
 └── adguardhome
```

---

# Estado Atual

- ✅ External Switch
- ✅ Endereço IP estático
- ✅ Docker Compose
- ✅ Rede Docker dedicada
- ✅ DNS interno do Docker
- ✅ Reverse Proxy
- ✅ DNS local (AdGuard Home)
- ⏳ HTTPS (planejado)

---

# Observação sobre Segurança

O endereço `192.168.0.50` pertence ao espaço de endereços privados (RFC 1918) e está acessível apenas dentro da rede local, não expondo diretamente os serviços à Internet.
