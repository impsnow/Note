##
在网络方面，需要在路由器和防火墙中开放如下端口。
2377/tcp：用于客户端与 Swarm 进行安全通信。
7946/tcp 与 7946/udp：用于控制面 gossip 分发。
4789/udp：用于基于 VXLAN 的覆盖网络。

大体流程包括初始化第一个管理节点 -> 加入额外的管理节点 -> 加入工作节点 -> 完成

管理节点manager： leader follower 通常为奇数个
worker：

service
task

一个node既可以是manager ，也可以是worker
Manager nodes elect a single leader to conduct orchestration tasks.
自己包含 dns ingress

The swarm manager uses ingress load balancing to expose the services you want to make available externally to the swarm. 
The swarm manager can automatically assign the service a PublishedPort or you can configure a PublishedPort for the service.
You can specify any unused port. 
If you do not specify a port, the swarm manager assigns the service a port in the 30000-32767 range.

Swarm management commands like docker node ls only work on manager nodes.

By default, manager nodes in a swarm can execute tasks just like worker nodes.

```
docker service inspect <SERVICE-ID>
docker service ps <SERVICE-ID>
docker service scale <SERVICE-ID>=<NUMBER-OF-TASKS>
docker service rm <SERVICE-ID>

docker service create \
  --replicas 3 \
  --name redis \
  --update-delay 1m10s \
  ---update-parallelism 2 \
  --update-failure-action xx \
  redis:3.0.6

docker service update --image redis:3.0.7 redis  

```



http://c.biancheng.net/view/3178.html
