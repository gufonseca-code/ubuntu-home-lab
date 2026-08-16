# Problemas Enfrentados e Soluções

Esta seção documenta os principais problemas encontrados durante a montagem do laboratório e como foram resolvidos. É uma das partes mais úteis da documentação, pois demonstra capacidade de troubleshooting.

---

## 1. Erro "out of memory" no Docker (mesmo com RAM livre)

### Sintoma

Ao executar `docker run hello-world` (e outros comandos), aparecia erro de *out of memory*, apesar do comando `free -h` mostrar vários GB disponíveis.

### Causa

A **Memória Dinâmica** do Hyper-V estava ativada. O gerenciamento dinâmico de memória do hipervisor interferia na forma como o Docker e o kernel Linux enxergavam a memória disponível.

### Solução

1. Desligar a VM
2. Em **Configurações → Memória**:
   - Desmarcar **Habilitar memória dinâmica**
   - Definir RAM fixa (6 GB ou 8 GB)
3. Ligar a VM novamente

Após essa alteração, o erro deixou de ocorrer.

---

## 2. Instalação do Ubuntu Server reiniciando e voltando ao início

### Sintoma

Durante a instalação do Ubuntu Server, a VM reiniciava e voltava para a tela inicial da instalação em loop.

### Causas possíveis e correções aplicadas

- **Secure Boot** ainda ativado → desativado em Configurações → Segurança
- ISO desconectada após o primeiro reboot → verificada e reconectada na unidade de DVD
- Ordem de boot → Unidade de DVD priorizada no Firmware

Com Secure Boot desativado e a ISO corretamente conectada, a instalação concluiu normalmente.

---

## 3. IP mudando a cada reinício da VM

### Sintoma

Após reiniciar a VM, o endereço IP mudava. Era necessário atualizar manualmente os links no Homarr, Uptime Kuma e nos monitores.

### Causa

Uso do **Default Switch** do Hyper-V (NAT + DHCP interno).

### Solução

1. Criação de um **External Switch** no Hyper-V
2. Associação da VM a esse switch
3. Configuração de IP estático via Netplan (`192.168.0.50/24`)

Com isso, o IP permaneceu fixo após reinícios.

---

## 4. Suspensão do Windows interrompendo os serviços

### Sintoma

Quando o computador host entrava em modo de suspensão (Sleep), os serviços da VM paravam de responder.

### Explicação

No modo Sleep/Hibernação, as VMs do Hyper-V são pausadas ou interrompidas.

### Solução / mitigação

- Configurar o Windows para **nunca suspender**
- Permitir apenas o desligamento da tela
- (Opcional) Configurar a VM para iniciar automaticamente com o Windows

---

## 5. Aprendizados gerais

- Preferir memória **fixixa** em VMs que rodam Docker
- Documentar problemas e soluções logo após resolvê-los
- IP estático é essencial quando se expõe vários serviços por endereço
- External Switch oferece mais controle e previsibilidade do que o Default Switch para laboratórios
- Ferramentas de monitoramento + alertas (Telegram) aumentam bastante a utilidade prática do lab
