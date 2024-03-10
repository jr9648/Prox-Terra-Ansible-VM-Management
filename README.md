# Purpose
These Ansible playbooks are designed to manage the initialization and removal of a VM component in the system using Terraform. The playbooks handle the initialization and cleanup tasks related to the terraform, ensuring proper configuration and removal without disrupting the service.
## Playbook Structure
The repository contains three main Ansible playbooks:

# Playbooks Overview

## 1. initialize.yml

This playbook initializes the system, setting the 'In Progress' status for the initialization phase where it gathers necessary facts, imports the role for initializing the base system, and finally sets the 'Complete' status for the initialization phase.

### Tasks Accomplished:
- Change SSHD Config - Password Authentication
- Change SSHD Config - Permit Root Login
- Change Sudoers Config - Sudo with No Password
- Restart SSHD
- Install Ansible Authorized Key
- Install Terraform Dependencies
- Add HashiCorp GPG Key
- Add HashiCorp Repo
- Update Apt-get Repo and Cache
- Install Terraform Dependencies (Again)
- Create Terraform Environment
- Copy Provider Config
- Create Terraform Credentials File
- Init Terraform

## 2. deploy_gateway.yml

This playbook is responsible for deploying the gateway virtual machines. It sets the 'In Progress' status for the gateway deployment phase, imports the role for configuring the gateway base, and sets the 'Complete' status for the gateway deployment phase.

### Tasks Accomplished:
- Check if a Terraform plan file exists for the specified UUID.
- Check if an SSH public key exists for the specified UUID.
- Create a directory for SSH if it doesn't exist.
- Define an empty list sgw_pve_nodes if a plan file doesn't exist and the item state is "present".
- Generate SSH key pairs if the SSH public key doesn't exist and the item state is "present".
- Create a Terraform plan file from a template if it doesn't exist and the item state is "present".
- Apply Terraform configuration using terraform apply for each node if a plan file doesn't exist and the item state is "present".

## 3. remove_gateway.yml

This playbook allows the user to confirm their intention to stop or remove the gateway component. If confirmed, it sets the 'In Progress' status for the gateway removal phase, gathers necessary facts, imports the role for cleaning up the gateway, and finally sets the 'Complete' status for the gateway removal phase.

### Tasks Accomplished:
- Stop Gateways
  - Stops the gateway virtual machines using the proxmox_kvm module.
  - Loops over item.nodes and stops each VM specified by its UUID and index.
- Remove Gateway and Ceph Auth Keys
  - Destroys VMs using Terraform by executing terraform destroy.
  - Removes the locally stored Terraform plan file.
  - Removes locally stored SSH keys ({{ item.uuid }} and {{ item.uuid }}.pub) on the local host.
  - Removes Ceph key from monitors using the ceph auth del command delegated to the first monitor.
  - Removes the locally stored Ceph key/secret from /opt/vm-config/ceph/{{ item.uuid }}.
   
