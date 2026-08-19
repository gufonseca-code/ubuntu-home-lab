# Configuração da Máquina Virtual (Hyper-V)

## Ambiente Host

| Item | Detalhe |
|------|---------|
| **Dispositivo** | LAPTOP-SGMAE8O3 |
| **Sistema Operacional** | Windows 11 Pro (64 bits) |
| **Processador** | Intel Core i5-8350U @ 1.70 GHz (Turbo até 3.60 GHz) |
| **Memória RAM** | 24 GB (23,8 GB utilizáveis) |
| **Placa Gráfica** | Intel UHD Graphics 620 |
| **Armazenamento** | SSD/HD (~466 GB) |
| **Hipervisor** | Hyper-V |

---

# Máquina Virtual

A máquina virtual foi criada para hospedar todos os serviços do laboratório.

| Configuração | Valor |
|--------------|-------|
| Nome | Ubuntu-Server-Lab |
| Geração | Geração 2 |
| Memória | 6–8 GB (**fixa**) |
| Processadores | 2 vCPUs |
| Disco Virtual | 40 GB |
| Sistema Operacional | Ubuntu Server LTS |

---

# Decisões de Projeto

## Memória fixa

A memória dinâmica do Hyper-V foi desativada.

Durante os testes, o Docker apresentou erros de *Out of Memory* mesmo existindo memória disponível dentro da máquina virtual.

A utilização de memória fixa tornou o ambiente mais estável para execução dos containers.

---

## Secure Boot

O Secure Boot foi desativado para simplificar a instalação do Ubuntu Server.

---

## Inicialização automática

A máquina virtual pode ser configurada para iniciar automaticamente junto com o Windows, permitindo que todos os serviços do laboratório permaneçam disponíveis após a inicialização do host.

---

# Sistema Operacional

Durante a instalação foram selecionados:

- OpenSSH Server
- Configuração inicial de rede via DHCP (Default Switch do Hyper-V)

Após a instalação:

```bash
sudo apt update
sudo apt upgrade -y
```

Posteriormente a rede foi migrada para um Switch Externo do Hyper-V e a máquina passou a utilizar um endereço IP estático.

---

# Acesso Remoto

A administração da VM é realizada via SSH.

```bash
ssh usuario@192.168.0.50
```

O Visual Studio Code também é utilizado através da extensão **Remote - SSH**, permitindo editar arquivos diretamente na máquina virtual.

---

# Docker

O Docker foi instalado utilizando o pacote Snap.

```bash
sudo snap install docker
```

---

# Decisão sobre o grupo docker

O usuário administrador **não** foi adicionado ao grupo `docker`.

Todos os comandos são executados utilizando `sudo`.

Exemplos:

```bash
sudo docker ps
sudo docker compose up -d
sudo docker compose down
```

## Motivo

O grupo `docker` possui privilégios equivalentes ao usuário root, pois concede acesso direto ao socket do Docker.

Optou-se por exigir elevação explícita de privilégios (`sudo`) para reduzir o risco de execução inadvertida de operações administrativas.

---

# Estado Atual

Atualmente esta máquina virtual hospeda:

- Docker
- Docker Compose
- Nginx Proxy Manager
- Homarr
- Uptime Kuma
- Portainer

Toda a infraestrutura é gerenciada através de um único arquivo `compose.yml`, permitindo recriar o ambiente de forma reproduzível.