# Configuração da Máquina Virtual (Hyper-V)

## Ambiente host

- **Sistema operacional**: Windows 11 Pro
- **RAM total do host**: 24 GB
- **Hipervisor**: Hyper-V (nativo do Windows)

## Criação da VM

A máquina virtual foi criada com as seguintes características:

| Configuração | Valor |
|--------------|-------|
| Nome | Ubuntu-Server-Lab (ou similar) |
| Geração | Geração 2 |
| Memória | 6–8 GB (fixixa) |
| Processadores | 2 |
| Disco virtual | 40 GB |
| Sistema operacional | Ubuntu Server (LTS) |

### Observações importantes

- **Memória dinâmica** foi desativada. Com memória dinâmica ativada, o Docker apresentava erros de *out of memory* mesmo com RAM livre disponível na VM.
- **Secure Boot** foi desativado para facilitar a instalação do Ubuntu Server.
- A VM foi configurada para iniciar automaticamente com o Windows (opcional, mas recomendado para manter os serviços disponíveis).

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

O Docker foi instalado na VM e o usuário foi adicionado ao grupo `docker` para evitar o uso constante de `sudo`:

```bash
sudo usermod -aG docker $USER
```

Após isso, é necessário sair e entrar novamente na sessão (ou reiniciar a VM) para a permissão valer.
