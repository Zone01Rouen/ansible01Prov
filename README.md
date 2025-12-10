# ansible01Prov

Ansible playbooks for provisioning a development environment with various tools and software.

## Overview

This repository contains two main Ansible playbooks:

1. **provision.yml** - Main provisioning playbook that installs and configures:
   - Latest version of Golang (from official website)
   - Visual Studio Code with plugins (Go, Python, JavaScript)
   - Google Chrome browser
   - Node.js via npm
   - @conotion/cli via npm
   - WiFi configuration (SSID: CampusStMarc with password: Thunderbolt)
   - Removes previous known WiFi networks

2. **reset_provision.yml** - Reset and provisioning playbook that:
   - Resets the 'student' user (removes and recreates)
   - Runs the main provisioning playbook

## Prerequisites

- Ubuntu/Debian-based Linux system
- Ansible installed (version 2.9 or later)
- sudo/root access
- Internet connection

## Usage

### Run the main provisioning playbook

```bash
ansible-playbook provision.yml
```

This will install all the required software and configure the system.

### Run with custom WiFi credentials (recommended for security)

```bash
ansible-playbook provision.yml --extra-vars "wifi_password=YourSecurePassword"
```

### Run the reset and provision playbook

```bash
ansible-playbook reset_provision.yml
```

**WARNING:** This will completely remove and recreate the 'student' user, including all their data!

### Dry run (check mode)

To see what changes would be made without actually making them:

```bash
ansible-playbook provision.yml --check
```

## Security Best Practices

- **WiFi Credentials:** For production use, avoid hardcoding WiFi passwords in the playbook. Use:
  - `--extra-vars` to pass credentials at runtime
  - Ansible Vault to encrypt sensitive data
  - Environment variables
  
  Example with Ansible Vault:
  ```bash
  # Create encrypted variables file
  ansible-vault create secrets.yml
  
  # Run playbook with vault
  ansible-playbook provision.yml --extra-vars "@secrets.yml" --ask-vault-pass
  ```

## Configuration

- **ansible.cfg** - Ansible configuration file
- **inventory** - Inventory file defining localhost as the target

## Notes

- The playbooks are designed to run on localhost
- Some tasks may require sudo/root privileges
- WiFi configuration requires NetworkManager to be installed
- VSCode extensions are installed for the 'student' user
- The reset playbook will completely remove and recreate the 'student' user
- For production deployments, secure sensitive credentials using Ansible Vault
- The playbooks gracefully handle situations where software is already installed

## Customization

You can customize the following variables in the playbooks:

- `golang_version` - Specific Golang version (defaults to latest)
- `vscode_extensions` - List of VSCode extensions to install
- `wifi_ssid` - WiFi network SSID
- `wifi_password` - WiFi network password
- `student_user` - Username to reset/configure (defaults to 'student')

## License

This project is open source and available under the MIT License.