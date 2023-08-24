## 使用命令行测试Kubernetes基本功能

实验环境介绍：以下实验基于Kubernetes 沙盒：https://killercoda.com/playgrounds/scenario/kubernetes



### **在 Kubernetes 群集上运行pod**

创建Pod定义文件

```bash
nano flask-pod.yaml
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world-flask
spec:
  containers:
    - name: flask
      image: chengzh/hello-world-flask:latest
      ports:
        - containerPort: 5000
```

这是一个 Kubernetes Pod 的配置文件，用 YAML 格式编写。具体解析如下：

- `apiVersion: v1`：这是 Kubernetes API 的版本，这里使用的是 v1 版本，这是最常用的版本。

- `kind: Pod`：这指定了要创建的 Kubernetes 对象类型，这里是 Pod，Pod 是 Kubernetes 的最小部署单元。

- `metadata`：这是关于 Pod 的元数据，包括名称、命名空间、标签和其他信息。
  - `name: hello-world-flask`：这是 Pod 的名称，可以用来唯一标识这个 Pod。

- `spec`：这是 Pod 的规格，定义了 Pod 的行为。包括容器、卷等信息。
  - `containers`：这是 Pod 中要运行的容器列表。
    - `name: flask`：这是容器的名称，用于标识容器。
    - `image: chengzh/hello-world-flask:latest`：这是容器的镜像，指定了容器运行的软件。这里使用的是 Docker 镜像 `chengzh/hello-world-flask` 的最新版本。
    - `ports`：这是容器要暴露的端口列表。
      - `containerPort: 5000`：这是容器内部的端口号，应用程序在此端口上监听连接。

总的来说，这个配置文件定义了一个名为 `hello-world-flask` 的 Pod，该 Pod 运行一个名为 `flask` 的容器，该容器使用 `chengzh/hello-world-flask:latest` 镜像，并在内部的 `5000` 端口上监听连接。

运行pod

```bash
kubectl apply -f flask-pod.yaml
```

查看pod

```bash
kubectl get pod
```


```bash
root@node1:~# kubectl apply -f flask-pod.yaml
pod/hello-world-flask created
root@node1:~# kubectl get pod
NAME                READY   STATUS              RESTARTS   AGE
hello-world-flask   0/1     ContainerCreating   0          6s
root@node1:~# kubectl get pod
NAME                READY   STATUS    RESTARTS   AGE
hello-world-flask   1/1     Running   0          7m10s
```

端口映射

```bash
kubectl port-forward pod/hello-world-flask 8000:5000
```

命令会将本地机器的 8000 端口的流量转发到名为 `hello-world-flask` 的 Pod 的 `5000` 端口。这样，你就可以在本地机器上通过访问 `8000 `端口来访问 Pod 中运行的应用程序了


```bash
root@node1:~# kubectl port-forward pod/hello-world-flask 8000:5000
Forwarding from 127.0.0.1:8000 -> 5000
Forwarding from [::1]:8000 -> 5000
Handling connection for 8000
```

打开另外一个终端会话进行访问

```bash
curl localhost:8000
```

```bash
root@node1:~# curl localhost:8000
Hello, my first docker images! hello-world-flaskroot@node1:~#
```

在沙盒环境中进行实验参考以下步骤

```bash
controlplane $ kubectl get pod -o wide
NAME                READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
hello-world-flask   1/1     Running   0          14s   192.168.1.3   node01   <none>           <none>
controlplane $ curl 192.168.1.3 
curl: (7) Failed to connect to 192.168.1.3 port 80: Connection refused
controlplane $ curl 192.168.1.3:5000 
Hello, my first docker images! hello-world-flaskcontrolplane $ 
```


删除 pod

```bash
kubectl delete -f flask-pod.yaml
```


```bash
root@node1:~# kubectl delete -f flask-pod.yaml
pod "hello-world-flask" deleted
```



### **创建实验工作负载**

创建 deployment

```bash
kubectl create deployment hello-world-flask --image=chengzh/hello-world-flask:latest --replicas=2 
```

命令会创建一个新的 Deployment，这个 Deployment 名为 `hello-world-flask`，包含 2 个使用 `chengzh/hello-world-flask:latest` 镜像的 Pod。

查看deployment

```bash
kubectl get deployment
```

查看deployment详细信息

```bash
kubectl describe deployment hello-world-flask
```

```bash
controlplane $ kubectl create deployment hello-world-flask --image=chengzh/hello-world-flask:latest --replicas=2 
deployment.apps/hello-world-flask created
controlplane $ kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
hello-world-flask   0/2     0            0           22s
controlplane $ kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
hello-world-flask   0/2     2            0           58s
controlplane $ kubectl get deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
hello-world-flask   2/2     2            2           2m17s
controlplane $ kubectl describe deployment hello-world-flask
Name:                   hello-world-flask
Namespace:              default
CreationTimestamp:      Thu, 24 Aug 2023 07:20:50 +0000
Labels:                 app=hello-world-flask
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=hello-world-flask
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=hello-world-flask
  Containers:
   hello-world-flask:
    Image:        chengzh/hello-world-flask:latest
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
NewReplicaSet:   hello-world-flask-564fdb6867 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  2m8s  deployment-controller  Scaled up replica set hello-world-flask-564fdb6867 to 2
controlplane $ 
```

创建 service

```bash
kubectl create service clusterip hello-world-flask --tcp=5000:5000
```

