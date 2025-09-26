
Ingress-Nginx 七层负载均衡 两次完整的TCP连接


```shell

[root@k8s-master ~]# helm repo add ingress-nginx
https://kubernetes.github.io/ingress-nginx
[root@k8s-master src]# helm pull ingress-nginx/ingress-nginx

# 修改 values.yaml 文件
1- 修改 hostNetwork 的值为 true
2- dnsPolicy的值改为:clusterFirstwithHostNet
3- kind类型更改为:DaemonSet
4- 关闭所有镜像的 digest
5- ingressclassResource. default=true

关于 dnsPolicy

ClusterFirstWithHostNet:

	● 当Pod的hostNetwork 设置为true时,使用该DNS策略。

	· 这意味着Pod的网络命名空间与主机共享,Pod使用主机的网络栈。

	· 在此配置下,Pod 将首先尝试通过主机上的DNS解析DNS请求。如果主机上没有找到,则会将请求发送到kube-dns服务,由kube-dns服务进行处理。

	● 这种策略适用于需要与主机网络共享的特殊情况,但它不会为Pod提供专用的DNS解析功能。

ClusterFirst:

	● 这是Kubernetes 中默认的DNS策略。

	● 当Pod的hostNetwork 设置为false或未设置时,使用该策略。

	· 在此策略下,Pod 首先尝试通过kube-dns服务解析DNS请求。如果kube-dns无法解析,则会向上级DNS服务器继续发起请求。

	· 这种策略适用于大多数情况,其中Pod需要使用Kubernetes集群的DNS服务解析其他Pod或服务的主机名。

```


```shell

kubectl create ns ingress

helm install ingress-nginx -n ingress . -f values.yaml


[root@k8s-master01 10]# kubectl get pod -n ingress -o wide
NAME
ingress-nginx-controller-mbjxf
ingress-nginx-controller-x5m6c

```
上层添加loadbalencer 对应ingress controller节点

```shell

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: ingress-httpproxy-www1
spec:
ingressClassName: nginx
rules:
- host: www1.xinxianghf.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: ingress-httpproxy-www1
port:
number: 80

kubectl get ingress


```

## Ingress HTTPS 代理

```shell

#创建tls分类的secret，名为ingress-nginx-tls
kubectl create secret tls ingress-nginx-tls --key tls.key --cert tls.crt

[root@k8s-master01 https]# cat ingress.yaml
> apiversion: networking.k8s.io/v1
kind: Ingress
metadata:
name: ingress-httpproxy-ssl
namespace: default
annotations:
	nginx. ingress. kubernetes. io/ssl-redirect: "true"
spec:
ingressclassName: nginx
rules:
- host: ssl.xinxianghf.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: ingress-httpproxy-ssl
port:
number: 80

tls:
	- hosts:
		- ssl.xinxianghf.com
	secretName: ingress-nginx-tls

```

## Ingress-nginx BasicAuth代理

```shell
http认证文件创建

$ dnf -y install httpd-tools
$ htpasswd -c auth xinxianghf
$ kubectl create secret generic ingress-basic-auth -- from-file=auth

[root@k8s-master01 auth]# cat ingress.yaml
apiversion: networking.k8s.io/v1
kind: Ingress
metadata:
name: ingress-with-auth
annotations:
nginx. ingress. kubernetes. io/auth-type: basic
nginx. ingress. kubernetes. io/auth-secret: ingress-basic-auth
nginx. ingress. kubernetes. io/auth-realm: 'Authentication Required -
xinxianghf'
。。。
	pathType:ImplementationSpecific 由ingress控制器本身去处理

```

## Ingress-nginx 域名重定向

```shell

[root@k8s-master01 ~]# cat ingress-redirect.yaml
apiversion: networking.k8s.jo/vl
kind. Ingress
metadata:
name: redirect.xinxianghf.com
namespace: default
annotations:
nginx. ingress. kubernetes. io/permanent-redirect: https://www.baidu.com
nginx. ingress. kubernetes. io/permanent-redirect-code: '301'
spec:
ingressclassName: "nginx"
rules:
- host: redirect.xinxianghf.com
http:


```

## Ingress-nginx 地址重写

```yaml
apiversion: networking.k8s.io/v1
kind: Ingress
metadata:
name: rew. xinxianghf.com
namespace: default
annotations:
nginx. ingress. kubernetes. io/rewrite-target: /$2   #匹配第二个正则分组
spec :
ingressclassName: "nginx"
rules:
- host: rew. xinxianghf.com
http:
paths:
- path: /api(/|$)(.*)
pathType: Implementationspecific
backend:
service:
name: www1svc
port:
number: 80


```


## Ingress-nginx 错误代码重定向 - 默认错误后端地址

在安装ingress时就需要配置好

helm unstall ingress-nginx

修改values.yaml
```yaml

defaultBackend:
enabled: true
name: defaultbackend
image:
registry: docker.io
image: wangyanglinux/tools
tag: "errweb1.0"
port: 80

```

helm install inginx-nginx

defaultbackend的pod被创建出来

在nginx层面返回错误

## Ingress-nginx 错误代码重定向 - 单独申明错误后端

