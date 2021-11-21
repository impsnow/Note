
## 认证

报名： https://www.cncf.io/certification/cka/ ￥2498

考生手册： https://trainingportal.linuxfoundation.org/

韩梅梅:
https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad-cks

韩梅梅:
https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad

使用考试终端的页面编辑
考试终端不要刷新
复制用ctrl+insert； 粘贴用shift+insert
自己设置命令补齐

```sh


kubectl create clusterrole deployment-clusterrole --verb=create --resource=statefulsets,daemonsets,deployments
kubectl create serviceaccount cicd-token -n app-team1
kubectl create rolebinding cicd-rolebinding --clusterrole deployment-clusterrole --serviceaccount=app-team1:cicd-token --namespace=app-team1

kubectl drain k8s-node1 --ignore-daemonsets


#01

   15  2021-11-19 13:47:05 root apt-mark unhold kubeadm && apt-get update && apt-get install -y kubeadm=1.22.1-00
   16  2021-11-19 13:47:25 root apt-mark hold kubeadm
   17  2021-11-19 13:47:34 root kubeadm version
   18  2021-11-19 13:48:10 root kubeadm upgrade apply v1.22.1
   19  2021-11-19 13:49:44 root kubectl drain k8s-master --ignore-daemonsets
   20  2021-11-19 13:50:25 root apt-mark hold kubelet kubectl
   21  2021-11-19 13:50:31 root sudo systemctl daemon-reload
   22  2021-11-19 13:50:32 root sudo systemctl restart kubelet
   23  2021-11-19 13:50:39 root kubectl uncordon k8s-master
   24  2021-11-19 13:50:46 root k get nodes


ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=/opt/KUIN00601/ca.crt --cert=/opt/KUIN00601/etcd-client.crt --key=/opt/KUIN00601/etcd-client.key \
  snapshot save /data/backup/etcd-snapshot.db

ETCDCTL_API=3 etcdctl --endpoints https://127.0.0.1:2379  snapshot restore /srv/data/etcd-snapshot-previous.db

#05
k get ns --show-labels

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-port-from-namespace
  namespace: internal
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          project: internal
    ports:
    - protocol: TCP
      port: 8080

#06
k edit deployments.apps front-end

        ports:
        - containerPort: 80
          name: http
          protocol: TCP

kubectl expose deployment front-end --port=80 --target-port=http --type=NodePort --name=front-end-svc

#07
k get ingressclasses.networking.k8s.io

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ping
  namespace: ing-internal
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /hi
        pathType: Prefix
        backend:
          service:
            name: hi
            port:
              number: 5678


k get pods -A -owide |grep ingress
curl -kl 192.168.0.11/hi

#08
kubectl scale deployment presentation --replicas=3

#09
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    disk: spinning

#10

k describe nodes |grep -i taint

#11

apiVersion: v1
kind: Pod
metadata:
  name: kucc8
spec:
  containers:
  - name: nginx
    image: nginx
  - name: redis
    image: redis
  - name: memcached
    image: memcached
  - name: consul
    image: consul

#12

apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadOnlyMany
  hostPath:
    path: "/srv/app-config"
    type: DirectoryOrCreate


#13

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-volume
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: csi-hostpath-sc
  resources:
    requests:
      storage: 13Mi
---
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: pv-volume
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage

k edit pvc pv-volume --record

#14
k logs bar | grep "unable-to-access-website" >/opt/KUTR00101/bar



#15

k get pods big-corp-app -o yaml > test15.yaml
...
k replace -f test15.yaml --force

#16 

k top pods -l name=cpu-loader -A --sort-by=cpu

#17
k describe nodes k8s-node2
root@k8s-node2:~# systemctl start kubelet
root@k8s-node2:~# systemctl enable  kubelet
```


kubeadm token create --print-join-command
kubeadm reset
