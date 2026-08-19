# Custom Execution Environment for AIOps

Execution environment for the [ansible-aiops](https://github.com/iamgini/ansible-aiops) project.

## Base Image

`registry.redhat.io/ansible-automation-platform-27/ee-supported-rhel9:latest`

## What's Included

### Python Tools

| Tool | Purpose |
|---|---|
| `yamllint` | YAML syntax validation |
| `ansible-lint` | Ansible best practices checking |
| `molecule` | Ansible role/playbook testing (delegated driver) |

### System Packages

`python3-pip`, `python3-devel`, `gcc`, `pkgconf-pkg-config`, `systemd-devel`

### Collections

Collections are **not baked into the EE** — they are installed via AAP project sync or Private Automation Hub. The required collections for the aiops project are:

| Collection | Purpose |
|---|---|
| `ansible.mcp` | MCP client for AAP integration |
| `ansible.utils` | Utility functions (dependency of ansible.mcp) |
| `ansible.scm` | Git operations (clone, branch, commit, push) |
| `ansible.eda` | Event-driven automation |
| `ansible.controller` | AAP controller modules (project, JT, WF, job_launch) |
| `ansible.platform` | AAP platform integration |

To bake collections into the EE instead, uncomment the `galaxy.collections` list in `execution-environment.yml`.

## Build

```bash
ansible-builder build -t custom-ee-aiops:latest
```

## Build with Private Automation Hub

Uncomment the `prepend_galaxy` section in `execution-environment.yml` and pass PAH tokens:

```bash
ansible-builder build -t custom-ee-aiops:latest \
  --build-arg ANSIBLE_GALAXY_SERVER_RH_CERTIFIED_TOKEN=your_token \
  --build-arg ANSIBLE_GALAXY_SERVER_PAH_COMMUNITY_TOKEN=your_token
```

## Use with ansible-navigator

```bash
ansible-navigator run playbook.yml --eei custom-ee-aiops:latest -m stdout
```

Or set in `ansible-navigator.yml`:

```yaml
ansible-navigator:
  execution-environment:
    image: custom-ee-aiops:latest
```

## Push to Registry

```bash
podman tag custom-ee-aiops:latest registry.example.com/custom-ee-aiops:latest
podman push registry.example.com/custom-ee-aiops:latest
```

## Tool Availability Comparison

| Tool | AAP 2.6 EE | AAP 2.7 EE | custom-ee-aiops |
|---|---|---|---|
| `ansible-core` | 2.16.19 | 2.16.19 | 2.15.13 |
| `ansible-lint` | 25.8.2 | No | 6.22.2 |
| `yamllint` | 1.35.1 | No | 1.37.1 |
| `molecule` | No | No | 6.0.3 |
