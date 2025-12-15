## Role: prepare_external_db_host

This role prepares an external host for running a PostgreSQL database, suitable for use with Red Hat Ansible Automation Platform (AAP) deployments. It automates the installation and configuration steps required to make the host ready for AAP database usage.

### Features
- Installs required packages for PostgreSQL
- Configures system settings for database operation
- Sets up users, groups, and permissions as needed
- Can be customized via variables in `defaults/main.yml`

### Requirements
- Target host should be a supported Linux distribution (RHEL/CentOS recommended)
- Sudo/root access on the target host
- Ansible 2.9+

### Role Variables
Variables can be set in your playbook or inventory, or overridden in `defaults/main.yml`:

```yaml
# Example variables
postgresql_packages:
  - postgresql-server
  - postgresql-contrib
postgresql_service_name: postgresql
postgresql_data_dir: /var/lib/pgsql/data
postgresql_port: 5432
# Add more variables as needed
```

### Example Playbook
```yaml
- hosts: dbserver
  become: true
  roles:
    - role: prepare_external_db_host
```

### Role Structure
- `defaults/main.yml`: Default variables for the role
- `meta/main.yml`: Role metadata and dependencies
- `tasks/main.yml`: Main tasks for preparing the external DB host

### License
GPL-3.0-only

### Author
Showroom Project Team
