# Ansible Automation Platform (AAP) Deployment and Configuration

This repository contains Ansible roles and playbooks for designing, deploying, and configuring Red Hat Ansible Automation Platform (AAP) 2.5 environments using Config as Code methodology.

## Overview

The automation leverages Ansible good practices and GitOps principles to ensure consistency, repeatability, and maintainability across environments. This example deployment uses HashiCorp Vault as external vault and netbox inventory.

## Prerequisites

### System Requirements

- Ansible 2.9 or higher
- Python 3.8+
- SSH access to target hosts
- Red Hat Enterprise Linux 9.x or 10.x
- All nodes except DB with minimum 16GB RAM, 4 core CPU, >)3000 IOPS and >=60GB disk, partition for `/tmp` >=10GB, `/var/tmp` >=5GB, `<home of aap installation user>` >=20GB
- DB can be used installer managed or external; minimum 32GB memory for installer managed database
- x86_64, aarch6

### Authentication Requirements
- dedicated non-root user on all AAP nodes including Bastion host as a service account (normal account) for AAP Deployment (uid must be > 500)
- SSH public key authentication for the above non-root user from Bastion host to all AAP nodes
- SSH private key for the technical user is stored on external Vault `aap_service_user_ssh_private_key`

### General Requirements
- forward and reverse DNS records configured for all  nodes
- NTP configuration for all nodes
- FQDN properly configured on all nodes
- common DNS name registered on DNS to point to the LB
- selinux enabled on all nodes
- /var/tmp mounted without noexec rights, can be changed after deployment
- Shared NFS storage is configured that PAH nodes can consume
- local firewall configurations have the required ports open or not in place
- passwordless sudo is configured and allowed on all AAP nodes

#### For disconnected Installation only
- /etc/pip.conf configured on Bastion host that allows access to custom PyPi repository for Python dependencies
`hvac` - for HashiCorp Vault when using HashiCorp community collection
`pexpect` - for AAP Backup
`pytz` - for Netbox collection
`pynetbox` - for Netbox Collection

### Subscription Requirements
- valid RH AAP subscriptions exist and allocations configured
- RHEL >= 9.4 BaseOS repositories configured on all nodes
- RHEL >= 9.4 AppStream repositories configured on all nodes

#### For disconnected Installation only
- Manifest file downloaded on the Bastion host on `/home/aap/aap_downloads` folder with a
special name `license.zip` (or the name can be set on the inventory as variables)
- `yq` package is required for exporting configuration as code using infra.aap_configuration_extended collection.
- Ansible Automation Platform 2.5 repository on Bastion host (to be able to deploy ansible-
builder package for creating custom execution environment)

### Development Requirements
#### For disconnected Installation only

validated and/or certified collections needed on the Bastion host or in the Git repository

- infra.aap_utilities: >= 2.8.0
- ansible.posix: >= 2.1.0
- kubernetes.core: >= 6.2.0
- infra.aap_configuration: >= 3.8.3
- ansible.eda: >= 2.5.0
- ansible.hub: >= 1.0.0
- ansible.platform: >= 2.5.0
- ansible.controller: >= 4.6.0
- infra.aap_configuration_extended: >= 3.0.2
- ansible.hub: >1.0.0
- ansible.platform: >= 2..5.0
- ansible.controller: >= 4.6.0
- infra.aap_configuration: >= 3.4.0
- infra.ee_utilities: >= 4.2.0
- ansible.utils: >= 6.0.0
- community.hashi_vault >= 6.2.0
- netbox.netbox: >= 3.21.0
- redhat-rhel_system_roles: >= 1.108.6
- community.general >= 12.0.0
- community.postgresql >= 4.1.0
- containers.podman >= 1.18.0
- redhat.rhel_system_roles >= 1.108.6

### Integrations
- Certificates are generated and stored on external Vault
- Chain certificate consisting of RootCA and IntermediateCA certificates, stored on external Vault
- access to git repositories from Bastion host and Controller nodes
- access to LDAP from Gateway nodes
- groups are defined on LDAP that will grant access
- access to SMTP Server from Controller nodes
- access to external vault from Controller nodes, Git and Bastion host

### Vault

Setting following credentials on the External Vault

