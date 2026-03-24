# AnsibleAutomationPlatform.spec.api - gateway

In Ansible Automation Platform 2.6, there is no separate, standalone CRD for the platform gateway. Instead, the gateway is managed directly by the top-level `AnsibleAutomationPlatform` custom resource itself.

```shell
 $  oc explain --recursive AnsibleAutomationPlatform.spec.api
KIND:     AnsibleAutomationPlatform
VERSION:  aap.ansible.com/v1alpha1

RESOURCE: api <Object>

DESCRIPTION:
     The gateway api deployment.

FIELDS:
   log_level    <string>
   node_selector        <map[string]string>
   replicas     <integer>
   resource_requirements        <Object>
      limits    <Object>
         cpu    <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         memory <string>
         storage        <string>
   security_context     <>
   strategy     <Object>
      rollingUpdate     <Object>
         maxSurge       <>
         maxUnavailable <>
      type      <string>
   tolerations  <[]Object>
      effect    <string>
      key       <string>
      operator  <string>
      tolerationSeconds <integer>
      value     <string>
   topology_spread_constraints  <[]Object>
      labelSelector     <Object>
         matchExpressions       <[]Object>
            key <string>
            operator    <string>
            values      <[]string>
         matchLabels    <map[string]string>
      matchLabelKeys    <[]string>
      maxSkew   <integer>
      minDomains        <integer>
      nodeAffinityPolicy        <string>
      nodeTaintsPolicy  <string>
      topologyKey       <string>
      whenUnsatisfiable <string>
```
