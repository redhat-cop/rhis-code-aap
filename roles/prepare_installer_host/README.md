## Role: prepare_installer_host

This Ansible role prepares the installer host for deploying Red Hat Ansible Automation Platform (AAP). It ensures the host is ready by installing required packages, configuring SSH key on the Bastion host, setting up repositories, and applying recommended system settings.

### Features
- Installs essential packages (git, ansible-core, etc.)
- Configures SSH keys for passwordless access
- Sets up required repositories
- Applies recommended system settings for AAP installation
- Optionally configures firewall and SELinux

### Requirements
- Supported OS (RHEL recommended)
- Sudo/root access on the installer host
- Internet access for package installation and repository setup

### Role Variables
Customize these variables as needed:

```yaml
installer_packages:
  - git
  - ansible-core
  - python3
configure_firewall: true
configure_selinux: true
ssh_key_path: "~/.ssh/id_rsa"
```

### Example Playbook
```yaml
- hosts: installer_host
  become: true
  roles:
    - role: prepare_installer_host
      vars:
        installer_packages:
          - git
          - ansible-core
          - python3
        configure_firewall: false
        configure_selinux: true
        ssh_key_path: "~/.ssh/id_rsa"
```

### Tasks Overview
- Install required packages
- Configure SSH keys
- Set up repositories
- Apply system settings
- (Optional) Configure firewall and SELinux

### License
GPL-3.0-only

### Author
Showroom Project Team
