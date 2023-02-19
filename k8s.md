# 网络
namespace,veth pair,网桥，iptalbes+netfilter，路由

通过veth pair在不同namepace通信： ip netns exec netns1 ip link show

网桥是虚拟的二层网络设备，通过mac表

主机中有虚拟网桥docker0，每个容器分配一个虚拟网卡veth pair

同宿主机的容器通过网桥docker0通信

容器默认使用bridge模式，每个容器创建一个veth pair，一端在网桥，一端映射到容器内eth0.容器mac在02：42：ac：11：00：00-02：42：ac：11：ff：ff

POD之间通过私有IP通信

每个prod都有一个pause容器，其余容器网络模型为container模式


## k8s网络 CNI容器通信网络接口
有flannel， calico，weave，contiv等

宿主机网桥变为cni网桥：cni0

CNI插件三种网络实现模式：

overlay：隧道技术，容器网络和主机网络不通网段 flannel（UDP，vxlan），calico（IPIP）

三层路由模式：容器网络和主机网络不通网段，容器互通基于主机间路由表打通 flannel（host-gw），calico（BGP）

			需要二层网络在同一局域网，即主机网络能直接互通，不经过路由
			
			flannel通过kubernates或etcd保存路由信息；calico通过BGP动态路由协议来分发整个集群路由信息
			
			BGP： border gateway protocol，实现大规模网络中节点路由信息同步共享的一种协议
			
			这时的calico的cni插件会在容器的veth pair设备添加路由规则，没有了docker0网桥
			
underlay网络：容器网络和主机网络不通网段，但地位相同，由底层网络互通，即底层路由器添加容器的路由表 calico（BGP）


### calico 
calico默认维护的网络模式位 node-to-node mesh ，节点进行路由交换会以2的N次方增长，应控制在50节点一下

超过时用RR模式（Router Reflector），RR节点负责和所有节点通信路由信息，建议RR》=2




# flannel

# K8S

每三个月发布一个新版本

master/worker

Master：
- kube-apiserver
- Etcd： 分布式键值型数据库
- kube-controller-manager
- kube-scheduler：选择节点分配pod
- kube-proxy：pod方式部署时需要，也可以pod方式部署
- kubelet

worker节点：
- kube-proxy：管理service的访问入口,pod到service，集群外访问service，维护iptables
- kubelet：
- 容器引擎

Add-ons：
- core-dns
- Ingress-controller
- Dashboard
- Flannel/Calico
- metric：监控


架构接口：
- CRI runtime
- CNI network
- CSI storage

部署工具：
- 源码部署：github，编译，生成证书，启动参数，安装
- kubeadmin：pod方式，默认访问google镜像
- 脚本或第三方工具


## Kubeadmin

社区主推，add-on只负责dns

```sh
ufw disable
swapoff -a 
sed -i 's/.*swap.*/#&/' /etc/fstab
modprobe br_netfilter
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
apt update -y
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common gnupg-agent ntpdate
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get -y update
apt-get -y install docker-ce
sed -i '18c ExecStartPost=/sbin/iptables -P FORWARD ACCEPT' /lib/systemd/system/docker.service
cat <<EOF > /etc/docker/daemon.json
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
systemctl daemon-reload && systemctl restart docker.service
systemctl daemon-reload
systemctl restart docker
systemctl enable docker

```

https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

安装kubeadm，kubectl，kubelet，修改配置文件，初始化，添加令牌，安装网络插件，加入worker，检查集群状态，bash补齐+别名

```sh
apt-get install -y kubelet=1.22.0-00 kubeadm=1.22.0-00 kubectl=1.22.0-00
apt-mark hold kubelet kubeadm kubectl
...
kubeadm config print init-defaults > init.yaml
kubeadm init --config init.yaml |tee k8s.txt


/etc/kubernetes/manifests
k get componentstatuses

kubectl get nodes
kubectl get pods -o wide -A
kubectl cluster-info
kubectl get all -A
```

配置文件：



### yaml
yaml：
- 字符串可以不用引号
- 大小写敏感
- apiversion api组

```language
查看api version
kubectl api-versions

查看api resources，namespace为false的为集群资源
kubectl api-resources
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1      
```

### namespace

默认的namespace：default，kube-system，kube-public，kube-node-lease

kube-public；可以被所有人访问，包括未认证用户

kubectl -n lishuo get all

