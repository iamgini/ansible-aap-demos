# ansible-aap-demos
Demo Repo for Ansible Automation Platform

## Configuring `requirements.yml`

```yaml
collections:

# from galaxy.ansible.com, no authentication required.
- name: cisco.ios
  version: 5.2.0
  source: https://galaxy.ansible.com

# from Private Automation Hub
- name: amazon.aws
  version: 6.5.0
  source: https://aap-rhel-92-1.lab.local:444/api/galaxy/content/rh-certified/

# from Private Automation Hub
- name: iamgini.demo_colelction_roles_modules
  version: 1.0.1
  source: https://aap-rhel-92-1.lab.local:444/api/galaxy/content/community/ 
```

## Ignore SSL Certificate validation

In `ansible.cfg`,

```ini
.
.
.
[galaxy]
server_list = pah_community, rh_certified, release_galaxy
ignore_certs = True
.
.
```

## Configuring Authentication

- To pull collections from Ansible Galaxy, you do not require authentication usually.
- To pull collections from Red Hat Automation Hub or Private Automation Hub (On-premise), You need to pass the credential.

### Method 1: Keep Galaxy Token in the `ansible.cfg`

```ini
[galaxy]
server_list = pah_community, rh_certified, release_galaxy

[galaxy_server.pah_community]
url=https://aap-rhel-92-1.lab.local:444/api/galaxy/content/community/
token=token_goes_here

[galaxy_server.rh_certified]
url=https://aap-rhel-92-1.lab.local:444/api/galaxy/content/rh-certified/
token=token_goes_here

[galaxy_server.release_galaxy]
url=https://galaxy.ansible.com/
```

But there is a risk if you are pushing this `ansible.cfg` to Git server as you cannot keep this plain text token inside this file.

Alternatively you can keep your `ansible.cfg` outside of the repository (eg: `/home/myname/.ansible.cfg`) and use it.


### Method 2: Pass the Token as Environment Variable

As well as defining these server options in the ansible.cfg file, you can also define them as environment variables. The environment variable is in the form `ANSIBLE_GALAXY_SERVER_{{ id }}_{{ key }}` where `{{ id }}` is the upper case form of the server identifier and `{{ key }}` is the key to define. 

For example, for the `rh_certified` in Automation Hub, you can export it as follows.

```shell
$ export ANSIBLE_GALAXY_SERVER_RH_CERTIFIED_TOKEN=token_goes_here
```

And for `pah_community` in Automation Hub, you can export it as follows.


```shell
$ export ANSIBLE_GALAXY_SERVER_PAH_COMMUNITY_TOKEN=token_goes_here
```

## Building Execution Environment image

```shell
$ ansible-builder build -t custom-ee-pah:1.0
File context/_build/ansible.cfg had modifications and will be rewritten
Running command:
  podman build -f context/Containerfile -t custom-ee-pah:1.0 context
Complete! The build context can be found at: /home/iamgini/ansible/ansible-aap-demos/context
```

### Building EEs with environment variables for Galaxy configuration

You can add additional environment variables and arguments as follows. 

```yaml
.
.
additional_build_steps:
  prepend_galaxy:
    - ENV ANSIBLE_GALAXY_SERVER_LIST=pah_community,rh_certified
    - ARG ANSIBLE_GALAXY_SERVER_RH_CERTIFIED_TOKEN
    - ARG ANSIBLE_GALAXY_SERVER_PAH_COMMUNITY_TOKEN
```

And you need to pass the same while building the container as follows.

```shell
$ ansible-builder build -f execution-environment-v3.yml \
  -t test:v1 \
  --build-arg ANSIBLE_GALAXY_SERVER_PAH_COMMUNITY_TOKEN \
  --build-arg ANSIBLE_GALAXY_SERVER_RH_CERTIFIED_TOKEN 
```

**References**

Find Automation Hub API using the following API endpoint:

`https://<AutomationHub-URL>/pulp/api/v3/docs/`

- [Configuring Red Hat Automation Hub as the primary source for content](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/1.2/html/getting_started_with_red_hat_ansible_automation_hub/proc-configure-automation-hub-server)
- [Configuring the ansible-galaxy client](https://docs.ansible.com/ansible/latest/collections_guide/collections_installing.html#configuring-the-ansible-galaxy-client)
- [Building EEs with environment variables for Galaxy configuration](https://ansible.readthedocs.io/projects/builder/en/latest/scenario_guides/scenario_custom/)
- [Execution environment definition - Version 3 sample file](https://ansible.readthedocs.io/projects/builder/en/latest/definition/#version-3-format)