命令会创建一个新的 Service，这个 Service 名为 `hello-world-flask`，类型为 ClusterIP，将监听 5000 端口，并将流量转发到 Pod 的 5000 端口。这样，Kubernetes 集群内的其他 Pod 就可以通过这个 Service 来访问 `hello-world-flask` Pod 了。

查看service

```bash
kubectl get svc
```

查看service细节

```bash
describe  svc hello-world-flask
```

访问服务

```bash
curl http://10.106.195.22:5000
```

```bash
controlplane $ kubectl create service clusterip hello-world-flask --tcp=5000:5000
service/hello-world-flask created
controlplane $ kubectl get svc
NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
hello-world-flask   ClusterIP   10.106.195.22   <none>        5000/TCP   10s
kubernetes          ClusterIP   10.96.0.1       <none>        443/TCP    15d
controlplane $ kubectl describe  svc hello-world-flask
Name:              hello-world-flask
Namespace:         default
Labels:            app=hello-world-flask
Annotations:       <none>
Selector:          app=hello-world-flask
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.106.195.22
IPs:               10.106.195.22
Port:              5000-5000  5000/TCP
TargetPort:        5000/TCP
Endpoints:         192.168.0.8:5000,192.168.1.3:5000
Session Affinity:  None
Events:            <none>
controlplane $ curl http://10.106.195.22:5000
Hello, my first docker images! hello-world-flask-564fdb6867-hcpmzcontrolplane $ curl http://10.106.195.22:5000
Hello, my first docker images! hello-world-flask-564fdb6867-2vp25controlplane $ curl http://10.106.195.22:5000
Hello, my first docker images! hello-world-flask-564fdb6867-hcpmzcontrolplane $ 
```

安装ingress控制器

```bash
kubectl label node node01 ingress-ready=true
```

这个命令的作用是给名为 "node01" 的节点添加一个名为 "ingress-ready" 的标签，其值为 "true"。在这个例子中，标签 "ingress-ready=true" 用来指示某些 ingress 控制器应该在这个节点上运行。

安装ingress

```bash
kubectl create -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/ingress-nginx/ingress-nginx.yaml
```

创建 ingress

```bash
kubectl create ingress hello-world-flask --rule="/=hello-world-flask:5000"
```

命令会创建一个新的 Ingress，这个 Ingress 名为 `hello-world-flask`，并设置了一个路由规则，将所有访问根路径的请求路由到 `hello-world-flask` 服务的 5000 端口。

查看ingress

``` bash
kubectl get ingress
```

查看ingress详细信息

``` bash
kubectl describe  ingress hello-world-flask
```

使用ingress访问服务

``` bash
 curl http://node01
```

```bash
hello-world-flaskcontrolplane $ kubectl create ingress hello-world-flask --rule="/=hello-world-flask:5000"
ingress.networking.k8s.io/hello-world-flask created
controlplane $ kubectl get ingress
NAME                CLASS    HOSTS   ADDRESS   PORTS   AGE
hello-world-flask   <none>   *                 80      10s
controlplane $ kubectl describe  ingress hello-world-flask
Name:             hello-world-flask
Labels:           <none>
Namespace:        default
Address:          localhost
Ingress Class:    <none>
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /   hello-world-flask:5000 (192.168.0.8:5000,192.168.1.3:5000)
Annotations:  <none>
Events:
  Type    Reason  Age               From                      Message
  ----    ------  ----              ----                      -------
  Normal  Sync    2s (x2 over 40s)  nginx-ingress-controller  Scheduled for sync
  controlplane $ curl http://node01
Hello, my first docker images! hello-world-flask-564fdb6867-2vp25controlplane $ 
```



小结


- Pod：Pod 是 Kubernetes 中的最小部署单元，每个 Pod 包含一个或多个容器。Pod 会被 Deployment 工作负载管理起来，例如创建和销毁等。Deployment 控制器可以确保在任何时候，都有指定数量的 Pod 实例在运行。如果某个 Pod 出现问题，Deployment 会自动创建新的 Pod 来替代。这样可以保证应用的高可用性和容错性。
- Service：Service 是 Kubernetes 中的一个抽象概念，它定义了一种访问 Pod 的方法。Service 相当于弹性伸缩组的负载均衡器，它能以加权轮训的方式将流量转发到多个 Pod 副本上。Service 通过标签选择器选择后端的 Pod，然后通过自己的 ClusterIP（集群内部）或 LoadBalancer（集群外部）提供稳定的访问地址。这样，即使后端的 Pod 发生了变化，Service 的访问地址也不会改变，从而实现了服务发现和负载均衡。

- Ingress：Ingress 是 Kubernetes 中用于管理外部访问集群内部服务的规则的对象，相当于集群的外网访问入口。Ingress 可以为 Service 提供 HTTP 和 HTTPS 路由，以及基于主机名和 URL 路径的路由。通过 Ingress，可以将外部的 HTTP/HTTPS 路由到多个内部的 Service，实现了复杂的负载均衡、SSL 终止和名称虚拟主机等功能。但是，Ingress 需要一个 Ingress 控制器才能工作，Ingress 控制器负责实现 Ingress 的规则。



### **工作负载自愈**

打开另外一个会话窗口访问服务

```bash
while true; do sleep 1; curl http://node01; echo -e '\n'$(date);done
```

有了 Ingress，访问 Pod 就不再需要进行端口转发了，可以直接访问ingress控制器所在的 node01。上面的命令会每隔 1 秒钟发送一次请求，并打印出时间和返回内容：

```bash
root@node1:~# while true; do sleep 1; curl http://127.0.0.1; echo -e '\n'$(date);done
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:40 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:01:41 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:42 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:43 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:01:44 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:01:45 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:46 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:01:47 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:48 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:49 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:01:50 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-5tdzs
Mon 06 Feb 2023 12:01:51 PM CST

```

