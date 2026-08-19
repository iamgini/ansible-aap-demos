# Custom Execution Environment for AIOps

Execution environment for the [ansible-aiops](https://github.com/iamgini/ansible-aiops) project.

## Collections Included

| Collection | Purpose |
|---|---|
| `ansible.mcp` | MCP client for AAP integration |
| `ansible.utils` | Utility functions (dependency of ansible.mcp) |
| `ansible.scm` | Git operations (clone, branch, commit, push) |
| `ansible.eda` | Event-driven automation |
| `ansible.controller` | AAP controller modules (project, JT, WF, job_launch) |
| `ansible.platform` | AAP platform integration |

## Build

```bash
ansible-builder build -t ansible-ee-aiops:latest
```

## Build with Private Automation Hub

Uncomment the `prepend_galaxy` section in `execution-environment.yml` and pass PAH tokens:

```bash
ansible-builder build -t ansible-ee-aiops:latest \
  --build-arg ANSIBLE_GALAXY_SERVER_RH_CERTIFIED_TOKEN=your_token \
  --build-arg ANSIBLE_GALAXY_SERVER_PAH_COMMUNITY_TOKEN=your_token
```

## Use with ansible-navigator

```bash
ansible-navigator run playbook.yml --eei ansible-ee-aiops:latest -m stdout
```

Or set in `ansible-navigator.yml`:

```yaml
ansible-navigator:
  execution-environment:
    image: ansible-ee-aiops:latest
```

## Push to Registry

```bash
podman tag ansible-ee-aiops:latest registry.example.com/ansible-ee-aiops:latest
podman push registry.example.com/ansible-ee-aiops:latest
```
