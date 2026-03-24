# AAP on OCP

- [AAP on OCP](#aap-on-ocp)
  - [Default AAP Operator Resources](#default-aap-operator-resources)
  - [AAP With Default Setting](#aap-with-default-setting)
  - [Hub Persistent Storage](#hub-persistent-storage)
    - [Utilize S3 as backend RWX storage](#utilize-s3-as-backend-rwx-storage)
    - [Using NFS/EFS as RWX Backend](#using-nfsefs-as-rwx-backend)
  - [Redis Storage (YET TO TEST)](#redis-storage-yet-to-test)
  - [Controller Persistent Storage](#controller-persistent-storage)
    - [Using local disk (YET TO COMPLETE TESTING)](#using-local-disk-yet-to-complete-testing)
    - [Using Local Storage Operator (YET TO COMPLETE TESTING)](#using-local-storage-operator-yet-to-complete-testing)
    - [Local-Path Provisioner (YET TO COMPLETE TESTING)](#local-path-provisioner-yet-to-complete-testing)
  - [CRD Specifications](#crd-specifications)
    - [AnsibleAutomationPlatform.spec](#ansibleautomationplatformspec)
    - [AutomationController.spec](#automationcontrollerspec)
    - [AutomationHub.spec](#automationhubspec)
    - [EDA.spec](#edaspec)

## Default AAP Operator Resources

```shell
$ oc get all,pvc
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
NAME                                                                  READY   STATUS    RESTARTS   AGE
pod/aap-gateway-operator-controller-manager-6577ccdb74-cgncf          2/2     Running   10         4d22h
pod/ansible-lightspeed-operator-controller-manager-7b99647cff-ftjk9   2/2     Running   10         4d22h
pod/automation-controller-operator-controller-manager-f69f544crxpjg   2/2     Running   10         4d22h
pod/automation-hub-operator-controller-manager-7d948bccdc-74w4q       2/2     Running   10         4d22h
pod/eda-server-operator-controller-manager-5f97d95bf6-nmh27           2/2     Running   10         4d22h
pod/resource-operator-controller-manager-85cbd9fb49-zc4rt             2/2     Running   11         4d22h

NAME                                                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/aap-gateway-operator-controller-manager             1/1     1            1           4d22h
deployment.apps/ansible-lightspeed-operator-controller-manager      1/1     1            1           4d22h
deployment.apps/automation-controller-operator-controller-manager   1/1     1            1           4d22h
deployment.apps/automation-hub-operator-controller-manager          1/1     1            1           4d22h
deployment.apps/eda-server-operator-controller-manager              1/1     1            1           4d22h
deployment.apps/resource-operator-controller-manager                1/1     1            1           4d22h

NAME                                                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/aap-gateway-operator-controller-manager-6577ccdb74            1         1         1       4d22h
replicaset.apps/ansible-lightspeed-operator-controller-manager-7b99647cff     1         1         1       4d22h
replicaset.apps/automation-controller-operator-controller-manager-f69f544c5   1         1         1       4d22h
replicaset.apps/automation-hub-operator-controller-manager-7d948bccdc         1         1         1       4d22h
replicaset.apps/eda-server-operator-controller-manager-5f97d95bf6             1         1         1       4d22h
replicaset.apps/resource-operator-controller-manager-85cbd9fb49               1         1         1       4d22h
```

```shell
$ oc get csv
NAME                               DISPLAY                       VERSION              REPLACES                           PHASE
aap-operator.v2.6.0-0.1772556720   Ansible Automation Platform   2.6.0+0.1772556720   aap-operator.v2.6.0-0.1768951397   Succeeded
```

## AAP With Default Setting

Sample YAML for [AnsibleAutomationPlatform](aap-26-on-ocp-all.yaml) with,

- Gateway
- Controller
- Hub (with [S3 Bucket](aap-hub-data-secret.yaml) for data with [IAM](aap-hub-data-access-iam.json))
- EDA
- PostgreSQL (Operator managed with default StorageClass for PV)

```shell
 $  oc get po |grep aap-demo
aap-demo-instance-controller-migration-4.7.9-8bf22                0/1     Completed   0          3m35s
aap-demo-instance-controller-task-7c64798f59-pk8kd                4/4     Running     0          3m44s
aap-demo-instance-controller-web-55bc556594-2j694                 3/3     Running     0          3m46s
aap-demo-instance-eda-activation-worker-85fd8bb6db-n87hr          1/1     Running     0          4m5s
aap-demo-instance-eda-activation-worker-85fd8bb6db-nfscs          1/1     Running     0          4m5s
aap-demo-instance-eda-api-86d6f74fdd-56t6t                        3/3     Running     0          4m7s
aap-demo-instance-eda-default-worker-6885796b94-c92g5             1/1     Running     0          4m6s
aap-demo-instance-eda-default-worker-6885796b94-scm97             1/1     Running     0          4m6s
aap-demo-instance-eda-event-stream-ff78c9bb5-kd5mv                2/2     Running     0          4m2s
aap-demo-instance-eda-scheduler-648dbc5c89-5hbbb                  1/1     Running     0          4m4s
aap-demo-instance-eda-scheduler-648dbc5c89-rcx2z                  1/1     Running     0          4m4s
aap-demo-instance-gateway-58955659c9-8fr5q                        2/2     Running     0          5m55s
aap-demo-instance-hub-api-f97b5796f-rq8bf                         1/1     Running     0          3m32s
aap-demo-instance-hub-content-5fbb7d8664-dtcwt                    1/1     Running     0          3m46s
aap-demo-instance-hub-content-5fbb7d8664-s652t                    1/1     Running     0          3m46s
aap-demo-instance-hub-redis-67f64464fc-dpjsb                      1/1     Running     0          3m53s
aap-demo-instance-hub-web-6ff7868df8-5pwjp                        1/1     Running     0          3m57s
aap-demo-instance-hub-worker-8c45799-9zlcm                        1/1     Running     0          3m41s
aap-demo-instance-hub-worker-8c45799-fvp4c                        1/1     Running     0          3m41s
aap-demo-instance-postgres-15-0                                   1/1     Running     0          6m43s
aap-demo-instance-redis-0                                         1/1     Running     0          6m54s
```

```shell
$ oc get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                             STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
persistentvolume/pvc-5a88b285-ea1d-431f-a747-337d44a39df4   1Gi        RWO            Delete           Bound    aap/aap-demo-instance-hub-redis-data              gp3-csi        <unset>                          9m
persistentvolume/pvc-c8cf391d-5398-447b-9f7a-78387c394670   100Gi      RWO            Delete           Bound    aap/postgres-15-aap-demo-instance-postgres-15-0   gp3-csi        <unset>                          11m

NAME                                                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
persistentvolumeclaim/aap-demo-instance-hub-redis-data              Bound    pvc-5a88b285-ea1d-431f-a747-337d44a39df4   1Gi        RWO            gp3-csi        <unset>                 9m4s
persistentvolumeclaim/postgres-15-aap-demo-instance-postgres-15-0   Bound    pvc-c8cf391d-5398-447b-9f7a-78387c394670   100Gi      RWO            gp3-csi        <unset>                 11m
```

## Hub Persistent Storage

### Utilize S3 as backend RWX storage

- Sample IAM for S3 - [aap-hub-data-access-iam.json](aap-hub-data-access-iam.json)
- Sample Secret for Storing S3 info - [aap-hub-data-secret.yaml](aap-hub-data-secret.yaml)


### Using NFS/EFS as RWX Backend

1. Create EFS (or NFS on EC2) and access point (e.g. `/aap-hub`)
  - Remember to add proper Security Group entries to allow OCP workder nodes to access EFS mount targets.
2. Create a dummy StorageClass - [aap-hub-data-on-efs-storageclass.yaml](aap-hub-data-on-efs-storageclass.yaml)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-rwx
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: Immediate
```

```shell
$ oc apply -f aap-hub-data-on-efs-storageclass.yaml
storageclass.storage.k8s.io/efs-rwx created
```

3. Create PV using EFS and dummy StorageClass - [aap-hub-data-on-efs-pv.yaml](aap-hub-data-on-efs-pv.yaml)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: efs-aap-hub-pv
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: efs-rwx
  nfs:
    server: 192.168.0.175
    path: /aap-hub
```

```shell
$ oc apply -f aap-hub-data-on-efs-pv.yaml
persistentvolume/efs-aap-hub-pv created
```

```shell
$ oc get pv,pvc
NAME                              CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                               STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
persistentvolume/efs-aap-hub-pv   100Gi      RWX            Retain           Released   aap/aap-demo-efs-hub-file-storage   efs-rwx        <unset>                          11m
```

4. Mention the Hub storage in AAP CRD with dummy StorageClass

Sample: [aap-26-on-ocp-hub-with-efs.yaml](aap-26-on-ocp-hub-with-efs.yaml)

```yaml
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
spec:
...
  hub:
    disabled: false
    storage_type: file
    file_storage_storage_class: efs-rwx
    file_storage_size: 100Gi
```

1. Validate PV and PVC

```shell
$ oc get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                        STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
persistentvolume/efs-aap-hub-pv                             100Gi      RWX            Retain           Bound    aap/aap-demo-efs-hub-file-storage            efs-rwx        <unset>                          3m37s
persistentvolume/pvc-784d6b76-931f-47df-bd79-489735e9100e   100Gi      RWO            Delete           Bound    aap/postgres-15-aap-demo-efs-postgres-15-0   gp3-csi        <unset>                          11m
persistentvolume/pvc-b131b9aa-fee8-4825-b0e3-79b1bd050c45   1Gi        RWO            Delete           Bound    aap/aap-demo-efs-hub-redis-data              gp3-csi        <unset>                          8m54s

NAME                                                           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
persistentvolumeclaim/aap-demo-efs-hub-file-storage            Bound    efs-aap-hub-pv                             100Gi      RWX            efs-rwx        <unset>                 8m52s
persistentvolumeclaim/aap-demo-efs-hub-redis-data              Bound    pvc-b131b9aa-fee8-4825-b0e3-79b1bd050c45   1Gi        RWO            gp3-csi        <unset>                 8m59s
persistentvolumeclaim/postgres-15-aap-demo-efs-postgres-15-0   Bound    pvc-784d6b76-931f-47df-bd79-489735e9100e   100Gi      RWO            gp3-csi        <unset>                 11m
```

## Redis Storage (YET TO TEST)


## Controller Persistent Storage

```yaml
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
metadata:
...
  controller:
    disabled: false
    projects_persistence: true
    projects_storage_access_mode: ReadWriteOnce
    projects_storage_size: 20Gi
```

```shell
 $  oc get pvc
NAME                                          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
aap-demo-instance-controller-projects-claim   Bound    pvc-6edd8851-c5c1-4d53-b548-54b839d22e1d   20Gi       RWO            gp3-csi        <unset>                 39s
aap-demo-instance-hub-redis-data              Bound    pvc-5a88b285-ea1d-431f-a747-337d44a39df4   1Gi        RWO            gp3-csi        <unset>                 63m
postgres-15-aap-demo-instance-postgres-15-0   Bound    pvc-c8cf391d-5398-447b-9f7a-78387c394670   100Gi      RWO            gp3-csi        <unset>                 65m
```

### Using local disk (YET TO COMPLETE TESTING)

*Note: Controller replicas (web and task) cannot be more than 1!*

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-path
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

Label nodes

```shell
$ oc label node ip-10-0-13-179.us-east-2.compute.internal aap-storage=true
node/ip-10-0-13-179.us-east-2.compute.internal labeled

$ oc label node ip-10-0-52-51.us-east-2.compute.internal aap-storage=true
node/ip-10-0-52-51.us-east-2.compute.internal labeled
```

Create mount directories for PV

```shell
sudo mkdir -p /mnt/aap-projects
sudo chmod 777 /mnt/aap-projects
sudo chown -R 1000:1000 /mnt/aap-projects
```

Create PV

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: aap-projects-pv
spec:
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  persistentVolumeReclaimPolicy: Delete
  local:
    path: /mnt/aap-projects
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: aap-storage
              operator: In
              values:
                - "true"
            # - key: kubernetes.io/hostname
            #   operator: In
            #   values:
            #     - ip-10-0-13-179.us-east-2.compute.internal
            #     - ip-10-0-52-51.us-east-2.compute.internal
```

*Note: Use any of the nodeAffinity methods or use LocalStorageOperator to manage provisioning dynamically (recommended).*


```yaml
spec:
  projects_persistence: true
  projects_storage_class: local-path
  projects_storage_access_mode: ReadWriteOnce
  projects_storage_size: 20Gi
```

```shell
$  oc get pvc,pv
NAME                                                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
persistentvolumeclaim/aap-demo-instance-controller-projects-claim   Bound    aap-projects-pv                            20Gi       RWO            local-path     <unset>                 3m41s
persistentvolumeclaim/aap-demo-instance-hub-redis-data              Bound    pvc-5a88b285-ea1d-431f-a747-337d44a39df4   1Gi        RWO            gp3-csi        <unset>                 94m
persistentvolumeclaim/postgres-15-aap-demo-instance-postgres-15-0   Bound    pvc-c8cf391d-5398-447b-9f7a-78387c394670   100Gi      RWO            gp3-csi        <unset>                 97m

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                             STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
persistentvolume/aap-projects-pv                            20Gi       RWO            Delete           Bound    aap/aap-demo-instance-controller-projects-claim   local-path     <unset>                          35s
persistentvolume/pvc-5a88b285-ea1d-431f-a747-337d44a39df4   1Gi        RWO            Delete           Bound    aap/aap-demo-instance-hub-redis-data              gp3-csi        <unset>                          94m
persistentvolume/pvc-c8cf391d-5398-447b-9f7a-78387c394670   100Gi      RWO            Delete           Bound    aap/postgres-15-aap-demo-instance-postgres-15-0   gp3-csi        <unset>                          97m
```

### Using Local Storage Operator (YET TO COMPLETE TESTING)

*Note: Controller replicas (web and task) cannot be more than 1!*
*Note: if you don't have extra disks, then LSO won't work!*

- Install from Operator Hub (UI)

or

`oc apply -f https://raw.githubusercontent.com/redhat-cop/openshift-local-storage/master/operator.yaml`

Create LocalVolumeSet

```yaml
apiVersion: local.storage.openshift.io/v1alpha1
kind: LocalVolumeSet
metadata:
  name: localblock
  namespace: openshift-local-storage
spec:
  nodeSelector:
    nodeSelectorTerms:
      - matchExpressions:
          - key: node-role.kubernetes.io/worker
            operator: Exists
  storageClassName: localblock-sc
  volumeMode: Filesystem
  fsType: xfs
  deviceInclusionSpec:
    deviceTypes:
      - disk
    minSize: 10Gi
```

```shell
$ oc apply -f LocalVolumeSet.yaml
localvolumeset.local.storage.openshift.io/localblock created
```

```shell
$  oc get sc localblock-sc
NAME            PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
localblock-sc   kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  32s
```

Use it in AAP:

```yaml
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
metadata:
...
spec:
  projects_persistence: true
  projects_storage_class: localblock-sc
  projects_storage_access_mode: ReadWriteOnce
  projects_storage_size: 20Gi
  web_replicas: 1
  task_replicas: 1
```

### Local-Path Provisioner (YET TO COMPLETE TESTING)

```shell
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

```yaml
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
metadata:
...
  controller:
    disabled: false
    projects_persistence: true
    projects_storage_class: local-path
    projects_storage_access_mode: ReadWriteOnce
    projects_storage_size: 20Gi
    web_replicas: 1
    task_replicas: 1
```

## CRD Specifications

### AnsibleAutomationPlatform.spec

```shell
$  oc explain AnsibleAutomationPlatform.spec
KIND:     AnsibleAutomationPlatform
VERSION:  aap.ansible.com/v1alpha1

RESOURCE: spec <Object>

DESCRIPTION:
     Spec defines the desired state of AnsibleAutomationPlatform

FIELDS:
   admin_password_secret        <string>
     Secret where the admin password can be found

   api  <Object>
     The gateway api deployment.

   bundle_cacert_secret <string>
     Secret where the trusted Certificate Authority Bundle is stored

   controller   <>
     Controller defines the desired state of AutomationController

   database     <Object>
     The Gateway PostgreSQL database StatefulSet

   db_fields_encryption_secret  <string>
     Secret where the DB fields encryption key can be found. If not specified,
     one will be generated.

   eda  <>
     EDA defines the desired state of EDA

   extra_settings       <[]Object>
     Environment variables to configure the application-level settings

   feature_flags        <map[string]boolean>
     Platform Feature Flags Dictionary

   hostname     <string>
     Override default hostname of the Ansible Automation Platform

   hub  <>
     Hub defines the desired state of AutomationHub

   idle_aap     <boolean>
     Scale down replicas to put AAP into an idle mode

   image        <string>
     AAP Gateway container image

   image_proxy  <string>
     AAP Gateway Proxy container image

   image_proxy_version  <string>
     AAP Gateway Proxy container image version (tag)

   image_pull_policy    <string>
     The image pull policy

   image_pull_secrets   <[]string>
     Image pull secrets for app and database containers

   image_version        <string>
     AAP Gateway container image version (tag)

   ingress_annotations  <string>
     Annotations to add to the Ingress Controller

   ingress_api_version  <string>
     The Ingress API version to use

   ingress_class_name   <string>
     The name of ingress class to use instead of the cluster default.

   ingress_path <string>
     The ingress path used to reach the deployed service

   ingress_path_type    <string>
     The ingress path type for the deployed service

   ingress_tls_secret   <string>
     Secret where the Ingress TLS secret can be found

   ingress_type <string>
     The ingress type to use to reach the deployed instance

   lightspeed   <>
     Lightspeed defines the desired state of AnsibleLightspeed

   loadbalancer_port    <integer>
     Port to use for the loadbalancer

   loadbalancer_protocol        <string>
     Protocol to use for the loadbalancer

   mcp  <>
     MCP defines the desired state of the Ansible MCP Server

   no_log       <boolean>
     Configure no_log for no_log tasks

   postgres_image       <string>
     Registry path to the PostgreSQL container to use

   postgres_image_version       <string>
     PostgreSQL container image version to use

   public_base_url      <string>
     Public base URL for AAP

   redhat_registry      <string>
     Default Redhat Container Image Registry

   redhat_registry_ns   <string>
     Default Redhat Container Image Registry Namespace

   redis        <Object>
     Defines desired state of cache resources

   redis_image  <string>
     Registry path to the redis container to use

   redis_image_version  <string>
     Redis container image version to use

   redis_mode   <string>
     Redis Mode

   route_annotations    <string>
     Annotations to add to the route

   route_api_version    <string>
     The route API version to use

   route_host   <string>
     The DNS to use to points to the instance

   route_tls_secret     <string>
     Secret where the TLS related credentials are stored

   route_tls_termination_mechanism      <string>
     The secure TLS termination mechanism to use

   service_account_annotations  <string>
     ServiceAccount annotations

   service_annotations  <string>
     Annotations to add to the load balancer service

   service_type <string>
     The service type to be used on the deployed instance

```

### AutomationController.spec

```shell
$  oc explain --recursive AutomationController.spec
KIND:     AutomationController
VERSION:  automationcontroller.ansible.com/v1beta1

RESOURCE: spec <Object>

DESCRIPTION:
     <empty>

FIELDS:
   additional_labels    <[]string>
   admin_email  <string>
   admin_password_secret        <string>
   admin_user   <string>
   affinity     <Object>
      nodeAffinity      <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            preference  <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <Object>
            nodeSelectorTerms   <[]Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
      podAffinity       <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
      podAntiAffinity   <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
   annotations  <string>
   api_urlpattern_prefix        <string>
   api_version  <string>
   auto_upgrade <boolean>
   broadcast_websocket_secret   <string>
   bundle_cacert_secret <string>
   ca_trust_bundle      <string>
   control_plane_ee_image       <string>
   control_plane_priority_class <string>
   create_preload_data  <boolean>
   csrf_cookie_secure   <string>
   deployment_type      <string>
   development_mode     <boolean>
   ee_extra_env <string>
   ee_extra_volume_mounts       <string>
   ee_images    <[]Object>
      image     <string>
      name      <string>
   ee_pull_credentials_secret   <string>
   ee_resource_requirements     <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   extra_settings       <[]Object>
      setting   <string>
      value     <>
   extra_settings_files <Object>
      configmaps        <[]Object>
         key    <string>
         name   <string>
      secrets   <[]Object>
         key    <string>
         name   <string>
   extra_volumes        <string>
   garbage_collect_secrets      <boolean>
   host_aliases <[]Object>
      hostnames <[]string>
      ip        <string>
   hostname     <string>
   idle_deployment      <boolean>
   image        <string>
   image_pull_policy    <string>
   image_pull_secret    <string>
   image_pull_secrets   <[]string>
   image_version        <string>
   ingress_annotations  <string>
   ingress_api_version  <string>
   ingress_class_name   <string>
   ingress_controller   <string>
   ingress_hosts        <[]Object>
      hostname  <string>
      tls_secret        <string>
   ingress_path <string>
   ingress_path_type    <string>
   ingress_tls_secret   <string>
   ingress_type <string>
   init_container_extra_commands        <string>
   init_container_extra_volume_mounts   <string>
   init_container_image <string>
   init_container_image_version <string>
   init_container_resource_requirements <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   init_projects_container_image        <string>
   ipv6_disabled        <boolean>
   kind <string>
   ldap_cacert_secret   <string>
   ldap_password_secret <string>
   loadbalancer_class   <string>
   loadbalancer_ip      <string>
   loadbalancer_port    <integer>
   loadbalancer_protocol        <string>
   metrics_utility_configmap    <string>
   metrics_utility_console_enabled      <boolean>
   metrics_utility_cronjob_gather_schedule      <string>
   metrics_utility_cronjob_report_schedule      <string>
   metrics_utility_enabled      <boolean>
   metrics_utility_image        <string>
   metrics_utility_image_pull_policy    <string>
   metrics_utility_image_version        <string>
   metrics_utility_pvc_claim    <string>
   metrics_utility_pvc_claim_size       <string>
   metrics_utility_pvc_claim_storage_class      <string>
   metrics_utility_secret       <string>
   metrics_utility_ship_target  <string>
   nginx_client_max_body_size   <integer>
   nginx_listen_queue_size      <integer>
   nginx_worker_connections     <integer>
   nginx_worker_cpu_affinity    <string>
   nginx_worker_processes       <integer>
   no_log       <boolean>
   node_selector        <string>
   nodeport_port        <integer>
   old_postgres_configuration_secret    <string>
   pg_dump_suffix       <string>
   postgres_annotations <string>
   postgres_configuration_secret        <string>
   postgres_data_volume_init    <boolean>
   postgres_extra_args  <[]string>
   postgres_extra_settings      <[]Object>
      setting   <string>
      value     <string>
   postgres_extra_volume_mounts <string>
   postgres_extra_volumes       <string>
   postgres_image       <string>
   postgres_image_version       <string>
   postgres_init_container_commands     <string>
   postgres_init_container_resource_requirements        <Object>
      limits    <Object>
         cpu    <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         memory <string>
         storage        <string>
   postgres_keep_pvc_after_upgrade      <boolean>
   postgres_keepalives  <boolean>
   postgres_keepalives_count    <integer>
   postgres_keepalives_idle     <integer>
   postgres_keepalives_interval <integer>
   postgres_label_selector      <string>
   postgres_priority_class      <string>
   postgres_resource_requirements       <Object>
      limits    <Object>
         cpu    <string>
         memory <string>
      requests  <Object>
         cpu    <string>
         memory <string>
   postgres_security_context_settings   <>
   postgres_selector    <string>
   postgres_storage_class       <string>
   postgres_storage_requirements        <Object>
      limits    <Object>
         storage        <string>
      requests  <Object>
         storage        <string>
   postgres_tolerations <string>
   projects_existing_claim      <string>
   projects_persistence <boolean>
   projects_storage_access_mode <string>
   projects_storage_class       <string>
   projects_storage_size        <string>
   projects_use_existing_claim  <string>
   public_base_url      <string>
   receptor_log_level   <string>
   redis_capabilities   <[]string>
   redis_image  <string>
   redis_image_version  <string>
   redis_resource_requirements  <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   replicas     <integer>
   route_api_version    <string>
   route_host   <string>
   route_tls_secret     <string>
   route_tls_termination_mechanism      <string>
   rsyslog_args <[]string>
   rsyslog_command      <[]string>
   rsyslog_extra_env    <string>
   rsyslog_extra_volume_mounts  <string>
   rsyslog_resource_requirements        <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   secret_key_secret    <string>
   security_context_settings    <>
   service_account_annotations  <string>
   service_annotations  <string>
   service_labels       <string>
   service_type <string>
   session_cookie_secure        <string>
   set_self_labels      <boolean>
   task_affinity        <Object>
      nodeAffinity      <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            preference  <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <Object>
            nodeSelectorTerms   <[]Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
      podAffinity       <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
      podAntiAffinity   <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
   task_annotations     <string>
   task_args    <[]string>
   task_command <[]string>
   task_extra_env       <string>
   task_extra_volume_mounts     <string>
   task_liveness_failure_threshold      <integer>
   task_liveness_initial_delay  <integer>
   task_liveness_period <integer>
   task_liveness_timeout        <integer>
   task_manage_replicas <boolean>
   task_node_selector   <string>
   task_privileged      <boolean>
   task_readiness_failure_threshold     <integer>
   task_readiness_initial_delay <integer>
   task_readiness_period        <integer>
   task_readiness_timeout       <integer>
   task_replicas        <integer>
   task_resource_requirements   <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   task_tolerations     <string>
   task_topology_spread_constraints     <string>
   termination_grace_period_seconds     <integer>
   tolerations  <string>
   topology_spread_constraints  <string>
   uwsgi_listen_queue_size      <integer>
   uwsgi_processes      <integer>
   web_affinity <Object>
      nodeAffinity      <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            preference  <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <Object>
            nodeSelectorTerms   <[]Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchFields      <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
      podAffinity       <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
      podAntiAffinity   <Object>
         preferredDuringSchedulingIgnoredDuringExecution        <[]Object>
            podAffinityTerm     <Object>
               labelSelector    <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaceSelector        <Object>
                  matchExpressions      <[]Object>
                     key        <string>
                     operator   <string>
                     values     <[]string>
                  matchLabels   <map[string]string>
               namespaces       <[]string>
               topologyKey      <string>
            weight      <integer>
         requiredDuringSchedulingIgnoredDuringExecution <[]Object>
            labelSelector       <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaceSelector   <Object>
               matchExpressions <[]Object>
                  key   <string>
                  operator      <string>
                  values        <[]string>
               matchLabels      <map[string]string>
            namespaces  <[]string>
            topologyKey <string>
   web_annotations      <string>
   web_args     <[]string>
   web_command  <[]string>
   web_extra_env        <string>
   web_extra_volume_mounts      <string>
   web_liveness_failure_threshold       <integer>
   web_liveness_initial_delay   <integer>
   web_liveness_period  <integer>
   web_liveness_timeout <integer>
   web_manage_replicas  <boolean>
   web_node_selector    <string>
   web_readiness_failure_threshold      <integer>
   web_readiness_initial_delay  <integer>
   web_readiness_period <integer>
   web_readiness_timeout        <integer>
   web_replicas <integer>
   web_resource_requirements    <Object>
      limits    <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         ephemeral-storage      <string>
         memory <string>
         storage        <string>
   web_tolerations      <string>
   web_topology_spread_constraints      <string>
```

### AutomationHub.spec

```shell
$  oc explain --recursive AutomationHub.spec
KIND:     AutomationHub
VERSION:  automationhub.ansible.com/v1beta1

RESOURCE: spec <Object>

DESCRIPTION:
     <empty>

FIELDS:
   admin_password_secret        <string>
   affinity     <Object>
      node_affinity     <>
   api  <Object>
      log_level <string>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
   bundle_cacert_secret <string>
   container_token_secret       <string>
   content      <Object>
      log_level <string>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
   db_fields_encryption_secret  <string>
   deployment_type      <string>
   file_storage_access_mode     <string>
   file_storage_size    <string>
   file_storage_storage_class   <string>
   force_drop_db        <boolean>
   gunicorn_api_workers <integer>
   gunicorn_content_workers     <integer>
   gunicorn_timeout     <integer>
   haproxy_timeout      <string>
   hostname     <string>
   idle_deployment      <boolean>
   image        <string>
   image_pull_policy    <string>
   image_pull_secret    <string>
   image_pull_secrets   <[]string>
   image_version        <string>
   image_web    <string>
   image_web_version    <string>
   ingress_annotations  <string>
   ingress_class_name   <string>
   ingress_tls_secret   <string>
   ingress_type <string>
   loadbalancer_port    <integer>
   loadbalancer_protocol        <string>
   nginx_client_max_body_size   <string>
   nginx_proxy_connect_timeout  <string>
   nginx_proxy_read_timeout     <string>
   nginx_proxy_send_timeout     <string>
   no_log       <boolean>
   node_selector        <string>
   nodeport_port        <string>
   object_storage_azure_secret  <string>
   object_storage_s3_secret     <string>
   postgres_configuration_secret        <string>
   postgres_data_volume_init    <boolean>
   postgres_extra_args  <[]string>
   postgres_extra_settings      <[]Object>
      setting   <string>
      value     <string>
   postgres_host_auth_method    <string>
   postgres_image       <string>
   postgres_init_container_commands     <string>
   postgres_initdb_args <string>
   postgres_keep_pvc_after_upgrade      <boolean>
   postgres_label_selector      <string>
   postgres_migrant_configuration_secret        <string>
   postgres_resource_requirements       <Object>
      limits    <Object>
         cpu    <string>
         memory <string>
      requests  <Object>
         cpu    <string>
         memory <string>
   postgres_selector    <string>
   postgres_storage_class       <string>
   postgres_storage_requirements        <Object>
      limits    <Object>
         storage        <string>
      requests  <Object>
         storage        <string>
   postgres_tolerations <string>
   public_base_url      <string>
   pulp_settings        <>
   redis        <Object>
      log_level <string>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
   redis_data_persistence       <boolean>
   redis_image  <string>
   redis_resource_requirements  <Object>
      limits    <Object>
         cpu    <string>
         memory <string>
         storage        <string>
      requests  <Object>
         cpu    <string>
         memory <string>
         storage        <string>
   redis_storage_class  <string>
   redis_storage_size   <string>
   resource_manager     <Object>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
   route_host   <string>
   route_tls_secret     <string>
   route_tls_termination_mechanism      <string>
   service_annotations  <string>
   signing_scripts_configmap    <string>
   signing_secret       <string>
   sso_secret   <string>
   storage_type <string>
   tolerations  <string>
   topology_spread_constraints  <string>
   web  <Object>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
   worker       <Object>
      replicas  <integer>
      resource_requirements     <Object>
         limits <Object>
            cpu <string>
            memory      <string>
            storage     <string>
         requests       <Object>
            cpu <string>
            memory      <string>
            storage     <string>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
```

### EDA.spec

```shell
$  oc explain --recursive EDA.spec
KIND:     EDA
VERSION:  eda.ansible.com/v1alpha1

RESOURCE: spec <Object>

DESCRIPTION:
     Spec defines the desired state of EDA

FIELDS:
   activation_worker    <Object>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   additional_labels    <[]string>
   admin_password_secret        <string>
   admin_user   <string>
   api  <Object>
      gunicorn_workers  <integer>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   automation_server_ssl_verify <string>
   automation_server_url        <string>
   bundle_cacert_secret <string>
   database     <Object>
      database_secret   <string>
      node_selector     <map[string]string>
      postgres_data_volume_init <boolean>
      postgres_extra_args       <[]string>
      postgres_extra_settings   <[]Object>
         setting        <string>
         value  <string>
      postgres_init_container_commands  <string>
      postgres_keep_pvc_after_upgrade   <boolean>
      postgres_ssl_mode <string>
      postgres_storage_class    <string>
      priority_class    <string>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      storage_requirements      <Object>
         limits <Object>
            storage     <string>
         requests       <Object>
            storage     <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
   db_fields_encryption_secret  <string>
   default_worker       <Object>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   event_stream <Object>
      gunicorn_workers  <integer>
      mtls      <boolean>
      mtls_prefix       <string>
      node_selector     <map[string]string>
      prefix    <string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   extra_settings       <[]Object>
      setting   <string>
      value     <>
   force_drop_db        <boolean>
   hostname     <string>
   idle_deployment      <boolean>
   image        <string>
   image_pull_policy    <string>
   image_pull_secrets   <[]string>
   image_version        <string>
   image_web    <string>
   image_web_version    <string>
   ingress_annotations  <string>
   ingress_api_version  <string>
   ingress_class_name   <string>
   ingress_path <string>
   ingress_path_type    <string>
   ingress_tls_secret   <string>
   ingress_type <string>
   ipv6_disabled        <boolean>
   loadbalancer_port    <integer>
   loadbalancer_protocol        <string>
   no_log       <boolean>
   nodeport_port        <integer>
   old_postgres_configuration_secret    <string>
   pg_dump_suffix       <string>
   postgres_image       <string>
   postgres_image_version       <string>
   postgres_label_selector      <string>
   public_base_url      <string>
   redis        <Object>
      node_selector     <map[string]string>
      redis_secret      <string>
      replicas  <integer>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
   redis_image  <string>
   redis_image_version  <string>
   route_api_version    <string>
   route_host   <string>
   route_tls_secret     <string>
   route_tls_termination_mechanism      <string>
   scheduler    <Object>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   service_account_annotations  <string>
   service_type <string>
   set_self_labels      <boolean>
   ui   <Object>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
   ui_disabled  <boolean>
   worker       <Object>
      node_selector     <map[string]string>
      replicas  <>
      resource_requirements     <Object>
         claims <[]Object>
            name        <string>
         limits <map[string]>
         requests       <map[string]>
      strategy  <Object>
         rollingUpdate  <Object>
            maxSurge    <>
            maxUnavailable      <>
         type   <string>
      tolerations       <[]Object>
         effect <string>
         key    <string>
         operator       <string>
         tolerationSeconds      <integer>
         value  <string>
      topology_spread_constraints       <[]Object>
         labelSelector  <Object>
            matchExpressions    <[]Object>
               key      <string>
               operator <string>
               values   <[]string>
            matchLabels <map[string]string>
         matchLabelKeys <[]string>
         maxSkew        <integer>
         minDomains     <integer>
         nodeAffinityPolicy     <string>
         nodeTaintsPolicy       <string>
         topologyKey    <string>
         whenUnsatisfiable      <string>
```
