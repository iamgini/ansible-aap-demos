# Managing Containerized AAP 2.5 Services with systemd


```shell
# To Restart Services Only on Automation Gateway Nodes:
$ ansible-playbook -i inventory manage_aap_services.yaml -e action=restart -e target_group=automationgateway

# To Restart Services Only on Automation EDA Nodes:
$ ansible-playbook -i inventory manage_aap_services.yaml -e action=restart -e target_group=automationeda

# To Restart Services Only on Automation Controller Nodes:
$ ansible-playbook -i inventory manage_aap_services.yaml -e action=restart -e target_group=automationcontroller
```