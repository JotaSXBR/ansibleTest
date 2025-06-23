# Ansible Secure Ubuntu Server Setup

This Ansible project automates the initial security hardening and configuration of a fresh Ubuntu LTS server instance. It correctly handles both x86 and ARM architectures, sets up a new user idempotently, configures an advanced firewall, and applies a comprehensive set of security hardening measures based on industry best practices.

## Features

- **System Administration**:
  - Ensures all packages are up to date.
  - Detects server architecture (x86/ARM) to use the correct software repositories.
  - Creates a `4G` swap file for system stability.
  - Idempotent User Creation: On the first run, it prompts for a password and creates a `deploy` user with `sudo` privileges. On subsequent runs, it skips this step.

- **Firewall & Network Security**:
  - Configures UFW firewall to deny incoming traffic by default.
  - Rate-limits SSH connections to prevent brute-force attacks.
  - Disables uncommon network protocols to reduce the kernel's attack surface.

- **System & Security Hardening**:
  - **Fail2ban**: Installs and enables `fail2ban`.
  - **Unattended Upgrades**: Configures automatic security patches.
  - **Auditd**: Installs and configures `auditd` with custom rules for deep system monitoring.
  - **Login Security**: Hardens login policies and strengthens password encryption.
  - **Secure Memory**: Secures shared memory and disables core dumps.
  - **Legal Banner**: Sets a pre-login legal banner.
  - **Auditing Tools**: Installs `lynis` and `rkhunter` for manual security scans.

- **Automatic Reboot**: Reboots the server upon completion to apply all changes.

## How to Use

### 1. Prerequisites
- **Ansible Control Node**: A machine with Ansible and `sshpass` installed.
- **Ubuntu Server**: A fresh Ubuntu Server LTS instance, accessible via SSH. You need the `root` user's password.

### 2. Configure the Inventory
Open `inventory.ini` and replace `your_server_ip` with your server's public IP address.

### 3. Run the Playbook
Execute the playbook from your control node's terminal:
```bash
ansible-playbook playbook.yml --ask-pass
```
- The command will first ask for the `root` SSH password for `your_server_ip`. This is needed for the initial connection.
- If this is the first time running the playbook, it will then prompt you to enter and confirm a new password for the `deploy` user.

On subsequent runs, the playbook will recognize that the user and configuration already exist and will complete very quickly without prompting for a user password.

### 4. Connect as the New User
After the playbook completes and the server reboots, you can log in:
```bash
ssh deploy@your_server_ip
```
Use the password you created during the playbook run.

## Notes & Troubleshooting

- **Host Key Checking**: The `ansible.cfg` is set with `host_key_checking = False` for convenience in test environments. For production, it is highly recommended to set this to `True` to prevent security risks.

- **Package Availability**: During development, we found that the `apt-listbugs` and `apt-listchanges` packages were not available for the Ubuntu 24.04 ARM64 architecture. They have been removed from the playbook. If you re-enable them and encounter a "Package not found" error, this is the likely cause.

## Playbook Structure
- `inventory.ini`: Defines the server(s) to be configured.
- `ansible.cfg`: Ansible configuration file.
- `playbook.yml`: The main playbook that orchestrates all the configuration tasks.
- `files/`: Contains configuration files to be copied to the server.
  - `20auto-upgrades`: Config for unattended security updates.
  - `99-custom.rules`: Custom rules for the `auditd` service.
  - `99-disable-coredumps.conf`: Disables system core dumps.
  - `99-disable-uncommon-net.conf`: Disables unused network protocols.
  - `issue_banner`: The legal banner text for `/etc/issue`. 