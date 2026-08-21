# Lições Aprendidas

Este documento reúne os principais problemas encontrados durante o desenvolvimento do laboratório, bem como as soluções adotadas e os conhecimentos adquiridos.

---

# Hyper-V

## Default Switch

### Problema

O Default Switch atribuía endereços IP dinâmicos à máquina virtual.

Isso dificultava o acesso aos serviços e exigia alterações frequentes na configuração do ambiente.

### Solução

Foi criado um External Switch e configurado um endereço IP estático para a máquina virtual.

### Aprendizado

Embora o Default Switch seja suficiente para testes rápidos, um laboratório permanente exige uma configuração de rede previsível.

---

# Docker

## Docker via Snap

### Problema

Inicialmente havia dúvidas sobre a utilização do Docker instalado via Snap.

### Solução

Foi mantida essa instalação por apresentar boa integração com o Ubuntu Server e atender plenamente às necessidades do laboratório.

### Aprendizado

Independentemente do método de instalação, o mais importante é compreender onde os volumes, redes e configurações são armazenados.

---

# Docker Compose

## Migração do docker run

### Problema

Todos os serviços eram criados individualmente utilizando `docker run`.

Isso dificultava:

- reproduzir o ambiente;
- manter as configurações;
- documentar a infraestrutura.

### Solução

Todos os containers foram migrados para um único arquivo `compose.yml`.

### Aprendizado

O Docker Compose transforma a infraestrutura em código, permitindo recriar todo o ambiente de maneira simples e reproduzível.

---

## Conflito de nomes dos containers

### Problema

Ao executar o Docker Compose pela primeira vez ocorreu um conflito porque já existiam containers criados manualmente utilizando os mesmos nomes.

### Solução

Os containers antigos foram removidos e a infraestrutura passou a ser gerenciada exclusivamente pelo Docker Compose.

### Aprendizado

Uma migração para Compose deve ser planejada para evitar conflitos entre recursos existentes e aqueles definidos no novo ambiente.

---

# Redes Docker

## Comunicação entre containers

### Problema

O Nginx Proxy Manager utilizava o endereço IP da máquina virtual para acessar os serviços.

```text
192.168.0.50:7575
```

Isso criava uma dependência desnecessária do host.

### Solução

Foi criada uma rede Docker dedicada e o Nginx Proxy Manager passou a utilizar os nomes dos containers.

Exemplo:

```text
homarr:7575
```

### Aprendizado

O Docker fornece um servidor DNS interno para cada rede criada.

Sempre que possível, containers devem se comunicar utilizando seus nomes em vez do endereço IP do host.

---

## DNS do Uptime Kuma

### Problema

O Uptime Kuma não conseguia resolver os domínios locais (`*.lab.local`), apesar de funcionarem normalmente no sistema operacional.

### Solução

Foi identificado que os containers possuem resolução de nomes independente do sistema hospedeiro.

Como solução temporária, foram utilizadas entradas adicionais de resolução de nomes até a implantação de um servidor DNS dedicado.

### Aprendizado

A resolução de nomes dentro de containers deve ser planejada separadamente da resolução utilizada pelo sistema operacional.

---

# Reverse Proxy

## Nginx Proxy Manager

### Problema

Os serviços eram acessados diretamente pelas portas expostas.

Exemplo:

```
http://192.168.0.50:7575
```

### Solução

Foi implantado o Nginx Proxy Manager para centralizar o acesso através de domínios locais.

Exemplo:

```
http://homarr.lab.local
```

### Aprendizado

O uso de um Reverse Proxy torna a infraestrutura mais organizada, facilita futuras implantações de HTTPS e reduz a exposição direta das portas dos serviços.

---

# AdGuard Home

## Conflito de porta da interface web

### Problema

Após concluir o setup inicial do AdGuard Home, a interface administrativa não respondia mais na porta 3000.

Os acessos a `http://192.168.0.50` e `http://192.168.0.50:80` abriam a página padrão do Nginx Proxy Manager. A porta 3000 retornava erro de página não encontrada.

### Causa

Durante o primeiro start, o AdGuard Home utiliza a porta 3000 para o assistente de instalação.
Após a conclusão do setup, a interface web passa a escutar na porta **80** dentro do container.

No `compose.yml` original, apenas a porta 3000 estava publicada. A porta 80 do container não estava mapeada para o host. Além disso, a porta 80 do host já era utilizada pelo Nginx Proxy Manager.

### Solução

O mapeamento de portas foi alterado para:

```yaml
ports:
  - "53:53/tcp"
  - "53:53/udp"
  - "3000:80/tcp"
```

Com isso, a interface web do AdGuard Home ficou acessível em:

```text
http://192.168.0.50:3000
```

### Aprendizado

Serviços que alteram a porta da interface administrativa após o setup inicial exigem atenção especial no mapeamento de portas.
Quando a porta 80 do host já está ocupada (como no caso do Nginx Proxy Manager), é necessário publicar a interface em uma porta alternativa ou expor o serviço através do próprio Reverse Proxy.

---

# Documentação

## Documentar durante o desenvolvimento

### Problema

Conforme o laboratório evoluía, detalhes importantes começavam a ser esquecidos.

### Solução

Toda decisão de arquitetura passou a ser documentada imediatamente após sua implementação.

### Aprendizado

Documentar continuamente é mais eficiente do que tentar reconstruir o histórico do projeto posteriormente.

---

# Próximos Aprendizados

As próximas etapas do projeto deverão introduzir novos conceitos, incluindo:

- HTTPS
- Certificados SSL
- Monitoramento de infraestrutura
- Backup
- Recuperação de ambiente
- Expansão dos serviços