在这里，“Hello, my first docker images” 后面紧接的内容是 Pod 名称。通过返回内容会发现，请求被平均分配到了两个 Pod 上，Pod 名称是交替出现的。要保留这个命令行窗口，以便继续观察。

模拟某个pod失效

```bash
kubectl get pod
```


```bash
root@node1:~# kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-6bdf7b45dc-5tdzs   1/1     Running   0          12m
hello-world-flask-6bdf7b45dc-hnvqm   1/1     Running   0          12m
```

```bash
kubectl delete pod hello-world-flask-6bdf7b45dc-5tdzs
```


所有的流量被压在另一个正常的pod

```bash
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:31 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:32 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:33 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:34 PM CST
```


新pod上线后，流量在新旧两个pod上负载均衡

```bash
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
Mon 06 Feb 2023 12:08:35 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:36 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
Mon 06 Feb 2023 12:08:37 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
Mon 06 Feb 2023 12:08:38 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:39 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
Mon 06 Feb 2023 12:08:40 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:41 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
Mon 06 Feb 2023 12:08:42 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-hnvqm
Mon 06 Feb 2023 12:08:43 PM CST
Hello, my first docker images! hello-world-flask-6bdf7b45dc-qbvf2
```

再次查看pod列表

```bash
kubectl get pod
```

```bash
root@node1:~# kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-6bdf7b45dc-hnvqm   1/1     Running   0          17m
hello-world-flask-6bdf7b45dc-qbvf2   1/1     Running   0          2m1s
```

首先，Kubernetes 系统立刻察觉到业务 Pod 的故障情况，迅速地执行了故障转移操作，同时将出现故障的 Pod 进行了隔离处理。此后，系统将外部请求智能地转发到了其他健康的 Pod 中。接着，系统自动重启了出现故障的 Pod，并在它恢复正常后，将其重新纳入到负载均衡机制中，开始接收外部的请求。这一系列复杂的操作，Kubernetes 都能够自动化地完成，无需人工干预。



### **自动扩容**

安装 metrics 

```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/metrics/metrics.yaml
```

```bash
kubectl wait deployment -n kube-system metrics-server --for condition=Available=True --timeout=90s
```

创建自动扩缩容策略

```bash
kubectl autoscale deployment hello-world-flask --cpu-percent=50 --min=2 --max=10
```

这个命令的作用是：当 "hello-world-flask" 的 Deployment 中的 Pod 的 CPU 利用率超过 50% 时，Kubernetes 会自动增加 Pod 的数量，最多增加到 10 个；当 CPU 利用率降低时，Kubernetes 会自动减少 Pod 的数量，但至少保留 2 个。这样可以根据负载的变化自动调整 Pod 的数量，实现弹性伸缩。

查看hpa

```bash
kubectl get hpa
```

查看hpa详细信息

```bash
kubectl describe hpa hello-world-flask 
```


```bash
controlplane $ kubectl autoscale deployment hello-world-flask --cpu-percent=50 --min=2 --max=10
horizontalpodautoscaler.autoscaling/hello-world-flask autoscaled
controlplane $ kubectl get hpa
NAME                REFERENCE                      TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
hello-world-flask   Deployment/hello-world-flask   <unknown>/50%   2         10        0          13s
controlplane $ kubectl describe hpa hello-world-flask 
Name:                                                  hello-world-flask
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Thu, 24 Aug 2023 07:44:35 +0000
Reference:                                             Deployment/hello-world-flask
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 50%
Min replicas:                                          2
Max replicas:                                          10
Deployment pods:                                       2 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: failed to get cpu utilization: missing request for cpu in container hello-world-flask of Pod hello-world-flask-564fdb6867-hcpmz
Events:
  Type     Reason                        Age               From                       Message
  ----     ------                        ----              ----                       -------
  Warning  FailedGetResourceMetric       6s (x2 over 23s)  horizontal-pod-autoscaler  failed to get cpu utilization: missing request for cpu in container hello-world-flask of Pod hello-world-flask-564fdb6867-hcpmz
  Warning  FailedComputeMetricsReplicas  6s (x2 over 23s)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: missing request for cpu in container hello-world-flask of Pod hello-world-flask-564fdb6867-hcpmz
```

这个 `kubectl describe hpa hello-world-flask` 命令用于描述名为 "hello-world-flask" 的 Horizontal Pod Autoscaler (HPA) 的状态和性能。在输出的信息中，存在两个警告信息，这两个警告信息都与获取 CPU 利用率有关。

1. `FailedGetResourceMetric`: 这个警告表示 HPA 无法获取到 Pod 的 CPU 利用率。具体的错误信息为 "missing request for cpu in container hello-world-flask of Pod hello-world-flask-564fdb6867-hcpmz"，这意味着在 "hello-world-flask-564fdb6867-hcpmz" 这个 Pod 的 "hello-world-flask" 容器中，没有定义 CPU 的请求量，所以无法计算 CPU 的利用率。

2. `FailedComputeMetricsReplicas`: 这个警告表示 HPA 无法根据获取到的度量值（这里是 CPU 利用率）来计算出需要的副本数量。这个警告的原因同样是由于无法获取到 CPU 利用率。

为了解决这个问题，你需要在 Pod 的定义中为 "hello-world-flask" 这个容器设置 CPU 的请求量。



为deployment注入资源配额

