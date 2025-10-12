# Automation Dashboard

Tested with AAP 2.5 and Dashboard for AAP containerized installer 2.6.

```shell
# sample variable file for auth - ~/.config/aap-auth.yaml
aap_auth_provider_client_id: <client_id from AAP>
aap_auth_provider_client_secret:  <client_secret from AAP>
aap_auth_provider_host: aap.example.org
```

```shell
# sample variable file for Red Hat Registry credential - ~/.config/registry.yaml
registry_username: '<registry-username>'
registry_password: '<token>'
```

```shell
# Sample variable file for secret values
$ ansible-vault view dashboard-credential.yaml
Vault password:
postgresql_admin_password: postgresadminsecretpass
dashboard_admin_password: aapadmin
dashboard_pg_password: aapdashboardpgadminsecretpass
```

Note: You could put all these variables in one file, but I prefer keeping them separate to avoid mixing up credentials.

## Run installer

```shell
$ ansible-playbook -i inventory \
    ansible.containerized_installer.dashboard_install \
    -u ec2-user \
    -e @dashboard-credential.yaml
    -e @~/.config/aap-auth.yaml \
    -e @~/.config/registry.yaml
```

## Configure clusters

Sample `clusters.yaml`

```yaml
---
clusters:
  - protocol: https
    address: aap.example.org
    port: 443
    access_token: <access token from AAP>
    verify_ssl: false
    sync_schedules:
      - name: Every 1 minutes sync
        rrule: DTSTART;TZID=Europe/Ljubljana:20250630T070000 FREQ=MINUTELY;INTERVAL=1
        enabled: true
```

```shell
$ podman cp clusters.yaml automation-dashboard-web:/
$ podman exec automation-dashboard-web /venv/bin/python ./manage.py setclusters /clusters.yaml

# Use the following command to test your Automation Dashboard configuration by manually fetching data:
$ podman exec automation-dashboard-web /venv/bin/python ./manage.py syncdata --since=2025-04-01 --until=2025-12-31
```