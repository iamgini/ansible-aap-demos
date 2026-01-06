
```shell
$ ansible-builder build -t custom-ee-cloud-terraform:0.4
```

```shell
$ podman run -it --rm localhost/custom-ee-lib:1.0  whoami
```

Test the CA Certificate:

```shell
$ podman run --rm -it custom-ee-cloud-terraform:0.4 bash

bash-5.1$ ls /etc/pki/ca-trust/source/anchors/
otter-ca.cer

```

If facing issue with rootless podman:

```shell
$ sudo setenforce 0
```


- https://github.com/ansible-cloud/terraform_ee/tree/main