```bash
kubectl patch deployment hello-world-flask --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/resources", "value": {"requests": {"memory": "100Mi", "cpu": "100m"}}}]'
```

这个命令的作用是为 "hello-world-flask" 这个 Deployment 中的第一个容器设置 100Mi 的内存请求和 100m 的 CPU 请求。这样做可以让 Kubernetes 知道这个容器需要多少资源，从而更好地进行资源调度，同时也可以让 HPA 根据这些请求来计算资源的利用率。


```bash
root@node1:~# kubectl patch deployment hello-world-flask --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/resources", "value": {"requests": {"memory": "100Mi", "cpu": "100m"}}}]'
deployment.apps/hello-world-flask patched
root@node1:~# kubectl get pod
NAME                                 READY   STATUS        RESTARTS   AGE
hello-world-flask-5d4494cc9b-5p7vx   1/1     Running       0          12s
hello-world-flask-5d4494cc9b-jmwxd   1/1     Running       0          8s
hello-world-flask-6bdf7b45dc-hnvqm   1/1     Terminating   0          31m
hello-world-flask-6bdf7b45dc-qbvf2   1/1     Terminating   0          15m
root@node1:~# kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-5d4494cc9b-5p7vx   1/1     Running   0          49s
hello-world-flask-5d4494cc9b-jmwxd   1/1     Running   0          45s
```

进入到某一个pod的上下文

```bash
 kubectl exec -it hello-world-flask-5d4494cc9b-jmwxd  -- bash
```

模拟业务高峰场景

```bash
ab -c 50 -n 10000 http://27.0.0.1:5000/
```

在这条压力测试的命令中，-c 代表 50 个并发数，-n 代表一共请求 10000 次，整个过程大概会持续十几秒。


```bash
root@node1:~# kubectl exec -it hello-world-flask-5d4494cc9b-jmwxd -- bash
root@hello-world-flask-5d4494cc9b-jmwxd:/app# ab -c 50 -n 10000 http://127.0.0.1:5000/
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        Werkzeug/2.2.2
Server Hostname:        127.0.0.1
Server Port:            5000

Document Path:          /
Document Length:        65 bytes

Concurrency Level:      50
Time taken for tests:   9.177 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      2380000 bytes
HTML transferred:       650000 bytes
Requests per second:    1089.67 [#/sec] (mean)
Time per request:       45.886 [ms] (mean)
Time per request:       0.918 [ms] (mean, across all concurrent requests)
Transfer rate:          253.26 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       2
Processing:     2   46   5.6     44      80
Waiting:        1   45   5.5     44      80
Total:          2   46   5.6     44      80

Percentage of the requests served within a certain time (ms)
  50%     44
  66%     46
  75%     48
  80%     49
  90%     53
  95%     56
  98%     60
  99%     63
 100%     80 (longest request)

```


再开一个会话窗口（对，第三个）

```bash
kubectl get pods --watch
```


```bash
root@node1:~# kubectl get pods --watch
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-5d4494cc9b-5p7vx   1/1     Running   0          8m
hello-world-flask-5d4494cc9b-87vx2   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-bwh5s   1/1     Running   0          66s
hello-world-flask-5d4494cc9b-jmwxd   1/1     Running   0          7m56s
hello-world-flask-5d4494cc9b-mn4w9   1/1     Running   0          66s
hello-world-flask-5d4494cc9b-r5fh5   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-trsm4   0/1     Pending   0          36s
hello-world-flask-5d4494cc9b-vvn9v   0/1     Pending   0          36s
hello-world-flask-5d4494cc9b-vx2kb   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-xkqxv   1/1     Running   0          51s
```

参数 `--watch` 表示持续监听 Pod 状态变化。在 ab 压力测试的过程中，会不断创建新的 Pod 副本，这说明 K8s 已经感知到了 Pod 的业务压力，并且正在自动进行横向扩容


延长时间会观测到自动缩容

```bash
root@node1:~# kubectl get pods --watch
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-5d4494cc9b-5p7vx   1/1     Running   0          8m
hello-world-flask-5d4494cc9b-87vx2   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-bwh5s   1/1     Running   0          66s
hello-world-flask-5d4494cc9b-jmwxd   1/1     Running   0          7m56s
hello-world-flask-5d4494cc9b-mn4w9   1/1     Running   0          66s
hello-world-flask-5d4494cc9b-r5fh5   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-trsm4   0/1     Pending   0          36s
hello-world-flask-5d4494cc9b-vvn9v   0/1     Pending   0          36s
hello-world-flask-5d4494cc9b-vx2kb   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-xkqxv   1/1     Running   0          51s
hello-world-flask-5d4494cc9b-trsm4   0/1     Terminating   0          4m30s
hello-world-flask-5d4494cc9b-vvn9v   0/1     Terminating   0          4m30s
hello-world-flask-5d4494cc9b-trsm4   0/1     Terminating   0          4m30s
hello-world-flask-5d4494cc9b-vvn9v   0/1     Terminating   0          4m30s
hello-world-flask-5d4494cc9b-bwh5s   1/1     Terminating   0          5m30s
hello-world-flask-5d4494cc9b-vx2kb   1/1     Terminating   0          5m15s
hello-world-flask-5d4494cc9b-r5fh5   1/1     Terminating   0          5m15s
hello-world-flask-5d4494cc9b-xkqxv   1/1     Terminating   0          5m15s
hello-world-flask-5d4494cc9b-mn4w9   1/1     Terminating   0          5m30s
hello-world-flask-5d4494cc9b-87vx2   1/1     Terminating   0          5m15s
hello-world-flask-5d4494cc9b-xkqxv   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-xkqxv   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-xkqxv   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-mn4w9   0/1     Terminating   0          6m1s
hello-world-flask-5d4494cc9b-mn4w9   0/1     Terminating   0          6m1s
hello-world-flask-5d4494cc9b-mn4w9   0/1     Terminating   0          6m1s
hello-world-flask-5d4494cc9b-vx2kb   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-vx2kb   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-vx2kb   0/1     Terminating   0          5m46s
hello-world-flask-5d4494cc9b-87vx2   0/1     Terminating   0          5m47s
hello-world-flask-5d4494cc9b-87vx2   0/1     Terminating   0          5m47s
hello-world-flask-5d4494cc9b-87vx2   0/1     Terminating   0          5m47s
hello-world-flask-5d4494cc9b-bwh5s   0/1     Terminating   0          6m2s
hello-world-flask-5d4494cc9b-bwh5s   0/1     Terminating   0          6m3s
hello-world-flask-5d4494cc9b-bwh5s   0/1     Terminating   0          6m3s
hello-world-flask-5d4494cc9b-r5fh5   0/1     Terminating   0          5m48s
hello-world-flask-5d4494cc9b-r5fh5   0/1     Terminating   0          5m48s
hello-world-flask-5d4494cc9b-r5fh5   0/1     Terminating   0          5m48s

```

