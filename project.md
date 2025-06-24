# Projeto de Configuração de Servidor Ubuntu com Ansible

Este projeto contém um playbook Ansible que executa a configuração inicial e o endurecimento de segurança de um servidor Ubuntu LTS.

## Objetivo do projeto

Automatizar a preparação de um novo servidor Ubuntu, garantindo uma base segura, estável e consistente para futuras aplicações, seguindo boas práticas de segurança e utilizando ferramentas como o **Lynis**.

## Etapas principais da configuração

O playbook realiza as seguintes ações:

1. **Atualizações do sistema**: atualiza todos os pacotes para as versões mais recentes.
2. **Configuração de repositórios**: identifica a arquitetura (x86 ou ARM) e habilita os repositórios `universe` e `security`.
3. **Instalação de pacotes**: instala pacotes essenciais como `ufw`, `fail2ban`, `unattended-upgrades`, `auditd`, `lynis` e `rkhunter`.
4. **Criação de arquivo de swap**: cria um arquivo de swap (tamanho definido em `group_vars/all.yml`) e ajusta o parâmetro `swappiness`.
5. **Gerenciamento de usuários**:
   * Pressupõe que o usuário `deploy` (com privilégios sudo) seja criado manualmente antes da execução.
   * Todas as tarefas subsequentes são executadas por esse usuário via `become`.
6. **Configuração do firewall (UFW)**:
   * Define a política padrão de entrada como `deny` e saída como `allow`.
   * Limita a taxa de conexões na porta 22 (SSH).
   * Libera as portas 80 (HTTP) e 443 (HTTPS).
   * Ativa o firewall.
7. **Endurecimento de segurança**:
   * **Fail2ban** habilitado.
   * **Atualizações automáticas** configuradas.
   * Memória compartilhada montada de forma segura.
   * Regras de login reforçadas em `/etc/login.defs`.
   * Desativação de core dumps.
   * Desativação de protocolos de rede incomuns.
   * Banner legal adicionado.
8. **Configuração do Auditd**: aplica regras personalizadas para auditoria.
9. **Reinicialização**: reinicia o servidor para aplicar todas as alterações.

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