```yaml

kind: Ingress
+
metadata:
name: err.xinxianghf.com
namespace: default
annotations:
nginx. ingress. kubernetes. io/default-backend: 'errcode'
nginx. ingress. kubernetes. io/custom-http-errors: "404,415"  #指定错误码
spec:
rules:
- host: err.xinxianghf.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: errtest
port:
number: 80

```

## Ingress-nginx 匹配请求头

Ingress annotations的 nginx.ingress.kubernetes.io/server-snippet 配置。Snippet配置是专门用于
一些复杂的Nginx配置,和Nginx配置通用,在这里模拟下移动端与电脑端访问同一个域名转发到不同服务。
```bash

$ kubectl edit ConfigMap ingress-nginx-controller -n ingress
data:
allow-snippet-annotations: "true"

---
apiVersion: networking. k8s. io/vl
kind: Ingress
metadata:
name: snippet.xinxianghf.com
namespace: default
annotations:
nginx. ingress. kubernetes. io/server-snippet: |
	set Sagentflag 0;
	if (Shttp_user_agent ~* "(Android| IPhone)") {
		set $agentflag 1;
		}
	if (Sagentflag = 1) {
		return 302 http://www.baidu.com;
	}
spec :
rules:

- host: snippet.xinxianghf.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:

```


## Ingress-nginx 配置黑白名单

配置方案
●Annotations:只对指定的ingress生效
● ConfigMap:全局生效
● 若是同时配置了Annotations和configmap,一般都是annotations生效,configmap不生效,
因为 annotations 优先级比configmap 高

黑白名单区别
● 白名单是默认是拒绝所有,只允许一个地址去访问
● 黑名单是不允许该地址去访问所有

黑白名单配置使用 configmap还是 annotations
● 黑名单可以使用 ConfigMap去配置
● 白名单建议使用Annotations 去配置

```yaml

$ kubectl edit cm ingress-nginx-controller -n ingress
data:
allow-snippet-annotations: "true"
block-cidrs: 192.168.10.12

or:whitelist-source-range: 192.168.10.1/22

OR:

#annotations黑名单
kind: Ingress
metadata:
annotations:
nginx. ingress. kubernetes. io/server-snippet: |-
deny 192.168.10.11;
allow all;
name: black.xinxianghf.com

#annotations白名单
kind: Ingress
metadata:
annotations:
nginx. ingress. kubernetes. io/whitelist-source-range: 192.168.10.11
name: white.xinxianghf.com





```


## Ingress-nginx 


有时候可能需要限制速率以降低后端压力,或者限制单个IP每秒的访问速率防止攻击。此时可以使用
Nginx的 rate limit 进行配置

annotations：
◆nginx.ingress.kubernetes.io/limit-rps:限制每秒的连接,单个IP
◆nginx.ingress.kubernetes.io/limit-rpm:限制每分钟的连接,单个IP
◆nginx.ingress.kubernetes.io/limit-rate:限制客户端每秒传输的字节数,单位为K,需要开启proxy-buffering
◆nginx.ingress.kubernetes.io/limit-whitelist: 速率限制白名单

ad -c 10 -n 100 url |grep requests


## Ingress-nginx 灰度或金丝雀发布


```bash
kind: Ingress
metadata:
name: v2xinxianghf.com
namespace: default
annotations:
nginx. ingress. kubernetes. io/canary: "true"
nginx. ingress. kubernetes. io/canary-weight: "10"   #v2在ingress请求中占比
spec:
rules:
- host: svc.xinxianghf.com  #相同的host名
http:
paths:
- pathType: Prefix
path: "/"
backend:
service:
name: v2-svc
port:
number: 80

```

## Ingress-nginx 代理后端https协议


```bash

apiversion: networking.k8s. jo/v1.
kind: Ingress
metadata:
annotations:
nginx. ingress. kubernetes. io/backend-protocol: HTTPS
name: proxyhttps. xinxianghf.com
namespace: default
spec :
rules:
- host: proxyhttps. xinxianghf.com
http:
paths:
- backend:
service:
name: proxyhttps-svc
port:
number: 443
path: /
pathType: ImplementationSpecific

```

## Ingress-nginx 四层代理 （不建议）

```bash
$ kubectl edit ds -n ingress ingress-nginx-controller
spec:
containers:
- args:
- /nginx-ingress-controller
- -- udp-services-configmap=$(POD_NAMESPACE)/nginx-ingress-udp-configmap

apiversion: v1
kind: ConfigMap
metadata:
name: nginx-ingress-udp-configmap
namespace: ingress
data:
"53": "kube-system/kube-dns:53"


```


## Ingress-nginx 链路追踪

采用插件Zipkin或Jaeger


```bash

# 官方部署示例文件
https://raw.githubusercontent.com/jaegertracing/jaeger-kubernetes/master/all-
in-one/jaeger-all-in-one-template.yml

$ kubectl edit cm ingress-nginx-controller -n ingress
apiversion: v1
data:
	allow-snippet-annotations: "true"
	enable-opentracing: "true" #开启
	jaeger-collector-host: jaeger-agent.ingress-ns.svc.cluster.local #链路追踪的sVC名称
kind: ConfigMap
metadata:
	name: ingress-nginx-controller
	namespace: ingress-ns

she11

```
