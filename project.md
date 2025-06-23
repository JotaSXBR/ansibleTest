# Ansible Ubuntu Server Setup Project

This project contains an Ansible playbook to perform a secure initial setup and hardening for an Ubuntu LTS server.

## Project Goal

The primary objective is to automate the hardening and configuration of a new Ubuntu server, ensuring a secure, stable, and consistent base for future applications, with configurations based on security best practices and tools like Lynis.

## Key Configuration Steps

The Ansible playbook performs the following actions:

1.  **System Updates**: Updates all system packages to their latest versions.
2.  **Repository Setup**: Correctly identifies the server's architecture (x86 or ARM) and enables the `universe` and `security` repositories.
3.  **Package Installation**: Installs essential packages for security and administration, including `ufw`, `fail2ban`, `unattended-upgrades`, `auditd`, `lynis`, and `rkhunter`.
4.  **Swap File Creation**: Creates a `4G` swap file to ensure system stability and tunes `swappiness` for server performance.
5.  **User Management (Idempotent)**:
    *   Checks if the `deploy` user exists.
    *   If the user does *not* exist, it interactively prompts for a password and creates the new `deploy` user with `sudo` privileges.
    *   If the user *does* exist, this step is skipped.
6.  **Firewall (UFW) Configuration**:
    *   Sets the default incoming policy to `deny` and outgoing to `allow`.
    *   Rate-limits incoming connections on port 22 (SSH) to prevent brute-force attacks.
    *   Allows traffic on ports 80 (HTTP) and 443 (HTTPS).
    *   Enables the firewall.
7.  **Security Hardening**:
    *   **Fail2ban**: Enables and starts `fail2ban`.
    *   **Unattended Upgrades**: Configures automatic installation of security patches.
    *   **Shared Memory**: Secures shared memory (`/run/shm`).
    *   **Login Security**: Hardens `/etc/login.defs` with a stricter `UMASK` and stronger `SHA512` password encryption.
    *   **Core Dumps**: Disables core dumps.
    *   **Kernel Hardening**: Disables uncommon network protocols.
    *   **Legal Banner**: Creates a legal notice displayed before login.
8.  **Auditd Configuration**: Installs `auditd` and applies a custom ruleset to monitor critical system events.
9.  **Reboot**: Reboots the server after the configuration is applied to ensure all changes take effect.

## Project Structure

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