```bash
root@node1:~# kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
hello-world-flask-5d4494cc9b-5p7vx   1/1     Running   0          15m
hello-world-flask-5d4494cc9b-jmwxd   1/1     Running   0          15m
```





## 复杂应用的配置分析

建议重开一个沙盒环境，并安装ingress控制器和metric

安装ingress控制器

```bash
kubectl label node node01 ingress-ready=true
```

安装ingress

```bash
kubectl create -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/ingress-nginx/ingress-nginx.yaml
```

安装metric

```bash
kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/cloudzun/resource/main/metrics/metrics.yaml
```



### 数据库配置分析

```yaml
apiVersion: v1
data:
  CreateDB.sql: |-
    CREATE TABLE text (
        id serial PRIMARY KEY,
        text VARCHAR ( 100 ) UNIQUE NOT NULL
    );
kind: ConfigMap
metadata:
  name: pg-init-script
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  labels:
    app: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      containers:
      - name: postgres
        image: postgres
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: sqlscript
          mountPath: /docker-entrypoint-initdb.d
        env:
          - name: POSTGRES_USER
            value: "postgres"
          - name: POSTGRES_PASSWORD
            value: "postgres"
      volumes:
        - name: sqlscript
          configMap:
            name: pg-init-script
---
apiVersion: v1
kind: Service
metadata:
  name: pg-service
  labels:
    app: database
spec:
  type: ClusterIP
  selector:
    app: database
  ports:
  - port: 5432
```

这个 Kubernetes 清单文件定义了一个使用 Postgres 镜像的 Deployment，其中包含一个初始化脚本（在 ConfigMap 中定义）来创建一个 "text" 表。同时，它还创建了一个 ClusterIP 类型的 Service，用于在集群内部访问 Postgres 数据库。

通过这个配置，当 Postgres 容器启动时，会自动执行 ConfigMap 中的 "CreateDB.sql" 脚本来创建 "text" 表。服务 "pg-service" 则提供了一个在集群内访问该 Postgres 数据库的统一入口，其他应用可以通过该服务与数据库进行通信。这种方式使得部署和管理 Postgres 数据库变得更加简单和灵活。

1.  ConfigMap:

    -   `apiVersion: v1`: 使用 Kubernetes API 的 v1 版本。
    -   `kind: ConfigMap`: 表示创建一个 ConfigMap 资源。
    -   `metadata`: ConfigMap 的元数据，包括名称 "pg-init-script"。
    -   `data`: 包含一个 SQL 脚本 "CreateDB.sql"，用于创建一个名为 "text" 的表。这个表有两个字段：一个名为 "id" 的自增主键和一个名为 "text" 的唯一非空 VARCHAR 字段。
2.  Deployment:

    -   `apiVersion: apps/v1`: 使用 Kubernetes API 的 apps/v1 版本。
    -   `kind: Deployment`: 表示创建一个 Deployment 资源。
    -   `metadata`: Deployment 的元数据，包括名称 "postgres" 和一个标签 "app: database"。
    -   `spec`: Deployment 的详细规格。
        -   `replicas: 1`: 只创建一个副本。
        -   `selector`: 用于选择具有 "app: database" 标签的 Pods。
        -   `template`: 定义了 Pod 的模板。
            -   `metadata`: 指定 Pod 的标签 "app: database"。
            -   `spec`: Pod 的详细规格。
                -   `containers`: 容器的列表，只有一个容器 "postgres"。
                    -   `name: postgres`: 容器的名称。
                    -   `image: postgres`: 容器的镜像。
                    -   `imagePullPolicy: IfNotPresent`: 如果镜像已经存在，就不需要拉取。
                    -   `ports`: 容器暴露的端口列表，这里只有一个端口 5432。
                    -   `volumeMounts`: 挂载的卷列表，挂载名为 "sqlscript" 的卷到容器内的 "/docker-entrypoint-initdb.d" 目录。
                    -   `env`: 定义了环境变量 "POSTGRES_USER" 和 "POSTGRES_PASSWORD"，分别设置为 "postgres" 和 "postgres"。
                -   `volumes`: 卷的列表，这里只有一个卷 "sqlscript"。
                    -   `name: sqlscript`: 卷的名称。
                    -   `configMap`: 从 ConfigMap "pg-init-script" 创建卷。
