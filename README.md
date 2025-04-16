# Ansible Role: Chrony

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-chrony)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-chrony)](https://github.com/grzegorzfranus/ansible-role-chrony/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-chrony/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-chrony/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures Chrony, a versatile implementation of the Network Time Protocol (NTP). It provides a robust and secure time synchronization solution with features like NTP server/pool configuration, log rotation management, and service state control.

## Main Actions

- Handle system requirements (stop conflicting services)
- Install Chrony package
- Configure Chrony service
- Manage service state
- Set up NTP servers/pools
- Configure log rotation
- Upgrade package
- Verify time synchronization

## Requirements

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Focal) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Oracle Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role requires root access for some tasks. Make sure that you are using a user with root privileges.

## Role Variables

### General Options

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_role_action` | Define which parts of the role to execute (Options: 'all', 'install', 'config') | `"all"` |
| `chrony_service_name` | Name of the Chrony service | `chrony` |
| `chrony_service_enabled` | Whether to enable Chrony service | `true` |
| `chrony_service_state` | Desired state of Chrony service | `started` |
| `chrony_configure_logrotate` | Enable/disable logrotate configuration for Chrony logs | `false` |
| `chrony_run_test` | Enable test mode (useful for debugging) | `false` |

### System Clock Management

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_ntp_source_mode` | Define how this host should operate (Options: 'server', 'client') | `"server"` |
| `chrony_ntp_servers` | List of NTP servers to sync with (with options like iburst, minpoll, maxpoll) | See defaults/main.yml |
| `chrony_rtcsync_enable` | Enable kernel synchronization of the real-time clock (RTC) | `true` |
| `chrony_maxdistance` | Maximum allowed root distance in seconds | `3.0` |
| `chrony_ntsdumpdir` | Directory for storing NTS cookies and keys | `/var/lib/chrony` |
| `chrony_num_minsources` | Minimum number of sources required for synchronization | `1` |
| `chrony_maxupdateskew` | Maximum allowed skew for updates (in ppm) | `100.0` |
| `chrony_makestep` | Step clock if offset is larger than threshold (format: "<threshold> <limit>") | `"1.0 3"` |
| `chrony_driftfile_path` | Path to the drift file | `/var/lib/chrony/drift` |

### Security Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_keyfile_path` | Path to the keys file | `/etc/chrony/chrony.keys` |
| `chrony_command_key_id` | Key ID for NTP commands | `1` |
| `chrony_default_access` | Default access policy (deny/allow) | `"deny"` |
| `chrony_auth_settings` | Authentication settings (enable, selectmode) | See defaults/main.yml |
| `chrony_ntp_clients` | List of allowed NTP clients | `[]` |

### Leap Second Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_leapsectz` | Leap seconds timezone | `"right/UTC"` |
| `chrony_leapsec_settings` | Leap second handling configuration | See defaults/main.yml |

### Logging Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_log_enable` | Enable logging | `true` |
| `chrony_logdir_path` | Directory for storing log files | `/var/log/chrony` |
| `chrony_log_options` | What information to log | `"measurements statistics tracking"` |
| `chrony_logchange` | Log clock changes larger than specified seconds | `"0.5"` |

### Logrotate Options

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_logrotate_options` | Dictionary of logrotate settings | See below |
| `chrony_logrotate_options.archive_directory_path` | Directory where archived logs will be stored | `/var/log/chrony` |
| `chrony_logrotate_options.frequency` | How often to rotate logs | `"daily"` |
| `chrony_logrotate_options.count` | Number of rotated log files to keep | `30` |
| `chrony_logrotate_options.missingok` | Don't error if log file is missing | `true` |
| `chrony_logrotate_options.compress` | Compress rotated logs using gzip | `true` |
| `chrony_logrotate_options.nocreate` | Don't create new empty log file | `true` |
| `chrony_logrotate_options.copytruncate` | Don't use copy+truncate - use default move | `false` |
| `chrony_logrotate_options.dateext` | Add date extension to rotated logs | `true` |

### Hardware Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_hardware_settings.enable_hw_timestamp` | Enable hardware timestamping | `true` |
| `chrony_hardware_settings.hw_timestamp_interfaces` | Network interfaces for hardware timestamping | `"*"` |
| `chrony_hardware_settings.refclock_enable` | Enable reference clock | `false` |
| `chrony_hardware_settings.refclock_pps` | Path to PPS device | `"/dev/pps0"` |
| `chrony_hardware_settings.refclock_precision` | Precision for reference clock | `"1e-7"` |
| `chrony_hardware_settings.rtcautotrim` | Interval for automatic RTC trimming | `30` |

