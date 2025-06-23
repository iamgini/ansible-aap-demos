# Ansible Automation Platform API

## Test API from CLI

```shell
$ curl --location 'https://aap25.lab.iamgini.com/api/controller/v2/job_templates/15/launch/' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <removed>' \
    --data '{"defaults": {"verbosity": 3}, "extra_vars":{"your_name": "John"}}'
```

## Test API from API WEBUI

Eg: `https://aap25.lab.iamgini.com/api/controller/v2/job_templates/18/launch/`

Under content:

```json
{
  "extra_vars": {
    "your_name": "Gineesh",
    "env": "dev",
    "nodes": "App-A"
  },
  "verbosity": 3
}
```