3.  Service:

    -   `apiVersion: v1`: 使用 Kubernetes API 的 v1 版本。
    -   `kind: Service`: 表示创建一个 Service 资源。
    -   `metadata`: Service 的元数据，包括名称 "pg-service" 和一个标签 "app: database"。
    -   `spec`: Service 的详细规格。
        -   `type: ClusterIP`: 使用 ClusterIP 类型的 Service。
        -   `selector`: 选择具有 "app: database" 标签的 Pods。
        -   `ports`: 服务暴露的端口列表，这里只有一个端口 5432。

### 后端服务分析

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  labels:
    app: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: flask-backend
        image: chengzh/backend:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 5000
        env:
        - name: DATABASE_URI
          value: pg-service
        - name: DATABASE_USERNAME
          value: postgres
        - name: DATABASE_PASSWORD
          value: postgres
        resources:
          requests:
            memory: "128Mi"
            cpu: "128m"
          limits:
            memory: "256Mi"
            cpu: "256m"
        readinessProbe: 
          httpGet:
            path: /healthy
            port: 5000
            scheme: HTTP
          initialDelaySeconds: 10
          failureThreshold: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        livenessProbe: 
          httpGet:
            path: /healthy
            port: 5000
            scheme: HTTP
          failureThreshold: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        startupProbe: 
          httpGet:
            path: /healthy
            port: 5000
            scheme: HTTP
          initialDelaySeconds: 10
          failureThreshold: 5
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
---
apiVersion: v1
kind: Service
metadata:
  name: backend-service
  labels:
    app: backend
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
  - port: 5000
    targetPort: 5000
```

这个 Kubernetes 清单文件定义了一个基于 Flask 的后端应用的 Deployment 和一个 ClusterIP 类型的 Service。部署中的容器使用了一个自定义镜像 "chengzh/backend:latest"，并暴露了 5000 端口。同时设置了环境变量以连接到前面创建的 Postgres 数据库。Service "backend-service" 则提供了一个统一的访问点，使得集群内其他服务可以与此后端应用通信。此外，还配置了探针以确保应用的正常运行和故障恢复。

1.  Deployment:

    -   `apiVersion: apps/v1`: 使用 Kubernetes API 的 apps/v1 版本。
    -   `kind: Deployment`: 表示创建一个 Deployment 资源。
    -   `metadata`: Deployment 的元数据，包括名称 "backend" 和一个标签 "app: backend"。
    -   `spec`: Deployment 的详细规格。
        -   `replicas: 1`: 只创建一个副本。
        -   `selector`: 用于选择具有 "app: backend" 标签的 Pods。
        -   `template`: 定义了 Pod 的模板。
            -   `metadata`: 指定 Pod 的标签 "app: backend"。
            -   `spec`: Pod 的详细规格。
                -   `containers`: 容器的列表，只有一个容器 "flask-backend"。
                    -   `name: flask-backend`: 容器的名称。
                    -   `image: chengzh/backend:latest`: 容器的镜像。
                    -   `imagePullPolicy: IfNotPresent`: 如果镜像已经存在，就不需要拉取。
                    -   `ports`: 容器暴露的端口列表，这里只有一个端口 5000。
                    -   `env`: 定义了环境变量 "DATABASE_URI"、"DATABASE_USERNAME" 和 "DATABASE_PASSWORD"，用于连接到前面创建的 Postgres 数据库。
                    -   `resources`: 定义容器的资源限制和请求。
                    -   `readinessProbe`: 定义容器的就绪探针，用于检查应用是否准备好接受流量。
                    -   `livenessProbe`: 定义容器的存活探针，用于检查应用是否仍在运行。
                    -   `startupProbe`: 定义容器的启动探针，用于检查应用是否成功启动。
2.  Service:

    -   `apiVersion: v1`: 使用 Kubernetes API 的 v1 版本。
    -   `kind: Service`: 表示创建一个 Service 资源。
    -   `metadata`: Service 的元数据，包括名称 "backend-service" 和一个标签 "app: backend"。
    -   `spec`: Service 的详细规格。
        -   `type: ClusterIP`: 使用 ClusterIP 类型的 Service。
        -   `selector`: 选择具有 "app: backend" 标签的 Pods。
        -   `ports`: 服务暴露的端口列表，这里只有一个端口 5000，对应容器的 5000 端口。

探针配置说明：

-   `readinessProbe`: 就绪探针

    -   `httpGet`: 使用 HTTP GET 请求检查应用是否准备好接受流量。
    -   `path`: 探针访问的路径，这里设置为 "/healthy"。
    -   `port`: 探针访问的端口，这里设置为 5000。
    -   `initialDelaySeconds`: 容器启动后多少秒开始进行探测，默认值为 0。
    -   `failureThreshold`: 探测失败多少次后认为容器不健康，默认值为 3。
    -   `periodSeconds`: 探测的周期，默认值为 10。
    -   `successThreshold`: 探测成功多少次后认为容器健康，默认值为 1。
    -   `timeoutSeconds`: 探测超时时间，默认值为 1。
-   `livenessProbe`: 存活探针

    -   使用与就绪探针相同的设置进行存活探测。
-   `startupProbe`: 启动探针

    -   使用与就绪探针相同的设置进行启动探测。

通过这个配置，应用在启动、运行过程中和准备好接收请求时，Kubernetes 都会根据探针的配置检查应用的状态。这有助于确保应用在出现问题时能够自动恢复，提高了应用的可用性和稳定性。

### 前端服务分析

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: react-frontend
        image: chengzh/frontend:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
        resources:
          requests:
            memory: "128Mi"
            cpu: "128m"
          limits:
            memory: "256Mi"
            cpu: "256m"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: ClusterIP
  selector:
    app: frontend
  ports:
  - port: 3000
    targetPort: 3000
```

