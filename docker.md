#
chroot

lxc

namespace

cgroup

overlay


多个容器可共用一个基础镜像（lowerdir），运行时增加可写层（upperdir），
容器中看到是merged（mount方式）

# 网络
网络模型：
- host： 共用主机的ip和端口
- container： 和指定的容器共享IP
- none
- bridge：默认


```
# 自定义网桥
yum install bridge-utils
brctl addbr docker1
ip addr add dev docker1 10.1.1.1/24
ip link set dev docker1 up
vim /etc/sysconfig/docker-network
DOCKER_NETWORK_OPTIONS="-b=docker1"
systemctl restart docker
```








