# Troubleshooting

## Ansible Connectivity

Test connectivity:

```bash
ansible dse -m ping
```

If this fails, check:

- SSH connectivity
- Inventory IP addresses
- Ansible username
- SSH key configuration
- `sudo` permissions
- Firewall rules

## DSE Service

Check the service:

```bash
systemctl status dse
```

Restart if appropriate:

```bash
systemctl restart dse
```

## Cluster Status

Run:

```bash
nodetool status
```

The expected healthy state is generally represented by `UN`:

- `U` = Up
- `N` = Normal

## CQL Connectivity

The deployment waits for TCP port `9042`.

Check the listener:

```bash
ss -lntp | grep 9042
```

Check local connectivity:

```bash
nc -zv 127.0.0.1 9042
```

## Network Connectivity

Verify connectivity between all DSE nodes.

Ensure required ports are permitted by the Linux firewall and any network security controls.

## Configuration

Review:

```text
/etc/dse/cassandra/cassandra.yaml
```

Confirm:

- Cluster name
- Listen address
- RPC address
- Seed nodes

## Logs

Inspect DSE/Cassandra logs according to the DSE 6.9 installation layout.

## Repository / Package Errors

If the DSE package cannot be installed, verify:

1. The approved DataStax repository is configured.
2. The required credentials are valid.
3. The target operating system is supported by the selected DSE 6.9 installation method.
4. The package manager can reach the repository.

Do not commit repository credentials to GitHub.

## Safe Secret Handling

Use Ansible Vault:

```bash
ansible-vault create group_vars/secrets.yml
```

Avoid placing passwords, tokens, private keys, or production secrets in source-controlled files.
