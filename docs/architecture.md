# Architecture

## Overview

The laboratory architecture contains:

1. An Ansible control node
2. Three Linux servers running DSE 6.9
3. SSH connectivity between the control node and DSE nodes
4. A three-node DSE cluster

## Nodes

| Node | Example IP | Role |
|---|---|---|
| dse01 | 192.168.56.101 | DSE cluster node / seed |
| dse02 | 192.168.56.102 | DSE cluster node / seed |
| dse03 | 192.168.56.103 | DSE cluster node |

The addresses are examples for a laboratory environment and must be replaced for another environment.

## Data Flow

```text
Ansible Control Node
        |
        | SSH / Ansible
        |
        +------------------+------------------+
        |                  |                  |
      dse01              dse02              dse03
        |                  |                  |
        +------------------+------------------+
                           |
                      DSE 6.9 Cluster
```

## Important Configuration

The cluster configuration is controlled through `group_vars/dse.yml`.

Key values include:

- Cluster name
- Datacenter
- Rack
- Listen address
- RPC address
- Seed nodes
- Repository configuration
