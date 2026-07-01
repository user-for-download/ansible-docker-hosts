# Ansible Docker Hosts

Automated Ubuntu server provisioning with Docker, security hardening, and modern Zsh environment.

## Quick Start

```bash
# Clone
git clone git@github.com:user-for-download/ansible-docker-hosts.git
cd ansible-docker-hosts

# Run pre-settings (system prep + security)
ansible-playbook pre-settings.yml --limit <host>

# Install Docker
ansible-playbook docker-setup.yml --limit <host>

# Setup Zsh environment
ansible-playbook user-setup.yml --limit <host>
```

## Inventory

Edit `inventory.yml` to add your hosts:

```yaml
all:
  children:
    ubuntu:
      hosts:
        server-01:
          static_ip: "10.0.2.100/24"
    docker:
      hosts:
        server-01:
```

## Playbooks

| Playbook | Description |
|----------|-------------|
| `pre-settings.yml` | System preparation, NTP, firewall, security hardening |
| `docker-setup.yml` | Docker CE + Compose plugin installation |
| `docker-storage.yml` | Docker storage on dedicated disk |
| `user-setup.yml` | Zsh + Zinit + Powerlevel10k environment |
| `set-ip.yml` | Static IP configuration via Netplan |

## Roles

### host_settings
- Cloud-init removal
- Swap disabled
- Automatic updates disabled
- IPv6 disabled (sysctl + GRUB)
- Package upgrades + core dependencies
- Timezone configuration

### chrony_client
- NTP synchronization via Chrony
- Custom server configuration

### security_hardening
- **UFW** (default): SSH from trusted networks only
- **nftables** (commented alternative): `/etc/nftables.conf` with port 22 open
- Fail2Ban for SSH brute-force protection

### docker_install
- Docker CE from official repository
- Daemon configuration (json-file logging, overlay2, live-restore)
- User group management

### docker_compose
- Docker Compose plugin (default)
- Standalone binary (alternative)

### docker_storage
- Dedicated disk partitioning (GPT + ext4)
- Data migration from default Docker directory
- UUID-based fstab mount

### netplan_static
- Static IP via Netplan
- Post-apply connectivity verification

### user_setup
- User creation with sudo access
- SSH key deployment
- Passwordless sudo configuration

### zsh_setup
- **Zinit** plugin manager (Antigen alternative commented)
- **Powerlevel10k** theme
- Prezto modules via Zinit snippets
- **Eza** (ls replacement with icons/git)
- **Zoxide** (smart cd)
- **FZF** (fuzzy finder, installed from Git)
- Modern CLI tools: bat, ripgrep, fd, ncdu, htop

## Variables

Key variables in `group_vars/`:

| File | Variables |
|------|-----------|
| `all.yml` | `trusted_networks`, `timezone`, `static_interface`, `apt_proxy` |
| `ubuntu.yml` | `core_dependencies`, `ntp_servers`, `ssh_port`, `fail2ban_*` |
| `docker.yml` | `docker_version`, `docker_compose_version`, `docker_daemon_config` |

## Requirements

- Ansible 2.12+
- Target: Ubuntu 20.04+
- SSH access to target hosts
