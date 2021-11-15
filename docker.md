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
# 自定义网桥 obselate
yum install bridge-utils
brctl addbr docker1
ip addr add dev docker1 10.1.1.1/24
ip link set dev docker1 up
vim /etc/sysconfig/docker-network
DOCKER_NETWORK_OPTIONS="-b=docker1"
systemctl restart docker
```


docker 版本： CE（社区版）； EE（企业版）

image_name： hub.docker.com/lishuo666/container:tag

```sh

docker pull
docker push

docker commit container_name image_name

docker history image_name

docker build -t name:tag -f dockerfile Workdir

docker tag old new

docker volume ls 

docker cp  

docker network ls 

docker network inspect bridge1

docker network create --driver bridge --subnet 172.30.0.0/16 --gateway 172.30.0.1 bridge1

dockers run --name centos1 -dit --network=bridge1 centos
docker network connect bridge centos1 //再添加另外一个bridge里的网卡


```
进入容器“
- docker exec: 启动新的终端，推荐使用
- docker attach: 打开已有终端, ctrl+p+q退出

### UnionFS 联合文件系统

容器增删改查：
- 创建：
- 删除一个文件：whiteout 遮挡
- 修改： copy-on-write
- 读取 ：自上而下

### DOCKERFILE
每条指令生成一层镜像层

组成：

指令：
- ADD :复制，会解压
- COPY : 复制
- CMD: enterpoint 之前跑
- ENTERPOINT: 
- EXPOSE
- VOLUME

### 存储机制
storage driver：
- overlay2： 默认首选
- Device Mapper：
- Btrfs：仅在btrfs

持久数据存储方式：
- volume： /var/lib/docker/voumes // -v container_path //两块存储空间互相复制同步 copy
- bind mount： -v host_path:container_path //一块存储空间 mount

容器数据共享：
- volume
- bind mount
- container volume：--volumes-from vc1 container1

### 网络

### 私有仓库

docker run -d -p 1000:5000 -v /data/registry:/var/lib/registry registry

/v2/_category

/v2/hanmei/httpd/


### 实现原理

namespace cgroup

namespace:pid,mount,network,user,UTS(主机名和域名),IPC(信号量，消息队列和共享内存)

--cpu-share num 权重值

--memory

--memory-swap: 设置内存加swap的使用配额









