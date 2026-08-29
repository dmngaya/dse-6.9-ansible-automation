# Deployment Guide

## 1. Prepare the Servers

Prepare three supported Linux servers and ensure:

- DNS or host resolution is configured as required
- SSH access is available
- The Ansible user has the required privileges
- Required network connectivity exists between all nodes

## 2. Configure Inventory

Edit:

```text
inventory/hosts.yml
```

Replace the example IP addresses.

## 3. Configure DSE Variables

Edit:

```text
group_vars/dse.yml
```

Set the cluster name, addresses, seed nodes, and approved DSE repository configuration.

Do not place passwords or other secrets in this file.

## 4. Test Connectivity

```bash
ansible all -m ping
```

## 5. Check Playbook Syntax

```bash
ansible-playbook playbooks/site.yml --syntax-check
```

## 6. Run Check Mode

```bash
ansible-playbook playbooks/site.yml --check
```

## 7. Deploy

```bash
ansible-playbook playbooks/site.yml
```

## 8. Validate

```bash
ansible-playbook playbooks/validate.yml
```

You can also run:

```bash
ansible dse -a "nodetool status"
```

## 9. Verify Cluster State

The expected cluster state is that the configured nodes are available and appear in the appropriate Up/Normal state.

## Repository Credentials

DSE 6.9 installation requires access to the appropriate DataStax package/repository. Configure that access according to your organization's approved method.

Credentials should be handled with Ansible Vault or an external secret/credential manager.
