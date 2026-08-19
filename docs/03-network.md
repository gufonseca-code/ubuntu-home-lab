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

Durante o desenvolvimento inicial do laboratório, foi utilizado o arquivo `hosts` para resolver os domínios locais.

Exemplo:

```text
192.168.0.50 homarr.lab.local
192.168.0.50 uptime.lab.local
192.168.0.50 portainer.lab.local
192.168.0.50 npm.lab.local
```

Essa abordagem permitiu validar o funcionamento do Reverse Proxy antes da implantação de um servidor DNS dedicado.

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
homarr.lab.local
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
 └── portainer
```

---

# Próximos Passos

A próxima etapa da evolução da infraestrutura será a implantação de um servidor DNS local.

Objetivos:

- eliminar a dependência do arquivo `hosts`;
- distribuir os domínios automaticamente para todos os dispositivos da rede;
- simplificar a administração dos serviços.

Após essa implantação, os registros DNS passarão a substituir completamente as entradas manuais atualmente utilizadas.

---

# Estado Atual

- ✅ External Switch
- ✅ Endereço IP estático
- ✅ Docker Compose
- ✅ Rede Docker dedicada
- ✅ DNS interno do Docker
- ✅ Reverse Proxy
- ⏳ DNS local (planejado)

---

# Observação sobre Segurança

O endereço `192.168.0.50` pertence ao espaço de endereços privados (RFC 1918) e está acessível apenas dentro da rede local, não expondo diretamente os serviços à Internet.