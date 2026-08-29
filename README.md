# Automated Deployment, Configuration, and Validation of a DataStax Enterprise 6.9 Cluster Using Ansible

## 📌 Overview

This project demonstrates the automated deployment, configuration, and validation of a **DataStax Enterprise (DSE) 6.9 cluster** using **Ansible**.

The objective is to create a repeatable and consistent deployment process for a multi-node DSE 6.9 environment while applying infrastructure-as-code and configuration-management practices.

The project covers the complete workflow from Linux server preparation to cluster validation.

> **Important:** DSE 6.9 installation requires access to the appropriate DataStax package/repository and credentials. This repository intentionally keeps the repository configuration as variables and does not contain credentials.

## 🎯 Objectives

- Prepare Linux servers for DataStax Enterprise 6.9
- Configure SSH access for Ansible
- Build an Ansible inventory
- Install required system dependencies
- Configure the DSE 6.9 installation source
- Automate DSE 6.9 installation
- Configure Cassandra/DSE cluster parameters
- Configure seed nodes
- Configure datacenter and rack information
- Manage DSE services with Ansible
- Validate cluster health
- Automate deployment across multiple nodes
- Implement reusable Ansible roles
- Document troubleshooting procedures

## 🏗️ Architecture

The laboratory consists of an Ansible control node and three DSE 6.9 nodes.

```text
                    +----------------------+
                    |  Ansible Control Node |
                    +----------+-----------+
                               |
                         SSH / Ansible
                               |
          +--------------------+--------------------+
          |                    |                    |
          v                    v                    v
   +-------------+      +-------------+      +-------------+
   |   DSE 6.9   |      |   DSE 6.9   |      |   DSE 6.9   |
   |   Node 1    |      |   Node 2    |      |   Node 3    |
   |    Linux    |      |    Linux    |      |    Linux    |
   +------+------+      +------+------+      +------+------+
          |                    |                    |
          +--------------------+--------------------+
                               |
                       DSE 6.9 Cluster
```

## 📁 Repository Structure

```text
dse-6.9-ansible-automation/
├── README.md
├── .gitignore
├── ansible.cfg
├── inventory/
│   └── hosts.yml
├── group_vars/
│   └── dse.yml
├── playbooks/
│   ├── site.yml
│   ├── install.yml
│   ├── configure.yml
│   └── validate.yml
├── roles/
│   └── dse/
│       ├── defaults/
│       │   └── main.yml
│       ├── handlers/
│       │   └── main.yml
│       ├── tasks/
│       │   ├── main.yml
│       │   ├── prerequisites.yml
│       │   ├── install.yml
│       │   ├── configure.yml
│       │   └── validate.yml
│       └── templates/
│           ├── cassandra.yaml.j2
│           └── dse.yaml.j2
└── docs/
    ├── architecture.md
    ├── deployment.md
    └── troubleshooting.md
```

## ⚙️ Prerequisites

Before using this project, make sure you have:

- An Ansible control node
- Three Linux servers for the DSE cluster
- SSH connectivity from the Ansible control node to all DSE nodes
- A user with passwordless `sudo` access, or an appropriate privilege-escalation configuration
- Access to the required DSE 6.9 package repository
- Valid DataStax repository credentials if required by your installation method

> The example IP addresses in this repository are laboratory addresses. Replace them with your actual server addresses.

## 🔧 Ansible Configuration

`ansible.cfg`:

```ini
[defaults]
inventory = inventory/hosts.yml
remote_user = ansible
host_key_checking = False
retry_files_enabled = False
roles_path = roles

[privilege_escalation]
become = True
become_method = sudo
```

## 🖥️ Example Ansible Inventory

`inventory/hosts.yml`:

```yaml
all:
  children:
    dse:
      hosts:
        dse01:
          ansible_host: 192.168.56.101
        dse02:
          ansible_host: 192.168.56.102
        dse03:
          ansible_host: 192.168.56.103
      vars:
        ansible_user: ansible
        ansible_become: true
```

Replace the example IP addresses with the addresses of your laboratory servers.

## 📊 DSE Configuration Variables

`group_vars/dse.yml` contains the main cluster configuration:

```yaml
dse_version: "6.9"
dse_cluster_name: "DSE-6.9-Training-Cluster"
dse_datacenter: "DC1"
dse_rack: "RACK1"
dse_listen_address: "{{ ansible_host }}"
dse_rpc_address: "0.0.0.0"

dse_seed_nodes:
  - "192.168.56.101"
  - "192.168.56.102"

dse_repository_url: "REPLACE_WITH_DSE_REPOSITORY"
```

Additional DSE configuration parameters should be defined according to the exact DSE 6.9 deployment architecture.

## 🚀 Deployment Workflow

```text
1. Prepare Linux Servers
            ↓
2. Configure SSH
            ↓
3. Configure Ansible Inventory
            ↓
4. Install Prerequisites
            ↓
5. Configure DSE 6.9 Installation Source
            ↓
6. Install DataStax Enterprise 6.9
            ↓
7. Configure Cassandra/DSE
            ↓
8. Configure Cluster Parameters
            ↓
9. Start DSE Services
            ↓
10. Validate Cluster
            ↓
11. Troubleshoot / Monitor
```

## 🔌 Test Ansible Connectivity

Before starting the installation:

```bash
ansible all -m ping
```

Expected result:

```text
dse01 | SUCCESS
dse02 | SUCCESS
dse03 | SUCCESS
```

## 🧪 Validate the Playbook

Check the syntax:

```bash
ansible-playbook playbooks/site.yml --syntax-check
```

Run a dry run:

```bash
ansible-playbook playbooks/site.yml --check
```

## ▶️ Deploy DSE 6.9

Run the main deployment:

```bash
ansible-playbook playbooks/site.yml
```

For detailed output:

```bash
ansible-playbook playbooks/site.yml -v
```

## 🔎 Cluster Validation

After deployment, validate the DSE cluster using the administrative tools appropriate to the DSE 6.9 installation.

For Cassandra cluster status:

```bash
nodetool status
```

Expected output should show the configured nodes in an **Up/Normal** state.

Example:

```text
Datacenter: DC1
================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving

UN  192.168.56.101
UN  192.168.56.102
UN  192.168.56.103
```

Where:

- `U` = Up
- `N` = Normal

## 🛠️ Validation Playbook

The dedicated validation playbook checks:

- DSE service state
- Cassandra CQL port
- Cluster status using `nodetool status`

Run:

```bash
ansible-playbook playbooks/validate.yml
```

## 🔐 Security

Sensitive information must never be committed to GitHub.

Do not commit:

- Passwords
- Private SSH keys
- API tokens
- DSE credentials
- TLS private keys
- Production secrets

Use **Ansible Vault** or your automation platform's credential management.

Example:

```bash
ansible-vault create group_vars/secrets.yml
```

## 🧰 Troubleshooting

### 1. Ansible connectivity

```bash
ansible dse -m ping
```

### 2. DSE service

```bash
systemctl status dse
```

### 3. Cluster status

```bash
nodetool status
```

### 4. Network connectivity

Verify connectivity between all DSE nodes and ensure the required ports are allowed by the Linux firewall.

### 5. Logs

Inspect the DSE/Cassandra logs according to the DSE 6.9 installation layout.

## 🧭 Project Focus

**Automate → Configure → Validate → Administer → Troubleshoot**

**Linux → Ansible → Cassandra → DataStax Enterprise 6.9 → Automation → Database Administration**
