# Configuração da Máquina Virtual (Hyper-V)

## Ambiente host

| Item | Detalhe |
|------|---------|
| **Dispositivo** | LAPTOP-SGMAE8O3 |
| **Sistema operacional** | Windows 11 Pro (64 bits) |
| **Processador** | Intel Core i5-8350U @ 1.70 GHz (até 1.90 GHz) |
| **Memória RAM** | 24 GB (23,8 GB utilizáveis) |
| **Placa gráfica** | Intel UHD Graphics 620 |
| **Armazenamento** | SSD/HD com ~466 GB (cerca de 167 GB em uso no momento da documentação) |
| **Hipervisor** | Hyper-V (nativo do Windows) |

## Criação da VM

A máquina virtual foi criada com as seguintes características:

| Configuração | Valor |
|--------------|-------|
| Nome | Ubuntu-Server-Lab |
| Geração | Geração 2 |
| Memória | 6–8 GB (**fixixa**) |
| Processadores | 2 |
| Disco virtual | 40 GB |
| Sistema operacional | Ubuntu Server (LTS) |

### Observações importantes

- **Memória dinâmica** foi desativada. Com memória dinâmica ativada, o Docker apresentava erros de *out of memory* mesmo com RAM livre disponível na VM.
- **Secure Boot** foi desativado para facilitar a instalação do Ubuntu Server.
- A VM pode ser configurada para iniciar automaticamente com o Windows (útil para manter os serviços disponíveis).

## Instalação do Ubuntu Server

Durante a instalação:

- Foi marcado o **OpenSSH Server** para acesso remoto
- Rede inicialmente via DHCP (Default Switch do Hyper-V)
- Usuário administrador criado normalmente

Após a instalação, o sistema foi atualizado:

```bash
sudo apt update && sudo apt upgrade -y
```

## Acesso à VM

O acesso é feito via SSH a partir do Windows:

```bash
ssh usuario@192.168.0.50
```

## Docker

O Docker foi instalado via **Snap**:

```bash
sudo snap install docker
```

### Decisão sobre o grupo `docker`

O usuário **não** foi adicionado ao grupo `docker`. Com isso, os comandos Docker são executados com `sudo`.

**Motivo:**  
Manter a necessidade de elevação de privilégio (`sudo`) reduz o risco de um processo ou script sem privilégios controlar o daemon do Docker. O grupo `docker` concede, na prática, poder equivalente a root sobre o sistema (via acesso ao socket do Docker).

No caso da instalação via Snap, o modelo de permissões também é diferente da instalação clássica por `apt`. Por isso, a abordagem adotada foi manter o uso explícito de `sudo docker`.

Exemplos de uso:

```bash
sudo docker ps
sudo docker run hello-world
```
