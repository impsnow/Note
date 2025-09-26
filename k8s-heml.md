
helm的本质是让k8s的应用管理（deployment，service等）可配置，能动态生成。

重要概念：
	Chart：是创建一个应用的信息集合，类似yum中的rpm
	Release:是chart的运行实例，代表了一个正在运行的应用。
	Helm cli：
	Repository：用于发布和存储chart的仓库

现行版本 HELM V3： helm client -》 kubeconfig -》 kube-apiServer （直接使用当前环境中的kubeconfig配置）


### 安装 初始化


```shell
# 安装
$ curl -fsSL -o get_helm.sh
https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

# 初始化

#当您已经安装好了Helm之后,您可以添加一个chart仓库。从Artifact Hub中查找有效的Helm chart
仓库
(官方仓库)
$ helm repo add bitnami https://charts.bitnami.com/bitnami

#当添加完成,您将可以看到可以被您安装的charts列表:

$ helm search repo bitnami
```

```bash
helm version

helm repo list

安装 chart 示例

$ helm repo update
$ helm show values bitnami/apache
$ helm install bitnami/apache -- generate-name

# 确定我们可以拿到最新的 charts 列表
helm list -n default
$ helm show chart bitnami/apache
$ helm show all bitnami/apache # chart 的所有信息

# chart 的基本信息

卸载一个版本

$ helm uninstall apache-1612624192
1612624192,它将删除和该版本相关的所有相关资源(service、deployment、pod等等)甚至版本历
史

# 该命令会从Kubernetes卸载 mysql-

-- keep-history #选项,Helm 将会保存版本历史

$ helm status apache-1612624192

# 查看该版本的信息

# Helm search

# 用于在 Helm Hub(https://hub.helm.sh)上搜索 Helm charts
$ helm search hub wordpress

# 用于在配置的 Helm 仓库中搜索 Helm charts,~/.config/helm/repositories.yaml 中被定义
持久化
helm search repo wordpress
# Helm 搜索使用模糊字符串匹配算法,所以你可以只输入名字的一部分

#自定义安装的chart名字
$ helm install apache-1612624192 bitnami/apache

$ helm show values bitnami/apache

# 查看 chart 中的可配置选项

# 使用 YAML 格式的文件覆盖上述任意配置项,并在安装过程中使用该文件
$ vi values.yaml
service:

type: NodePort

$ helm install -f values.yaml bitnami/apache -- generate-name

#也可以使用--set
 --set name=value,a=b


更多安装方法

helm instal1 命令可以从多个来源进行安装:

●chart的仓库(如上所述)

● 本地chart压缩包(helm install foo foo-0.1.1.tgz)

●解压后的chart目录(helm install foo path/to/foo)

● 完整的URL(helm install foo https://example.com/charts/foo-1.2.3.tgz

'helm upgrade'和'helm rollback':升级release和失败时恢
复

当你想升级到chart的新版本,或是修改release的配置,你可以使用helm upgrade 命令。Helm
会尝试执行最小侵入式升级。即它只会更新自上次发布以来发生了更改的内容

$ helm upgrade -f clusterip.yaml apache-23213213 bitnami/apache

在上面的例子中,apache-23213213 这个release使用相同的chart进行升级,但是使用了一个新
的YAML文件:

service. type: ClusterIP

我们可以使用 he1m get values 命令来看看配置值是否真的生效了:

$ helm get values apache-23213213

现在,假如在一次发布过程中,发生了不符合预期的事情,也很容易通过 helm rollback
[RELEASE][REVISION] 命令回滚到之前的发布版本

$ helm rollback apache-23213213 1

上面这条命令将我们的apache-23213213 回滚到了它最初的版本。release版本其实是一个增量修
订(revision)。每当发生了一次安装、升级或回滚操作,revision的值就会加1。第一次 revision
的值永远是1。我们可以使用helm history[RELEASE]命令来查看一个特定release的修订版本
号













```



```shell

基本模式

# 1、创建一个模板
$ helm create myapp
# 2、删除不用的文件
# 3、编写 template 下的 service 和 deployment 资源清单
[root@k8s-master01 myapp]# cat templates/nodePort.yaml
apiversion: v1
kind: Service
metadata:
labels:
app: myapp-test
name: myapp-test-202401110926-svc
spec:
ports:
- name: 80-80
port: 80
protocol: TCP
targetPort: 80
nodePort: 31111
selector:
app: myapp-test
type: NodePort
[root@k8s-master01 myapp]# cat templates/deployment.yaml
apiversion: apps/v1
kind: Deployment
metadata:
labels:
app: myapp-test
name: myapp-test-202401110926-deploy
spec:
replicas: 5
selector:
matchLabels:
app: myapp-test
template:
metadata:
labels:
app: myapp-test
spec:
containers:
- image: wangyanglinux/myapp:v1.0
name: myapp

# 4.发布部署
help install myapp



```

```bash
注入HELM灵魂：函数和流水线


[root@k8s-master01 myapp]# cat templates/NOTES.txt
1、这是一个测试的 myapp chart

2. myapp release %%: myapp-test-{ now | date "20060102030405" }}-deploy

3. service %: myapp-test-{{ now | date "20060102030405" }}-svc

[root@k8s-master01 myapp]# cat templates/deployment.yaml
apiversion: apps/v1
kind: Deployment
metadata:
	labels:
		app: myapp-test
	name: myapp-test-{{ now | date "20060102030405" }}-deploy
spec:
	replicas: {{ .Values. replicacount }}
	selector:
		matchLabels:
			app: myapp-test
	template:
		metadata:
			labels:
				app: myapp-test
	spec:
		containers:
		- image: {{ .Values. image. repository }}: {{ .values. image. tag }}
		name: myapp

[root@k8s-master01 myapp]# cat values.yaml
# Default values for myapp.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicacount: 5

image:
	repository: wangyanglinux/myapp
	tag: "v1.0"

service:
	type: NodePort
	nodeport: 32321



```