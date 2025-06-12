
```shell

[myuser@node1 ~]$ su - root
Password:
[root@node1 ~]# whoami
root
[root@node1 ~]#  podman pull quay.io/iamgini/custom-ee-lib:1.0
Trying to pull quay.io/iamgini/custom-ee-lib:1.0...
Getting image source signatures
Copying blob 245c19dd8e4e done   |
Copying blob 2384c7c17092 done   |
Copying blob 275227975e83 done   |
Copying blob 1ef204b07915 done   |
Copying blob 181114929125 done   |
Copying blob ee6fd7093dcd done   |
Copying blob 91fd56c95cbb done   |
Copying blob 95aab2dc5394 done   |
Copying blob ac4988d5fdde done   |
Copying blob 01c159771872 done   |
Copying blob 37df5cbef672 done   |
Copying blob 3e56a74ca1f3 done   |
Copying blob 86e263707a05 done   |
Copying blob 591d020add03 done   |
Copying config 31a13c84fb done   |
Writing manifest to image destination
31a13c84fbe4e3b65f4b5b4fba555f8b7c0b5c74370585881b3a1c7536c6926e
[root@node1 ~]# podman run -dit --name ee-container quay.io/iamgini/custom-ee-lib:1.0 bash
fc7b9fe940a74bcba461582561159537bea112e837059bc11dfd379a5f0a66ce
[root@node1 ~]# podman exec -it ee-container bash                                       bash-4.4$ whoami
myuser
bash-4.4$
```