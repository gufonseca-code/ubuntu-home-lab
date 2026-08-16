# Visão Geral do Home Lab

## O que é este laboratório?

Este é um ambiente de laboratório pessoal criado para praticar e documentar habilidades de **infraestrutura** e **suporte técnico**.

O foco principal está em atividades comuns de:

- Suporte Técnico N1 / Service Desk
- Administração básica de servidores Linux
- Monitoramento de serviços
- Redes e configuração de ambiente
- Documentação de infraestrutura

## Objetivo

Criar um ambiente prático, estável e bem documentado que demonstre:

- Capacidade de montar e manter serviços
- Resolução de problemas reais
- Organização e documentação técnica
- Uso de ferramentas comuns em ambientes de TI

## Arquitetura atual

| Componente | Detalhe |
|------------|---------|
| **Host** | Windows 11 Pro com Hyper-V |
| **Máquina Virtual** | Ubuntu Server |
| **Rede** | External Switch + IP estático (`192.168.0.50`) |
| **Plataforma de containers** | Docker |
| **Gerenciamento** | Portainer |
| **Monitoramento** | Uptime Kuma (com alertas no Telegram) |
| **Dashboard** | Homarr |

## Serviços principais

| Serviço | Função |
|---------|--------|
| Portainer | Gerenciamento de containers Docker |
| Uptime Kuma | Monitoramento de serviços e notificações |
| Homarr | Dashboard central com atalhos dos serviços |

## Público-alvo desta documentação

Esta documentação foi escrita tanto para meu próprio aprendizado quanto para demonstrar, de forma clara, o processo de construção e manutenção de um ambiente de infraestrutura alinhado a vagas de Suporte Técnico e Analista de TI Júnior.