kubectl delete namespace_name   //删除namespace会将namespace下的资源一并删除

### POD

- 一个pod只有一个独立的IP
- 每个POD中会有一个pause容器，即根容器。pod中的多个容器共享pause容器的ip和存储，容器间可以通过localhost互访



生命周期：
- Pending（悬决）	Pod 已被 Kubernetes 系统接受，但有一个或者多个容器尚未创建亦未运行。此阶段包括等待 Pod 被调度的时间和通过网络下载镜像的时间，
- Running（运行中）	Pod 已经绑定到了某个节点，Pod 中所有的容器都已被创建。至少有一个容器仍在运行，或者正处于启动或重启状态。
- Succeeded（成功）	Pod 中的所有容器都已成功终止，并且不会再重启。
- Failed（失败）	Pod 中的所有容器都已终止，并且至少有一个容器是因为失败终止。也就是说，容器以非 0 状态退出或者被系统终止。
- Unknown（未知）	因为某些原因无法取得 Pod 的状态。这种情况通常是因为与 Pod 所在主机通信失败。

```sh
vim ~/.vimrc
:set paste  //为了复制时保留缩进

kubectl -n lishuo run nginx --image nginx --dry-run=client -o yaml > nginx.yaml  //打印api对象而不创建它们
kubectl describe pod mypod
kubectl delete -f xxx.yaml
//create只在第一次创建时有用，建议apply
kubectl edit pod nginx
kubectl exec -it mypod --container mycontainer -- /bin/bash

--show-labels



```

patch修改时只能以json格式来改


init container:
- 运行一次就结束，必须成功才继续，可用来做一些初始化工资

sidecar容器：
可以日志代理/转发，service mesh，探活，其他辅助性工作

Static-Pod:
- 由kubelet直接管理的，
- staticPodPath: /etc/kubernetes/manifests  //kubelet配置文件中的配置


### Lalel

```sh
kubectl label nodes k8s-node1 env=test

删除： kubectl lalel pod nginx time-

kubectl get pod -L app  //筛选带app键的标签
kubectl get pod -l app=nginx   //赛选pod中标签为app=nginx的pod
```

### Annotation

用于注释信息
```sh

kubectl describe xx //查看
kubectl annotate  node k8s-master tel=561561
kubectl annotate pod nginx author-
```






### POD控制器
用来控制pod的具体状态和行为

- replicaset：管理副本
- deployment：应用部署，副本管理，滚动升级，弹性伸缩等。
- daemonset：每个node都运行一个pod
- job：执行一次性任务
- cron job： 定时执行周期性任务
- statefulset：

replicaset：管理副本,不直接使用，一般用deployment调用RS

replicaset：RS ，selector:matchlabels

kubectl get rs

kubectl scale replicset frontend --replicas=2


Deployment:
- 创建deployment时会自动创建rs

kubectl -f dep1.yaml 


```sh
kubectl create deploymnet mydep --image=nginx   //添加record会在history中显示命令，将废弃


root@k8s-master:/data/test1# kubectl rollout history deployment nginx-deployment
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=dep1.yaml --record=true
2         kubectl apply --filename=dep1.yaml --record=true
3         <none>
kubectl rollout history deployment nginx-deployment --revision=1   //查看1版本的详细信息

kubectl rollout undo deployment nginx-deployment --to-revision=2
```

Daemonset：新node会为他自动新增pod，pormetheus，存储，日志。calico，kube-proxy

StatefulSet：
- 稳定的持久化存储，基于pvc
- 重新调度后pod name，host name不变，基于headless service
- 有序部署，有序扩展 基于init container
- 有序收缩，有序删除

三步骤：pvc，statefulset，headless service

JOB:
- restartpolicy只能选never或onfailure
kubectl logs podname  //查看容器output

CRONJOB
- 给定时间运行一次
- 给定时间周期性运行(重新创建)
```
schedule： "* * * * *"
```
### SERVICE
通过label selector实现

实现类型：
- clusterIP：提供集群内的虚拟ip地址供pod访问，默认模式
- NodePort： 所有node的暴露端口
- External IP： 指定暴露IP地址
- LoadBalancer：通过外部的负载均衡器来访问


组成模型：
- endpoint controller：监控pod - service变化
- kube-proxy：维护iptables

kube-proxy：
- iptables： 默认
- IPVS ：采用hash查表，大规模时有速度优势

