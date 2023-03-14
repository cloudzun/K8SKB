

# Kubernetes 应用平台解析





## 部署Kind群集



### 安装 docker

安装 docker, (1.23 还能支持 docker 作为容器运行时, 考虑到 docker 可以兼容更多的实验场景, 所以此例中保留使用 docker)

```bash
apt -y install apt-transport-https ca-certificates curl software-properties-common
```



```bash
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```



```bash
apt update -y 
apt install docker-ce -y 
```



可选, 国际互联网直达安装方式

```bash
# curl -sSL https://get.docker.com/ | sh
# usermod -aG docker chengzh
```



```bash
mkdir /etc/docker
```



```bash
cat > /etc/docker/daemon.json << EOF
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m",
        "max-file": "10"
    },
    "registry-mirrors": ["https://pqbap4ya.mirror.aliyuncs.com"]
}
EOF
```



```bash
systemctl restart docker
systemctl enable docker
```



### 安装 kubelet

```
apt-get update && apt-get install -y apt-transport-https
```



```bash
root@node1:~# apt-get update && apt-get install -y apt-transport-https
Hit:1 http://repo.huaweicloud.com/ubuntu focal InRelease
Hit:2 http://repo.huaweicloud.com/ubuntu focal-updates InRelease
Hit:3 http://repo.huaweicloud.com/ubuntu focal-backports InRelease
Hit:4 http://repo.huaweicloud.com/ubuntu focal-security InRelease
Hit:5 https://mirrors.aliyun.com/docker-ce/linux/ubuntu focal InRelease
Hit:6 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree
Reading state information... Done
apt-transport-https is already the newest version (2.0.9).
0 upgraded, 0 newly installed, 0 to remove and 171 not upgraded.
```



```bash
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
```



```bash
root@node1:~# curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2426  100  2426    0     0  20913      0 --:--:-- --:--:-- --:--:-- 20913
OK
```



```bash
cat > /etc/apt/sources.list.d/kubernetes.list << EOF 
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
```



```bash
apt update -y 
```



```bash
root@node1:~# cat > /etc/apt/sources.list.d/kubernetes.list << EOF
> deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
> EOF
root@node1:~# apt update -y
Hit:1 http://repo.huaweicloud.com/ubuntu focal InRelease
Hit:2 http://repo.huaweicloud.com/ubuntu focal-updates InRelease
Hit:3 http://repo.huaweicloud.com/ubuntu focal-backports InRelease
Hit:4 http://repo.huaweicloud.com/ubuntu focal-security InRelease
Hit:5 https://mirrors.aliyun.com/docker-ce/linux/ubuntu focal InRelease
Hit:6 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial InRelease
Reading package lists... Done
Building dependency tree
Reading state information... Done
171 packages can be upgraded. Run 'apt list --upgradable' to see them.
```



```bash
apt-cache madison kubelet
```



```bash
root@node1:~# apt-cache madison kubelet
   kubelet |  1.26.0-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.5-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.4-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.3-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.2-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.1-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.25.0-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.9-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.8-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.7-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.6-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.5-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.4-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.3-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.2-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.1-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.24.0-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.15-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.14-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.13-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.12-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.11-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.23.10-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.9-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.8-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.7-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.6-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.5-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.4-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.3-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.2-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.1-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet |  1.23.0-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
   kubelet | 1.22.17-00 | https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 Packages
```



安装 `1.23.00` 

```bash
apt install -y kubelet=1.23.0-00 kubeadm=1.23.0-00  kubectl=1.23.0-00
```



可选:安装当前最新版本 

```text
apt install -y kubelet kubeadm kubectl
```



### 安装 Kind 

官方方式
```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```


加速方式
```bash
curl -Lo ./kind https://chengzhstor.blob.core.windows.net/k8slab/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```



### 创建实验用群集（单节点）

创建群集配置文件
```bash
nano  config.yaml
```


```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  apiServerAddress: "192.168.1.231" # 使用虚机的IP，其他场景则设置为 127.0.0.1
  apiServerPort: 6443
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
```


创建群集
```bash
kind create cluster --config config.yaml
```

```bash
root@node1:~# kind create cluster --config config.yaml
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.25.3) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Have a question, bug, or feature request? Let us know! https://kind.sigs.k8s.io/#community 🙂
root@node1:~# kubectl cluster-info --context kind-kind
Kubernetes control plane is running at https://192.168.1.231:6443
CoreDNS is running at https://192.168.1.231:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

查看群集节点
```bash
kubectl get node -o wide
```

```bash
root@node1:~# kubectl get node -o wide
NAME                 STATUS   ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
kind-control-plane   Ready    control-plane   3m32s   v1.25.3   172.18.0.2    <none>        Ubuntu 22.04.1 LTS   5.4.0-107-generic   containerd://1.6.9
```


查看目前的pod
```bash
kubectl get pod -o wide -A
```

```bash
root@node1:~# kubectl get pod -o wide -A
NAMESPACE            NAME                                         READY   STATUS    RESTARTS   AGE   IP           NODE                 NOMINATED NODE   READINESS GATES
kube-system          coredns-565d847f94-7j587                     1/1     Running   0          24s   10.244.0.4   kind-control-plane   <none>           <none>
kube-system          coredns-565d847f94-gqpnw                     1/1     Running   0          24s   10.244.0.3   kind-control-plane   <none>           <none>
kube-system          etcd-kind-control-plane                      1/1     Running   0          38s   172.18.0.2   kind-control-plane   <none>           <none>
kube-system          kindnet-2tpcs                                1/1     Running   0          24s   172.18.0.2   kind-control-plane   <none>           <none>
kube-system          kube-apiserver-kind-control-plane            1/1     Running   0          39s   172.18.0.2   kind-control-plane   <none>           <none>
kube-system          kube-controller-manager-kind-control-plane   1/1     Running   0          37s   172.18.0.2   kind-control-plane   <none>           <none>
kube-system          kube-proxy-76lx7                             1/1     Running   0          24s   172.18.0.2   kind-control-plane   <none>           <none>
kube-system          kube-scheduler-kind-control-plane            1/1     Running   0          37s   172.18.0.2   kind-control-plane   <none>           <none>
local-path-storage   local-path-provisioner-684f458cdd-2jfl4      1/1     Running   0          24s   10.244.0.2   kind-control-plane   <none>           <none>

```



### 安装基础群集服务

安装ingress
```bash
kubectl create -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/ingress-nginx/ingress-nginx.yaml
```

安装metrics
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/metrics/metrics.yaml
```

安装 helm
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

(可选)打印kubeconfig文件
```bash
cat $HOME/.kube/config
```

(可选) 安装 Prometheus
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade prometheus prometheus-community/kube-prometheus-stack \
--namespace prometheus  --create-namespace --install \
--set prometheus.prometheusSpec.podMonitorSelectorNilUsesHelmValues=false \
--set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false
```



### 部署实例应用

创建 example 命名空间
```bash
kubectl create namespace example
```

创建数据库
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/kubernetes-example/main/deploy/database.yaml -n example
```

创建后端服务
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/kubernetes-example/main/deploy/backend.yaml -n example
```

创建前端服务
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/kubernetes-example/main/deploy/frontend.yaml -n example
```

创建 ingress
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/kubernetes-example/main/deploy/ingress.yaml -n example
```

创建HPA策略
```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/kubernetes-example/main/deploy/hpa.yaml -n example
```

(可选) 批量创建所有资源
```bash
git clone https://ghproxy.com/https://github.com/cloudzun/kubernetes-example && cd kubernetes-example

kubectl apply -f deploy -n example
```

检查pod是否部署到位
```bash
kubectl wait --for=condition=Ready pods --all -n example
```

```bash
root@node1:~# kubectl wait --for=condition=Ready pods --all -n example
pod/backend-6b55f869fd-5mnxq condition met
pod/frontend-6b5b58d5f8-97cmm condition met
pod/postgres-fbd8f9f49-n89tl condition met
```

检查 example 命名空间所有的资源对象是否正常
```bash
kubectl get all -n example
```

```bash
root@node1:~# kubectl get all -n example
NAME                            READY   STATUS    RESTARTS        AGE
pod/backend-6b55f869fd-5mnxq    1/1     Running   0               12m
pod/backend-6b55f869fd-xfsdr    1/1     Running   0               44s
pod/frontend-6b5b58d5f8-97cmm   1/1     Running   3 (9m53s ago)   12m
pod/frontend-6b5b58d5f8-zszmq   1/1     Running   0               44s
pod/postgres-fbd8f9f49-n89tl    1/1     Running   0               13m

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/backend-service    ClusterIP   10.96.194.109   <none>        5000/TCP   12m
service/frontend-service   ClusterIP   10.96.95.46     <none>        3000/TCP   12m
service/pg-service         ClusterIP   10.96.66.228    <none>        5432/TCP   13m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/backend    2/2     2            2           12m
deployment.apps/frontend   2/2     2            2           12m
deployment.apps/postgres   1/1     1            1           13m

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/backend-6b55f869fd    2         2         2       12m
replicaset.apps/frontend-6b5b58d5f8   2         2         2       12m
replicaset.apps/postgres-fbd8f9f49    1         1         1       13m

NAME                                           REFERENCE             TARGETS           MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/backend    Deployment/backend    26%/50%, 0%/50%   2         10        2          60s
horizontalpodautoscaler.autoscaling/frontend   Deployment/frontend   0%/80%            2         2         2          60s
```





## 如何使用命名空间



创建命名空间
```bash
kubectl create namespace example
```

查看命名空间
```bash
kubectl get ns
```

查看命名空间属性
```bash
kubectl describe namespace example
```

将资源部署到命名空间（示意）

```bash
kubectl apply -f deploy.yaml -n example
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: example   # 设置 namesapce
  labels:
    app: frontend
spec:
  ......
```

查看命名空间下的资源

```bash
kubectl get all -n example
```

删除命名空间
```bash
kubectl delete ns example
```



## 使用工作负载



### 使用 deployment 维护服务数量



使用以下命令生成deployment的原始配置

```bash
kubectl create deployment webserver --image=nginx --dry-run=client -o yaml 
```



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null # 删掉
  labels:
    app: webserver
  name: webserver
spec:
  replicas: 1 # 定义副本数量
  selector: # 通过lable定义所管理的pod
    matchLabels:
      app: webserver
  strategy: {} # 定义滚动升级的策略
  template: # 此处以下替换成pod yaml文件，注意缩进
    metadata:
      creationTimestamp: null
      labels:
        app: webserver # 使用相同的lable和deployment保持对仗工整
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {} #删掉
```



将之前的pod的最简版本的 yaml 文件整合（copy）进来，注意缩进以及 Pod 的 `label` 和depolyment的 `lable` 保持一致（）



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webserver
  name: webserver
spec:
  replicas: 1 # 定义副本数量
  selector: # 通过lable定义所管理的pod
    matchLabels:
      app: webserver
  strategy: {} # 定义滚动升级的策略
  template: # 此处以下替换成pod yaml文件，注意缩进
    metadata:
      creationTimestamp: null
      labels:
        app: webserver # 使用相同的lable和deployment保持对仗工整
    spec:
      containers:
      - image: nginx:1.7.9
        name: nginx
        resources: {}
