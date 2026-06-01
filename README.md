# Ansible Role: Chrony

|Source|Version|CI|License|
|------|-------|--|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-chrony)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-chrony)](https://github.com/grzegorzfranus/ansible-role-chrony/releases)|[![CI](https://github.com/grzegorzfranus/ansible-role-chrony/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-chrony/actions/workflows/ci.yml)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures Chrony, a versatile implementation of the Network Time Protocol (NTP). It provides a robust, production-ready, and secure time synchronization solution with features like NTP server/pool configuration, log rotation management, service state control, and RTC alignment.

## ✨ Features

- ⏰ **Accurate Time Synchronization**: High-precision NTP client/server implementation
- 🔧 **Automatic Configuration**: Zero-configuration setup with sensible defaults
- 🛡️ **Security Hardening**: Secure file permissions and restrict binding/access control
- 🚀 **Service Management**: Complete systemd service configuration and management
- 📊 **Comprehensive Logging**: Detailed logging with rotation and archival
- 🧪 **Time Verification**: Built-in accuracy testing and validation
- 🌐 **Flexible Topology**: Support for both client, server, and hybrid configurations
- 🔄 **Automatic Updates**: Package upgrade capabilities with service management
- 📝 **Configuration Backup**: Safe configuration deployment with rollback capability
- 🧪 **Container Testing**: Full Molecule test suite for CI/CD integration

## 🎯 Architecture

The role provides a flexible time synchronization architecture supporting both:

- **Client Mode**: Synchronizes time from external NTP servers/pools
- **Server Mode**: Acts as NTP server for local network clients
- **Hybrid Mode**: Can simultaneously serve and sync time

```
External NTP Servers ←→ Chrony Server ←→ Local Clients
      (upstream)         (this host)      (downstream)
```

## 📋 Requirements

- **Ansible**: 2.15 or higher
- **Python**: 3.9 or higher on target hosts
- **Network**: Internet access for NTP synchronization (client mode) or local connectivity
- **Privileges**: sudo/root access on target hosts

### Supported operating systems
List of officially supported operating systems for this role:

| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 26.04 (Resolute) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 13 (Trixie)   | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| EL (RHEL, Rocky, Alma, Oracle) | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

> **Note**: EL 8 is not supported — `python3-dnf` bindings are compiled for Python 3.6, which is incompatible with ansible-core >= 2.17. Use EL 9 or newer.

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role requires root access for package installation and service management. Make sure you are using a user with root privileges.

## 🚀 Quick Start

### 1. Basic NTP Client Setup

```yaml
---
- name: Configure NTP Client
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_ntp_source_mode: "pool"
        chrony_service_enabled: true
```

### 2. NTP Server Configuration

```yaml
---
- name: Configure NTP Server
  hosts: ntp_servers
  become: true
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_ntp_source_mode: "server"
        chrony_ntp_clients:
          - "allow 192.0.2.0/24"
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory chrony-setup.yml
```

## ⚙️ Configuration

### Default Configuration

The role comes with production-ready defaults:

```yaml
# Time synchronization
chrony_ntp_source_mode: "server"
chrony_service_enabled: true

# Network sources
chrony_ntp_servers:
  - "0.pool.ntp.org iburst minpoll 4 maxpoll 8"
  - "1.pool.ntp.org iburst minpoll 4 maxpoll 8"

# RTC settings
chrony_rtc_settings:
  rtcsync_enable: true
chrony_maxdistance: 3.0
```

### Advanced Configuration

Customize for specific requirements:

```yaml
---
- name: Advanced Chrony Configuration
  hosts: all
  become: true
  vars:
    chrony_ntp_source_mode: "pool"
    chrony_configure_logrotate: true
    chrony_log_enable: true
    chrony_hardware_settings:
      enable_hw_timestamp: true
      hw_timestamp_interfaces: "*"
  roles:
    - role: grzegorzfranus.chrony
```

## 📊 Variables

### General Options

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_role_action` | Define which parts of the role to execute (Options: `all`, `prerequisites`, `install`, `configure`, `logrotate`, `upgrade`) | `"all"` |
| `chrony_service_enabled` | Whether to enable Chrony service | `true` |
| `chrony_configure_logrotate` | Enable/disable logrotate configuration for Chrony logs | `false` |
| `chrony_port_disabled` | Disable NTP listening port (UDP 123) for client-only nodes | `false` |
| `chrony_bind_cmd_address_enable` | Restrict command socket binding to specific loopback addresses | `true` |
| `chrony_bind_cmd_addresses` | List of loopback addresses to bind the command interface to | `["127.0.0.1", "::1"]` |
| `chrony_run_test` | Enable test mode (useful for debugging) | `false` |

### System Clock Management

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_ntp_source_mode` | Define how this host should operate (Options: `pool`, `server`) | `"server"` |
| `chrony_ntp_servers` | List of NTP servers to sync with (with options like iburst, minpoll, maxpoll) | *See defaults/main.yml* |
| `chrony_maxdistance` | Maximum allowed root distance in seconds | `3.0` |
| `chrony_ntsdumpdir` | Directory for storing NTS cookies and keys | `"/var/lib/chrony"` |
| `chrony_num_minsources` | Minimum number of sources required for synchronization | `1` |
| `chrony_maxupdateskew` | Maximum allowed skew for updates (in ppm) | `100.0` |
| `chrony_makestep` | Step clock if offset is larger than threshold (format: `"<threshold> <limit>"`) | `"1.0 3"` |

### Security Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_command_key_id` | Key ID for NTP commands | `1` |
| `chrony_default_access` | Default access policy (deny/allow) | `"deny"` |
| `chrony_local_stratum_enable` | Enable local reference source to serve time even when unsynchronized | `false` |
| `chrony_local_stratum` | Stratum level to report when serving time unsynchronized | `10` |
| `chrony_auth_settings` | Authentication settings dictionary (enable, selectmode) | *See defaults/main.yml* |
| `chrony_ntp_clients` | List of allowed NTP clients | `[]` |

### Leap Second Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_leapsectz` | Leap seconds timezone | `"right/UTC"` |
| `chrony_leapsec_settings` | Leap second handling configuration dictionary (mode, maxslewrate, smoothtime) | *See defaults/main.yml* |

### Logging Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_log_enable` | Enable logging | `true` |
| `chrony_logdir_path` | Directory for storing log files | `"/var/log/chrony"` |
| `chrony_log_options` | What information to log | `"measurements statistics tracking"` |
| `chrony_logchange` | Log clock changes larger than specified seconds | `"0.5"` |

### Logrotate Options

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_logrotate_options` | Dictionary of logrotate settings (archive_directory_path, frequency, count, missingok, compress, nocreate, copytruncate, dateext) | *See defaults/main.yml* |

### Real-Time Clock (RTC) Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_rtc_settings` | Dictionary of RTC settings (rtcsync_enable, rtconutc_enable, rtcfile_enable, rtcfile_path, rtcautotrim_enable, rtcautotrim_interval, rtcdevice) | *See defaults/main.yml* |

### Hardware Timestamping & Reference Clock

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_hardware_settings` | Hardware timestamping and reference clock dictionary (enable_hw_timestamp, hw_timestamp_interfaces, refclock_enable, refclock_pps, refclock_precision) | *See defaults/main.yml* |

### System Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_system_settings.dumpdir` | Directory for storing clock state dumps | `"/var/lib/chrony"` |

### Temperature Compensation

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_temp_compensation` | Temperature compensation dictionary (enable, sensor_file, update_interval, config_file) | *See defaults/main.yml* |

### Client Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_client_settings` | NTP client options dictionary (use_dhcp, dhcp_sourcedir, conf_dir, initstepslew) | *See defaults/main.yml* |

## 📌 Role Properties

| Property | Value | Description |
|----------|-------|-------------|
| **Idempotent** | ✅ Yes | Running the role multiple times with the same parameters produces the same result. |
| **Atomic** | ❌ No | The role can be partially applied. A failure mid-execution may leave the system in an intermediate state (e.g. package installed but not configured). |
| **Check Mode** | ✅ Supported | Most tasks work in check mode. Mutating commands (installation, service state modification) are skipped. |
| **Diff Mode** | ✅ Supported | Template tasks support diff mode for change preview. |

## 📤 Role Output

This role does not set any public output facts.

## 🔍 Verification

After deployment, verify time synchronization is working:

### Check Chrony Status

```bash
# Check service status
sudo systemctl status chrony

# View synchronization status
sudo chronyc tracking

# Check time sources
sudo chronyc sources -v
```

### Verify Time Accuracy

```bash
# Check current time sync status
sudo chronyc tracking | grep "Last offset"

# Force time synchronization
sudo chronyc makestep

# Wait for sync and verify
sudo chronyc waitsync 30
```

### Check Logs

```bash
# View Chrony logs (if logging enabled)
sudo tail -f /var/log/chrony/chrony.log

# Check systemd logs
sudo journalctl -u chrony -f
```

## 🛡️ Security Features

- ✅ **Secure Default Configuration**: Minimal attack surface with deny-by-default access
- ✅ **File Permissions**: Proper ownership and permissions for configuration files
- ✅ **Service Isolation**: Runs with minimal required privileges
- ✅ **Authentication Support**: NTP authentication key management
- ✅ **Access Control**: Client whitelist/blacklist capability
- ✅ **Configuration Validation**: Input validation and secure defaults

### Enhanced Security Configuration

```yaml
# Restrict client access
chrony_default_access: "deny"
chrony_ntp_clients:
  - "allow 192.0.2.0/24"
  - "deny 192.0.2.100"

# Enable authentication
chrony_auth_settings:
  enable: true
  selectmode: "require"
```

### Uninstall

To remove Chrony and its configuration from a host:

```yaml
---
- name: Uninstall Chrony
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_role_action: "uninstall"
```
*Note: Service removal is typically done by running standard package uninstall commands or overriding variables to remove.*

### Roll-back Capabilities

Configuration files are backed up automatically using Ansible's `backup: true` directive. If you need to revert to a previous state:

1. Restore the configuration files from the `.bak` timestamps created in the configuration directory.
2. Restart the chrony service.

## 🔒 Security considerations

- Keep keys inside `chrony.keys` protected. Use Ansible Vault for key variables.
- Restrict command interface binding using `chrony_bind_cmd_address_enable: true` and specify loopback interfaces.

## 🧪 Check mode behavior

- Most validation and status checks run normally in Check Mode.
- Mutating commands (such as package installation and service management) are safely skipped.

## 🏷️ Tags usage

- Use `--tags` to run selective parts of the role: `always`, `chrony_setup`, `chrony_init`, `chrony_validate`, `chrony_requirements`, `chrony_install`, `chrony_configure`, `chrony_logrotate`, `chrony_upgrade`, `chrony_test`, `chrony_verify`.

## 🌐 Network resilience

- Chrony uses NTP over UDP port 123. Ensure that firewalls are configured to allow outgoing UDP port 123 traffic to the specified NTP servers or pools.

## 🧰 Repository management

- This role relies on default OS package repositories to install chrony. It does not configure custom upstream repository sources directly.

## 🔧 Troubleshooting

### Service Issues

```bash
# Check configuration syntax
sudo chronyc -n tracking

# View detailed journald logs
sudo journalctl -u chrony --no-pager -n 50
```

### Sync Issues

```bash
# Force time synchronization immediately
sudo chronyc makestep

# Check firewall rules for UDP 123
sudo nmap -p 123 -sU <ntp-server-ip>
```

### Log Rotation Problems

```bash
# Check logrotate dry run
sudo logrotate -d /etc/logrotate.d/chrony
```

## 📁 File Structure

```
ansible-role-chrony/
├── defaults/
│   └── main.yml             # Default configuration variables
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   ├── main.yml             # Role metadata and Galaxy information
│   └── argument_specs.yml   # Native argument specification validation
├── molecule/                # Molecule testing framework
│   └── default/             # Default testing scenario
├── tasks/
│   ├── main.yml             # Main task orchestration
│   ├── assert.yml           # Variable validation and system compatibility
│   ├── prerequisites.yml    # System preparation and conflict checking
│   ├── install.yml          # Package installation
│   ├── configure.yml        # Chrony configuration file and service
│   ├── logrotate.yml        # Logrotate setup
│   ├── upgrade.yml          # Package upgrade tasks
│   └── test.yml             # Validation and time check
├── templates/
│   ├── chrony/
│   │   └── chrony.conf.j2   # Main chrony configuration template
│   └── logrotate/
│       └── chrony.j2        # Logrotate configuration template
└── vars/
    ├── main.yml             # Internal variables
    ├── debian.yml           # Debian family variables
    └── redhat.yml           # RedHat/EL9 family variables
```

## 🏷️ Tags

All tags are prefixed with `chrony_` to avoid collisions.

| Tag | Description |
|-----|-------------|
| `always` | Tasks that always run (variable loading and validation) |
| `chrony_setup` | Setup tasks including OS-specific variables, requirements, installation, and configuration |
| `chrony_init` | Initial setup tasks |
| `chrony_validate` | Variable validation tasks |
| `chrony_requirements` | System requirements verification |
| `chrony_install` | Package installation tasks |
| `chrony_configure` | Service configuration tasks |
| `chrony_config` | Configuration related tasks |
| `chrony_logrotate` | Log rotation configuration tasks |
| `chrony_upgrade` | Package upgrade tasks |
| `chrony_test` | Testing and verification tasks |
| `chrony_verify` | Verification tasks |

## Example Playbooks

```yaml
---
- name: Configure Chrony NTP Synchronization
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_service_enabled: true
        chrony_ntp_source_mode: "pool"
        chrony_ntp_servers:
          - "0.pool.ntp.org iburst"
          - "1.pool.ntp.org iburst"
        chrony_log_enable: true
        chrony_configure_logrotate: true
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Submit a pull request describing your changes

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