这个 Kubernetes 清单文件定义了一个基于 React 的前端应用的 Deployment 和一个 ClusterIP 类型的 Service。部署中的容器使用了一个自定义镜像 "chengzh/frontend:latest"，并暴露了 3000 端口。Service "frontend-service" 则提供了一个统一的访问点，使得集群内其他服务可以与此前端应用通信。

1.  Deployment:

    -   `apiVersion: apps/v1`: 使用 Kubernetes API 的 apps/v1 版本。
    -   `kind: Deployment`: 表示创建一个 Deployment 资源。
    -   `metadata`: Deployment 的元数据，包括名称 "frontend" 和一个标签 "app: frontend"。
    -   `spec`: Deployment 的详细规格。
        -   `replicas: 1`: 只创建一个副本。
        -   `selector`: 用于选择具有 "app: frontend" 标签的 Pods。
        -   `template`: 定义了 Pod 的模板。
            -   `metadata`: 指定 Pod 的标签 "app: frontend"。
            -   `spec`: Pod 的详细规格。
                -   `containers`: 容器的列表，只有一个容器 "react-frontend"。
                    -   `name: react-frontend`: 容器的名称。
                    -   `image: chengzh/frontend:latest`: 容器的镜像。
                    -   `imagePullPolicy: IfNotPresent`: 如果镜像已经存在，就不需要拉取。
                    -   `ports`: 容器暴露的端口列表，这里只有一个端口 3000。
                    -   `resources`: 定义容器的资源限制和请求。
2.  Service:

    -   `apiVersion: v1`: 使用 Kubernetes API 的 v1 版本。
    -   `kind: Service`: 表示创建一个 Service 资源。
    -   `metadata`: Service 的元数据，包括名称 "frontend-service"。
    -   `spec`: Service 的详细规格。
        -   `type: ClusterIP`: 使用 ClusterIP 类型的 Service。
        -   `selector`: 选择具有 "app: frontend" 标签的 Pods。
        -   `ports`: 服务暴露的端口列表，这里只有一个端口 3000，对应容器的 3000 端口。

###  ingress 分析

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - http:
        paths:
          - path: /?(.*)
            pathType: Prefix
            backend:
              service:
                name: frontend-service
                port:
                  number: 3000
          - path: /api/?(.*)
            pathType: Prefix
            backend:
              service:
                name: backend-service
                port:
                  number: 5000
  ingressClassName: nginx
```

这个 Kubernetes 清单文件定义了一个 Ingress 资源，用于配置基于 Nginx Ingress 控制器的应用路由规则。它将根路径（及其子路径）的请求路由到 "frontend-service" 服务（端口 3000），将以 "/api" 开头的请求路由到 "backend-service" 服务（端口 5000）。这为前端和后端服务提供了一个统一的访问点。

1.  Ingress:

    -   `apiVersion: networking.k8s.io/v1`: 使用 Kubernetes API 的 `networking.k8s.io/v1` 版本。
    -   `kind: Ingress`: 表示创建一个 Ingress 资源。
    -   `metadata`: Ingress 的元数据，包括名称 "frontend-ingress"。
    -   `annotations`: Ingress 的注解，用于配置 Ingress 控制器的行为。
        -   `nginx.ingress.kubernetes.io/rewrite-target: /$1`: 为 Nginx Ingress 控制器设置 URL 重写规则。
2.  `spec`: Ingress 的详细规格。

    -   `rules`: 定义 Ingress 的路由规则。
        -   第一条规则：
            -   `path: /?(.*)`: 匹配根路径及其子路径。问号表示该子组是可选的，星号表示匹配任意字符。
            -   `pathType: Prefix`: 表示路径匹配类型为前缀匹配。
            -   `backend`: 定义后端服务的信息。
                -   `service`: 指定后端服务的名称和端口。
                    -   `name: frontend-service`: 指定后端服务名称为 "frontend-service"。
                    -   `port`: 指定后端服务端口号为 3000。
        -   第二条规则：
            -   `path: /api/?(.*)`: 匹配以 "/api" 开头的路径及其子路径。
            -   `pathType: Prefix`: 表示路径匹配类型为前缀匹配。
            -   `backend`: 定义后端服务的信息。
                -   `service`: 指定后端服务的名称和端口。
                    -   `name: backend-service`: 指定后端服务名称为 "backend-service"。
                    -   `port`: 指定后端服务端口号为 5000。
    -   `ingressClassName: nginx`: 指定使用名为 "nginx" 的 Ingress 类。

### HPA策略分析

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: frontend
spec:
  scaleTargetRef:
    kind: Deployment
    name: frontend
    apiVersion: apps/v1
  minReplicas: 2
  maxReplicas: 2
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 80

---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend
spec:
  scaleTargetRef:
    kind: Deployment
    name: backend
    apiVersion: apps/v1
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 50
```

这个 Kubernetes 清单文件定义了两个 HorizontalPodAutoscaler（HPA）资源，用于自动伸缩前端和后端应用的 Deployment。前端应用的 HPA 配置会根据 CPU 利用率进行伸缩，最小和最大副本数均为 2。后端应用的 HPA 配置会根据 CPU 和内存利用率进行伸缩，最小副本数为 2，最大副本数为 10。这些配置将确保在负载变化时，应用能够根据需要自动调整副本数量，以提供稳定的性能和资源利用率。

