# Home Lab - Infraestrutura e Suporte Técnico

Laboratório pessoal de infraestrutura focado em aprendizado prático de suporte técnico, monitoramento e administração de serviços.

## Objetivo

Criar um ambiente prático para desenvolver e demonstrar habilidades relacionadas a:

- Administração básica de servidores Linux
- Docker e containers
- Monitoramento de serviços
- Redes e configuração de IP
- Documentação de infraestrutura

Este laboratório foi montado com foco em atividades comuns de **Suporte Técnico N1**, **Service Desk** e **Analista de TI Júnior**.

## Arquitetura

- **Host**: Windows 11 Pro + Hyper-V
- **VM**: Ubuntu Server
- **IP Estático**: 192.168.0.50
- **Rede**: External Switch (Hyper-V)
- **Container Runtime**: Docker

## Serviços em execução

| Serviço         | Porta  | Descrição                          | URL                          |
|-----------------|--------|------------------------------------|------------------------------|
| Portainer       | 9443   | Gerenciamento de containers        | https://192.168.0.50:9443    |
| Uptime Kuma     | 3001   | Monitoramento + alertas Telegram   | http://192.168.0.50:3001     |
| Homarr          | 7575   | Dashboard central dos serviços     | http://192.168.0.50:7575     |

## Principais aprendizados

- Configuração de máquina virtual no Hyper-V
- Resolução de problemas de memória (Dynamic Memory + Docker)
- Configuração de rede com External Switch e IP estático (Netplan)
- Implantação de serviços com Docker
- Configuração de monitoramento e notificações (Telegram)
- Organização de serviços em dashboard

## Estrutura da documentação

- [Visão Geral](docs/01-overview.md)
- [Configuração da VM](docs/02-hardware-vm.md)
- [Rede e IP Estático](docs/03-network.md)
- [Serviços](docs/04-services.md)
- [Problemas enfrentados e soluções](docs/08-lessons-learned.md)

## Tecnologias utilizadas

- Windows 11 Pro
- Hyper-V
- Ubuntu Server
- Docker
- Portainer
- Uptime Kuma
- Homarr
- Netplan
- SSH

## Próximos passos

- [ ] Adicionar FileBrowser
- [ ] Configurar proxy reverso (Nginx Proxy Manager)
- [ ] Melhorar documentação com screenshots
- [ ] Criar status page pública