### System Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_system_settings.dumpdir` | Directory for storing clock state dumps | `"/var/lib/chrony"` |

### Temperature Compensation

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_temp_compensation.enable` | Enable temperature compensation | `false` |
| `chrony_temp_compensation.sensor_file` | Path to temperature sensor file | `"/sys/class/thermal/thermal_zone0/temp"` |
| `chrony_temp_compensation.update_interval` | How often to update compensation | `30` |
| `chrony_temp_compensation.config_file` | Path to tempcomp config file | `"/etc/chrony/tempcomp"` |

### Client Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `chrony_client_settings.use_dhcp` | Use NTP servers provided by DHCP | `false` |
| `chrony_client_settings.dhcp_sourcedir` | Directory for DHCP NTP server info | `"/run/chrony-dhcp"` |
| `chrony_client_settings.conf_dir` | Directory for extra config files | `"/etc/chrony/conf.d"` |
| `chrony_client_settings.initstepslew.threshold` | Threshold for initial step correction | `30` |
| `chrony_client_settings.initstepslew.enable` | Enable rapid synchronization at startup | `true` |

## Tags

- `always` - Tasks that always run (variable loading and validation)
- `setup` - Setup tasks including OS-specific variables, requirements, installation, and configuration
- `init` - Initial setup tasks
- `validate` - Variable validation tasks
- `check` - Validation and verification tasks
- `requirements` - System requirements verification
- `install` - Package installation tasks
- `configure` - Service configuration tasks
- `config` - Configuration related tasks
- `logrotate` - Log rotation configuration tasks
- `upgrade` - Package upgrade tasks (tagged with 'never' by default)
- `test` - Testing and verification tasks
- `verify` - Verification tasks

## Example Playbook

```yaml
---
- name: Configure Chrony Time Synchronization
  hosts: all
  roles:
    - role: grzegorzfranus.chrony
      vars:
        # Basic Configuration
        chrony_service_enabled: true
        chrony_service_state: started
        chrony_configure_logrotate: true
        
        # NTP Server Configuration
        chrony_ntp_source_mode: "client"
        chrony_ntp_servers:
          - "0.pool.ntp.org iburst minpoll 4 maxpoll 8"
          - "1.pool.ntp.org iburst minpoll 4 maxpoll 8"
          - "time.google.com iburst"
          - "time.cloudflare.com iburst"
        
        # Advanced Time Settings
        chrony_rtcsync_enable: true
        chrony_maxdistance: 2.0
        chrony_num_minsources: 2
        chrony_maxupdateskew: 100.0
        chrony_makestep: "0.1 3"
        
        # Logging Configuration
        chrony_log_enable: true
        chrony_log_options: "measurements statistics tracking rtc"
        
        # Log Rotation Settings
        chrony_logrotate_options:
          frequency: "daily"
          count: 14
          compress: true
          missingok: true
          dateext: true
        
        # Hardware Settings
        chrony_hardware_settings:
          enable_hw_timestamp: true
          hw_timestamp_interfaces: "*"
          rtcautotrim: 30
        
        # System Settings
        chrony_system_settings:
          dumpdir: "/var/lib/chrony"
        
        # Client Access Control (if acting as server)
        chrony_ntp_clients:
          - "allow 10.0.0.0/8"
          - "allow 172.16.0.0/12"
          - "allow 192.168.0.0/16"

# Example of using the role with different settings for different host groups
- name: Configure Chrony NTP Servers
  hosts: ntp_servers
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_ntp_source_mode: "server"
        chrony_ntp_servers:
          - "0.pool.ntp.org iburst"
          - "1.pool.ntp.org iburst"
        chrony_ntp_clients:
          - "allow 10.0.0.0/8"
        chrony_hardware_settings:
          enable_hw_timestamp: true
          refclock_enable: true
          refclock_pps: "/dev/pps0"

- name: Configure Chrony NTP Clients
  hosts: workstations
  roles:
    - role: grzegorzfranus.chrony
      vars:
        chrony_ntp_source_mode: "client"
        chrony_ntp_servers:
          - "ntp1.internal.example.com iburst"
          - "ntp2.internal.example.com iburst"
        chrony_log_options: "tracking rtc"
```

## Testing

This role includes Molecule tests for multiple platforms:

```bash
# Run tests for all platforms
molecule test

```

## License

Apache-2.0

## Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).

## Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.