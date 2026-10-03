# homelab-ansible-role-configure-system

[![Lint](https://github.com/iamrobertyoung/homelab-ansible-role-configure-system/actions/workflows/lint.yml/badge.svg)](https://github.com/iamrobertyoung/homelab-ansible-role-configure-system/actions/workflows/lint.yml)
[![Release](https://github.com/iamrobertyoung/homelab-ansible-role-configure-system/actions/workflows/release.yml/badge.svg)](https://github.com/iamrobertyoung/homelab-ansible-role-configure-system/actions/workflows/release.yml)
[![SLSA 3](https://slsa.dev/images/gh-badge-level3.svg)](https://slsa.dev)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/iamrobertyoung/homelab-ansible-role-configure-system/badge)](https://scorecard.dev/viewer/?uri=github.com/iamrobertyoung/homelab-ansible-role-configure-system)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Ansible role for configuring homelab systems. Handles package updates, hostname configuration, log rotation, and system maintenance tasks.

## Requirements

- Ansible >= 2.15
- Target: Ubuntu (focal, jammy, noble) or Debian (bullseye, bookworm, trixie)

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `configure_system_hostname` | *undefined* | Hostname to set (required for hostname tasks) |
| `configure_system_reboot_after_update` | `true` | Reboot automatically if required after package updates |
| `configure_system_log_retention_days` | `30` | Days to retain log files |
| `configure_system_journal_retention_days` | `30` | Days to retain systemd journal |
| `configure_system_logrotate_rotate_count` | `10` | Number of rotated log files to keep |
| `configure_system_docker_log_max_size` | `"100m"` | Max size for Docker container logs |
| `configure_system_docker_log_max_file` | `"3"` | Max number of Docker log files |
| `configure_system_tmp_cleanup_days` | `30` | Delete files in /tmp older than this |
| `configure_system_ci_user` | `ansible` | User GitHub Actions logs in as |
| `configure_system_ci_authorized_keys` | homelab CI key | Public keys authorized for `configure_system_ci_user` |

## Tags

Run specific parts of the role using tags:

- `configure_system` - All tasks
- `packages` - Package updates and installation
- `updates` - Package updates only
- `hostname` - Hostname configuration
- `maintenance` - System maintenance (log rotation, cleanup)
- `ci_access` - Authorize the CI key for the CI user

## Usage

### Install via requirements.yml

```yaml
- src: git@github.com:iamrobertyoung/homelab-ansible-role-configure-system.git
  scm: git
  version: main
  name: configure_system
```

```bash
ansible-galaxy install -r requirements.yml
```

### Example Playbook

```yaml
- hosts: servers
  become: true
  roles:
    - role: configure_system
```

### Override defaults

```yaml
- hosts: servers
  become: true
  roles:
    - role: configure_system
      vars:
        configure_system_hostname: myserver
        configure_system_reboot_after_update: false
        configure_system_journal_retention_days: 14
```

### Run specific tasks

```bash
ansible-playbook site.yml --tags "packages"
ansible-playbook site.yml --tags "hostname"
```

## What Gets Configured

- System packages installed (curl, gpg, net-tools, acl, vim, git, htop, nfs-common)
- APT packages updated with safe upgrade
- Hostname configuration (if specified)
- Systemd journal retention settings
- Logrotate configuration
- /tmp directory cleanup cron job

## Supply Chain Security

This project implements [SLSA](https://slsa.dev/) Level 3 provenance for release artifacts.

- Provenance attestations are submitted to [GitHub Attestations](https://github.com/iamrobertyoung/homelab-ansible-role-configure-system/attestations)
- Release artifacts include `.intoto.jsonl` provenance files
- Security posture tracked via [OpenSSF Scorecard](https://scorecard.dev/viewer/?uri=github.com/iamrobertyoung/homelab-ansible-role-configure-system)

### Verifying Release Provenance

```bash
# Using GitHub CLI (recommended)
gh attestation verify configure_system-<VERSION>.tar.gz \
  --repo iamrobertyoung/homelab-ansible-role-configure-system

# Or using slsa-verifier
VERSION="v1.0.0"  # Replace with desired version
slsa-verifier verify-artifact configure_system-${VERSION}.tar.gz \
  --provenance-path configure_system-${VERSION}.tar.gz.intoto.jsonl \
  --source-uri github.com/iamrobertyoung/homelab-ansible-role-configure-system \
  --source-tag "${VERSION}"
```

## License

MIT