- `gateway_admin_password_secret`
- `gateway_pg_password_secret`
- `controller_admin_password_secret`
- `controller_pg_password_secret`
- `hub_admin_password_secret`
- `hub_pg_password_secret`
- `eda_admin_password_secret`
- `eda_pg_password_secret`
- `postgresql_admin_username_secret`
- `postgresql_admin_password_secret`
- `aap_service_user_ssh_private_key`
- `aap_install_vault_token_secret`

#### For disconnected Installation only

Setting the following credentials on the External Vault

- `registry_username_secret`
- `registry_password_secret`
- `aap_setup_down_offline_token_secret`
- `aap_setup_automation_hub_token_secret`

### Hardening

- Allow only necessary users to access to the Bastion host with SSH protocol.

### Certificates

Certificates are generated and stored on External Vault

- gateway.crt
- gateway.key
- controller.crt
- controller.key
- hub.crt
- hub.key
- eda.crt
- eda.key
- postgre.crt
- postgre.key

Chain certificate consisting of RootCA and IntermediateCA certificates, stored on External Vault

- `ca-chain.crt`

## Quick Start

### 1. Preparation

- all prerequisites as described in the requirements section.
- Log in to the Bastion host.
- Switch to the aap user.
- Create the working directory:

  ```bash
    mkdir /home/aap/git
  ```
- Install Ansible Core:

  ```bash
    sudo dnf install ansible-core
  ```

### 2. Clone the Repository
- Clone the repositories:

  ```bash
  git clone git@github.com:redhat-cop/rhis-code-aap.git
  cd rhis-code-aap
  ```

### 3. Update inventory variables

Review & adjust inventory variables based on the environment in the cloned inventory-aap
repository as needed.

- Review and update host definitions on the file inventory-dev.yml
- Review adjust variables for NFS that are used for AAP backup in the file `group_vars/bastion/nfs_mount.yml`.
- Review adjust variables for NFS that are used for Private Automation Hub in the file `group_vars/automationhub/nfs_mount.yml`.
- Review and adjust external Vault secret lookups for the deployment in the file `group_vars/bastion/aap_secrets.yml`.
- Review and adjust external Vault certificate lookups for the deployment in the file `group_vars/bastion/aap_cert_secrets.yml`.
- Review and adjust AAP deployment variables, such as version number or inventory variables in the file `group_vars/bastion/aap_install_config.yml`.
- Review and adjust external Vault variables in the file `group_vars/all/hashicorp_authentication.yml`.
- Review and adjust Galaxy requirements for the Bastion host in the file `group_vars/all/galaxy_requirements.yml`.
 - Review and adjust proxy definition variables in the file `group_vars/all/proxy.yml`.

#### For disconnected Installation only
- Download the subscription manifest and save it as:
`/home/aap/aap_downloads/license.zip`
- Download the AAP installer from Red Hat and place it in the folder:
`/home/aap/aap_downloads/`
- Ensure that all the required collections are published from the internal sources to be able to deploy.

### 4. Prepare external Vault Authentication

- Create a file named `creds_env.sh` for placing the role_id and secret id definitions as environment variables.

  ```bash
  vi creds_env.sh
  ```

- File content looks like below

  ```bash
  #!/bin/bash
  export ANSIBLE_HASHI_VAULT_SECRET_ID=<secret_id>
  export ANSIBLE_HASHI_VAULT_ROLE_ID=<role_id>
  ```

  and set it executable

  ```bash
  chmod 700 creds_env.sh
  ```

- Export the external Vault connection definitions from the file. Do not export using from
command line to make credentials visible in the history for security reasons.

  ```bash
  source creds_env.sh
  ```

### 5. Initialize Bastion host for Deployment

- Run the playbook that prepares Bastion host.

  ```bash
  cd /home/aap/git/rhis-code-aap
  ansible-playbook -i inventory/inventory-dev.yml playbooks/landscape_init.yml
  ```

### 6. Run Deployment Playbook
- Run the playbook that prepares Bastion host.

  ```bash
  cd /home/aap/git/rhis-code-aap
  ansible-playbook -i inventory/inventory-dev.yml playbooks/landscape.yml
  ```
