
```shell
$ ansible-builder build -t custom-ee-cloud-terraform:0.3
```

```shell
$ podman run -it --rm localhost/custom-ee-lib:1.0  whoami
```

If facing issue with rootless podman:

```shell
$ sudo setenforce 0
```