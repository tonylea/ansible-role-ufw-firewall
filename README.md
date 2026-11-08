# ufw_firewall

A host firewall with ufw:

- Incoming traffic blocked by default, outgoing allowed.
- SSH allowed only from the networks you give, and rate-limited.
- The firewall starts at boot.

Other roles add their own rules on top; this role never removes them.

The role refuses to run when no SSH networks are given, so it can't lock everyone out. SSH rules are added before
the firewall is enabled, so the session running Ansible survives.

Tested on Debian trixie (Raspberry Pi OS).

## Variables

| Variable | Default |
| --- | --- |
| `ufw_firewall_ssh_sources` | `[]` (required: the networks allowed to SSH in) |

## Requirements

The `community.general` collection, pinned in `requirements.yml`.

## Usage

```yaml
- hosts: k3s_cluster
  become: true
  roles:
    - role: ufw_firewall
      vars:
        ufw_firewall_ssh_sources:
          - 192.168.10.0/24
```

## Development

Tests run with Molecule in a Debian trixie container running systemd. They read the rules loaded in the kernel
(`iptables -S`), check the firewall starts at boot, and check the role stops when given no SSH networks.

```sh
pip install -r requirements-test.txt
molecule test
```

Commits follow Conventional Commits. Releases are versioned and tagged automatically from `main`.
