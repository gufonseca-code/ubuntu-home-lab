# Serviços Instalados

Todos os serviços abaixo rodam como containers Docker na VM Ubuntu Server (`192.168.0.50`).

## Resumo

| Serviço | Porta | URL de acesso | Função |
|---------|-------|---------------|--------|
| Portainer | 9443 | https://192.168.0.50:9443 | Gerenciamento visual de containers |
| Uptime Kuma | 3001 | http://192.168.0.50:3001 | Monitoramento e alertas |
| Homarr | 7575 | http://192.168.0.50:7575 | Dashboard central |

---

## 1. Portainer

Interface web para gerenciar o Docker (containers, imagens, volumes, redes).

### Instalação

```bash
docker volume create portainer_data

docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

Na primeira execução é necessário criar o usuário administrador.

---

## 2. Uptime Kuma

Ferramenta de monitoramento de serviços com interface visual e suporte a notificações.

### Instalação

```bash
docker run -d \
  --name uptime-kuma \
  --restart=always \
  -p 3001:3001 \
  -v uptime-kuma:/app/data \
  louislam/uptime-kuma:1
```

### O que está sendo monitorado

- Portainer (HTTPS na porta 9443)
- Próprio Uptime Kuma
- SSH da VM (porta 22)
- Outros serviços conforme necessidade

### Notificações

Foi configurada notificação via **Telegram**:

1. Criação de um bot com o @BotFather
2. Obtenção do Bot Token e do Chat ID
3. Cadastro da notificação no Uptime Kuma
4. Associação da notificação aos monitores

Assim, qualquer queda ou recuperação de serviço gera alerta no Telegram.

---

## 3. Homarr

Dashboard moderno para centralizar o acesso aos serviços do laboratório.

### Instalação

```bash
# Gerar chave de criptografia
openssl rand -hex 32

docker run -d \
  --name homarr \
  --restart unless-stopped \
  -p 7575:7575 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v homarr-appdata:/appdata \
  -e SECRET_ENCRYPTION_KEY='SUA_CHAVE_AQUI' \
  ghcr.io/homarr-labs/homarr:latest
```

O socket do Docker foi montado para permitir integração e visualização do status dos containers.

No Homarr foram adicionados atalhos para Portainer, Uptime Kuma e o próprio Homarr.

---

## Política de reinício

Todos os containers principais usam `--restart=always` ou `unless-stopped`, de forma que voltem automaticamente após reinício da VM.
