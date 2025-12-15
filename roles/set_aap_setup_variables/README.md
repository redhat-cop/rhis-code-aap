#
## Role: set_aap_setup_variables

This Ansible role centralizes and manages all variables required for Red Hat Ansible Automation Platform (AAP) setup and installation. It provides a single source of truth for configuration values, making deployments consistent and easier to maintain.

### Features
- Defines and organizes all AAP setup variables
- Supports environment-specific overrides
- Integrates with Ansible Vault for secrets
- Makes variables available to dependent roles and playbooks

### Requirements
- No special requirements; used as a dependency by other roles

### Role Variables
Customize these variables as needed:

```yaml
aap_version: "2.5"
aap_license_file: "/tmp/license.zip"
aap_controller_admin_user: "admin"
aap_controller_admin_password: "CHANGEME"  # Use Vault for secrets
aap_inventory_file: "inventory.yml"
# Add more variables as needed for your environment
```

### Example Playbook
```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: set_aap_setup_variables
      vars:
        aap_version: "2.5"
        aap_license_file: "/tmp/license.zip"
        aap_controller_admin_user: "admin"
        aap_controller_admin_password: "{{ vault_controller_admin_password }}"
        aap_inventory_file: "inventory.yml"
```

### Tasks Overview
- Set all required variables for AAP installation
- Make variables available to other roles and playbooks

### License
GPL-3.0-only

### Author
Showroom Project Team