service中targetport 为pod端口
```sh
root@k8s-master:/data/test1# kubectl get endpoints
NAME            ENDPOINTS                                           AGE
kubernetes      192.168.0.10:6443                                   22h
nginx-service   172.16.169.144:80,172.16.36.83:80,172.16.36.84:80   93s
root@k8s-master:/data/test1# kubectl get svc
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP   22h
nginx-service   ClusterIP   10.98.138.159   <none>        80/TCP    116s

```
默认轮询方式

NodePort： 
- 通过任意集群地址都可以访问
- type：NodePort

expose命令也可以创建service

kubectl expose deployment nginx-deployment --port 80 --target-port 80 --name nginx-service --type NodePort

External IP：
- 暴露指定IP
- type：ClusterIP， 另加externalIPs属性

### coreDNS

- 服务的dns记录名称：服务名称.namespace.svc.cluster.local
- pod的dns记录名称：pod-ip.服务名称.namespace.svc.cluster.local
- 同一namespace直接用<服务名称>访问；不通namespace用<服务名称>.<namespace>

Headless Service：
- clusterIP：None 。
- 靠集群转发，域名解析的IP时Pod的IP地址

有时不需要或不想要负载均衡，以及单独的 Service IP。 遇到这种情况，可以通过指定 Cluster IP（spec.clusterIP）的值为 "None" 来创建 Headless Service。

你可以使用无头 Service 与其他服务发现机制进行接口，而不必与 Kubernetes 的实现捆绑在一起。

对这无头 Service 并不会分配 Cluster IP，kube-proxy 不会处理它们， 而且平台也不会为它们进行负载均衡和路由。 DNS 如何实现自动配置，依赖于 Service 是否定义了选择算符。


### ingress

- 实现对URL，path，SSL的请求转发
- 创建后放在ingress-nginx的namesapce里
- 访问时  为ingress-nginx-controller的 pod
```sh
root@k8s-master:/data# k get ingressclasses.networking.k8s.io -A  //查看ingress控制器class
NAME    CONTROLLER             PARAMETERS   AGE
nginx   k8s.io/ingress-nginx   <none>       26m

root@k8s-master:/data/test1# k get ingress
NAME                        CLASS   HOSTS                     ADDRESS   PORTS   AGE
name-virtual-host-ingress   nginx   foo.bar.com,bar.foo.com             80      5s

```yaml
spec:
  ingressClassName: nginx  //1.22之后需要


  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /  基于路径，后端pod没有path时需要加上rewrite 到/
```

## 存储
通过volume来抽象出存储细节
### EmptyDir
- 生命周期同pod一致
- 容器故障时不受影响
- 可限制sizeLimit

emptyDir 的一些用途：

- 缓存空间，例如基于磁盘的归并排序。
- 为耗时较长的计算任务提供检查点，以便任务能方便地从崩溃前状态恢复执行。
- 在 Web 服务器容器服务数据时，保存内容管理器容器获取的文件。

```sh
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {}

```

### HostPath

注意下层文件权限

hostPath 的一些用法有：

- 运行一个需要访问 Docker 内部机制的容器；可使用 hostPath 挂载 /var/lib/docker 路径。kube-proxy就用这个了
- 在容器中运行 cAdvisor 时，以 hostPath 方式挂载 /sys。
- 允许 Pod 指定给定的 hostPath 在运行 Pod之前是否应该存在，是否应该创建以及应该以什么方式存在。

hostpath类型：
- DirectoryOrCreate	如果在给定路径上什么都不存在，那么将根据需要创建空目录，权限设置为 0755，具有与 kubelet 相同的组和属主信息。
- Directory	在给定路径上必须存在的目录。
- FileOrCreate	如果在给定路径上什么都不存在，那么将在那里根据需要创建空文件，权限设置为 0644，具有与 kubelet 相同的组和所有权。
- File	在给定路径上必须存在的文件。
- ···


FileOrCreate 模式不会负责创建文件的父目录。 如果欲挂载的文件的父目录不存在，Pod 启动会失败。 为了确保这种模式能够工作，可以尝试把文件和它对应的目录分开挂载


### PV/PVC

- 用于抽象存储细节
- pvc可以请求pv特定的接入模式(读写等)和大小
- pvc可以指定pv（volumeName），也可以自动筛选
- 一个pv只能绑一个pvc

accessModes访问模式：
- ReadWriteOnce
- ReadOnlyMany
- ReadWriteMany

persistentVolumeReclaimPolicy：
- Retain： 删除pvc时，pv离得数据保留；如需关联新的pvc，需删除其中的ClaimRef参数
- Recycle： pvs删除时把数据清空，保留pv
- Delete


StorageClass:
定义说明一类存储；ssd，highio，nfs等

供给方式：
- 静态： pvc中指定storageClassName
- 动态卷供给：将一个storageclass设为default,属于插件，需要修改kube-api的yaml配置。storageclass中指定provisioner。pv中env对应provisioner


```sh


