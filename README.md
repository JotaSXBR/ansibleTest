# Configuração Segura de Servidor Ubuntu com Ansible

Este projeto Ansible automatiza o endurecimento inicial e a configuração de um servidor Ubuntu LTS recém-instalado. Ele detecta corretamente a arquitetura (x86 ou ARM), configura um firewall avançado e aplica um conjunto abrangente de medidas de segurança baseadas em boas práticas do setor. O playbook **pressupõe** a existência prévia de um usuário não-root `deploy`.

## Recursos

### Administração do sistema
- Garante que todos os pacotes estejam atualizados.
- Detecta a arquitetura do servidor para selecionar os repositórios corretos.
- Cria um arquivo de swap (tamanho configurável em `group_vars/all.yml`).
- Executa todas as tarefas privilegiadas através de escalonamento de privilégios (sudo) do usuário `deploy`.

### Firewall e segurança de rede
- Configura o UFW para negar tráfego de entrada por padrão.
- Limita a taxa de conexões SSH para evitar ataques de força bruta.
- Desativa protocolos de rede incomuns para reduzir a superfície de ataque do kernel.

### Endurecimento do sistema
- **Fail2ban**: instala e habilita o serviço.
- **Atualizações automáticas**: habilita atualizações de segurança sem intervenção.
- **Auditd**: instala e aplica regras personalizadas para auditoria profunda.
- **Segurança de login**: reforça políticas de login e criptografia de senhas.
- **Memória compartilhada**: monta `/run/shm` de forma segura.
- **Banner legal**: apresenta aviso legal antes do login.
- **Ferramentas de auditoria**: instala `lynis` e `rkhunter` para verificações manuais.

- **Reinicialização automática**: reinicia o servidor ao final para aplicar todas as alterações.

## Como Utilizar

### 1. Pré-instalação (manual, somente uma vez)

Conectado como `root` no VPS recém-criado:

```bash
adduser deploy
usermod -aG sudo deploy
```

Em seguida, edite `/etc/ssh/sshd_config` para:

* Desabilitar login como root (`PermitRootLogin no`);
* (Opcional) Alterar a porta padrão do SSH;
* Garantir que a autenticação por chave pública esteja ativada e a chave do usuário `deploy` em `~/.ssh/authorized_keys`.

Caso altere a porta, libere-a no UFW **antes** de reiniciar o serviço.

Confirme que é possível acessar via `ssh deploy@servidor` antes de prosseguir.

### 2. Configurar o inventário

Edite `inventory.ini` e substitua `your_server_ip` pelo IP público do servidor:

```ini
[servers]
your_server_ip ansible_user=deploy
```

### 3. Executar o playbook

No nó de controle:

```bash
ansible-playbook playbook.yml -i inventory.ini --ask-become-pass
```

Será solicitada a senha de sudo do usuário `deploy`.

### 4. Verificação pós-execução

```bash
ssh deploy@your_server_ip
# ou, se mudou a porta
ssh deploy@your_server_ip -p <porta>
```

## Observações

- **Verificação de chave do host**: o `ansible.cfg` está com `host_key_checking = False` para conveniência em ambientes de teste. Em produção, altere para `True`.
- **Disponibilidade de pacotes**: `apt-listbugs` e `apt-listchanges` não estavam disponíveis na arquitetura ARM64 do Ubuntu 24.04 na data do desenvolvimento; por isso foram removidos.

## Estrutura do projeto

```
.
├── ansible.cfg
├── files
│   ├── 20auto-upgrades
│   ├── 99-custom.rules
│   ├── 99-disable-coredumps.conf
│   ├── 99-disable-uncommon-net.conf
│   └── issue_banner
├── inventory.ini
├── playbook.yml
├── project.md
└── README.md
```

---

### Reforço manual adicional do SSH

Os passos de hardening do serviço SSH são realizados manualmente para evitar perda de acesso. Siga as instruções acima para garantir a segurança do serviço. 