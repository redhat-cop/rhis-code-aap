## Role: aap_license_download

This Ansible role automates the process of downloading the Red Hat Ansible Automation Platform (AAP) subscription/license file from the Red Hat Customer Portal using an offline token. The downloaded license can be used for AAP installations and upgrades.

### Features
- Downloads the AAP license/subscription file from Red Hat
- Uses secure offline token authentication
- Stores the license file locally for use in AAP installation

### Requirements
- Valid Red Hat subscription offline token
- Internet access from the host running the playbook
- Ansible installed

### Role Variables
Customize these variables as needed:

```yaml
aap_license_download_token: "<your_offline_token>"
aap_license_download_dest: "/path/to/license/subscription.json"
```

### Example Playbook
```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: aap_license_download
      vars:
        aap_license_download_token: "{{ vault_offline_token }}"
        aap_license_download_dest: "/tmp/subscription.json"
```

### Tasks Overview
- Download AAP license file from Red Hat
- Save license file to specified location

### License
GPL-3.0-only

### Author Information
Showroom Project Team