```



使用示例文件创建yaml文件

```bash
nano deployment.yaml
```



创建deployment

```bash
kubectl apply -f deployment.yaml
```



查看deployment列表

```bash
kubectl get deployment -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get deployment -o wide
NAME        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES        SELECTOR
webserver   1/1     1            1           33s   nginx        nginx:1.7.9   app=webserver
```



查看 deployment 细节

```bash
kubectl describe deployment webserver
```



```bash
root@node1:~/k8slab/deployment# kubectl describe deployment webserver
Name:                   webserver
Namespace:              default
CreationTimestamp:      Wed, 21 Dec 2022 14:26:32 +0800
Labels:                 app=webserver
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=webserver
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=webserver
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   webserver-6b7c64974d (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  58s   deployment-controller  Scaled up replica set webserver-6b7c64974d to 1
```



```bash
kubectl get deployment -o yaml
```



```bash
root@node1:~/k8slab/deployment# kubectl get deployment -o yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"webserver"},"name":"webserver","namespace":"default"},"spec":{"replicas":1,"selector":{"matchLabels":{"app":"webserver"}},"strategy":{},"template":{"metadata":{"creationTimestamp":null,"labels":{"app":"webserver"}},"spec":{"containers":[{"image":"nginx:1.7.9","name":"nginx","resources":{}}]}}}}
    creationTimestamp: "2022-12-21T06:26:32Z"
    generation: 1
    labels:
      app: webserver
    name: webserver
    namespace: default
    resourceVersion: "29339"
    uid: 6034b236-d394-47d9-922a-53f0fa459552
  spec:
    progressDeadlineSeconds: 600
    replicas: 1
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: webserver
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: webserver
      spec:
        containers:
        - image: nginx:1.7.9
          imagePullPolicy: IfNotPresent
          name: nginx
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 1
    conditions:
    - lastTransitionTime: "2022-12-21T06:26:56Z"
      lastUpdateTime: "2022-12-21T06:26:56Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2022-12-21T06:26:32Z"
      lastUpdateTime: "2022-12-21T06:26:56Z"
      message: ReplicaSet "webserver-6b7c64974d" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 1
    readyReplicas: 1
    replicas: 1
    updatedReplicas: 1
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```



查看pod

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME                         READY   STATUS    RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                        1/1     Running   0          4h57m   10.244.135.3    node3   <none>           <none>
webserver-6b7c64974d-4qrtz   1/1     Running   0          2m29s   10.244.104.20   node2   <none>           <none>
```

注意新建 pod 的名称



删除某个pod

```bash
kubectl delete pod webserver-6b7c64974d-4qrtz
```



观测pod重建过程

```text
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl delete pod webserver-6b7c64974d-4qrtz
pod "webserver-6b7c64974d-4qrtz" deleted
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME                         READY   STATUS    RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                        1/1     Running   0          4h58m   10.244.135.3    node3   <none>           <none>
webserver-6b7c64974d-77g4f   1/1     Running   0          10s     10.244.104.21   node2   <none>           <none>
```



编辑deployment，将副本数调整成5个

```bash
KUBE_EDITOR="nano" kubectl edit deployment webserver
```



```yaml
spec:
  progressDeadlineSeconds: 600
  replicas: 5 # 调整此处的副本数量
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: webserver
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
```



观测pod横向扩展过程

```text
kubectl get pod 
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME                         READY   STATUS              RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                        1/1     Running             0          5h1m    10.244.135.3    node3   <none>           <none>
webserver-6b7c64974d-2t575   1/1     Running             0          9s      10.244.104.23   node2   <none>           <none>
webserver-6b7c64974d-77g4f   1/1     Running             0          2m27s   10.244.104.21   node2   <none>           <none>
webserver-6b7c64974d-cn9cc   0/1     ContainerCreating   0          9s      <none>          node3   <none>           <none>
webserver-6b7c64974d-cxd82   0/1     ContainerCreating   0          9s      <none>          node3   <none>           <none>
webserver-6b7c64974d-wwn2j   1/1     Running             0          9s      10.244.104.22   node2   <none>           <none>
root@node1:~/k8slab/deployment# kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
nginx                        1/1     Running   0          5h1m
webserver-6b7c64974d-2t575   1/1     Running   0          41s
webserver-6b7c64974d-77g4f   1/1     Running   0          2m59s
webserver-6b7c64974d-cn9cc   1/1     Running   0          41s
webserver-6b7c64974d-cxd82   1/1     Running   0          41s
webserver-6b7c64974d-wwn2j   1/1     Running   0          41s
```



使用命令进行收缩

```bash
kubectl scale deployment webserver --replicas=3
```



观测pod横向扩展过程

```bash
kubectl get pod -o wide -w 
```



```bash
root@node1:~/k8slab/deployment# kubectl scale deployment webserver --replicas=3
deployment.apps/webserver scaled
root@node1:~/k8slab/deployment# kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
nginx                        1/1     Running   0          5h2m
webserver-6b7c64974d-77g4f   1/1     Running   0          4m6s
webserver-6b7c64974d-cn9cc   1/1     Running   0          108s
webserver-6b7c64974d-cxd82   1/1     Running   0          108s
```



查看 deployment 伸缩历史

```bash
kubectl describe deployment webserver
```



```bash
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  8m34s  deployment-controller  Scaled up replica set webserver-6b7c64974d to 1
  Normal  ScalingReplicaSet  2m49s  deployment-controller  Scaled up replica set webserver-6b7c64974d to 5
  Normal  ScalingReplicaSet  64s    deployment-controller  Scaled down replica set webserver-6b7c64974d to 3
```



删除deployment

```bash
kubectl delete -f deployment.yaml 
```



###   使用 deployment 实现滚动更新



使用示例文件创建yaml文件

```bash
nano webserver-strategy.yaml
```



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: webserver-strategy
  name: webserver-strategy
spec:
  replicas: 6
  selector:
    matchLabels:
      app: webserver-strategy
  strategy:
    type: RollingUpdate
    rollingUpdate:  # 滚动更新策略
      maxUnavailable: 2 # 先下线两个
      maxSurge: 0
  template:
    metadata:
      name: webserver
      namespace: default
      labels:
        app: webserver-strategy
    spec:
      containers:
      - image: nginx:1.7.9
        name: nginx
        resources: {}
```



创建 deployment

```bash
kubectl apply -f webserver-strategy.yaml 
```



查看 deployment 列表,关注 pod 节点数映像版本信息

```bash
kubectl get deployment -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get deployment -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES        SELECTOR
webserver-strategy   6/6     6            6           28s   nginx        nginx:1.7.9   app=webserver-strategy
```



查看 deployment 细节，确定目前的 deployment 的滚动更新策略：`RollingUpdateStrategy`

```bash
kubectl describe deployment webserver-strategy
```



```bash
RollingUpdateStrategy:  2 max unavailable, 0 max surge
Pod Template:
  Labels:  app=webserver-strategy
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
```



修改deployment配置，将映像版本提升到1.8

```bash
kubectl set image deployment webserver-strategy nginx=nginx:1.8
```



观察pod滚动升级过程

```text
kubectl get pod -o wide -w 
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide -w
NAME                                  READY   STATUS              RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                                 1/1     Running             0          5h19m   10.244.135.3    node3   <none>           <none>
webserver-strategy-568bc9cf6b-rt4qg   1/1     Terminating         0          80s     10.244.135.22   node3   <none>           <none>
webserver-strategy-568bc9cf6b-zplmv   1/1     Terminating         0          76s     10.244.104.41   node2   <none>           <none>
webserver-strategy-5c5fcb9b54-2jl95   1/1     Running             0          4s      10.244.135.26   node3   <none>           <none>
webserver-strategy-5c5fcb9b54-46xxv   0/1     ContainerCreating   0          2s      <none>          node2   <none>           <none>
webserver-strategy-5c5fcb9b54-4djbg   0/1     ContainerCreating   0          1s      <none>          node3   <none>           <none>
webserver-strategy-5c5fcb9b54-4hdz8   1/1     Running             0          8s      10.244.135.25   node3   <none>           <none>
webserver-strategy-5c5fcb9b54-b4ptl   1/1     Running             0          5s      10.244.104.43   node2   <none>           <none>
webserver-strategy-5c5fcb9b54-ftqnf   1/1     Running             0          8s      10.244.104.42   node2   <none>           <none>
webserver-strategy-5c5fcb9b54-4djbg   0/1     ContainerCreating   0          2s      <none>          node3   <none>           <none>
webserver-strategy-568bc9cf6b-rt4qg   0/1     Terminating         0          81s     10.244.135.22   node3   <none>           <none>
webserver-strategy-568bc9cf6b-rt4qg   0/1     Terminating         0          81s     10.244.135.22   node3   <none>           <none>
webserver-strategy-568bc9cf6b-rt4qg   0/1     Terminating         0          81s     10.244.135.22   node3   <none>           <none>
webserver-strategy-568bc9cf6b-zplmv   0/1     Terminating         0          77s     <none>          node2   <none>           <none>
webserver-strategy-568bc9cf6b-zplmv   0/1     Terminating         0          77s     <none>          node2   <none>           <none>
webserver-strategy-568bc9cf6b-zplmv   0/1     Terminating         0          77s     <none>          node2   <none>           <none>
webserver-strategy-5c5fcb9b54-46xxv   1/1     Running             0          3s      10.244.104.44   node2   <none>           <none>
webserver-strategy-5c5fcb9b54-4djbg   1/1     Running             0          3s      10.244.135.27   node3   <none>           <none>

```



借助 lens 进行观察

![image-20221221150836318](README.assets/image-20221221150836318-1678758869076-7.png)



查看deployment列表,重点关注映像版本信息

```bash
kubectl get deployment -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get deployment -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES      SELECTOR
webserver-strategy   6/6     6            6           5m7s   nginx        nginx:1.8   app=webserver-strategy
```



修改deployment滚动升级配置，配置为以下设置

```yaml
nano webserver-strategy.yaml
      maxSurge: 2 #先上线两个
      maxUnavailable: 0
```



更新deployment

```bash
kubectl apply -f webserver-strategy.yaml 
```



查看deployment细节，确定目前的deployment的滚动更新策略

```bash
kubectl describe deployment webserver-strategy
```



```bash
RollingUpdateStrategy:  0 max unavailable, 2 max surge
Pod Template:
  Labels:  app=webserver-strategy
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
```



修改deployment配置，将映像版本提升到1.9.1

```text
kubectl set image deployment webserver-strategy nginx=nginx:1.9.1
```



观测pod滚动升级过程

```text
kubectl get pod -o wide -w 
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide -w
NAME                                  READY   STATUS              RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                                 1/1     Running             0          5h26m   10.244.135.3    node3   <none>           <none>
webserver-strategy-568bc9cf6b-87k54   1/1     Running             0          12s     10.244.135.35   node3   <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   1/1     Running             0          12s     10.244.104.52   node2   <none>           <none>
webserver-strategy-568bc9cf6b-lsx6w   1/1     Terminating         0          14s     10.244.104.51   node2   <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   1/1     Running             0          14s     10.244.135.34   node3   <none>           <none>
webserver-strategy-7c646cdb9b-67pwv   1/1     Running             0          5s      10.244.135.37   node3   <none>           <none>
webserver-strategy-7c646cdb9b-ccnpw   1/1     Running             0          3s      10.244.104.55   node2   <none>           <none>
webserver-strategy-7c646cdb9b-fkdpr   0/1     ContainerCreating   0          2s      <none>          node3   <none>           <none>
webserver-strategy-7c646cdb9b-nnqdc   1/1     Running             0          5s      10.244.104.54   node2   <none>           <none>
webserver-strategy-7c646cdb9b-vj6w2   0/1     ContainerCreating   0          1s      <none>          node2   <none>           <none>
webserver-strategy-568bc9cf6b-lsx6w   1/1     Terminating         0          14s     10.244.104.51   node2   <none>           <none>
webserver-strategy-7c646cdb9b-fkdpr   1/1     Running             0          2s      10.244.135.38   node3   <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   1/1     Terminating         0          14s     10.244.135.34   node3   <none>           <none>
webserver-strategy-7c646cdb9b-jq7n8   0/1     Pending             0          0s      <none>          <none>   <none>           <none>
webserver-strategy-7c646cdb9b-jq7n8   0/1     Pending             0          0s      <none>          node3    <none>           <none>
webserver-strategy-7c646cdb9b-jq7n8   0/1     ContainerCreating   0          0s      <none>          node3    <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   1/1     Terminating         0          14s     10.244.135.34   node3    <none>           <none>
webserver-strategy-568bc9cf6b-lsx6w   0/1     Terminating         0          14s     10.244.104.51   node2    <none>           <none>
webserver-strategy-568bc9cf6b-lsx6w   0/1     Terminating         0          14s     10.244.104.51   node2    <none>           <none>
webserver-strategy-568bc9cf6b-lsx6w   0/1     Terminating         0          14s     10.244.104.51   node2    <none>           <none>
webserver-strategy-7c646cdb9b-vj6w2   0/1     ContainerCreating   0          2s      <none>          node2    <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   0/1     Terminating         0          15s     10.244.135.34   node3    <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   0/1     Terminating         0          15s     10.244.135.34   node3    <none>           <none>
webserver-strategy-568bc9cf6b-t9mzt   0/1     Terminating         0          15s     10.244.135.34   node3    <none>           <none>
webserver-strategy-7c646cdb9b-jq7n8   0/1     ContainerCreating   0          1s      <none>          node3    <none>           <none>
webserver-strategy-7c646cdb9b-vj6w2   1/1     Running             0          2s      10.244.104.56   node2    <none>           <none>
webserver-strategy-568bc9cf6b-87k54   1/1     Terminating         0          13s     10.244.135.35   node3    <none>           <none>
webserver-strategy-568bc9cf6b-87k54   1/1     Terminating         0          14s     10.244.135.35   node3    <none>           <none>
webserver-strategy-7c646cdb9b-jq7n8   1/1     Running             0          2s      10.244.135.39   node3    <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   1/1     Terminating         0          14s     10.244.104.52   node2    <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   1/1     Terminating         0          14s     10.244.104.52   node2    <none>           <none>
webserver-strategy-568bc9cf6b-87k54   0/1     Terminating         0          15s     <none>          node3    <none>           <none>
webserver-strategy-568bc9cf6b-87k54   0/1     Terminating         0          15s     <none>          node3    <none>           <none>
webserver-strategy-568bc9cf6b-87k54   0/1     Terminating         0          15s     <none>          node3    <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   0/1     Terminating         0          15s     <none>          node2    <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   0/1     Terminating         0          15s     <none>          node2    <none>           <none>
webserver-strategy-568bc9cf6b-f9rmk   0/1     Terminating         0          15s     <none>          node2    <none>           <none>
```



借助 lens 进行观察

![image-20221221150628729](README.assets/image-20221221150628729-1678758869077-8.png)



查看版本历史信息

```bash
kubectl rollout history deployment/webserver-strategy
```



```bash
root@node1:~/k8slab/deployment# kubectl rollout history deployment/webserver-strategy
deployment.apps/webserver-strategy
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
4         <none>
```



查看历史版本

```bash
kubectl rollout history deployment/webserver-strategy  --revision=3
```



```bash
kubectl rollout history deployment/webserver-strategy  --revision=2
```



```bash
root@node1:~/k8slab/deployment# kubectl rollout history deployment/webserver-strategy  --revision=3
deployment.apps/webserver-strategy with revision #3
Pod Template:
  Labels:       app=webserver-strategy
        pod-template-hash=568bc9cf6b
  Containers:
   nginx:
    Image:      nginx:1.7.9
    Port:       <none>
    Host Port:  <none>
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>

root@node1:~/k8slab/deployment# kubectl rollout history deployment/webserver-strategy  --revision=2
deployment.apps/webserver-strategy with revision #2
Pod Template:
  Labels:       app=webserver-strategy
        pod-template-hash=5c5fcb9b54
  Containers:
   nginx:
    Image:      nginx:1.8
    Port:       <none>
    Host Port:  <none>
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
```



回滚到ver 2版本

```bash
kubectl rollout undo deployment/webserver-strategy --to-revision=2
```



验证回滚结果

```text
kubectl get deployment -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get deployment -o wide
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES      SELECTOR
webserver-strategy   6/6     6            6           10m   nginx        nginx:1.8   app=webserver-strategy
```



删除deployment

```bash
kubectl delete -f webserver-strategy.yaml
```



### 使用 StatefulSet 编排有状态应用



使用示例文件创建 yaml 文件

```bash
nano webserver.yaml
```



```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  labels:
    app: webserver
  name: webserver
spec:
  serviceName: webserver
  replicas: 3
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      name: webserver
      namespace: default
      labels:
        app: webserver
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        resources: {}
```



创建StatefulSet

```bash
kubectl apply -f webserver.yaml
```



查看pod创建过程

```bash
kubectl get pod -o wide -w
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide -w
NAME          READY   STATUS    RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx         1/1     Running   0          5h39m   10.244.135.3    node3   <none>           <none>
webserver-0   1/1     Running   0          12s     10.244.104.3    node2   <none>           <none>
webserver-1   1/1     Running   0          10s     10.244.135.49   node3   <none>           <none>
webserver-2   1/1     Running   0          8s      10.244.104.4    node2   <none>           <none>
```

关注 pod 的名称和 ip 地址



查看 StatefulSet

```bash
kubectl get sts -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get sts -o wide
NAME        READY   AGE   CONTAINERS   IMAGES
webserver   3/3     78s   nginx        nginx:1.7.9
```



查看 StatefulSet细节

```bash
kubectl describe sts webserver
```



```bash
root@node1:~/k8slab/deployment# kubectl describe sts webserver
Name:               webserver
Namespace:          default
CreationTimestamp:  Wed, 21 Dec 2022 15:10:51 +0800
Selector:           app=webserver
Labels:             app=webserver
Annotations:        <none>
Replicas:           3 desired | 3 total
Update Strategy:    RollingUpdate
  Partition:        0
Pods Status:        3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=webserver
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Volume Claims:    <none>
Events:
  Type    Reason            Age    From                    Message
  ----    ------            ----   ----                    -------
  Normal  SuccessfulCreate  2m53s  statefulset-controller  create Pod webserver-0 in StatefulSet webserver successful
  Normal  SuccessfulCreate  2m51s  statefulset-controller  create Pod webserver-1 in StatefulSet webserver successful
  Normal  SuccessfulCreate  2m49s  statefulset-controller  create Pod webserver-2 in StatefulSet webserver successful

```

关注 `Update Strategy`



修改StatefulSet配置，将映像版本提升到1.9.1

```bash
kubectl set image sts webserver nginx=nginx:1.9.1
```



观测pod滚动升级过程

```bash
kubectl get pod -o wide -w 
```



```bash
root@node1:~/k8slab/deployment# kubectl set image sts webserver nginx=nginx:1.9.1
statefulset.apps/webserver image updated
root@node1:~/k8slab/deployment# kubectl get pod -o wide -w
NAME          READY   STATUS              RESTARTS   AGE     IP             NODE    NOMINATED NODE   READINESS GATES
nginx         1/1     Running             0          5h43m   10.244.135.3   node3   <none>           <none>
webserver-0   1/1     Running             0          3m45s   10.244.104.3   node2   <none>           <none>
webserver-1   0/1     ContainerCreating   0          0s      <none>         node3   <none>           <none>
webserver-2   1/1     Running             0          3s      10.244.104.5   node2   <none>           <none>
webserver-1   0/1     ContainerCreating   0          1s      <none>         node3   <none>           <none>
webserver-1   1/1     Running             0          2s      10.244.135.50   node3   <none>           <none>
webserver-0   1/1     Terminating         0          3m47s   10.244.104.3    node2   <none>           <none>
webserver-0   1/1     Terminating         0          3m47s   10.244.104.3    node2   <none>           <none>
webserver-0   0/1     Terminating         0          3m48s   10.244.104.3    node2   <none>           <none>
webserver-0   0/1     Terminating         0          3m48s   10.244.104.3    node2   <none>           <none>
webserver-0   0/1     Terminating         0          3m48s   10.244.104.3    node2   <none>           <none>
webserver-0   0/1     Pending             0          0s      <none>          <none>   <none>           <none>
webserver-0   0/1     Pending             0          0s      <none>          node2    <none>           <none>
webserver-0   0/1     ContainerCreating   0          0s      <none>          node2    <none>           <none>
webserver-0   0/1     ContainerCreating   0          1s      <none>          node2    <none>           <none>
webserver-0   1/1     Running             0          2s      10.244.104.6    node2    <none>           <none>
```



删除StatefulSet

```bash
kubectl delete -f webserver.yaml
```



### 使用 job 实现一次性作业 



使用示例文件创建yaml文件

```bash
nano job.yaml
```



```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: resouer/ubuntu-bc 
        command: ["sh", "-c", "echo 'scale=1000; 4*a(1)' | bc -l "]
      restartPolicy: Never
  backoffLimit: 4
```



创建job

```bash
kubectl create -f job.yaml
```



观察对应的pod，几秒之后运算结束，pod会进入到completed状态

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME       READY   STATUS              RESTARTS   AGE   IP             NODE    NOMINATED NODE   READINESS GATES
nginx      1/1     Running             0          8h    10.244.135.3   node3   <none>           <none>
pi-dzfkn   0/1     ContainerCreating   0          16s   <none>         node2   <none>           <none>
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME       READY   STATUS      RESTARTS   AGE   IP             NODE    NOMINATED NODE   READINESS GATES
nginx      1/1     Running     0          8h    10.244.135.3   node3   <none>           <none>
pi-dzfkn   0/1     Completed   0          31s   10.244.104.7   node2   <none>           <none>
```



查看运算结果

```bash
kubectl logs pi-dzfkn 
```



```bash
root@node1:~/k8slab/deployment# kubectl logs pi-dzfkn
3.141592653589793238462643383279502884197169399375105820974944592307\
81640628620899862803482534211706798214808651328230664709384460955058\
22317253594081284811174502841027019385211055596446229489549303819644\
28810975665933446128475648233786783165271201909145648566923460348610\
45432664821339360726024914127372458700660631558817488152092096282925\
40917153643678925903600113305305488204665213841469519415116094330572\
70365759591953092186117381932611793105118548074462379962749567351885\
75272489122793818301194912983367336244065664308602139494639522473719\
07021798609437027705392171762931767523846748184676694051320005681271\
45263560827785771342757789609173637178721468440901224953430146549585\
37105079227968925892354201995611212902196086403441815981362977477130\
99605187072113499999983729780499510597317328160963185950244594553469\
08302642522308253344685035261931188171010003137838752886587533208381\
42061717766914730359825349042875546873115956286388235378759375195778\
18577805321712268066130019278766111959092164201988
```



查看job对象

```bash
kubectl describe jobs/pi
```



```bash
root@node1:~/k8slab/deployment# kubectl describe jobs/pi
Name:             pi
Namespace:        default
Selector:         controller-uid=b97fb100-b9cf-4112-b48f-cdd0ab8e1944
Labels:           controller-uid=b97fb100-b9cf-4112-b48f-cdd0ab8e1944
                  job-name=pi
Annotations:      batch.kubernetes.io/job-tracking:
Parallelism:      1
Completions:      1
Completion Mode:  NonIndexed
Start Time:       Wed, 21 Dec 2022 18:01:52 +0800
Completed At:     Wed, 21 Dec 2022 18:02:15 +0800
Duration:         23s
Pods Statuses:    0 Active / 1 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=b97fb100-b9cf-4112-b48f-cdd0ab8e1944
           job-name=pi
  Containers:
   pi:
    Image:      resouer/ubuntu-bc
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo 'scale=1000; 4*a(1)' | bc -l
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  101s  job-controller  Created pod: pi-dzfkn
  Normal  Completed         78s   job-controller  Job completed
```



查看 jobs

```bash
kubectl get jobs -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get jobs -o wide
NAME   COMPLETIONS   DURATION   AGE    CONTAINERS   IMAGES              SELECTOR
pi     1/1           23s        2m8s   pi           resouer/ubuntu-bc   controller-uid=b97fb100-b9cf-4112-b48f-cdd0ab8e1944
```



删除job

```bash
kubectl delete -f job.yaml
```



### 使用 cronjob 实现定时作业 

使用示例文件创建yaml文件

```bash
nano cronjob.yaml
```



```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```



创建cornjob

```bash
kubectl create -f cronjob.yaml
```



查看pods

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get pod -o wide
NAME                   READY   STATUS      RESTARTS   AGE   IP             NODE    NOMINATED NODE   READINESS GATES
hello-27860285-qzrtw   0/1     Completed   0          22s   10.244.104.8   node2   <none>           <none>
nginx                  1/1     Running     0          8h    10.244.135.3   node3   <none>           <none>
```



每隔一分钟执行一次查看jobs

```bash
kubectl get jobs -o wide
```



```bash
root@node1:~/k8slab/deployment# kubectl get jobs -o wide
NAME             COMPLETIONS   DURATION   AGE    CONTAINERS   IMAGES    SELECTOR
hello-27860285   1/1           17s        3m3s   hello        busybox   controller-uid=e93d4b9b-a3a6-4bd3-bb79-3b6f8e64610a
hello-27860286   1/1           16s        2m3s   hello        busybox   controller-uid=e0364cec-3436-49f6-aa89-e55ae0b51c3b
hello-27860287   1/1           17s        63s    hello        busybox   controller-uid=a4f5dba3-d577-4581-b411-3b87f3092f0c
hello-27860288   0/1           3s         3s     hello        busybox   controller-uid=503bfd3e-653c-4b3e-a37e-6d7e88f5c795
```



查看 cronjob

```bash
kubectl get cronjob hello
```



```bash
root@node1:~/k8slab/deployment# kubectl get cronjob hello
NAME    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */1 * * * *   False     0        41s             3m51s
```





删除 cronjob

```bash
kubectl delete -f cronjob.yaml
```



### 使用 DaemonSet 运行守护进程应用

使用以下范例，创建 deamonset yaml

```bash
nano katacoda-daemonsets.yaml 
```



```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: katacoda-daemonsets
  name: katacoda-daemonsets
spec:
  selector:
    matchLabels:
      app: katacoda-daemonsets
  template:
    metadata:
      labels:
        app: katacoda-daemonsets
    spec:
      containers:
      - image: katacoda/docker-http-server
        name: docker-http-server
        resources: {}
```



启用 DaemonSet

```bash
kubectl apply -f katacoda-daemonsets.yaml 
```



观察 pod

```
kubectl get pods -o wide
```

 

```bash
root@node1:~/k8slab/deployment# kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE   IP              NODE    NOMINATED NODE   READINESS GATES
katacoda-daemonsets-l79w6   1/1     Running   0          48s   10.244.104.15   node2   <none>           <none>
katacoda-daemonsets-qfw82   1/1     Running   0          48s   10.244.135.51   node3   <none>           <none>
nginx                       1/1     Running   0          8h    10.244.135.3    node3   <none>           <none>
```

每个节点都有一个 katacoda，master 节点暂时还没有



删除 daemonsets

```bash
kubectl delete -f katacoda-daemonsets.yaml
```





## 服务内部发现和服务外部暴露



### 服务发现

查看 backend 的pod
```bash
kubectl get pods --selector=app=backend -n example -o wide
```

```bash
root@node1:~# kubectl get pods --selector=app=backend -n example -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP            NODE                 NOMINATED NODE   READINESS GATES
backend-6b55f869fd-5mnxq   1/1     Running   0          5h31m   10.244.0.11   kind-control-plane   <none>           <none>
backend-6b55f869fd-xfsdr   1/1     Running   0          5h20m   10.244.0.13   kind-control-plane   <none>           <none>
```
特别关注某个pod的ip地址，并记录，比如 `10.244.0.11`

查看服务
```bash
kubectl get service -n example
```

```bash
root@node1:~# kubectl get svc -n example
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
backend-service    ClusterIP   10.96.194.109   <none>        5000/TCP   5h32m
frontend-service   ClusterIP   10.96.95.46     <none>        3000/TCP   5h32m
pg-service         ClusterIP   10.96.66.228    <none>        5432/TCP   5h33m
```
特比关注`backend-service`  的 ip 地址，比如 `10.96.194.109`

进入到某个前端的pod，进行测试
```bash
kubectl exec -it $(kubectl get pods --selector=app=frontend -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
```

访问此前记录的后端pod ip
```bash
wget -O - http://10.244.0.11:5000/healthy
```

```bash
root@node1:~# kubectl exec -it $(kubectl get pods --selector=app=frontend -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
/frontend # wget -O - http://10.244.0.11:5000/healthy
Connecting to 10.244.0.11:5000 (10.244.0.11:5000)
writing to stdout
{"healthy":true}
-                    100% |***************************************************************************************************************|    17  0:00:00 ETA
written to stdout
```


访问 `backend-service` 的service ip
```bash
while true; do wget -q -O- http://10.96.194.109:5000/host_name && sleep 1; done
```

```bash
/frontend # while true; do wget -q -O- http://10.96.194.109:5000/host_name && sleep 1; done
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
^C
```

使用backend-service的完全限定域名 `backend-service.example.svc.cluster.local` 进行访问
```bash
while true; do wget -q -O- http://backend-service.example.svc.cluster.local:5000/host_name && sleep 1; done
```

```bash
/frontend # while true; do wget -q -O- http://backend-service.example.svc.cluster.local:5000/host_name && sleep 1; done
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
^C
```


使用backend-service的相对域名 `backend-service`  进行访问
```bash
while true; do wget -q -O- http://backend-service:5000/host_name && sleep 1; done
```

```bash
/frontend # while true; do wget -q -O- http://backend-service:5000/host_name && sleep 1; done
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-xfsdr"}
{"host_name":"backend-6b55f869fd-5mnxq"}
{"host_name":"backend-6b55f869fd-5mnxq"}
^C
```

退出pod
```bash
/frontend # exit
command terminated with exit code 130
root@node1:~#
```



### ingress

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.1/deploy/static/provider/cloud/deploy.yaml
```

```bash
kubectl get svc -n ingress-nginx
```





## 应用配置

连接到后端应用pod查看 env
```bash
kubectl exec -it $(kubectl get pods --selector=app=backend -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
```


```bash
env | grep DATABASE
```


```bash
root@node1:~# kubectl exec -it $(kubectl get pods --selector=app=backend -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
# env | grep DATABASE
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=postgres
DATABASE_URI=pg-service
```

```bash
exit
```


连接到数据库 pod 查看 configmap
```bash
kubectl exec -it $(kubectl get pods --selector=app=database -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
```

```bash
ls  /docker-entrypoint-initdb.d
```

```bash
cat /docker-entrypoint-initdb.d/CreateDB.sql
```


```bash
root@node1:~# kubectl exec -it $(kubectl get pods --selector=app=database -n example -o jsonpath="{.items[0].metadata.name}") -n example -- sh
# ls  /docker-entrypoint-initdb.d
CreateDB.sql
# cat /docker-entrypoint-initdb.d/CreateDB.sql
CREATE TABLE text (
    id serial PRIMARY KEY,
    text VARCHAR ( 100 ) UNIQUE NOT NULL
);#
```

```bash
exit
```





## 资源配额和弹性扩缩容



查看HPA设置
```bash
kubectl get hpa -n example
```

```bash
root@node1:~# kubectl get hpa -n example
NAME       REFERENCE             TARGETS           MINPODS   MAXPODS   REPLICAS   AGE
backend    Deployment/backend    28%/50%, 0%/50%   2         10        2          17h
frontend   Deployment/frontend   0%/80%            2         2         2          17h
```

查看pod数量
```bash
kubectl get pod -n example
```

```bash
root@node1:~# kubectl get pod -n example
NAME                        READY   STATUS    RESTARTS      AGE
backend-6b55f869fd-59vcn    1/1     Running   0             17h
backend-6b55f869fd-mbppw    1/1     Running   0             17h
frontend-6b5b58d5f8-4h5tz   1/1     Running   3 (16h ago)   17h
frontend-6b5b58d5f8-s2pm6   1/1     Running   1 (16h ago)   17h
postgres-fbd8f9f49-dsdr7    1/1     Running   0             17h
```

访问 http://192.168.1.231/api/ab

查看pod的性能负载，可以观察到已经开始横向扩容了
```bash
kubectl top pod -n example
```

```bash
root@node1:~# kubectl top pod -n example
NAME                        CPU(cores)   MEMORY(bytes)
backend-6b55f869fd-59vcn    255m         38Mi
backend-6b55f869fd-l5tlz    39m          33Mi
backend-6b55f869fd-mbppw    1m           35Mi
frontend-6b5b58d5f8-4h5tz   1m           183Mi
frontend-6b5b58d5f8-s2pm6   1m           172Mi
postgres-fbd8f9f49-dsdr7    1m           31Mi
```

查看 HPA
```bash
kubectl get hpa -n example
```

```bash
root@node1:~# kubectl get hpa -n example
NAME       REFERENCE             TARGETS             MINPODS   MAXPODS   REPLICAS   AGE
backend    Deployment/backend    27%/50%, 100%/50%   2         10        4          17h
frontend   Deployment/frontend   0%/80%              2         2         2          17h
```


多刷新几次页面，让性能负荷暴涨，再次观察HPA和pod
```bash
root@node1:~# kubectl get hpa -n example
NAME       REFERENCE             TARGETS             MINPODS   MAXPODS   REPLICAS   AGE
backend    Deployment/backend    30%/50%, 198%/50%   2         10        8          17h
frontend   Deployment/frontend   0%/80%              2         2         2          17h
root@node1:~# kubectl get pod -n example
NAME                        READY   STATUS    RESTARTS      AGE
backend-6b55f869fd-59vcn    1/1     Running   0             17h
backend-6b55f869fd-5vv9h    0/1     Pending   0             13s
backend-6b55f869fd-dc7mk    0/1     Pending   0             28s
backend-6b55f869fd-l5tlz    1/1     Running   0             4m43s
backend-6b55f869fd-lcgb4    0/1     Pending   0             28s
backend-6b55f869fd-mbppw    1/1     Running   0             17h
backend-6b55f869fd-pg5wp    0/1     Pending   0             28s
backend-6b55f869fd-rdwpb    1/1     Running   0             4m43s
backend-6b55f869fd-rr8s2    0/1     Pending   0             28s
backend-6b55f869fd-xx4gq    0/1     Pending   0             13s
frontend-6b5b58d5f8-4h5tz   1/1     Running   3 (17h ago)   17h
frontend-6b5b58d5f8-s2pm6   1/1     Running   1 (17h ago)   17h
postgres-fbd8f9f49-dsdr7    1/1     Running   0             17h
```





## 健康状态检查



使用示例文件创建yaml文件

```bash
nano nginx-healthcheck-readinessprobe.yaml
```



```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-readinessprobe
  namespace: default
  labels:
    app: nginx-readinessprobe
  annotations:
    app: nginx-readinessprobe
spec:
  dnsPolicy: Default
  hostNetwork: false
  restartPolicy: Always
  hostAliases:
  - ip: "192.168.0.181"
    hostnames:
    - "cka01"
    - "cka-master"
  - ip: "192.168.0.41"
    hostnames:
    - "cka02"
  - ip: "192.168.0.241"
    hostnames:
    - "cka03"
  volumes:
  - name: web-root
    hostPath:
      path: /data
  - name: web-path
    emptyDir: 
  initContainers:
  - name: pullcode
    image: busybox
    volumeMounts:
    - name: web-path
      mountPath: /data
    command:
    - /bin/sh
    - -c
    - "echo hello > /data/index.html; touch /data/healthy"
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: Always
    resources:
      requests:
        cpu: "0.1"
        memory: "32Mi"
      limits:
        cpu: "0.2"
        memory: "64Mi"
    startupProbe: # 启动检查，脚本探活
      exec:
        command:
          - /bin/sh
          - -c
          - "cat /usr/share/nginx/html/healthy"
      initialDelaySeconds: 5 
      periodSeconds: 1
      timeoutSeconds: 1
      failureThreshold: 18
      successThreshold: 1 
    livenessProbe:  # 存活检查，端口探活
      tcpSocket:
        port: 8080
      periodSeconds: 10
      timeoutSeconds: 1
      failureThreshold: 3
      successThreshold: 1 
    readinessProbe: # 就绪检查，路径探活
      httpGet:
        port: 8080
        path: /
      periodSeconds: 1
      timeoutSeconds: 1
      failureThreshold: 3
      successThreshold: 1 
    volumeMounts:
    - name: web-root
      mountPath: /data
    - name: web-path
      mountPath: /usr/share/nginx/html
    env:
    - name: mysqlhost
      value: "10.96.0.110"
    - name: mysqlport
      value: "3306"
    - name: mysqldb
      value: "wordpress"
    ports:
    - name: web-port
      containerPort: 80
      protocol: TCP
```



创建pod

```bash
kubectl apply -f nginx-healthcheck-readinessprobe.yaml 
```



查看pod

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/pod# kubectl get pod -o wide
NAME                   READY   STATUS    RESTARTS      AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                  1/1     Running   0             4h27m   10.244.135.3    node3   <none>           <none>
nginx-readinessprobe   0/1     Running   2 (29s ago)   109s    10.244.104.16   node2   <none>          <none>
```

多查看几次，可以观测到pod有重启现象



查看pod详细信息

```bash
kubectl describe pod nginx-readinessprobe
```



```bash
...
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  2m16s                default-scheduler  Successfully assigned default/nginx-readinessprobe to node2
  Normal   Pulling    2m16s                kubelet            Pulling image "busybox"
  Normal   Pulled     2m16s                kubelet            Successfully pulled image "busybox" in 336.609183ms
  Normal   Created    2m16s                kubelet            Created container pullcode
  Normal   Started    2m15s                kubelet            Started container pullcode
  Normal   Pulling    2m15s                kubelet            Pulling image "nginx"
  Normal   Pulled     119s                 kubelet            Successfully pulled image "nginx" in 15.311334882s
  Normal   Created    119s                 kubelet            Created container nginx
  Normal   Started    119s                 kubelet            Started container nginx
  Warning  Unhealthy  107s                 kubelet            Liveness probe failed: dial tcp 10.244.104.16:8080: connect: connection refused
  Warning  Unhealthy  99s (x16 over 114s)  kubelet            Readiness probe failed: Get "http://10.244.104.16:8080/": dial tcp 10.244.104.16:8080: connect: connection refused
```

可以观察到 `Liveness` 和 `Readiness` 都有报错



删除 pod

```bash
kubectl delete -f nginx-healthcheck-readinessprobe.yaml 
```



修改yaml文件

```bash
nano nginx-healthcheck-readinessprobe.yaml
```



```yaml
 livenessProbe:  # 存活检查，端口探活
      tcpSocket:
        port: 80
      periodSeconds: 10
      timeoutSeconds: 1
      failureThreshold: 3
      successThreshold: 1
```

将 `livenessProbe` 的端口号改为80



重新创建pod

```bash
kubectl apply -f nginx-healthcheck-readinessprobe.yaml 
```



查看pod

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/pod# kubectl get pod -o wide
NAME                   READY   STATUS    RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                  1/1     Running   0          4h34m   10.244.135.3    node3   <none>           <none>
nginx-readinessprobe   0/1     Running   0          82s     10.244.104.18   node2   <none>           <none>
```

多查看几次，pod虽然不再重启，但是一直未能就绪



查看pod详细信息

```bash
kubectl describe pod nginx-readinessprobe
```



```bash
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  102s                default-scheduler  Successfully assigned default/nginx-readinessprobe to node2
  Normal   Pulling    101s                kubelet            Pulling image "busybox"
  Normal   Pulled     86s                 kubelet            Successfully pulled image "busybox" in 15.263132583s
  Normal   Created    86s                 kubelet            Created container pullcode
  Normal   Started    86s                 kubelet            Started container pullcode
  Normal   Pulling    85s                 kubelet            Pulling image "nginx"
  Normal   Pulled     70s                 kubelet            Successfully pulled image "nginx" in 15.275607139s
  Normal   Created    70s                 kubelet            Created container nginx
  Normal   Started    70s                 kubelet            Started container nginx
  Warning  Unhealthy  49s (x17 over 65s)  kubelet            Readiness probe failed: Get "http://10.244.104.18:8080/": dial tcp 10.244.104.18:8080: connect: connection refused
```

可以观察到Readiness还有报错



删除pod

```bash
kubectl delete -f nginx-healthcheck-readinessprobe.yaml 
```



修改yaml文件

```bash
nano nginx-healthcheck-readinessprobe.yaml
```



```bash
 readinessProbe: # 就绪检查，路径探活
      httpGet:
        port: 80
        path: /
      periodSeconds: 1
      timeoutSeconds: 1
      failureThreshold: 3
      successThreshold: 1
```

将readinessProbe的端口号改为80



重新创建pod

```bash
kubectl apply -f nginx-healthcheck-readinessprobe.yaml 
```



查看pod

```bash
kubectl get pod -o wide
```



```bash
root@node1:~/k8slab/pod# kubectl get pod -o wide
NAME                   READY   STATUS    RESTARTS   AGE     IP              NODE    NOMINATED NODE   READINESS GATES
nginx                  1/1     Running   0          4h37m   10.244.135.3    node3   <none>           <none>
nginx-readinessprobe   1/1     Running   0          60s     10.244.104.19   node2   <none>           <none>
```

pod应该很快完全就绪



查看pod详细信息

```bash
kubectl describe pod nginx-readinessprobe
```



```bash
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  85s   default-scheduler  Successfully assigned default/nginx-readinessprobe to node2
  Normal  Pulling    84s   kubelet            Pulling image "busybox"
  Normal  Pulled     69s   kubelet            Successfully pulled image "busybox" in 15.293741464s
  Normal  Created    69s   kubelet            Created container pullcode
  Normal  Started    69s   kubelet            Started container pullcode
  Normal  Pulling    68s   kubelet            Pulling image "nginx"
  Normal  Pulled     53s   kubelet            Successfully pulled image "nginx" in 15.309241357s
  Normal  Created    53s   kubelet            Created container nginx
  Normal  Started    53s   kubelet            Started container nginx
```

除了查看Events中没有报错信息之外，重点查看Condition中各个阶段是否都已经Ready



清理pod

```bash
 kubectl delete -f nginx-healthcheck-readinessprobe.yaml 
```







# 应用程序容器化



## 为不同语言构建镜像



### Java 应用容器化 启动 Jar 包的构建方式

```bash
git clone https://github.com/cloudzun/gitops
```


```bash
cd gitops/docker/13/spring-boot
ls -al
```


```bash
root@node1:~# cd gitops/docker/13/spring-boot
root@node1:~/gitops/docker/13/spring-boot# ls -al
total 56
drwxr-xr-x 4 root root  4096 Feb 11 12:42 .
drwxr-xr-x 7 root root  4096 Feb 11 12:42 ..
-rw-r--r-- 1 root root   367 Feb 11 12:42 Dockerfile
-rw-r--r-- 1 root root   196 Feb 11 12:42 Dockerfile-Boot
-rw-r--r-- 1 root root     6 Feb 11 12:42 .dockerignore
-rw-r--r-- 1 root root   395 Feb 11 12:42 .gitignore
drwxr-xr-x 3 root root  4096 Feb 11 12:42 .mvn
-rwxr-xr-x 1 root root 10284 Feb 11 12:42 mvnw
-rw-r--r-- 1 root root  6734 Feb 11 12:42 mvnw.cmd
-rw-r--r-- 1 root root  1226 Feb 11 12:42 pom.xml
drwxr-xr-x 4 root root  4096 Feb 11 12:42 src
```


```bash
 nano src/main/java/com/example/demo/DemoApplication.java
```


```java
package com.example.demo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class DemoApplication {
        public static void main(String[] args) {
                SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("/hello")
        public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
                return String.format("Hello %s!", name);
        }
}

```
内容是 Demo 应用的主体文件，它包含一个 /hello 接口，使用 Get 请求访问后会返回 “Hello World”。


```bash
nano Dockerfile
```

```Dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy as builder
WORKDIR /opt/app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:go-offline
COPY ./src ./src
RUN ./mvnw clean install


FROM eclipse-temurin:17-jre-jammy
WORKDIR /opt/app
EXPOSE 8080
COPY --from=builder /opt/app/target/*.jar /opt/app/*.jar
CMD ["java", "-jar", "/opt/app/*.jar" ]
```

这是一个 Dockerfile，用于构建一个基于 Maven 构建的 Java Web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM eclipse-temurin:17-jdk-jammy as builder`：从官方的 Eclipse Temurin 镜像中拉取一个标记为 17-jdk-jammy 的版本，并将其命名为 builder。

2. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

3. `COPY .mvn/ .mvn`：将本地目录下的 .mvn 文件夹复制到容器内的 .mvn 文件夹。

4. `COPY mvnw pom.xml ./`：将本地目录下的 mvnw 和 pom.xml 文件复制到容器内的当前工作目录。

5. `RUN ./mvnw dependency:go-offline`：在容器内运行 `./mvnw dependency:go-offline` 命令，下载应用所需的依赖包并缓存到本地。

6. `COPY ./src ./src`：将本地目录下的 ./src 文件夹复制到容器内的 ./src 文件夹。

7. `RUN ./mvnw clean install`：在容器内运行 `./mvnw clean install` 命令，使用 Maven 构建应用并打包成 jar。

8. `FROM eclipse-temurin:17-jre-jammy`：从官方的 Eclipse Temurin 镜像中拉取一个标记为 17-jre-jammy 的版本。

9. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

10. `EXPOSE 8080`：声明镜像运行时监听的端口号为 8080。

11. `COPY --from=builder /opt/app/target/*.jar /opt/app/*.jar`：从之前命名为 builder 的容器中，将构建好的 jar 文件复制到当前容器的 /opt/app 目录下。

12. `CMD ["java", "-jar", "/opt/app/*.jar" ]`：设置容器启动时执行的默认命令，即运行 /opt/app 目录下的所有 jar 文件。

```bash
docker build -t spring-boot .
```



```bash
docker run --publish 8080:8080 spring-boot
```




```bash
curl localhost:8080/hello
```



```bash
root@node1:~# curl localhost:8080/hello
Hello World!root@node1:~#
```



### Spring Boot 插件的构建方式

```bash
nano Dockerfile-Boot
```


```Dockerfile
# syntax=docker/dockerfile:1

FROM eclipse-temurin:17-jdk-jammy

WORKDIR /app

COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve

COPY src ./src

CMD ["./mvnw", "spring-boot:run"]
```



这是一个 Dockerfile，用于构建一个基于 Maven 构建的 Java Web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM eclipse-temurin:17-jdk-jammy`：从官方的 Eclipse Temurin 镜像中拉取一个标记为 17-jdk-jammy 的版本。

2. `WORKDIR /app`：在容器内设置工作目录为 /app。

3. `COPY .mvn/ .mvn`：将本地目录下的 .mvn 文件夹复制到容器内的 .mvn 文件夹。

4. `COPY mvnw pom.xml ./`：将本地目录下的 mvnw 和 pom.xml 文件复制到容器内的当前工作目录。

5. `RUN ./mvnw dependency:resolve`：在容器内运行 `./mvnw dependency:resolve` 命令，下载应用所需的依赖包并缓存到本地。

6. `COPY src ./src`：将本地目录下的 ./src 文件夹复制到容器内的 ./src 文件夹。

7. `CMD ["./mvnw", "spring-boot:run"]`：设置容器启动时执行的默认命令，即使用 Maven 启动 Spring Boot 应用。



```bash
docker build -t spring-boot . -f Dockerfile-Boot
```




```bash
docker run --publish 8080:8080 spring-boot
```




```bash
root@node1:~# curl localhost:8080/hello
Hello World!root@node1:~#
```




```bash
docker images
```



```bash
root@node1:~/gitops/docker/13/spring-boot# docker images
REPOSITORY                       TAG            IMAGE ID       CREATED          SIZE
spring-boot                      latest         a61a851ebb1e   17 seconds ago   525MB
<none>                           <none>         bf0a8dd08bed   24 minutes ago   284MB
```



### Golang 应用容器化

```bash
root@node1:~/gitops/docker/13/golang# dir
Dockerfile    Dockerfile-2  Dockerfile-4  Dockerfile-6  example  go.sum
Dockerfile-1  Dockerfile-3  Dockerfile-5  Dockerfile-7  go.mod   main.go
```


```bash
nano main.go
```

```go
package main

import (
        "net/http"
        "github.com/labstack/echo/v4"
)

func main() {
        e := echo.New()
        e.GET("/hello", func(c echo.Context) error {
                return c.String(http.StatusOK, "Hello World Golang")
        })
        e.Logger.Fatal(e.Start(":8080"))
}
```

```bash
nano Dockerfile
```


```Dockerfile
# syntax=docker/dockerfile:1
FROM golang:1.17 as builder

WORKDIR /opt/app
COPY go.* ./
RUN go mod download

COPY . .

ARG LD_FLAGS='-linkmode external -w -extldflags "-static"'
RUN go build -ldflags "$LD_FLAGS" -o example

FROM alpine as runner
WORKDIR /opt/app
COPY --from=builder /opt/app/example /opt/app/example

EXPOSE 8080
CMD ["/opt/app/example"]
```


这是一个 Dockerfile，用于构建一个 Go Web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM golang:1.17 as builder`：从官方的 Golang 镜像中拉取一个标记为 1.17 的版本，并将其命名为 builder。

2. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

3. `COPY go.* ./`：将本地目录下的 go.mod 和 go.sum 文件复制到容器内的当前工作目录。

4. `RUN go mod download`：在容器内运行 `go mod download` 命令，安装应用所需的依赖包。

5. `COPY . .`：将当前目录下的所有文件和文件夹复制到容器内的当前工作目录。

6. `ARG LD_FLAGS='-linkmode external -w -extldflags "-static"'`：定义一个 LD_FLAGS 的构建参数。

7. `RUN go build -ldflags "$LD_FLAGS" -o example`：在容器内运行 `go build` 命令，构建可执行二进制文件 example，并使用定义好的 LD_FLAGS。

8. `FROM alpine as runner`：从官方的 Alpine 镜像中拉取一个最新版本，并将其命名为 runner。

9. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

10. `COPY --from=builder /opt/app/example /opt/app/example`：从之前命名为 builder 的容器中，将构建好的可执行二进制文件 example 复制到当前容器。

11. `EXPOSE 8080`：声明镜像运行时监听的端口号为 8080。

12. `CMD ["/opt/app/example"]`：设置容器启动时执行的默认命令，即运行 /opt/app/example 可执行文件。



```bash
docker build -t golang .
```


```bash
docker run --publish 8080:8080 golang
```

```bash
root@node1:~# curl localhost:8080/hello
Hello World Golangroot@node1:~#
```


### Node.js 应用容器化

```bash
root@node1:~/gitops/docker/13/node# ls -al
total 68
drwxr-xr-x  3 root root  4096 Feb 11 12:42 .
drwxr-xr-x  7 root root  4096 Feb 11 12:42 ..
-rw-r--r--  1 root root   230 Feb 11 12:42 app.js
-rw-r--r--  1 root root   589 Feb 11 12:42 Dockerfile
-rw-r--r--  1 root root    12 Feb 11 12:42 .dockerignore
drwxr-xr-x 59 root root  4096 Feb 11 12:42 node_modules
-rw-r--r--  1 root root   251 Feb 11 12:42 package.json
-rw-r--r--  1 root root 39326 Feb 11 12:42 package-lock.json

```

```bash
nano app.js
```

```node.js
const express = require('express')
const app = express()
const port = 3000

app.get('/hello', (req, res) => {
  res.send('Hello World Node.js')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```


```bash
nano Dockerfile
```

```Dockerfile
# syntax=docker/dockerfile:1
FROM node:latest AS build
RUN sed -i "s@http://\(deb\|security\).debian.org@https://mirrors.aliyun.com@g" /etc/apt/sources.list
RUN apt-get update && apt-get install -y dumb-init
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm ci --only=production


FROM node:16.17.0-bullseye-slim

ENV NODE_ENV production
COPY --from=build /usr/bin/dumb-init /usr/bin/dumb-init
USER node
WORKDIR /usr/src/app
COPY --chown=node:node --from=build /usr/src/app/node_modules /usr/src/app/node_modules
COPY --chown=node:node . /usr/src/app
CMD ["dumb-init", "node", "app.js"]
```


这是一个 Dockerfile，用于构建一个 Node.js Web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM node:latest AS build`：从官方的 Node.js 镜像中拉取最新版本，并将其命名为 build。

2. `RUN sed -i "s@http://\(deb\|security\).debian.org@https://mirrors.aliyun.com@g" /etc/apt/sources.list`：更换系统源为阿里云源。

3. `RUN apt-get update && apt-get install -y dumb-init`：在容器内更新源并安装 dumb-init。

4. `WORKDIR /usr/src/app`：在容器内设置工作目录为 /usr/src/app。

5. `COPY package*.json ./`：将本地目录下以 `package` 开头并且后缀为 `.json` 的文件复制到容器内的当前工作目录。

6. `RUN npm ci --only=production`：在容器内运行 `npm ci` 命令，只安装生产环境所需的依赖包。

7. `FROM node:16.17.0-bullseye-slim`：从官方的 Node.js 镜像中拉取一个标记为 16.17.0-bullseye-slim 的版本。

8. `ENV NODE_ENV production`：设置 NODE_ENV 环境变量为 production。

9. `COPY --from=build /usr/bin/dumb-init /usr/bin/dumb-init`：从之前命名为 build 的容器复制 dumb-init 到当前容器。

10. `USER node`：切换成 node 用户。

11. `WORKDIR /usr/src/app`：在容器内设置工作目录为 /usr/src/app。

12. `COPY --chown=node:node --from=build /usr/src/app/node_modules /usr/src/app/node_modules`：从之前命名为 build 的容器复制已经安装好的依赖包到当前容器。

13. `COPY --chown=node:node . /usr/src/app`：将本地目录下的所有文件和文件夹复制到容器内的当前工作目录。

14. `CMD ["dumb-init", "node", "app.js"]`：设置容器启动时执行的默认命令，即使用 dumb-init 启动 node 进程执行 app.js 文件。




```bash
docker build -t nodejs .
```


```bash
docker run --publish 3000:3000 nodejs
```


```bash
root@node1:~# curl localhost:3000/hello
Hello World Node.js
```



### VUE 应用容器化： http-server 构建方式

```bash
root@node1:~/gitops/docker/13/vue/example# dir -al
total 96
drwxr-xr-x 5 root root  4096 Feb 11 12:42 .
drwxr-xr-x 3 root root  4096 Feb 11 12:42 ..
-rw-r--r-- 1 root root   202 Feb 11 12:42 Dockerfile
-rw-r--r-- 1 root root   290 Feb 11 12:42 Dockerfile-Nginx
-rw-r--r-- 1 root root    12 Feb 11 12:42 .dockerignore
-rw-r--r-- 1 root root   302 Feb 11 12:42 .gitignore
-rw-r--r-- 1 root root   337 Feb 11 12:42 index.html
-rw-r--r-- 1 root root   285 Feb 11 12:42 package.json
-rw-r--r-- 1 root root 44004 Feb 11 12:42 package-lock.json
drwxr-xr-x 2 root root  4096 Feb 11 12:42 public
-rw-r--r-- 1 root root   631 Feb 11 12:42 README.md
drwxr-xr-x 4 root root  4096 Feb 11 12:42 src
-rw-r--r-- 1 root root   300 Feb 11 12:42 vite.config.js
drwxr-xr-x 2 root root  4096 Feb 11 12:42 .vscode
```

```bash
nano Dockerfile
```

```bash
# syntax=docker/dockerfile:1

FROM node:lts-alpine
RUN npm install -g http-server
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

EXPOSE 8080
CMD [ "http-server", "dist" ]

```
这是一个 Dockerfile，用于构建一个 web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM node:lts-alpine`：从官方的 Node.js 镜像中拉取一个标记为 lts-alpine 的版本。

2. `RUN npm install -g http-server`：在容器内全局安装 http-server 包。

3. `WORKDIR /app`：在容器内设置工作目录为 /app。

4. `COPY package*.json ./`：将本地目录下以 `package` 开头并且后缀为 `.json` 的文件复制到容器内的当前工作目录。

5. `RUN npm install`：在容器内运行 `npm install` 命令，安装应用所需的依赖包。

6. `COPY . .`：将当前目录下的所有文件和文件夹（除了前面已经复制的 package.json 文件）复制到容器内的当前工作目录。

7. `RUN npm run build`：在容器内运行 `npm run build` 命令，构建生产环境的应用代码。

8. `EXPOSE 8080`：声明镜像运行时监听的端口号为 8080。

9. `CMD [ "http-server", "dist" ]`：设置容器启动时执行的默认命令，即以当前工作目录下的 dist 目录为根目录启动 http-server。

```bash
docker build -t vue .
```

```bash
docker run --publish 8080:8080 vue
```

打开浏览器访问 http://localhost:8080 ，如果出现 Vue 示例应用的项目，说明镜像构建完成



### VUE 应用容器化：Nginx 构建方式

```bash
nano Dockerfile-Nginx
```

```Dockerfile
# syntax=docker/dockerfile:1

FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

```



这是一个 Dockerfile，用于构建一个 web 应用的容器镜像。以下是对每条指令的解释：

1. `FROM node:lts-alpine as build-stage`：从官方的 Node.js 镜像中拉取一个标记为 lts-alpine 的版本，并将其命名为 build-stage。

2. `WORKDIR /app`：在容器内设置工作目录为 /app。

3. `COPY package*.json ./`：将本地目录下以 `package` 开头并且后缀为 `.json` 的文件复制到容器内的当前工作目录。

4. `RUN npm install`：在容器内运行 `npm install` 命令，安装应用所需的依赖包。

5. `COPY . .`：将当前目录下的所有文件和文件夹（除了前面已经复制的 package.json 文件）复制到容器内的当前工作目录。

6. `RUN npm run build`：在容器内运行 `npm run build` 命令，构建生产环境的应用代码。

7. `FROM nginx:stable-alpine as production-stage`：从官方的 Nginx 镜像中拉取一个标记为 stable-alpine 的版本，并将其命名为 production-stage。

8. `COPY --from=build-stage /app/dist /usr/share/nginx/html`：从之前命名为 build-stage 的容器复制构建好的代码到 Nginx 安装目录下的 html 文件夹中。

9. `EXPOSE 80`：声明镜像运行时监听的端口号为 80。

10. `CMD ["nginx", "-g", "daemon off;"]`：设置容器启动时执行的默认命令，即运行 Nginx 并以 daemon 形式运行。



```bash
docker build -t vue-nginx -f Dockerfile-Nginx .
```


```bash
docker run --publish 8080:80 vue-nginx
```

打开浏览器访问 http://localhost:8080 验证一下，如果出现和前面提到的 http-server 构建方式一样的 Vue 示例应用界面，就说明镜像构建成功了。

### 构建多平台镜像 (使用docker desktop演示)

确认experiment功能开启之后，创建构建器
```bash
docker buildx create --name builder
```

设置为默认构建器
```bash
docker buildx use builder
```

初始化构建器，这一步主要是启动 buildkit 容器
```bash
docker buildx inspect --bootstrap
```

```bash
chengzh@TB15P:~$ docker buildx inspect --bootstrap
[+] Building 28.0s (1/1) FINISHED
 => [internal] booting buildkit                                                                                                                                               28.0s
 => => pulling image moby/buildkit:buildx-stable-1                                                                                                                            23.8s
 => => creating container buildx_buildkit_builder0                                                                                                                             4.2s
Name:   builder
Driver: docker-container

Nodes:
Name:      builder0
Endpoint:  unix:///var/run/docker.sock
Status:    running
Platforms: linux/amd64, linux/amd64/v2, linux/amd64/v3, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
```


```bash
git clone https://github.com/cloudzun/gitops
```

```bash
cd gitops/docker/13/multi-arch
```


```bash
root@node1:~/gitops/docker/13/multi-arch# ls -al
total 28
drwxr-xr-x 2 root root 4096 Feb 11 12:42 .
drwxr-xr-x 7 root root 4096 Feb 11 12:42 ..
-rw-r--r-- 1 root root  389 Feb 11 12:42 Dockerfile
-rw-r--r-- 1 root root 1075 Feb 11 12:42 go.mod
-rw-r--r-- 1 root root 6962 Feb 11 12:42 go.sum
-rw-r--r-- 1 root root  397 Feb 11 12:42 main.go

```

```bash
nano main.go
```

```go
package main
import (
    "net/http"
    "runtime"
    "github.com/gin-gonic/gin"
)
var (
    r = gin.Default()
)
func main() {
    r.GET("/", indexHandler)
    r.Run(":8080")
}
func indexHandler(c *gin.Context) {
    var osinfo = map[string]string{
        "arch":    runtime.GOARCH,
        "os":      runtime.GOOS,
        "version": runtime.Version(),
    }
    c.JSON(http.StatusOK, osinfo)
}
```

```bash
nano Dockerfile
```

```bash
# syntax=docker/dockerfile:1
FROM --platform=$BUILDPLATFORM golang:1.18 as build
ARG TARGETOS TARGETARCH
WORKDIR /opt/app
COPY go.* ./
RUN go mod download
COPY . .
RUN --mount=type=cache,target=/root/.cache/go-build \
GOOS=$TARGETOS GOARCH=$TARGETARCH go build -o /opt/app/example .

FROM ubuntu:latest
WORKDIR /opt/app
COPY --from=build /opt/app/example ./example
CMD ["/opt/app/example
```



这是一个 Dockerfile，用于构建一个 GO 应用的容器镜像。以下是对每条指令的解释：

1. `FROM --platform=$BUILDPLATFORM golang:1.18 as build`：从官方的 Golang 镜像中拉取一个标记为 1.18 的版本，并将其命名为 build。

2. `ARG TARGETOS TARGETARCH`：定义两个构建参数，分别为目标系统和架构。

3. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

4. `COPY go.* ./`：将本地目录下的 go.mod 和 go.sum 文件复制到容器内的当前工作目录。

5. `RUN go mod download`：在容器内运行 `go mod download` 命令，安装应用所需的依赖包。

6. `COPY . .`：将当前目录下的所有文件和文件夹复制到容器内的当前工作目录。

7. `RUN --mount=type=cache,target=/root/.cache/go-build \ GOOS=$TARGETOS GOARCH=$TARGETARCH go build -o /opt/app/example .`：使用 `--mount` 标志来将 `go-build` 缓存挂载到容器中，然后在容器中使用环境变量 `$TARGETOS` 和 `$TARGETARCH` 构建应用程序，并将其输出到容器内的 `/opt/app/example` 文件中。

8. `FROM ubuntu:latest`：从官方的 Ubuntu 镜像中拉取最新版本。

9. `WORKDIR /opt/app`：在容器内设置工作目录为 /opt/app。

10. `COPY --from=build /opt/app/example ./example`：从之前命名为 build 的容器中，将构建好的可执行二进制文件 example 复制到当前容器内的 ./example 目录中。

11. `CMD ["/opt/app/example"]`：设置容器启动时执行的默认命令，即运行 /opt/app/example 可执行文件。



登录到容器镜像库，默认是docker hub
```bash
docker login
```

构建多平台镜像
```bash
docker buildx build --platform linux/amd64,linux/arm64 -t chengzh/multi-arch:latest --push  .
```


```bash
chengzh@TB15P:~/gitops/docker/13/multi-arch$ docker buildx build --platform linux/amd64,linux/arm64 -t chengzh/multi-arch:latest --push  .
[+] Building 139.3s (28/28) FINISHED
 => [internal] load .dockerignore                                                                                                                                              0.1s
 => => transferring context: 2B                                                                                                                                                0.0s
 => [internal] load build definition from Dockerfile                                                                                                                           0.1s
 => => transferring dockerfile: 428B                                                                                                                                           0.0s
 => resolve image config for docker.io/docker/dockerfile:1                                                                                                                     6.9s
 => [auth] docker/dockerfile:pull token for registry-1.docker.io                                                                                                               0.0s
 => docker-image://docker.io/docker/dockerfile:1@sha256:d2d74ff22a0e47b21f4bbde337e2ac4cd0a02a2226ef79264878db3dc7e87df8                                                      12.4s
 => => resolve docker.io/docker/dockerfile:1@sha256:d2d74ff22a0e47b21f4bbde337e2ac4cd0a02a2226ef79264878db3dc7e87df8                                                           0.0s
 => => sha256:dd092abd7f3683f4e8e7a66e770a1cc279b2132ac7f66d3c11b7d4a0cb529b7d 11.55MB / 11.55MB                                                                              12.0s
 => => extracting sha256:dd092abd7f3683f4e8e7a66e770a1cc279b2132ac7f66d3c11b7d4a0cb529b7d                                                                                      0.3s
 => [linux/arm64 internal] load metadata for docker.io/library/ubuntu:latest                                                                                                   9.6s
 => [linux/amd64 internal] load metadata for docker.io/library/ubuntu:latest                                                                                                   9.0s
 => [linux/amd64 internal] load metadata for docker.io/library/golang:1.18                                                                                                     9.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                  0.0s
 => [auth] library/golang:pull token for registry-1.docker.io                                                                                                                  0.0s
 => [internal] load build context                                                                                                                                              0.2s
 => => transferring context: 8.97kB                                                                                                                                            0.0s
 => [linux/amd64 stage-1 1/3] FROM docker.io/library/ubuntu:latest@sha256:9a0bdde4188b896a372804be2384015e90e3f84906b750c1a53539b585fbbe7f                                    25.9s
 => => resolve docker.io/library/ubuntu:latest@sha256:9a0bdde4188b896a372804be2384015e90e3f84906b750c1a53539b585fbbe7f                                                         0.1s
 => => sha256:677076032cca0a2362d25cf3660072e738d1b96fe860409a33ce901d695d7ee8 29.53MB / 29.53MB                                                                              21.0s
 => => extracting sha256:677076032cca0a2362d25cf3660072e738d1b96fe860409a33ce901d695d7ee8                                                                                      4.7s
 => [linux/amd64 build 1/6] FROM docker.io/library/golang:1.18@sha256:50c889275d26f816b5314fc99f55425fa76b18fcaf16af255f5d57f09e1f48da                                        50.2s
 => => resolve docker.io/library/golang:1.18@sha256:50c889275d26f816b5314fc99f55425fa76b18fcaf16af255f5d57f09e1f48da                                                           0.1s
 => => sha256:cc7973a07a5b4a44399c5d36fa142f37bb343bb123a3736357365fd9040ca38a 156B / 156B                                                                                     0.4s
 => => sha256:06d0c5d18ef41fa1c2382bd2afd189a01ebfff4910b868879b6dcfeef46bc003 141.98MB / 141.98MB                                                                            37.0s
 => => sha256:bfcb68b5bd105d3f88a2c15354cff6c253bedc41d83c1da28b3d686c37cd9103 85.98MB / 85.98MB                                                                              26.9s
 => => sha256:9bd150679dbdb02d9d4df4457d54211d6ee719ca7bc77747a7be4cd99ae03988 54.58MB / 54.58MB                                                                               6.6s
 => => sha256:56261d0e6b05ece42650b14830960db5b42a9f23479d868256f91d96869ac0c2 10.88MB / 10.88MB                                                                               2.6s
 => => sha256:f049f75f014ee8fec2d4728b203c9cbee0502ce142aec030f874aa28359e25f1 5.16MB / 5.16MB                                                                                 1.1s
 => => sha256:bbeef03cda1f5d6c9e20c310c1c91382a6b0a1a2501c3436b28152f13896f082 55.03MB / 55.03MB                                                                               9.9s
 => => extracting sha256:bbeef03cda1f5d6c9e20c310c1c91382a6b0a1a2501c3436b28152f13896f082                                                                                      5.9s
 => => extracting sha256:f049f75f014ee8fec2d4728b203c9cbee0502ce142aec030f874aa28359e25f1                                                                                      0.6s
 => => extracting sha256:56261d0e6b05ece42650b14830960db5b42a9f23479d868256f91d96869ac0c2                                                                                      0.5s
 => => extracting sha256:9bd150679dbdb02d9d4df4457d54211d6ee719ca7bc77747a7be4cd99ae03988                                                                                      5.6s
 => => extracting sha256:bfcb68b5bd105d3f88a2c15354cff6c253bedc41d83c1da28b3d686c37cd9103                                                                                      3.6s
 => => extracting sha256:06d0c5d18ef41fa1c2382bd2afd189a01ebfff4910b868879b6dcfeef46bc003                                                                                      8.8s
 => => extracting sha256:cc7973a07a5b4a44399c5d36fa142f37bb343bb123a3736357365fd9040ca38a                                                                                      0.0s
 => [linux/arm64 stage-1 1/3] FROM docker.io/library/ubuntu:latest@sha256:9a0bdde4188b896a372804be2384015e90e3f84906b750c1a53539b585fbbe7f                                     7.6s
 => => resolve docker.io/library/ubuntu:latest@sha256:9a0bdde4188b896a372804be2384015e90e3f84906b750c1a53539b585fbbe7f                                                         0.1s
 => => sha256:8b150fd943bcd54ef788cece17523d19031f745b099a798de65247900d102e18 27.34MB / 27.34MB                                                                               4.6s
 => => extracting sha256:8b150fd943bcd54ef788cece17523d19031f745b099a798de65247900d102e18                                                                                      2.8s
 => [linux/arm64 stage-1 2/3] WORKDIR /opt/app                                                                                                                                 0.5s
 => [linux/amd64 stage-1 2/3] WORKDIR /opt/app                                                                                                                                 0.6s
 => [linux/amd64 build 2/6] WORKDIR /opt/app                                                                                                                                   2.1s
 => [linux/amd64 build 3/6] COPY go.* ./                                                                                                                                       0.1s
 => [linux/amd64->arm64 build 4/6] RUN go mod download                                                                                                                        11.1s
 => [linux/amd64 build 4/6] RUN go mod download                                                                                                                               11.1s
 => [linux/amd64 build 5/6] COPY . .                                                                                                                                           0.2s
 => [linux/amd64->arm64 build 5/6] COPY . .                                                                                                                                    0.2s
 => [linux/amd64 build 6/6] RUN --mount=type=cache,target=/root/.cache/go-build GOOS=linux GOARCH=amd64 go build -o /opt/app/example .                                         9.6s
 => [linux/amd64->arm64 build 6/6] RUN --mount=type=cache,target=/root/.cache/go-build GOOS=linux GOARCH=arm64 go build -o /opt/app/example .                                 21.0s
 => [linux/amd64 stage-1 3/3] COPY --from=build /opt/app/example ./example                                                                                                     0.2s
 => [linux/arm64 stage-1 3/3] COPY --from=build /opt/app/example ./example                                                                                                     0.1s
 => exporting to image                                                                                                                                                        25.0s
 => => exporting layers                                                                                                                                                        0.7s
 => => exporting manifest sha256:b90296633a64276c5387fe40be97c007daff45af552e0948d9fce2ea98b0cd71                                                                              0.0s
 => => exporting config sha256:468ff1c170c3aab2c74687571337651cc2aeb379136cb6fcf4e59d11b2a50140                                                                                0.0s
 => => exporting manifest sha256:b262a3e6c23afd2d819b33658327b61ccb8f1c618177074ce79e79e0373a4bca                                                                              0.0s
 => => exporting config sha256:ee491744bf4f1ed45db984405b1a01d0b2924394c3bc7ac3b29ab83522091661                                                                                0.0s
 => => exporting manifest list sha256:06d6b2cabc7bbf8ad57faebc6dee8e50e23abac65d21afa02277a5cfc3b489c4                                                                         0.0s
 => => pushing layers                                                                                                                                                         21.5s
 => => pushing manifest for docker.io/chengzh/multi-arch:latest@sha256:06d6b2cabc7bbf8ad57faebc6dee8e50e23abac65d21afa02277a5cfc3b489c4                                        2.7s
 => [auth] chengzh/multi-arch:pull,push token for registry-1.docker.io
```





## 缩减镜像体积

### 基本构建


```bash
git clone https://github.com/cloudzun/gitops
```

```bash
cd gitops/docker/13/golang
```

```bash
dir -al
```

```bash
root@node1:~/gitops/docker/13/golang# dir -al
total 6612
drwxr-xr-x 2 root root    4096 Feb 11 13:39 .
drwxr-xr-x 7 root root    4096 Feb 11 12:42 ..
-rw-r--r-- 1 root root     354 Feb 11 12:42 Dockerfile
-rw-r--r-- 1 root root     120 Feb 11 12:42 Dockerfile-1
-rw-r--r-- 1 root root     127 Feb 11 12:42 Dockerfile-2
-rw-r--r-- 1 root root     105 Feb 11 12:42 Dockerfile-3
-rw-r--r-- 1 root root     279 Feb 11 12:42 Dockerfile-4
-rw-r--r-- 1 root root     286 Feb 11 12:42 Dockerfile-5
-rw-r--r-- 1 root root     287 Feb 11 12:42 Dockerfile-6
-rw-r--r-- 1 root root     279 Feb 11 12:42 Dockerfile-7
-rwxr-xr-x 1 root root 6716498 Feb 11 12:42 example
-rw-r--r-- 1 root root     599 Feb 11 12:42 go.mod
-rw-r--r-- 1 root root    2825 Feb 11 12:42 go.sum
-rw-r--r-- 1 root root     240 Feb 11 12:42 main.go
```

Dockerfile-1
```Dockerfile
# syntax=docker/dockerfile:1
FROM golang:1.17
WORKDIR /opt/app
COPY . .
RUN go build -o example
CMD ["/opt/app/example"]

```
这个 Dockerfile 描述的构建过程非常简单，我们首选 Golang:1.17 版本的镜像作为编译环境，将源码拷贝到镜像中，然后运行 go build 编译源码生成二进制可执行文件，最后配置启动命令。

```bash
docker build -t golang:1 -f Dockerfile-1 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:1 -f Dockerfile-1 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  6.733MB
Step 1/5 : FROM golang:1.17
 ---> 276895edf967
Step 2/5 : WORKDIR /opt/app
 ---> Using cache
 ---> 50abe4ea5fa7
Step 3/5 : COPY . .
 ---> c0256f566166
Step 4/5 : RUN go build -o example
 ---> Running in 44ed5545552b
go: downloading github.com/labstack/echo/v4 v4.9.0
go: downloading github.com/labstack/gommon v0.3.1
go: downloading golang.org/x/crypto v0.0.0-20210817164053-32db794688a5
go: downloading golang.org/x/net v0.0.0-20211015210444-4f30a5c0130f
go: downloading github.com/mattn/go-colorable v0.1.11
go: downloading github.com/mattn/go-isatty v0.0.14
go: downloading github.com/valyala/fasttemplate v1.2.1
go: downloading golang.org/x/sys v0.0.0-20211103235746-7861aae1554b
go: downloading golang.org/x/text v0.3.7
go: downloading github.com/valyala/bytebufferpool v1.0.0
Removing intermediate container 44ed5545552b
 ---> 8942dac702f4
Step 5/5 : CMD ["/opt/app/example"]
 ---> Running in ddf4e9610d7d
Removing intermediate container ddf4e9610d7d
 ---> 40897efc32d5
Successfully built 40897efc32d5
Successfully tagged golang:1
```



查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:1
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
golang       1         ddb59fef2a33   12 seconds ago   1.04GB
golang       1.17      742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
74dbc784726c   12 minutes ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B
f32a98328157   12 minutes ago   /bin/sh -c go build -o example                  90.3MB
09aefcaec12f   12 minutes ago   /bin/sh -c #(nop) COPY dir:f9adead7af783e6c7…   6.72MB
df00d7d44056   12 minutes ago   /bin/sh -c #(nop) WORKDIR /opt/app              0B
276895edf967   13 months ago    /bin/sh -c #(nop) WORKDIR /go                   0B
<missing>      13 months ago    /bin/sh -c mkdir -p "$GOPATH/src" "$GOPATH/b…   0B
<missing>      13 months ago    /bin/sh -c #(nop)  ENV PATH=/go/bin:/usr/loc…   0B
<missing>      13 months ago    /bin/sh -c #(nop)  ENV GOPATH=/go               0B
<missing>      13 months ago    /bin/sh -c set -eux;  arch="$(dpkg --print-a…   408MB
<missing>      13 months ago    /bin/sh -c #(nop)  ENV GOLANG_VERSION=1.17.5    0B
<missing>      13 months ago    /bin/sh -c #(nop)  ENV PATH=/usr/local/go/bi…   0B
<missing>      13 months ago    /bin/sh -c set -eux;  apt-get update;  apt-g…   227MB
<missing>      13 months ago    /bin/sh -c apt-get update && apt-get install…   152MB
<missing>      13 months ago    /bin/sh -c set -ex;  if ! command -v gpg > /…   18.9MB
<missing>      13 months ago    /bin/sh -c set -eux;  apt-get update;  apt-g…   10.7MB
<missing>      13 months ago    /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>      13 months ago    /bin/sh -c #(nop) ADD file:c03517c5ddbed4053…   124MB

```

从返回的结果来看，这个 Dockerfile 构建的镜像大小非常惊人，Golang 示例程序使用 go build 命令编译后，二进制可执行文件大约 6M 左右，但容器化之后，镜像达到 900M，显然我们需要进一步优化镜像大小。




### 替换镜像

Dockerfile-2
```Dockerfile
# syntax=docker/dockerfile:1
FROM golang:1.17-alpine
WORKDIR /opt/app
COPY . .
RUN go build -o example
CMD ["/opt/app/example"]
```
将 Golang:1.17 基础镜像替换为 golang:1.17-alpine 版本，一般来说，Alpine 版本的镜像相比较普通镜像来说删除了一些非必需的系统应用，所以镜像体积更小

```bash
docker build -t golang:2 -f Dockerfile-2 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:2 -f Dockerfile-2 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  6.733MB
Step 1/5 : FROM golang:1.17-alpine
1.17-alpine: Pulling from library/golang
59bf1c3509f3: Already exists
666ba61612fd: Pull complete
8ed8ca486205: Pull complete
1ff5b6d8b8c6: Pull complete
40fcfd711f8d: Pull complete
Digest: sha256:4918412049183afe42f1ecaf8f5c2a88917c2eab153ce5ecf4bf2d55c1507b74
Status: Downloaded newer image for golang:1.17-alpine
 ---> d8bf44a3f6b4
Step 2/5 : WORKDIR /opt/app
 ---> Running in d52bb40ba30b
Removing intermediate container d52bb40ba30b
 ---> 79ab27824978
Step 3/5 : COPY . .
 ---> 76206dcc81e5
Step 4/5 : RUN go build -o example
 ---> Running in 698a63c24501
go: downloading github.com/labstack/echo/v4 v4.9.0
go: downloading github.com/labstack/gommon v0.3.1
go: downloading golang.org/x/crypto v0.0.0-20210817164053-32db794688a5
go: downloading golang.org/x/net v0.0.0-20211015210444-4f30a5c0130f
go: downloading github.com/mattn/go-colorable v0.1.11
go: downloading github.com/mattn/go-isatty v0.0.14
go: downloading github.com/valyala/fasttemplate v1.2.1
go: downloading golang.org/x/sys v0.0.0-20211103235746-7861aae1554b
go: downloading golang.org/x/text v0.3.7
go: downloading github.com/valyala/bytebufferpool v1.0.0
Removing intermediate container 698a63c24501
 ---> 43480b9dfb9c
Step 5/5 : CMD ["/opt/app/example"]
 ---> Running in 97d398d6f2da
Removing intermediate container 97d398d6f2da
 ---> 056ccad9a6ab
Successfully built 056ccad9a6ab
Successfully tagged golang:2
```



查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:2
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
golang       2             bfe63d3346bb   31 seconds ago   411MB
golang       1             ddb59fef2a33   6 minutes ago    1.04GB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:2
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
96de41a9c9f3   28 minutes ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B
40318a9af90b   28 minutes ago   /bin/sh -c go build -o example                  90.3MB
0bf118996932   29 minutes ago   /bin/sh -c #(nop) COPY dir:f9adead7af783e6c7…   6.72MB
d093f960daa8   29 minutes ago   /bin/sh -c #(nop) WORKDIR /opt/app              0B
d8bf44a3f6b4   14 months ago    /bin/sh -c #(nop) WORKDIR /go                   0B
<missing>      14 months ago    /bin/sh -c mkdir -p "$GOPATH/src" "$GOPATH/b…   0B
<missing>      14 months ago    /bin/sh -c #(nop)  ENV PATH=/go/bin:/usr/loc…   0B
<missing>      14 months ago    /bin/sh -c #(nop)  ENV GOPATH=/go               0B
<missing>      14 months ago    /bin/sh -c set -eux;  apk add --no-cache --v…   309MB
<missing>      14 months ago    /bin/sh -c #(nop)  ENV GOLANG_VERSION=1.17.5    0B
<missing>      14 months ago    /bin/sh -c #(nop)  ENV PATH=/usr/local/go/bi…   0B
<missing>      14 months ago    /bin/sh -c [ ! -e /etc/nsswitch.conf ] && ec…   17B
<missing>      14 months ago    /bin/sh -c apk add --no-cache ca-certificates   519kB
<missing>      14 months ago    /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
<missing>      14 months ago    /bin/sh -c #(nop) ADD file:9233f6f2237d79659…   5.59MB

```

### 本地编译

Dockerfile-3

在本地先编译出可执行文件，再将它复制到一个更小体积的 ubuntu 镜像内，直接引入了不包含 Golang 编译工具的 ubuntu 镜像作为基础运行环境，接下来使用 docker build 命令构建镜像

```bash
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o example .
```

```bash
root@node1:~/gitops/docker/13/golang# CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o example .
go: downloading github.com/labstack/echo/v4 v4.9.0
go: downloading github.com/labstack/gommon v0.3.1
go: downloading golang.org/x/crypto v0.0.0-20210817164053-32db794688a5
go: downloading golang.org/x/net v0.0.0-20211015210444-4f30a5c0130f
go: downloading github.com/mattn/go-colorable v0.1.11
go: downloading github.com/mattn/go-isatty v0.0.14
go: downloading github.com/valyala/fasttemplate v1.2.1
go: downloading golang.org/x/sys v0.0.0-20211103235746-7861aae1554b
go: downloading github.com/valyala/bytebufferpool v1.0.0
go: downloading golang.org/x/text v0.3.7
```



```Dockerfile
# syntax=docker/dockerfile:1
FROM ubuntu:latest
WORKDIR /opt/app
COPY example ./
CMD ["/opt/app/example"]
```


```bash
docker build -t golang:3 -f Dockerfile-3 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:3 -f Dockerfile-3 .
Sending build context to Docker daemon      7MB
Step 1/4 : FROM ubuntu:latest
latest: Pulling from library/ubuntu
76769433fd8a: Pull complete 
Digest: sha256:2adf22367284330af9f832ffefb717c78239f6251d9d0f58de50b86229ed1427
Status: Downloaded newer image for ubuntu:latest
 ---> 74f2314a03de
Step 2/4 : WORKDIR /opt/app
 ---> Running in 819e907b075b
Removing intermediate container 819e907b075b
 ---> 0398b920df19
Step 3/4 : COPY example ./
 ---> e4e0c73fee4e
Step 4/4 : CMD ["/opt/app/example"]
 ---> Running in c53349c11c4f
Removing intermediate container c53349c11c4f
 ---> 27414418a1cb
Successfully built 27414418a1cb
Successfully tagged golang:3
```

查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:3
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
golang       3             27414418a1cb   30 seconds ago   84.8MB
golang       2             bfe63d3346bb   2 minutes ago    411MB
golang       1             ddb59fef2a33   9 minutes ago    1.04GB
ubuntu       latest        74f2314a03de   13 days ago      77.8MB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:3
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
27414418a1cb   About a minute ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B        
e4e0c73fee4e   About a minute ago   /bin/sh -c #(nop) COPY file:20d600ca265b298d…   6.98MB    
0398b920df19   About a minute ago   /bin/sh -c #(nop) WORKDIR /opt/app              0B        
74f2314a03de   13 days ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      13 days ago          /bin/sh -c #(nop) ADD file:fb4c8244f4468cdd3…   77.8MB    
<missing>      13 days ago          /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      13 days ago          /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      13 days ago          /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      13 days ago          /bin/sh -c #(nop)  ARG RELEASE                  0B   

```

从返回内容可以看出，这种构建方式生成的镜像只有 84.8M，在体积上比最初的 1.04G 缩小了 90% 。镜像的最终大小就相当于 ubuntu:latest 的大小加上 Golang 二进制可执行文件的大小。

不过，这种方式将应用的编译过程拆分到了宿主机上，这会让 Dockerfile 失去描述应用编译和打包的作用，不是一个好的实践


### 多阶段构建

多阶段构建的本质其实就是将镜像构建过程拆分成编译过程和运行过程。
- 第一个阶段对应编译的过程，负责生成可执行文件；
- 第二个阶段对应运行过程，也就是拷贝第一阶段的二进制可执行文件，并为程序提供运行环境，最终镜像也就是第二阶段生成的镜像如下图所示。

Dockerfile-4
```Dockerfile
# syntax=docker/dockerfile:1

# Step 1: build golang binary
FROM golang:1.17 as builder
WORKDIR /opt/app
COPY . .
RUN go build -o example

# Step 2: copy binary from step1
FROM ubuntu:latest
WORKDIR /opt/app
COPY --from=builder /opt/app/example ./example
CMD ["/opt/app/example"]
```
多阶段构建其实就是将 Dockerfile-1 和 Dockerfile-3 的内容进行合并重组

这段内容里有两个 FROM 语句，所以这是一个包含两个阶段的构建过程。

第一个阶段是从第 4 行至第 7 行，它的作用是编译生成二进制可执行文件，就像我们之前在本地执行的编译操作一样。

第二阶段在第 10 行到 13 行，它的作用是将第一阶段生成的二进制可执行文件复制到当前阶段，把 ubuntu:latest 作为运行环境，并设置 CMD 启动命令。

```bash
docker build -t golang:4 -f Dockerfile-4 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:4 -f Dockerfile-4 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  6.733MB
Step 1/8 : FROM golang:1.17 as builder
 ---> 276895edf967
Step 2/8 : WORKDIR /opt/app
 ---> Using cache
 ---> 50abe4ea5fa7
Step 3/8 : COPY . .
 ---> Using cache
 ---> c0256f566166
Step 4/8 : RUN go build -o example
 ---> Using cache
 ---> 8942dac702f4
Step 5/8 : FROM ubuntu:latest
 ---> ba6acccedd29
Step 6/8 : WORKDIR /opt/app
 ---> Using cache
 ---> d61000368cb4
Step 7/8 : COPY --from=builder /opt/app/example ./example
 ---> 69bd446cbbf6
Step 8/8 : CMD ["/opt/app/example"]
 ---> Running in ccff4bd8c0f8
Removing intermediate container ccff4bd8c0f8
 ---> b2ef776eca71
Successfully built b2ef776eca71
Successfully tagged golang:4
```

查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:4
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
golang       4             54c92336ac1c   30 seconds ago   84.7MB
<none>       <none>        c564c307d3c2   31 seconds ago   1.04GB
golang       3             27414418a1cb   3 minutes ago    84.8MB
golang       2             bfe63d3346bb   5 minutes ago    411MB
golang       1             ddb59fef2a33   11 minutes ago   1.04GB
ubuntu       latest        74f2314a03de   13 days ago      77.8MB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:4
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
54c92336ac1c   59 seconds ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B        
138f936e2b64   59 seconds ago   /bin/sh -c #(nop) COPY file:2bd00ab8d3308a9a…   6.88MB    
0398b920df19   3 minutes ago    /bin/sh -c #(nop) WORKDIR /opt/app              0B        
74f2314a03de   13 days ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B        
<missing>      13 days ago      /bin/sh -c #(nop) ADD file:fb4c8244f4468cdd3…   77.8MB    
<missing>      13 days ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      13 days ago      /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B        
<missing>      13 days ago      /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B        
<missing>      13 days ago      /bin/sh -c #(nop)  ARG RELEASE                  0B 
```

### 进一步压缩

Dockerfile-5
```Dockerfile
# syntax=docker/dockerfile:1

# Step 1: build golang binary
FROM golang:1.17 as builder
WORKDIR /opt/app
COPY . .
RUN CGO_ENABLED=0 go build -o example

# Step 2: copy binary from step1
FROM alpine
WORKDIR /opt/app
COPY --from=builder /opt/app/example ./example
CMD ["/opt/app/example"]

```
要进一步缩小体积，我们可以继续使用其他更小的镜像作为第二阶段的运行镜像，这就要说到 Alpine 了。

Alpine 镜像是专门为容器化定制的 Linux 发行版，它的最大特点是体积非常小。现在，我们尝试使用它，将第二阶段构建的镜像替换为 Alpine 镜像

```bash
docker build -t golang:5 -f Dockerfile-5 .
```


```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:5 -f Dockerfile-5 .
Sending build context to Docker daemon      7MB
Step 1/8 : FROM golang:1.17 as builder
 ---> 742df529b073
Step 2/8 : WORKDIR /opt/app
 ---> Using cache
 ---> 40d9e3b11f11
Step 3/8 : COPY . .
 ---> Using cache
 ---> 88f9fa240559
Step 4/8 : RUN CGO_ENABLED=0 go build -o example
 ---> Running in 9a9597b50e70
go: downloading github.com/labstack/echo/v4 v4.9.0
go: downloading github.com/labstack/gommon v0.3.1
go: downloading golang.org/x/crypto v0.0.0-20210817164053-32db794688a5
go: downloading golang.org/x/net v0.0.0-20211015210444-4f30a5c0130f
go: downloading github.com/mattn/go-colorable v0.1.11
go: downloading github.com/mattn/go-isatty v0.0.14
go: downloading github.com/valyala/fasttemplate v1.2.1
go: downloading golang.org/x/sys v0.0.0-20211103235746-7861aae1554b
go: downloading github.com/valyala/bytebufferpool v1.0.0
go: downloading golang.org/x/text v0.3.7
Removing intermediate container 9a9597b50e70
 ---> a4de3e3e3d49
Step 5/8 : FROM alpine
latest: Pulling from library/alpine
63b65145d645: Pull complete 
Digest: sha256:ff6bdca1701f3a8a67e328815ff2346b0e4067d32ec36b7992c1fdc001dc8517
Status: Downloaded newer image for alpine:latest
 ---> b2aa39c304c2
Step 6/8 : WORKDIR /opt/app
 ---> Running in 0fee197555a5
Removing intermediate container 0fee197555a5
 ---> 4a7f1f27187d
Step 7/8 : COPY --from=builder /opt/app/example ./example
 ---> f98d161a91f7
Step 8/8 : CMD ["/opt/app/example"]
 ---> Running in 4ea83833228b
Removing intermediate container 4ea83833228b
 ---> 419b26609935
Successfully built 419b26609935
Successfully tagged golang:5
```



查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:5
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
golang       5             419b26609935   31 seconds ago   13.9MB
<none>       <none>        a4de3e3e3d49   34 seconds ago   1.05GB
golang       4             54c92336ac1c   2 minutes ago    84.7MB
<none>       <none>        c564c307d3c2   2 minutes ago    1.04GB
golang       3             27414418a1cb   5 minutes ago    84.8MB
golang       2             bfe63d3346bb   7 minutes ago    411MB
golang       1             ddb59fef2a33   13 minutes ago   1.04GB
ubuntu       latest        74f2314a03de   13 days ago      77.8MB
alpine       latest        b2aa39c304c2   4 weeks ago      7.05MB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:5
IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
419b26609935   About a minute ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B        
f98d161a91f7   About a minute ago   /bin/sh -c #(nop) COPY file:767cca1c08627393…   6.82MB    
4a7f1f27187d   About a minute ago   /bin/sh -c #(nop) WORKDIR /opt/app              0B        
b2aa39c304c2   4 weeks ago          /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B        
<missing>      4 weeks ago          /bin/sh -c #(nop) ADD file:40887ab7c06977737…   7.05MB
```

由于 Alpine 镜像和常规 Linux 发行版存在一些差异，通常并不推荐在生产环境下使用 Alpine 镜像作为业务的运行镜像


### 极限压缩

把第二个阶段的镜像替换为一个“空镜像”，这个空镜像称为 scratch 镜像，我们将 Dockerfile-4 第二阶段的构建替换为 scratch 镜像

Dockerfile-6
```Dockerfile
# syntax=docker/dockerfile:1

# Step 1: build golang binary
FROM golang:1.17 as builder
WORKDIR /opt/app
COPY . .
RUN CGO_ENABLED=0 go build -o example

# Step 2: copy binary from step1
FROM scratch
WORKDIR /opt/app
COPY --from=builder /opt/app/example ./example
CMD ["/opt/app/example"]
```
由于 scratch 镜像不包含任何内容，所以我们在编译 Golang 可执行文件的时候禁用了 CGO，这样才能让编译出来的程序在 scratch 镜像中运行

```bash
docker build -t golang:6 -f Dockerfile-6 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:6 -f Dockerfile-6 .
Sending build context to Docker daemon      7MB
Step 1/8 : FROM golang:1.17 as builder
 ---> 742df529b073
Step 2/8 : WORKDIR /opt/app
 ---> Using cache
 ---> 40d9e3b11f11
Step 3/8 : COPY . .
 ---> Using cache
 ---> 88f9fa240559
Step 4/8 : RUN CGO_ENABLED=0 go build -o example
 ---> Using cache
 ---> a4de3e3e3d49
Step 5/8 : FROM scratch
 ---> 
Step 6/8 : WORKDIR /opt/app
 ---> Running in 4bc677ec0f7f
Removing intermediate container 4bc677ec0f7f
 ---> b3cfc770d142
Step 7/8 : COPY --from=builder /opt/app/example ./example
 ---> 26a048c3fc5b
Step 8/8 : CMD ["/opt/app/example"]
 ---> Running in 6afd5b0574b9
Removing intermediate container 6afd5b0574b9
 ---> d05c2b689d37
Successfully built d05c2b689d37
Successfully tagged golang:6
```



查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:6
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
golang       6             d05c2b689d37   33 seconds ago   6.82MB
golang       5             419b26609935   2 minutes ago    13.9MB
<none>       <none>        a4de3e3e3d49   2 minutes ago    1.05GB
golang       4             54c92336ac1c   3 minutes ago    84.7MB
<none>       <none>        c564c307d3c2   3 minutes ago    1.04GB
golang       3             27414418a1cb   6 minutes ago    84.8MB
golang       2             bfe63d3346bb   9 minutes ago    411MB
golang       1             ddb59fef2a33   15 minutes ago   1.04GB
ubuntu       latest        74f2314a03de   13 days ago      77.8MB
alpine       latest        b2aa39c304c2   4 weeks ago      7.05MB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```

```bash
root@node1:~/gitops/docker/13/golang# docker history golang:6
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
d05c2b689d37   57 seconds ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B        
26a048c3fc5b   58 seconds ago   /bin/sh -c #(nop) COPY file:767cca1c08627393…   6.82MB    
b3cfc770d142   58 seconds ago   /bin/sh -c #(nop) WORKDIR /opt/app              0B  
```

scratch 镜像是一个空白镜像，甚至连 shell 都没有，所以也无法进入容器查看文件或进行调试。在生产环境中，如果对安全有极高的要求，可以考虑把 scratch 作为程序的运行镜像。


### 复用构建缓存，加快构建过程

要使用 Golang 依赖的缓存，最简单的办法是：先复制依赖文件，再下载依赖，最后再复制源码进行编译。

Dockerfile-7
```Dockerfile
# syntax=docker/dockerfile:1

# Step 1: build golang binary
FROM golang:1.17 as builder
WORKDIR /opt/app
COPY go.* ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o example

# Step 2: copy binary from step1
FROM scratch
WORKDIR /opt/app
COPY --from=builder /opt/app/example ./example
CMD ["/opt/app/example"]
```
这样，在每次代码变更而依赖不变的情况下，Docker 都会复用第 4 行和第 5 行产生的构建缓存，这可以加速镜像构建过程

```bash
docker build -t golang:7 -f Dockerfile-7 .
```

```bash
root@node1:~/gitops/docker/13/golang# docker build -t golang:7 -f Dockerfile-7 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  6.734MB
Step 1/10 : FROM golang:1.17 as builder
 ---> 276895edf967
Step 2/10 : WORKDIR /opt/app
 ---> Using cache
 ---> 50abe4ea5fa7
Step 3/10 : COPY go.* ./
 ---> Using cache
 ---> a92d3e22d2f1
Step 4/10 : RUN go mod download
 ---> Using cache
 ---> a32e9966c989
Step 5/10 : COPY . .
 ---> 62de3afbbcb1
Step 6/10 : RUN go build -o example
 ---> Running in 467d2d11cba9
Removing intermediate container 467d2d11cba9
 ---> 09dead76b62d
Step 7/10 : FROM alpine:latest
 ---> c059bfaa849c
Step 8/10 : WORKDIR /opt/app
 ---> Using cache
 ---> 89442c35f1c4
Step 9/10 : COPY --from=builder /opt/app/example ./example
 ---> 05461692b3d1
Step 10/10 : CMD ["/opt/app/example"]
 ---> Running in f54f0afa5838
Removing intermediate container f54f0afa5838
 ---> 32d7ee5d6a76
Successfully built 32d7ee5d6a76
Successfully tagged golang:7
```



查看映像

```bash
docker images
```

查看映像历史

```bash
docker history golang:7
```



```bash
root@node1:~/gitops/docker/13/golang# docker images
REPOSITORY   TAG           IMAGE ID       CREATED              SIZE
golang       7             990cb70181c3   24 seconds ago   13.9MB
<none>       <none>        4d777e500587   25 seconds ago   1.05GB
golang       6             d05c2b689d37   5 minutes ago    6.82MB
golang       5             419b26609935   7 minutes ago    13.9MB
<none>       <none>        a4de3e3e3d49   7 minutes ago    1.05GB
golang       4             54c92336ac1c   9 minutes ago    84.7MB
<none>       <none>        c564c307d3c2   9 minutes ago    1.04GB
golang       3             27414418a1cb   12 minutes ago   84.8MB
golang       2             bfe63d3346bb   14 minutes ago   411MB
golang       1             ddb59fef2a33   20 minutes ago   1.04GB
ubuntu       latest        74f2314a03de   13 days ago      77.8MB
alpine       latest        b2aa39c304c2   4 weeks ago      7.05MB
golang       1.17-alpine   270c4f58750f   7 months ago     314MB
golang       1.17          742df529b073   7 months ago     942MB
```


```bash
root@node1:~/gitops/docker/13/golang# docker history golang:7
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
990cb70181c3   14 seconds ago   /bin/sh -c #(nop)  CMD ["/opt/app/example"]     0B        
abf475bd2ed2   15 seconds ago   /bin/sh -c #(nop) COPY file:2bd00ab8d3308a9a…   6.88MB    
4a7f1f27187d   7 minutes ago    /bin/sh -c #(nop) WORKDIR /opt/app              0B        
b2aa39c304c2   4 weeks ago      /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B        
<missing>      4 weeks ago      /bin/sh -c #(nop) ADD file:40887ab7c06977737…   7.05MB  
```



# 使用 Helm 定义应用







# 构建GitOps工作流







# 高级发布策略







# 应用可观测性