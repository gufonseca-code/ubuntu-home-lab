# Rede e IP Estático

## Situação inicial

A VM foi criada inicialmente usando o **Default Switch** do Hyper-V.  
Esse switch utiliza NAT + DHCP interno, o que fazia o IP mudar a cada reinício da VM.

Isso obrigava a atualizar manualmente os endereços de acesso do Portainer, Uptime Kuma e Homarr sempre que a máquina era reiniciada.

## Solução adotada: External Switch

Foi criado um **External Switch** no Hyper-V para colocar a VM na mesma rede local do computador host.

### Passos realizados no Hyper-V

1. Abriu o **Gerenciador de Switch Virtual**
2. Criou um switch do tipo **Externo**
3. Selecionou a placa de rede física (Wi-Fi ou Ethernet)
4. Marcou a opção de permitir que o sistema operacional de gerenciamento compartilhasse o adaptador
5. Associou o adaptador de rede da VM a esse novo switch

Após a mudança, a VM passou a receber IP da rede local (`192.168.0.x`).

## Configuração de IP estático (Netplan)

Interface utilizada: `eth0`  
Gateway da rede: `192.168.0.1`

Arquivo de configuração (`/etc/netplan/00-installer-config.yaml`):

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.0.50/24
      routes:
        - to: default
          via: 192.168.0.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

Aplicação da configuração:

```bash
sudo netplan apply
```

## Resultado

- IP fixo: **192.168.0.50**
- Conectividade com a internet e com a rede local estável
- Serviços acessíveis de forma consistente após reinícios

## Observação sobre segurança

O endereço `192.168.0.50` é um IP **privado** (RFC 1918). Ele só é alcançável dentro da rede local e não expõe a VM diretamente à internet.
