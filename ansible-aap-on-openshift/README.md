# AAP on OCP

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

### Using local disk [YET TO COMPLETE TESTING]

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

### Using Local Storage Operator [YET TO COMPLETE TESTING]

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

### Local-Path Provisioner [YET TO COMPLETE TESTING]

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