1.  前端应用的 HPA 配置：

    -   `apiVersion: autoscaling/v2`: 使用 Kubernetes API 的 autoscaling/v2 版本。
    -   `kind: HorizontalPodAutoscaler`: 表示创建一个 HorizontalPodAutoscaler 资源。
    -   `metadata`: HPA 的元数据，包括名称 "frontend"。
    -   `spec`: HPA 的详细规格。
        -   `scaleTargetRef`: 需要伸缩的目标资源的引用。
            -   `kind: Deployment`: 目标资源类型是 Deployment。
            -   `name: frontend`: 目标资源的名称为 "frontend"。
            -   `apiVersion: apps/v1`: 目标资源的 API 版本为 apps/v1。
        -   `minReplicas: 2`: 最小副本数为 2。
        -   `maxReplicas: 2`: 最大副本数为 2。
        -   `metrics`: 用于驱动伸缩行为的指标。
            -   `type: Resource`: 指标类型为资源。
            -   `resource`: 目标资源的详细信息。
                -   `name: cpu`: 目标资源名称为 CPU。
                -   `target`: 目标资源的使用目标。
                    -   `type: Utilization`: 目标类型为利用率。
                    -   `averageUtilization: 80`: 平均利用率目标为 80%。
2.  后端应用的 HPA 配置：

    -   `apiVersion: autoscaling/v2`: 使用 Kubernetes API 的 autoscaling/v2 版本。
    -   `kind: HorizontalPodAutoscaler`: 表示创建一个 HorizontalPodAutoscaler 资源。
    -   `metadata`: HPA 的元数据，包括名称 "backend"。
    -   `spec`: HPA 的详细规格。
        -   `scaleTargetRef`: 需要伸缩的目标资源的引用。
            -   `kind: Deployment`: 目标资源类型是 Deployment。
            -   `name: backend`: 目标资源的名称为 "backend"。
            -   `apiVersion: apps/v1`: 目标资源的 API 版本为 apps/v1。
        -   `minReplicas: 2`: 最小副本数为 2。
        -   `maxReplicas: 10`: 最大副本数为 10。
        -   `metrics`: 用于驱动伸缩行为的指标。
            -   第一个指标：
                -   `type: Resource`: 指标类型为资源。
                -   `resource`: 目标资源的详细信息。
                    -   `name: cpu`: 目标资源名称为 CPU。
                    -   `target`: 目标资源的使用目标。
                        -   `type: Utilization`: 目标类型为利用率。
                        -   `averageUtilization: 50`: 平均利用率目标为 50%。
            -   第二个指标：
                -   `type: Resource`: 指标类型为资源。
                -   `resource`: 目标资源的详细信息。
                    -   `name: memory`: 目标资源名称为内存。 
                    -   `target`: 目标资源的使用目标。 
                        -   `type: Utilization`: 目标类型为利用率。 
                        -   `averageUtilization: 50`: 平均利用率目标为 50%。



## 在Kubernetes群集部署维护复杂应用



### 部署示例应用

```bash
kubectl create namespace example

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



### **服务调用和发布**

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

特别关注`backend-service`  的 ip 地址，比如 `10.96.194.109`

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



### **验证应用配置**

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

这个命令的目的是在当前环境变量中查找包含 "DATABASE" 的变量并显示它们的值。

- `env`: 这个命令会列出当前环境中的所有环境变量及其值。
- `|`: 这个符号是管道符，它的作用是将前一个命令的输出作为后一个命令的输入。
- `grep DATABASE`: 这个命令会在输入中查找包含 "DATABASE" 的行。

在你的输出中，我们可以看到有三个环境变量：

1. `DATABASE_USERNAME=postgres`: 这个环境变量表示数据库的用户名是 "postgres"。
2. `DATABASE_PASSWORD=postgres`: 这个环境变量表示数据库的密码也是 "postgres"。
3. `DATABASE_URI=pg-service`: 这个环境变量表示数据库的 URI 是 "pg-service"，这通常是一个服务名或者一个主机名，用于指向数据库的服务。

这些环境变量通常用于配置应用程序，使其能够连接到正确的数据库并使用正确的凭据进行身份验证。

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

这是一个 SQL 语句，用来创建一个名为 "text" 的表。以下是这个 SQL 语句的详细解释：

1. `CREATE TABLE text`: 这个语句是用来创建一个新的表，表的名字是 "text"。

2. `id serial PRIMARY KEY`: 这个语句定义了一个名为 "id" 的字段，字段的类型是 "serial"，这是 PostgreSQL 中的一个自增整数类型，通常用作主键。"PRIMARY KEY" 表示这个字段是这个表的主键，主键在表中的每一行都必须是唯一的，并且不能是 NULL。

3. `text VARCHAR ( 100 ) UNIQUE NOT NULL`: 这个语句定义了一个名为 "text" 的字段，字段的类型是 "VARCHAR(100)"，这表示这个字段是一个可以包含最多 100 个字符的可变长度字符串。"UNIQUE" 表示这个字段在表中的每一行都必须是唯一的。"NOT NULL" 表示这个字段不能是 NULL，也就是说，每一行都必须有这个字段的值。

总的来说，这个 SQL 语句创建了一个包含两个字段（"id" 和 "text"）的表，"id" 是主键并且会自动递增，"text" 字段必须是唯一的并且不能是 NULL。

```bash
exit
```



### **应用扩缩容**

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

访问前端服务api触发扩容

```bash
curl http://node01/api/ab
```

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

