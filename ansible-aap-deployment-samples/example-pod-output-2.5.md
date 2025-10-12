# Example Pods in an Enterprise Topology

```shell
--------controller1.example.org-------------
CONTAINER ID  IMAGE                                                                      COMMAND               CREATED      STATUS            PORTS       NAMES
951864ec7a8c  registry.redhat.io/rhel8/redis-6:latest                                    run-redis             2 hours ago  Up 2 hours        6379/tcp    redis-unix
a942b610816a  registry.redhat.io/ansible-automation-platform-25/receptor-rhel8:latest    /usr/bin/receptor...  2 hours ago  Up 2 hours                    receptor
523550a1aeb6  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-rsyslog
2caf072d6331  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-task
4296f58b1681  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-web

--------controller1.example.org-------------
CONTAINER ID  IMAGE                                                                      COMMAND               CREATED      STATUS            PORTS       NAMES
951864ec7a8c  registry.redhat.io/rhel8/redis-6:latest                                    run-redis             2 hours ago  Up 2 hours        6379/tcp    redis-unix
a942b610816a  registry.redhat.io/ansible-automation-platform-25/receptor-rhel8:latest    /usr/bin/receptor...  2 hours ago  Up 2 hours                    receptor
523550a1aeb6  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-rsyslog
2caf072d6331  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-task
4296f58b1681  registry.redhat.io/ansible-automation-platform-25/controller-rhel8:latest  /usr/bin/launch_a...  2 hours ago  Up About an hour  8052/tcp    automation-controller-web

--------gateway1.example.org-------------
CONTAINER ID  IMAGE                                                                         COMMAND               CREATED      STATUS      PORTS       NAMES
b618a3c1be69  registry.redhat.io/rhel8/redis-6:latest                                       run-redis             2 hours ago  Up 2 hours  6379/tcp    redis-tcp
b887a6f9ae6b  registry.redhat.io/ansible-automation-platform-25/gateway-proxy-rhel8:latest  /usr/bin/envoy --...  2 hours ago  Up 2 hours              automation-gateway-proxy
f35efcead731  registry.redhat.io/ansible-automation-platform-25/gateway-rhel8:latest        /usr/bin/supervis...  2 hours ago  Up 2 hours              automation-gateway

--------gateway2.example.org-------------
CONTAINER ID  IMAGE                                                                         COMMAND               CREATED      STATUS      PORTS       NAMES
58af2124bf63  registry.redhat.io/rhel8/redis-6:latest                                       run-redis             2 hours ago  Up 2 hours  6379/tcp    redis-tcp
494dca2db217  registry.redhat.io/ansible-automation-platform-25/gateway-proxy-rhel8:latest  /usr/bin/envoy --...  2 hours ago  Up 2 hours              automation-gateway-proxy
95aad730191b  registry.redhat.io/ansible-automation-platform-25/gateway-rhel8:latest        /usr/bin/supervis...  2 hours ago  Up 2 hours              automation-gateway

--------hub1.example.org-------------
CONTAINER ID  IMAGE                                                                   COMMAND               CREATED            STATUS            PORTS               NAMES
49b340af908f  registry.redhat.io/rhel8/redis-6:latest                                 run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-tcp
af9ee1a46cff  registry.redhat.io/rhel8/redis-6:latest                                 run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-unix
1623bf36fbab  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-api --na...  About an hour ago  Up About an hour                      automation-hub-api
00359ee4f744  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-content ...  About an hour ago  Up About an hour                      automation-hub-content
41ff1856a8e4  registry.redhat.io/ansible-automation-platform-25/hub-web-rhel8:latest  /bin/sh -c nginx ...  About an hour ago  Up About an hour  8080/tcp, 8443/tcp  automation-hub-web
6fbc8728fd81  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-worker       About an hour ago  Up About an hour                      automation-hub-worker-1
54b16a48e923  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-worker       About an hour ago  Up About an hour                      automation-hub-worker-2

--------hub2.example.org-------------
CONTAINER ID  IMAGE                                                                   COMMAND               CREATED            STATUS            PORTS               NAMES
ef8a85781428  registry.redhat.io/rhel8/redis-6:latest                                 run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-tcp
543da92744b7  registry.redhat.io/rhel8/redis-6:latest                                 run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-unix
05bf3505b7f9  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-api --na...  About an hour ago  Up About an hour                      automation-hub-api
5860ec2e1e50  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-content ...  About an hour ago  Up About an hour                      automation-hub-content
70b079a56453  registry.redhat.io/ansible-automation-platform-25/hub-web-rhel8:latest  /bin/sh -c nginx ...  About an hour ago  Up About an hour  8080/tcp, 8443/tcp  automation-hub-web
546535c16564  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-worker       About an hour ago  Up About an hour                      automation-hub-worker-1
5c298e521f9b  registry.redhat.io/ansible-automation-platform-25/hub-rhel8:latest      pulpcore-worker       About an hour ago  Up About an hour                      automation-hub-worker-2

--------eda1.example.org-------------
CONTAINER ID  IMAGE                                                                             COMMAND               CREATED            STATUS            PORTS               NAMES
35189a6a3c87  registry.redhat.io/rhel8/redis-6:latest                                           run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-tcp
fa03f04a3444  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     gunicorn --bind 1...  About an hour ago  Up About an hour                      automation-eda-api
7c65beba05ef  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     daphne --bind 127...  About an hour ago  Up About an hour                      automation-eda-daphne
e754dab176fd  registry.redhat.io/ansible-automation-platform-25/eda-controller-ui-rhel8:latest  /bin/sh -c nginx ...  About an hour ago  Up About an hour  8080/tcp, 8443/tcp  automation-eda-web
393077bd36f0  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-worker-1
0ead7c507b79  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-worker-2
bd2468e330a1  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-activation-worker-1
6a228ca93fb8  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-activation-worker-2
3e2b0eb0b5e3  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage sc...  About an hour ago  Up About an hour                      automation-eda-scheduler

--------eda2.example.org-------------
CONTAINER ID  IMAGE                                                                             COMMAND               CREATED            STATUS            PORTS               NAMES
be807533ba26  registry.redhat.io/rhel8/redis-6:latest                                           run-redis             2 hours ago        Up 2 hours        6379/tcp            redis-tcp
9b35a2e5065f  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     gunicorn --bind 1...  About an hour ago  Up About an hour                      automation-eda-api
ebd877361c95  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     daphne --bind 127...  About an hour ago  Up About an hour                      automation-eda-daphne
91bbec9b1612  registry.redhat.io/ansible-automation-platform-25/eda-controller-ui-rhel8:latest  /bin/sh -c nginx ...  About an hour ago  Up About an hour  8080/tcp, 8443/tcp  automation-eda-web
1fd8032ccaa8  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-worker-1
13e07500787a  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-worker-2
6b3094ed3259  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-activation-worker-1
ef0def7f1268  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage rq...  About an hour ago  Up About an hour                      automation-eda-activation-worker-2
be13b7c31590  registry.redhat.io/ansible-automation-platform-25/eda-controller-rhel8:latest     aap-eda-manage sc...  About an hour ago  Up About an hour                      automation-eda-scheduler

--------database1.example.org-------------
CONTAINER ID  IMAGE                                          COMMAND         CREATED      STATUS      PORTS       NAMES
1a461dcab328  registry.redhat.io/rhel8/postgresql-15:latest  run-postgresql  2 hours ago  Up 2 hours  5432/tcp    postgresql
````