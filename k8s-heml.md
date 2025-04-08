
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


```