- --feature-gates=RemoveSelfLink=false //1.20版本后修改kube-api的yaml配置

kubectl patch storageclass managed-nfs-storage -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'  //设置为default storageclass

kubectl edit storageclass managed-nfs-storage
allowVolumeExpansion: true  //允许此class动态扩展
```

## ConfigMap

用于容器的配置文件管理，处理不含敏感信息的配置文件，secrets处理敏感信息。

创建：
- 命令行创建时从目录，文件，命令行参数。 =
- yaml ： =

可以使用四种方式来使用 ConfigMap 配置 Pod 中的容器：
- 在容器命令和参数内：你可以使用 $(VAR_NAME) Kubernetes 替换语法在容器的 command 和 args 部分中使用 ConfigMap 定义的环境变量。
- 容器的环境变量；env。 env_from
- 在只读卷里面添加一个文件，让应用来读取。
- 编写代码在 Pod 中运行，使用 Kubernetes API 来读取 ConfigMap

```sh
k create configmap myinfo --from-file=/data/test1/configmaps //命令行从目录读取
k describe configmaps myinfo

k create configmap myuserinfo --from-file=/data/test1/configmaps/usrinfo //命令行从文件读取

--from-literal=username=lishuo  //命令行从命令参数读取
```
configmap更新
- 被挂载的 ConfigMap 内容会被自动更新。最长时间为kubelet同步周期+configmap在kubelet中的ttl
- 以环境变量方式使用的 ConfigMap 数据不会被自动更新。 更新这些数据需要重新启动 Pod。

https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-pod-configmap/

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  SPECIAL_LEVEL: very
  SPECIAL_TYPE: charm
---
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      envFrom:
      - configMapRef:
          name: special-config
  restartPolicy: Never

```
## Secret

- 只能在pod中访问明文
- 一个pod不能访问另一个pod中的secret
- 使用stringDate可以在yaml中显示明文

```sh

：r！ echo -n password123 |base64  //yaml中写编码之后的

```

secret类型：
- Opaque：用户定义的任意数据
- kubernetes.io/tls 

data 字段用来存储 base64 编码的任意数据。 提供 stringData 字段是为了方便，它允许 Secret 使用未编码的字符串。
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW5pc3RyYXRvcg==
#stringData:
#  username: administrator

```

## 主机调度

主机调度管理：
- NodeSelect: 指定有限调度
- NodeName： 指定节点部署
- Affinity and Antiaffinity：亲和性
- Taints ：污点
- Tolerations： 容忍度




NodeSelector：
- 给节点添加label，然后在spec.nodeSelector 选择node标签


NodeName：
- 强制约束到指定node节点
- nodeName: k8s-node2


Affinity and anti-affinity：
- 节点和pod。pod和pod 的关系
- 通过匹配表达式选择label
- Pod 亲和性与反亲和性的合法操作符有 In，NotIn，Exists，DoesNotExist，Gt，Lt。
```YAML
//node亲和性
spec: //deployment的tempate下。或pod下
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd     

```
亲和性类型：
- requiredDuringSchedulingIgnoredDuringExecution 
- preferredDuringSchedulingIgnoredDuringExecution weight 字段值的 范围是 1-100


```yaml
// pod亲和性
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone

```




Taints 污点：
- Taints和Tolerate可以配合
- 优先affnity
- key value不是label


Taints effect：
- NoSchedule：不允许调度，已调度的不受影响
- PreferNoSchedule: 尽量不调度
- NoExcute：不允许调度，已运行的会删除并转移

```sh
kubectl taints nodes node_name key=value effect

root@k8s-master:/data/test1# k describe nodes k8s-master |grep -i taints
Taints:             node-role.kubernetes.io/master:NoSchedule

