# 网络
主机中有虚拟网桥docker0，每个容器分配一个虚拟网卡veth pair

同宿主机的容器通过网桥docker0通信

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
组成：
- kubectl
- kube-apiserver（master）
- kube-controller-manager（master）
- kube-scheduler（master）
- etcd
- kubelet
- dns

同一pod中的容器仅需通过localhost就能互相通信