k taint node k8s-node1 env=test1:NoSchedule
kubectl taint nodes k8s-node1 env-
```

Tolerations：
- 可以容忍在有taints的上面运行
- operator可以定义为Equal，Exists，使用Exists无需定义value


## RBAC

user，poermission，role

user绑定role，role绑定permission

用户：
- User Account： 不通过k8s管理
- Service Account

### UserAccount
生成私钥->csr->得到public key

context name 可以自己随便写

context指定cluster 和user

```sh
cd /etc/kubernetes/pki
openssl genrsa -out lishuo.key 2048   //生成私钥
openssl req -new -key lishuo.key -out lishuo.csr -subj "/CN=lishuo/O=kubernetes"
openssl x509 -req -in lishuo.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out lishuo.crt -days 365

k config set-credentials lishuo --client-certificate=lishuo.crt --client-key=lishuo.key
k config set-context lishuo@kubernetes --cluster=kubernetes --user=lishuo  

k config use-context lishuo@kubernetes

kubectl config view
```

### Role 和 ClusterRole

- 绑定到user，group，service account
- Role作用于namespace级别
- ClusterRole作用于集群级别，用于集群组件授权
- rolebounding 可以额绑定 clusterrole，按bounding里的namepace来

```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" 被忽略，因为 ClusterRoles 不受名字空间限制
  name: clusterrole1
rules:
- apiGroups: ["apps"]   //k api-resources 查看
  # 在 HTTP 层面，用来访问 Secret 对象的资源的名称为 "secrets"
  resources: ["deployments","daemonsets","statefulsets"]
  verbs: ["get", "watch", "list","create"]

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: lishuo-clusterrole1
subjects:
- kind: User
  name: lishuo # 'name' 是区分大小写的
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: clusterrole1
  apiGroup: rbac.authorization.k8s.io
```
```sh
k get clusterrole



```

### serviceAccount

- 为pod中的进程设计的
- 只能作用于命名空间
- 每个namespace都会自动创建一个“default” serviceAccount给pod
- token controller检测service account的创建，并为它们创建secret

```sh
#在名字空间 "acme" 中，将名为 view 的 ClusterRole 对象中的权限授予名字空间 "myappnamespace" 中名称为 myapp 的服务账户：
kubectl create rolebinding myappnamespace-myapp-view-binding --clusterrole=view --serviceaccount=myappnamespace:myapp --namespace=acme
```

pod使用serviceaccount：
- sepc.serviceAccountName。 自动通过secret 的volume挂载到/var/kubernetes/

k get pods mypod2-sa01 -o yaml |grep seviceAccountName



## 网络策略

- networkPolicy作用域只在某个namespace
- pod与 Pod 运行所在的节点的通信总是被允许的， 无论 Pod 或节点的 IP 地址

基于三个标识符：
- 其他被允许的pods
- 被允许的命名空间
- IP组块（CIDR）

```yaml
--- 默认拒绝所有入站流量
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress

--- 默认允许所有入站流量
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-ingress
spec:
  podSelector: {}
  ingress:
  - {}
  policyTypes:
  - Ingress

--- 示例
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```


k label ns internal project=internal


## 日常维护

- 事件查看： describe
- 容器应用日志信息查看： logs  -f（持续输出） --tail n（后n行）


sidecar：一种理念，不是api resource
```sh


```

节点维护：
- cordon:禁止调度到该节点
- drain: 驱逐该节点所有pod

监控(需安装metric组件)：
- kubectl top pods -A -l app=test --sort-by=cpu
- kubectl top nodes

1线程cpu为1000 minicores

Mi 1024进制； 

ETCD：
- 备份，恢复
- 允许挂（n-1）/2 个etcd节点

```sh

docker cp etcd_docker:/usr/local/bin/etcdctl /usr/bin

ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>

ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  snapshot restore snapshotdb

 . . .enfpoint health

# 查看证书：/etc/kubernetes/manifests/api-server
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt

```


kubeadm集群升级：
- kubeadm 和kubernetes版本对应

master: 升级kubeadm -> upgrade api-server -> drain -> kubelet kubectl，重启kubelet ->解除 uncordon

手动升级cni etcd

worker: 升级kubeadm ->


https://kubernetes.io/zh/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

## 部署策略

默认：rollout

其他： recreate

手动方式实现其他： 
- 蓝绿：一次只有一种activate，资源*2
- 金丝雀：逐步升级



