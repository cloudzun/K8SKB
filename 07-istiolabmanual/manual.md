# Lab 1 Istio环境的安装

下载当前最新版本Istio

```bash
curl -L https://istio.io/downloadIstio | sh -
```



```bash
controlplane $ curl -L https://istio.io/downloadIstio | sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   101  100   101    0     0   1530      0 --:--:-- --:--:-- --:--:--  1530
100  4856  100  4856    0     0  55181      0 --:--:-- --:--:-- --:--:-- 55181

Downloading istio-1.16.0 from https://github.com/istio/istio/releases/download/1.16.0/istio-1.16.0-linux-amd64.tar.gz ...

Istio 1.16.0 Download Complete!

Istio has been successfully downloaded into the istio-1.16.0 folder on your system.

Next Steps:
See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

To configure the istioctl client tool for your workstation,
add the /root/istio-1.16.0/istio-1.16.0/bin directory to your environment path variable with:
         export PATH="$PATH:/root/istio-1.16.0/istio-1.16.0/bin"

Begin the Istio pre-installation check by running:
         istioctl x precheck 

Need more information? Visit https://istio.io/latest/docs/setup/install/ 
```



根据上述步骤的输出设置环境变量

```bash
export PATH="$PATH:/root/istio-1.16.0/bin"
```



进入目录

```bash
cd istio-1.16.0/
```



检查安装前提条件

```bash
istioctl x precheck 
```



```bash
controlplane $ istioctl x precheck 
✔ No issues found when checking the cluster. Istio is safe to install or upgrade!
  To get started, check out https://istio.io/latest/docs/setup/getting-started/
```



执行安装

```bash
istioctl manifest apply --set profile=demo
```



```bash
controlplane $ istioctl verify-install
0 Istio control planes detected, checking --revision "default" only
error while fetching revision : the server could not find the requested resource
0 Istio injectors detected
Error: could not load IstioOperator from cluster: the server could not find the requested resource. Use --filename
controlplane $ istioctl manifest apply --set profile=demo
This will install the Istio 1.16.0 demo profile with ["Istio core" "Istiod" "Ingress gateways" "Egress gateways"] components into the cluster. Proceed? (y/N) y
✔ Istio core installed                                                                                                                                                                         
✔ Istiod installed                                                                                                                                                                                
✔ Egress gateways installed                                                                                                                                                                       
✔ Ingress gateways installed                                                                                                                                                                      
✔ Installation complete                                                                                                                                                                           Making this installation the default for injection and validation.

Thank you for installing Istio 1.16.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/99uiMML96AmsXY5d6
```



安装仪表板：

```bash
kubectl apply -f samples/addons
```



```bash
controlplane $ kubectl apply -f samples/addons
serviceaccount/grafana created
configmap/grafana created
service/grafana created
deployment.apps/grafana created
configmap/istio-grafana-dashboards created
configmap/istio-services-grafana-dashboards created
deployment.apps/jaeger created
service/tracing created
service/zipkin created
service/jaeger-collector created
serviceaccount/kiali created
configmap/kiali created
clusterrole.rbac.authorization.k8s.io/kiali-viewer created
clusterrole.rbac.authorization.k8s.io/kiali created
clusterrolebinding.rbac.authorization.k8s.io/kiali created
role.rbac.authorization.k8s.io/kiali-controlplane created
rolebinding.rbac.authorization.k8s.io/kiali-controlplane created
service/kiali created
deployment.apps/kiali created
serviceaccount/prometheus created
configmap/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created
service/prometheus created
deployment.apps/prometheus created
```



检查 istio 安装版本：

```bash
istioctl version
```



```bash
controlplane $ istioctl version
client version: 1.16.0
control plane version: 1.16.0
data plane version: 1.16.0 (2 proxies)
```



查看 crd：

```bash
kubectl get crd | grep istio
```



```bash
controlplane $ kubectl get crd | grep istio
authorizationpolicies.security.istio.io               2022-12-01T02:49:38Z
destinationrules.networking.istio.io                  2022-12-01T02:49:38Z
envoyfilters.networking.istio.io                      2022-12-01T02:49:39Z
gateways.networking.istio.io                          2022-12-01T02:49:39Z
istiooperators.install.istio.io                       2022-12-01T02:49:39Z
peerauthentications.security.istio.io                 2022-12-01T02:49:39Z
proxyconfigs.networking.istio.io                      2022-12-01T02:49:39Z
requestauthentications.security.istio.io              2022-12-01T02:49:39Z
serviceentries.networking.istio.io                    2022-12-01T02:49:39Z
sidecars.networking.istio.io                          2022-12-01T02:49:39Z
telemetries.telemetry.istio.io                        2022-12-01T02:49:40Z
virtualservices.networking.istio.io                   2022-12-01T02:49:40Z
wasmplugins.extensions.istio.io                       2022-12-01T02:49:40Z
workloadentries.networking.istio.io                   2022-12-01T02:49:40Z
workloadgroups.networking.istio.io                    2022-12-01T02:49:40Z
```



查看 api 资源：

```bash
kubectl api-resources | grep istio
```



```bash
controlplane $ kubectl api-resources | grep istio
wasmplugins                                    extensions.istio.io/v1alpha1           true         WasmPlugin
istiooperators                    iop,io       install.istio.io/v1alpha1              true         IstioOperator
destinationrules                  dr           networking.istio.io/v1beta1            true         DestinationRule
envoyfilters                                   networking.istio.io/v1alpha3           true         EnvoyFilter
gateways                          gw           networking.istio.io/v1beta1            true         Gateway
proxyconfigs                                   networking.istio.io/v1beta1            true         ProxyConfig
serviceentries                    se           networking.istio.io/v1beta1            true         ServiceEntry
sidecars                                       networking.istio.io/v1beta1            true         Sidecar
virtualservices                   vs           networking.istio.io/v1beta1            true         VirtualService
workloadentries                   we           networking.istio.io/v1beta1            true         WorkloadEntry
workloadgroups                    wg           networking.istio.io/v1beta1            true         WorkloadGroup
authorizationpolicies                          security.istio.io/v1beta1              true         AuthorizationPolicy
peerauthentications               pa           security.istio.io/v1beta1              true         PeerAuthentication
requestauthentications            ra           security.istio.io/v1beta1              true         RequestAuthentication
telemetries                       telemetry    telemetry.istio.io/v1alpha1            true         Telemetry
```



查看命名空间：

```bash
kubectl get namespaces 
```



查看 istio 相关pod：

```bash
kubectl get pods -n istio-system
```



```bash
controlplane $ kubectl get pods -n istio-system
NAME                                    READY   STATUS    RESTARTS   AGE
grafana-56bdf8bf85-xm6gn                1/1     Running   0          5m2s
istio-egressgateway-5bdd756dfd-tqmz9    1/1     Running   0          6m31s
istio-ingressgateway-67f7b5f88d-j4v8z   1/1     Running   0          6m31s
istiod-58c6454c57-vq7l4                 1/1     Running   0          6m47s
jaeger-c4fdf6674-ltgkn                  1/1     Running   0          5m2s
kiali-5ff49b9f69-sf42z                  1/1     Running   0          5m2s
prometheus-85949fddb-z57vs              2/2     Running   0          5m1s
```



查看 istio 服务状态：

```bash
kubectl get svc -n istio-system 
```



```bash
controlplane $ kubectl get svc -n istio-system 
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                      AGE
grafana                ClusterIP      10.104.87.225    <none>        3000/TCP                                                                     5m43s
istio-egressgateway    ClusterIP      10.103.43.184    <none>        80/TCP,443/TCP                                                               7m11s
istio-ingressgateway   LoadBalancer   10.105.52.139    <pending>     15021:30275/TCP,80:31216/TCP,443:31018/TCP,31400:30363/TCP,15443:32013/TCP   7m11s
istiod                 ClusterIP      10.110.235.100   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP                                        7m28s
jaeger-collector       ClusterIP      10.109.101.148   <none>        14268/TCP,14250/TCP,9411/TCP                                                 5m42s
kiali                  ClusterIP      10.101.69.18     <none>        20001/TCP,9090/TCP                                                           5m42s
prometheus             ClusterIP      10.100.129.105   <none>        9090/TCP                                                                     5m42s
tracing                ClusterIP      10.96.122.246    <none>        80/TCP,16685/TCP                                                             5m42s
zipkin                 ClusterIP      10.106.66.61     <none>        9411/TCP                                                                     5m42s
```



查看各组件状态：

```bash
kubectl get svc,pod,hpa,pdb,Gateway,VirtualService -n istio-system
```



加载实验脚本目录

```bash
git clone https://github.com/cloudzun/istiolabmanual
```

 



# Lab 2 Bookinfo 示例程序安装

Bookinfo 是 Istio 社区官方推荐的示例应用之一。它可以用来演示多种 Istio 的特性，并且它是一个异构的微服务应用。
本章节大部分实验都和bookinfo有关，因此熟练 快速 准确地部署bookinfo是捣鼓istio重要基本功。



启动自动注入sidecar

```bash
kubectl label namespace default istio-injection=enabled
```



安装 bookinfo

```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```



```bash
controlplane $ kubectl label namespace default istio-injection=enabled
namespace/default labeled
controlplane $ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```



确认服务和 pod状态

```bash
kubectl get svc

kubectl get pod
```

此处需要等待大概2分钟，等到所有的pod都ready再进行下一步



```bash
controlplane $ kubectl get svc
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.103.216.219   <none>        9080/TCP   40s
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP    17d
productpage   ClusterIP   10.96.27.197     <none>        9080/TCP   39s
ratings       ClusterIP   10.96.54.189     <none>        9080/TCP   39s
reviews       ClusterIP   10.96.4.49       <none>        9080/TCP   39s
controlplane $ 
controlplane $ kubectl get pod
NAME                             READY   STATUS            RESTARTS   AGE
details-v1-5ffd6b64f7-s7src      2/2     Running           0          41s
productpage-v1-979d4d9fc-l97ck   0/2     PodInitializing   0          40s
ratings-v1-5f9699cfdf-465tm      2/2     Running           0          41s
reviews-v1-569db879f5-44qtz      0/2     PodInitializing   0          41s
reviews-v2-65c4dc6fdc-xpb2d      1/2     Running           0          41s
reviews-v3-c9c4fb987-xwdmj       0/2     PodInitializing   0          41s
```



检查sidecar自动注入

```bash
kubectl describe pod productpage-v1-979d4d9fc-l97ck
```

  *重点关注Container部分和Events部分

```bash
controlplane $ kubectl describe pod productpage-v1-979d4d9fc-l97ck
Name:             productpage-v1-979d4d9fc-l97ck
Namespace:        default
Priority:         0
Service Account:  bookinfo-productpage
Node:             controlplane/172.30.1.2
Start Time:       Thu, 01 Dec 2022 03:41:06 +0000
Labels:           app=productpage
                  pod-template-hash=979d4d9fc
                  security.istio.io/tlsMode=istio
                  service.istio.io/canonical-name=productpage
                  service.istio.io/canonical-revision=v1
                  version=v1
Annotations:      cni.projectcalico.org/containerID: 370eb16f6bbdde82e05db1b246ca9c9b7d624ea6da7725f2dd8c994833959c8b
                  cni.projectcalico.org/podIP: 192.168.0.7/32
                  cni.projectcalico.org/podIPs: 192.168.0.7/32
                  kubectl.kubernetes.io/default-container: productpage
                  kubectl.kubernetes.io/default-logs-container: productpage
                  prometheus.io/path: /stats/prometheus
                  prometheus.io/port: 15020
                  prometheus.io/scrape: true
                  sidecar.istio.io/status:
                    {"initContainers":["istio-init"],"containers":["istio-proxy"],"volumes":["workload-socket","credential-socket","workload-certs","istio-env...
Status:           Running
IP:               192.168.0.7
IPs:
  IP:           192.168.0.7
Controlled By:  ReplicaSet/productpage-v1-979d4d9fc
Init Containers:
  istio-init:
    Container ID:  containerd://0ed6fa7fc4424ca8e438b539a789f0d0f1cf83db44c51000f69e8ffc782f0ebd
    Image:         docker.io/istio/proxyv2:1.16.0
    Image ID:      docker.io/istio/proxyv2@sha256:f6f97fa4fb77a3cbe1e3eca0fa46bd462ad6b284c129cf57bf91575c4fb50cf9
    Port:          <none>
    Host Port:     <none>
    Args:
      istio-iptables
      -p
      15001
      -z
      15006
      -u
      1337
      -m
      REDIRECT
      -i
      *
      -x
      
      -b
      *
      -d
      15090,15021,15020
      --log_output_level=default:info
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Thu, 01 Dec 2022 03:41:18 +0000
      Finished:     Thu, 01 Dec 2022 03:41:18 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:        10m
      memory:     40Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-n7v78 (ro)
Containers:
  productpage:
    Container ID:   containerd://8a0f4d1e955d4b0c5349d5bc56467d6bb135bf06031dc397d483dda64fcd9e89
    Image:          docker.io/istio/examples-bookinfo-productpage-v1:1.17.0
    Image ID:       docker.io/istio/examples-bookinfo-productpage-v1@sha256:6668bcf42ef0afb89d0ccd378905c761eab0f06919e74e178852b58b4bbb29c5
    Port:           9080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 01 Dec 2022 03:42:06 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /tmp from tmp (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-n7v78 (ro)
  istio-proxy:
    Container ID:  containerd://01ec70eef67d93f2b67b0c274edf98c6c14718a61fe8e97137f792ec3023105b
    Image:         docker.io/istio/proxyv2:1.16.0
    Image ID:      docker.io/istio/proxyv2@sha256:f6f97fa4fb77a3cbe1e3eca0fa46bd462ad6b284c129cf57bf91575c4fb50cf9
    Port:          15090/TCP
    Host Port:     0/TCP
    Args:
      proxy
      sidecar
      --domain
      $(POD_NAMESPACE).svc.cluster.local
      --proxyLogLevel=warning
      --proxyComponentLogLevel=misc:error
      --log_output_level=default:info
      --concurrency
      2
    State:          Running
      Started:      Thu, 01 Dec 2022 03:42:06 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:      10m
      memory:   40Mi
    Readiness:  http-get http://:15021/healthz/ready delay=1s timeout=3s period=2s #success=1 #failure=30
    Environment:
      JWT_POLICY:                    third-party-jwt
      PILOT_CERT_PROVIDER:           istiod
      CA_ADDR:                       istiod.istio-system.svc:15012
      POD_NAME:                      productpage-v1-979d4d9fc-l97ck (v1:metadata.name)
      POD_NAMESPACE:                 default (v1:metadata.namespace)
      INSTANCE_IP:                    (v1:status.podIP)
      SERVICE_ACCOUNT:                (v1:spec.serviceAccountName)
      HOST_IP:                        (v1:status.hostIP)
      PROXY_CONFIG:                  {}
                                     
      ISTIO_META_POD_PORTS:          [
                                         {"containerPort":9080,"protocol":"TCP"}
                                     ]
      ISTIO_META_APP_CONTAINERS:     productpage
      ISTIO_META_CLUSTER_ID:         Kubernetes
      ISTIO_META_INTERCEPTION_MODE:  REDIRECT
      ISTIO_META_WORKLOAD_NAME:      productpage-v1
      ISTIO_META_OWNER:              kubernetes://apis/apps/v1/namespaces/default/deployments/productpage-v1
      ISTIO_META_MESH_ID:            cluster.local
      TRUST_DOMAIN:                  cluster.local
    Mounts:
      /etc/istio/pod from istio-podinfo (rw)
      /etc/istio/proxy from istio-envoy (rw)
      /var/lib/istio/data from istio-data (rw)
      /var/run/secrets/credential-uds from credential-socket (rw)
      /var/run/secrets/istio from istiod-ca-cert (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-n7v78 (ro)
      /var/run/secrets/tokens from istio-token (rw)
      /var/run/secrets/workload-spiffe-credentials from workload-certs (rw)
      /var/run/secrets/workload-spiffe-uds from workload-socket (rw)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  workload-socket:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  credential-socket:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  workload-certs:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  istio-envoy:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  <unset>
  istio-data:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  istio-podinfo:
    Type:  DownwardAPI (a volume populated by information about the pod)
    Items:
      metadata.labels -> labels
      metadata.annotations -> annotations
  istio-token:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  43200
  istiod-ca-cert:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      istio-ca-root-cert
    Optional:  false
  tmp:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  kube-api-access-n7v78:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  96s                default-scheduler  Successfully assigned default/productpage-v1-979d4d9fc-l97ck to controlplane
  Normal   Pulling    95s                kubelet            Pulling image "docker.io/istio/proxyv2:1.16.0"
  Normal   Pulled     85s                kubelet            Successfully pulled image "docker.io/istio/proxyv2:1.16.0" in 10.334996634s
  Normal   Created    85s                kubelet            Created container istio-init
  Normal   Started    84s                kubelet            Started container istio-init
  Normal   Pulling    84s                kubelet            Pulling image "docker.io/istio/examples-bookinfo-productpage-v1:1.17.0"
  Normal   Pulled     37s                kubelet            Successfully pulled image "docker.io/istio/examples-bookinfo-productpage-v1:1.17.0" in 46.9444707s
  Normal   Created    37s                kubelet            Created container productpage
  Normal   Started    36s                kubelet            Started container productpage
  Normal   Pulled     36s                kubelet            Container image "docker.io/istio/proxyv2:1.16.0" already present on machine
  Normal   Created    36s                kubelet            Created container istio-proxy
  Normal   Started    36s                kubelet            Started container istio-proxy
  Warning  Unhealthy  33s (x4 over 35s)  kubelet            Readiness probe failed: Get "http://192.168.0.7:15021/healthz/ready": dial tcp 192.168.0.7:15021: connect: connection refused
```



检查productpage页面访问

```bash
kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
```



```bash
controlplane $ kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>
```



启动默认网关

```bash
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```



使用默认目标规则

```bash
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
```



针对productpage启用nodeport，并确认对外访问路径和端口

```bash
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
echo $GATEWAY_URL
echo http://$GATEWAY_URL/productpage
```

 

这是一组shell命令，用于从Kubernetes集群中获取Istio Ingress Gateway的相关信息，将这些信息分别赋值给环境变量，并输出Istio Ingress Gateway的URL以及应用程序的产品页面URL。

1. `export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')`：
   这条命令使用`kubectl`查询标签为`istio=ingressgateway`的pod，位于名称空间`istio-system`中，然后从结果中提取第一个pod的`hostIP`。这个IP地址被赋值给环境变量`INGRESS_HOST`。

2. `export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')`：
   这条命令查询名称空间`istio-system`中名为`istio-ingressgateway`的服务，并从该服务的端口规格中提取名为`http2`的端口的`nodePort`。这个端口号被赋值给环境变量`INGRESS_PORT`。

3. `export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')`：
   类似于上一条命令，此命令查询名称空间`istio-system`中名为`istio-ingressgateway`的服务，但这次是从服务的端口规格中提取名为`https`的端口的`nodePort`。这个端口号被赋值给环境变量`SECURE_INGRESS_PORT`。

4. `export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT`：
   使用前面获取的`INGRESS_HOST`和`INGRESS_PORT`变量，将它们组合成一个完整的URL，并将其赋值给环境变量`GATEWAY_URL`。

5. `echo $GATEWAY_URL`：
   输出`GATEWAY_URL`的值，这是Istio Ingress Gateway的URL。

6. `echo http://$GATEWAY_URL/productpage`：
   在`GATEWAY_URL`基础上添加应用程序的产品页面路径`/productpage`，输出应用程序的产品页面URL。

这组命令的目的是收集Istio Ingress Gateway的信息，这将帮助你通过URL访问应用程序，验证Istio配置是否正确。



```bash
controlplane $ kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
destinationrule.networking.istio.io/productpage created
destinationrule.networking.istio.io/reviews created
destinationrule.networking.istio.io/ratings created
destinationrule.networking.istio.io/details created
controlplane $ export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
controlplane $ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
controlplane $ export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
controlplane $ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
controlplane $ echo $GATEWAY_URL
172.30.2.2:30175
controlplane $ echo http://$GATEWAY_URL/productpage
http://172.30.2.2:30175/productpage
controlplane $ 
```





# Lab 3 服务路由和流量管理

## 1.动态路由

（可选）启用默认目标规则

```bash
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
```



查看目标规则

```bash
nano samples/bookinfo/networking/destination-rule-all.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: productpage
spec:
  host: productpage
  subsets:
  - name: v1
    labels:
      version: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: ratings
spec:
  host: ratings
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v2-mysql
    labels:
      version: v2-mysql
  - name: v2-mysql-vm
    labels:
      version: v2-mysql-vm
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: details
spec:
  host: details
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
---
```

这是一组 Istio 的 YAML 配置文件，用于定义目标规则 (DestinationRules)。Istio 是一个开源的服务网格，提供了服务间通信、安全和流量管理的功能。在这组配置文件中，定义了 4 个目标规则，分别针对 `productpage`、`reviews`、`ratings` 和 `details` 服务。

1. 第一个 DestinationRule（目标规则）针对 `productpage` 服务：
   - host（主机）：目标规则所应用的服务的名称，即 productpage。
   - subsets（子集）：包含一个名为 v1 的子集，标签 version 为 v1。

2. 第二个 DestinationRule 针对 `reviews` 服务：
   - host：目标规则所应用的服务的名称，即 reviews。
   - subsets：包含 v1、v2 和 v3 三个子集，对应的 version 标签分别为 v1、v2 和 v3。

3. 第三个 DestinationRule 针对 `ratings` 服务：
   - host：目标规则所应用的服务的名称，即 ratings。
   - subsets：包含 v1、v2、v2-mysql 和 v2-mysql-vm 四个子集，对应的 version 标签分别为 v1、v2、v2-mysql 和 v2-mysql-vm。

4. 第四个 DestinationRule 针对 `details` 服务：
   - host：目标规则所应用的服务的名称，即 details。
   - subsets：包含 v1 和 v2 两个子集，对应的 version 标签分别为 v1 和 v2。

总的来说，这些配置文件定义了这些服务之间的子集和版本信息，以便在路由规则和流量管理中使用。

创建将`review`流量都指向`v1`虚拟服务

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```



查看该虚拟服务

```
nano samples/bookinfo/networking/virtual-service-all-v1.yaml
```



这个配置文件明确定义了任何情况下只呈现v1版本的reviews

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1
---
```

这个YAML配置文件包含了4个虚拟服务（VirtualService）配置，分别为productpage、reviews、ratings和details。它们在核心配置上与之前提及的基本配置相似，但涉及到了4个不同的服务。

让我们一一分析这些虚拟服务的差异：

1. ProductPage：
连接到产品页面服务的流量将会被路由至该服务的`v1`子集。这个虚拟服务定义了如下的路由规则：

```yaml
hosts:
- productpage
http:
- route:
  - destination:
      host: productpage
      subset: v1
```

2. Reviews：
连接到reviews服务的流量将会被路由至该服务的`v1`子集。这个虚拟服务定义了如下的路由规则：

```yaml
hosts:
- reviews
http:
- route:
  - destination:
      host: reviews
      subset: v1
```

3. Ratings：
连接到ratings服务的流量将会被路由至该服务的v1子集。这个虚拟服务定义了如下的路由规则：

```yaml
hosts:
- ratings
http:
- route:
  - destination:
      host: ratings
      subset: v1
```

4. Details：
连接到details服务的流量将会被路由至该服务的`v1`子集。这个虚拟服务定义了如下的路由规则：

```yaml
hosts:
- details
http:
- route:
  - destination:
      host: details
      subset: v1
```

总的来说，这些虚拟服务在多个服务上都实现了类似的功能。不同之处在于，它们各自针对不同的服务配置了相应的目标规则（DestinationRule）。这些配置确保了流量会分别路由至各服务对应的`v1`子集。

使用浏览器查看效果,	即使反复F5，也是无星星版

![image-20221201115502082](manual.assets/image-20221201115502082.png)

创建将登录用户的review流量都指向v2的虚拟服务

```bash
kubectl apply -f  samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```



Jason同志应该可以看到黑星星

![image-20221201115541479](manual.assets/image-20221201115541479.png)

查看该虚拟服务

```bash
nano samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
```

这是一个Istio `VirtualService` YAML配置文件，用于定义`reviews`服务的流量路由规则。

- `apiVersion: networking.istio.io/v1alpha3`：表示这个资源对象遵循Istio Networking API，版本是 v1alpha3。
- `kind: VirtualService`：表示我们正在定义一个VirtualService资源。
- `metadata`：资源的元数据部分。在这里，我们为VirtualService命名为 `reviews`。
- `spec`：指定详细的VirtualService配置。
  - `hosts`：此字段定义了一个或多个主机名，其流量将被应用VirtualService规则。在这个例子里，我们指定了一个主机名`reviews`。
  - `http`：此字段定义了一个或多个HTTP路由规则。
    - 第一条路由规则（在`- match`中定义）：
      - `headers`：匹配流量的HTTP头信息。这里，我们需要匹配的头信息是 `end-user`。
      - `exact: jason`：表示仅当头信息 `end-user` 精确匹配 `jason` 时，该规则才会应用。
      - `route`：当匹配成功时，流量将被转发到目标服务。
        - `destination`：指定流量的目标。
          - `host: reviews`: 指定流量的目标主机为`reviews`服务。
          - `subset: v2`: 指定流量应路由至`reviews`服务的子集（subset）`v2`。
   - 第二条路由规则（在`- route`中定义，没有`match`字段）：
     - `route`：这意味着这是一个默认路由，当没有满足上面的匹配规则时，流量将被转发到次目标服务。
       - `destination`：指定流量的目标。
         - `host: reviews`: 指定流量的目标主机为`reviews`服务。
         - `subset: v1`: 指定流量应路由至`reviews`服务的子集（subset）`v1`。

总结一下：这个VirtualService定义了如何处理目标为`reviews`服务的流量。当流量包含一个名为`end-user`、内容为`jason`的HTTP头时，流量将被路由至 `reviews` 服务的 `v2` 子集。在其他情况下（无匹配的头信息），流量将被路由至 `reviews` 服务的 `v1` 子集。

清理环境

```bash
kubectl delete -f samples/bookinfo/networking/virtual-service-all-v1.yaml
kubectl delete -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```



## 2.流量转移

（可选）启用默认目标规则

```bash
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
```



使用浏览器查看页面效果，主要是关注reviews的版本



将所有流量指向`reviews:v1`

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```



查看该虚拟服务

```bash
nano samples/bookinfo/networking/virtual-service-all-v1.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1
---
```

使用浏览器查看页面效果，主要是关注reviews的版本



将`50%` 的流量从 `reviews:v1` 转移到 `reviews:v3`

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
```



查看该虚拟服务

```bash
nano samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50
```



这个配置文件定义了一个Istio VirtualService，它设置了针对reviews服务的流量路由规则。其中关键部分是流量的分配。

根据这个配置，所有进入reviews服务的流量将被平均分配到reviews服务的`v1`子集（版本1）和`v3`子集（版本3）。每个子集分别接收50%的流量权重。这样可以实现对不同版本reviews服务的负载均衡，以及在服务的多个版本间实现平滑的流量切换。



使用浏览器查看页面效果，主要是关注reviews的版本



将 `100%` 的流量路由到 `reviews:v3`

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-v3.yaml
```



查看该虚拟服务

```bash
nano samples/bookinfo/networking/virtual-service-reviews-v3.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v3
```



这个配置文件同样定义了一个Istio VirtualService，它针对reviews服务设置了流量路由规则。然而，与之前的配置文件不同，这个配置文件没有为流量设置权重分配。

在这个配置文件中，所有进入reviews服务的流量将被直接路由至reviews服务的`v3`子集（即版本3）。因为这个配置文件没有为不同版本的reviews服务设置权重分配，所以`v3`子集会接收到全部的流量。这意味着仅使用reviews服务的`v3`版本，而不再对其他版本进行任何负载均衡。



使用浏览器查看页面效果，主要是关注`reviews`的版本

清理

```bash
kubectl delete -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```



## 3.网关

查看现有网关

```bash
kubectl get gw
```



```bash
root@node1:~/istio-1.16.0# kubectl get gw
NAME               AGE
bookinfo-gateway   13m
```



增加网关

```bash
kubectl apply -f istiolabmanual/gateway.yaml 
```



查看网关定义文件

```
nano istiolabmanual/gateway.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: test-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
```



这个配置文件定义了一个Istio Gateway，它指定如何将外部流量引入到Istio服务网格中。Gateway是Istio中用于在服务网格和外部网络之间建立连接关系的关键组件。

以下是关于这个配置文件的主要部分：
- `metadata.name` 指定了Gateway的名称，也就是 "test-gateway"。
- 在 `spec.selector` 中，配置文件使用键值对 "istio: ingressgateway" 来指定Gateway应绑定到Istio网格中的Ingress Gateway组件。这意味着所有进入Istio网格的流量将通过此Ingress Gateway处理。
- `spec.servers` 定义了一个服务器以接收进入服务网格的流量。
  - 使用端口号80，名为"http"，以及HTTP协议，这指定了Ingress Gateway接收HTTP流量的端口。
  - `hosts` 列表中的通配符 "*" 表示此Gateway将接受发送到任何域名的流量。

总之，这个Gateway配置文件定义了一个名为 "test-gateway" 的Ingress Gateway，在80端口上接收任何HTTP流量，并将这些流量引入到Istio服务网格中。



查看现有网关

```bash
kubectl get gw
```



```bash
root@node1:~/istio-1.16.0# kubectl get gw
NAME               AGE
bookinfo-gateway   15m
test-gateway       62s
```



查看该网关配置

```bash
kubectl describe gw test-gateway
```



```bash
root@node1:~/istio-1.16.0# kubectl describe gw test-gateway
Name:         test-gateway
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  networking.istio.io/v1beta1
Kind:         Gateway
Metadata:
  Creation Timestamp:  2022-12-01T07:16:28Z
  Generation:          1
  Managed Fields:
    API Version:  networking.istio.io/v1alpha3
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:selector:
          .:
          f:istio:
        f:servers:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-12-01T07:16:28Z
  Resource Version:  7717
  UID:               93077ab8-0f8d-42ef-94d3-d9fa718f5b83
Spec:
  Selector:
    Istio:  ingressgateway
  Servers:
    Hosts:
      *
    Port:
      Name:      http
      Number:    80
      Protocol:  HTTP
Events:          <none>
```



增加虚拟服务

```bash
kubectl apply -f istiolabmanual/virtualservice.yaml
```



查看虚拟服务定义文件

```bash
nano istiolabmanual/virtualservice.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: test-gateway
spec:
  hosts:
  - "*"
  gateways:
  - test-gateway
  http:
  - match:
    - uri:
        prefix: /details
    - uri:
        exact: /health
    route:
    - destination:
        host: details
        port:
          number: 9080
```



这是一个Istio VirtualService配置文件，用于定义基于特定条件将请求路由到目标服务。具体来说，这个配置文件指定了与网关 "test-gateway" 和 HTTP 请求路径相关的路由规则。

- `apiVersion`: 这表示Istio API 的版本号。
- `kind`: 表明这是一个VirtualService配置。

该配置文件具有以下元信息（metadata）：
- `name`: 配置文件的名称（在这里为 "test-gateway"）。

核心逻辑如下：

- `hosts`: 列出了匹配的主机名，星号表示匹配任何主机。
- `gateways`: 这里只有一个网关（"test-gateway"）。这是请求需要从该网关经过才能进入Istio服务网格的规则。

接着，定义了HTTP请求的路由规则：

- `match`: 包含两个匹配条件：
    - 第一个条件匹配所有以 `/details` 为前缀的URI。
    - 第二个条件匹配准确的URI `/health`。

- 当请求的URI与上述任一条件匹配时，路由规则将被触发。在这种情况下，请求将路由到以下目的地：

  - `destination`: 目标服务：
    - `host`: 目标服务的名称（这里是 "details"）。
    - `port`: 目标服务的端口号（这里是9080）。

总之，这个配置文件定义了一个VirtualService，当请求从"test-gateway"网关进入Istio服务网格并满足指定的URI匹配条件（以 `/details` 为前缀或准确匹配 `/health`）时，请求将被路由到 "details" 服务的9080端口。



查看虚拟服务

```bash
kubectl get vs
```



```bash
root@node1:~/istio-1.16.0# kubectl get vs
NAME           GATEWAYS               HOSTS   AGE
bookinfo       ["bookinfo-gateway"]   ["*"]   18m
test-gateway   ["test-gateway"]       ["*"]   100s
```



查看该虚拟服务配置

```bash
kubectl describe vs test-gateway
```



```bash
root@node1:~/istio-1.16.0# kubectl describe vs test-gateway
Name:         test-gateway
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  networking.istio.io/v1beta1
Kind:         VirtualService
Metadata:
  Creation Timestamp:  2022-12-01T07:18:53Z
  Generation:          1
  Managed Fields:
    API Version:  networking.istio.io/v1alpha3
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:gateways:
        f:hosts:
        f:http:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-12-01T07:18:53Z
  Resource Version:  8001
  UID:               329eb5c3-4518-4f32-b541-8399c282511f
Spec:
  Gateways:
    test-gateway
  Hosts:
    *
  Http:
    Match:
      Uri:
        Prefix:  /details
      Uri:
        Exact:  /health
    Route:
      Destination:
        Host:  details
        Port:
          Number:  9080
Events:            <none>
```



随后使用浏览器访问`/details/0` 和 `/health`，检查效果

![image-20221201152934012](manual.assets/image-20221201152934012.png)



![image-20221201152902244](manual.assets/image-20221201152902244.png)

清理环境

```bash
kubectl delete -f istiolabmanual/gateway.yaml 
kubectl delete -f istiolabmanual/virtualservice.yaml
```





## 4.服务入口

安装sleep应用

```bash
kubectl apply -f samples/sleep/sleep.yaml
```



查看pod

```bash
kubectl  get pod 
```



```bash
root@node1:~/istio-1.16.0# kubectl  get pod
NAME                             READY   STATUS    RESTARTS   AGE
details-v1-698b5d8c98-6qksf      2/2     Running   0          31m
productpage-v1-bf4b489d8-b6k9m   2/2     Running   0          31m
ratings-v1-5967f59c58-wstvp      2/2     Running   0          31m
reviews-v1-9c6bb6658-zwhcl       2/2     Running   0          31m
reviews-v2-8454bb78d8-fbj4k      2/2     Running   0          31m
reviews-v3-6dc9897554-n6947      2/2     Running   0          31m
sleep-75bbc86479-rz9hv           2/2     Running   0          44s
```



设置source_pod 变量

```bash
export SOURCE_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
```



查看出站访问效果

```bash
kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/headers
```



```bash
root@node1:~/istio-1.16.0# kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/headers
{
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.81.0-DEV",
    "X-Amzn-Trace-Id": "Root=1-6388586a-60445856410d4db06df83a5c",
    "X-B3-Sampled": "1",
    "X-B3-Spanid": "15e9d5ae982425d4",
    "X-B3-Traceid": "08e4b47d73abff7a15e9d5ae982425d4",
    "X-Envoy-Attempt-Count": "1",
    "X-Envoy-Peer-Metadata": "ChkKDkFQUF9DT05UQUlORVJTEgcaBXNsZWVwChoKCkNMVVNURVJfSUQSDBoKS3ViZXJuZXRlcwofCgxJTlNUQU5DRV9JUFMSDxoNMTAuMjQ0LjEwNC4xMgoZCg1JU1RJT19WRVJTSU9OEggaBjEuMTYuMAqhAQoGTEFCRUxTEpYBKpMBCg4KA2FwcBIHGgVzbGVlcAokChlzZWN1cml0eS5pc3Rpby5pby90bHNNb2RlEgcaBWlzdGlvCioKH3NlcnZpY2UuaXN0aW8uaW8vY2Fub25pY2FsLW5hbWUSBxoFc2xlZXAKLwojc2VydmljZS5pc3Rpby5pby9jYW5vbmljYWwtcmV2aXNpb24SCBoGbGF0ZXN0ChoKB01FU0hfSUQSDxoNY2x1c3Rlci5sb2NhbAogCgROQU1FEhgaFnNsZWVwLTc1YmJjODY0Nzktcno5aHYKFgoJTkFNRVNQQUNFEgkaB2RlZmF1bHQKSQoFT1dORVISQBo+a3ViZXJuZXRlczovL2FwaXMvYXBwcy92MS9uYW1lc3BhY2VzL2RlZmF1bHQvZGVwbG95bWVudHMvc2xlZXAKFwoRUExBVEZPUk1fTUVUQURBVEESAioAChgKDVdPUktMT0FEX05BTUUSBxoFc2xlZXA=",
    "X-Envoy-Peer-Metadata-Id": "sidecar~10.244.104.12~sleep-75bbc86479-rz9hv.default~default.svc.cluster.local"
  }
}
```



关闭默认出站访问

```bash
istioctl install  --set meshConfig.outboundTrafficPolicy.mode=REGISTRY_ONLY -y
```



```bash
root@node1:~/istio-1.16.0# istioctl install  --set meshConfig.outboundTrafficPolicy.mode=REGISTRY_ONLY -y
✔ Istio core installed
✔ Istiod installed
✔ Ingress gateways installed
- Pruning removed resources                                                                                                              Removed Deployment:istio-system:istio-egressgateway.
  Removed Service:istio-system:istio-egressgateway.
  Removed ServiceAccount:istio-system:istio-egressgateway-service-account.
  Removed RoleBinding:istio-system:istio-egressgateway-sds.
  Removed Role:istio-system:istio-egressgateway-sds.
  Removed PodDisruptionBudget:istio-system:istio-egressgateway.
✔ Installation complete                                                                                                                Making this installation the default for injection and validation.

Thank you for installing Istio 1.16.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/99uiMML96AmsXY5d6
```



查看出站访问效果

```bash
kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/headers
```



```
root@node1:~/istio-1.16.0# kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/headers
root@node1:~/istio-1.16.0#
```



创建指向 `httpbin.org` 的ServiceEntry

```bash
kubectl apply -f istiolabmanual/serviceentry.yaml
```



查看ServiceEntry配置文件

```yaml
nano istiolabmanual/serviceentry.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: httpbin-ext
spec:
  hosts:
  - httpbin.org
  ports:
  - number: 80
    name: http
    protocol: HTTP
  resolution: DNS
  location: MESH_EXTERNAL
```



这个配置文件是一个Istio ServiceEntry资源的定义，主要用于将外部服务（不属于服务网格的服务）纳入Istio服务网格内，使得网格内的服务能够调用这些外部服务并对流量应用Istio的流量管理和安全策略。

具体解释如下：

- `apiVersion: networking.istio.io/v1alpha3`：声明了这个资源使用Istio网络API的v1alpha3版本。
- `kind: ServiceEntry`：定义了这个资源的类型，即一个Istio ServiceEntry。
- `metadata:`：资源的元数据。
  - `name: httpbin-ext`：给这个资源分配了一个名字，叫做 "httpbin-ext"。
- `spec:`：资源的详细配置。
  - `hosts:`：一个外部服务的域名列表。
    - `httpbin.org`：指定了需要访问的外部服务的域名为"httpbin.org"。
  - `ports:`：定义外部服务需要暴露的端口信息。
    - `number: 80`：暴露的端口号为80。
    - `name: http`：给此端口分配一个名字，叫做 "http"。
    - `protocol: HTTP`：声明此端口上运行的协议是HTTP。
  - `resolution: DNS`：表示使用DNS解析来找到外部服务的IP地址。
  - `location: MESH_EXTERNAL`：指明这是一个外部服务（不在Istio服务网格内）。对此类服务，Istio将透明地执行域名解析和负载均衡。

总之，这个配置文件创建了一个名为 "httpbin-ext" 的Istio ServiceEntry，允许服务网格内的服务调用外部域名 "httpbin.org" 上的HTTP服务，访问端口为80。



查看ServiceEntry

```bash
kubectl get se
```

```bash
kubectl describe se httpbin-ext
```



```bash
root@node1:~/istio-1.16.0# kubectl get se
NAME          HOSTS             LOCATION        RESOLUTION   AGE
httpbin-ext   ["httpbin.org"]   MESH_EXTERNAL   DNS          75s
root@node1:~/istio-1.16.0# kubectl describe se httpbin-ext
Name:         httpbin-ext
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  networking.istio.io/v1beta1
Kind:         ServiceEntry
Metadata:
  Creation Timestamp:  2022-12-01T07:34:56Z
  Generation:          1
  Managed Fields:
    API Version:  networking.istio.io/v1alpha3
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:hosts:
        f:location:
        f:ports:
        f:resolution:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-12-01T07:34:56Z
  Resource Version:  10086
  UID:               df96eb55-35a1-46c0-9d1e-897d78dca7c4
Spec:
  Hosts:
    httpbin.org
  Location:  MESH_EXTERNAL
  Ports:
    Name:      http
    Number:    80
    Protocol:  HTTP
  Resolution:  DNS
Events:        <none>
```



稍等数秒钟之后，再次查看出站访问效果

```bash
kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
```



```bash
root@node1:~/istio-1.16.0# kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
{
  "origin": "20.205.45.50"
}
```



清理

```bash
kubectl delete -f samples/sleep/sleep.yaml
kubectl delete -f istiolabmanual/serviceentry.yaml
istioctl install --set profile=demo -y
```



## 5.Ingress

创建httpbin服务

```bash
kubectl apply -f samples/httpbin/httpbin.yaml
```



查看pod 

```bash
kubectl get pods
```



```bash
root@node1:~/istio-1.16.0# kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
details-v1-698b5d8c98-6qksf      2/2     Running   0          42m
httpbin-85d76b4bb6-brrkx         2/2     Running   0          84s
productpage-v1-bf4b489d8-b6k9m   2/2     Running   0          42m
ratings-v1-5967f59c58-wstvp      2/2     Running   0          42m
reviews-v1-9c6bb6658-zwhcl       2/2     Running   0          42m
reviews-v2-8454bb78d8-fbj4k      2/2     Running   0          42m
reviews-v3-6dc9897554-n6947      2/2     Running   0          42m
```



查看ingressgateway

```bash
kubectl get svc istio-ingressgateway -n istio-system
```



```bash
root@node1:~/istio-1.16.0# kubectl get svc istio-ingressgateway -n istio-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                                                                      AGE
istio-ingressgateway   LoadBalancer   10.105.85.107   <pending>     15021:31215/TCP,80:30193/TCP,443:32696/TCP,31400:32528/TCP,15443:32399/TCP   48m
```



设置ingress主机和端口变量

```bash
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].nodePort}')
```



这组命令行的主要目的是提取Istio Ingress Gateway的相关信息，并将它们存储为环境变量，以便以后使用。下面是对这些命令的逐行解析：

1. `export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')`

   这个命令从Istio系统名称空间（namespace）获取与Istio Ingress Gateway相关的pod（通过标签"istio=ingressgateway"）。然后，使用JSONPath表达式提取主机IP地址（hostIP）并将其值存储在名为`INGRESS_HOST`的环境变量中。

2. `export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')`

   这个命令在Istio系统名称空间中查找名为`istio-ingressgateway`的服务。接着，通过JSONPath表达式提取名为"http2"的端口对应的节点端口（nodePort）。将获取的端口号存储在名为`INGRESS_PORT`的环境变量中。

3. `export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')`

   与前一个命令类似，这个命令在Istio系统名称空间中查找名为`istio-ingressgateway`的服务。不过，这次我们提取名为"https"的端口对应的节点端口（nodePort）。将获取的端口号存储在名为`SECURE_INGRESS_PORT`的环境变量中。

4. `export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].nodePort}')`

   类似于第2和第3个命令，这个命令提取名为"tcp"的端口对应的节点端口（nodePort）并将端口号存储在名为`TCP_INGRESS_PORT`的环境变量中。

这组命令帮助我们收集了Istio Ingress Gateway的主机IP、HTTP/2端口、HTTPS端口以及TCP端口，以便在以后的操作中使用。



创建ingress gateway，定义接入点 

```bash
kubectl apply -f  istiolabmanual/ingressgateway.yaml 
```



查看ingress gateway配置文件

```bash
nano istiolabmanual/ingressgateway.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: httpbin-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "httpbin.example.com"
```



这是一个Istio Gateway配置文件，用于定义一个初始接入点，以使外部用户能够访问到在Istio Service Mesh内部运行的服务。以下是详细解释：

1. `apiVersion: networking.istio.io/v1alpha3`：配置文件的API版本，这里使用的是Istio网络API的v1alpha3版本。
2. `kind: Gateway`：表明这是一个Istio Gateway资源类型。
3. `metadata`：资源的元数据。
    - `name: httpbin-gateway`：Gateway资源的名称，这里叫做`httpbin-gateway`。
4. `spec`：配置资源的详细规格。
    - `selector`：用于选择一个或多个与此Gateway关联的Istio Ingress Gateway负载均衡器。
        - `istio: ingressgateway`：选择所有标签为`istio=ingressgateway`的Ingress Gateway。
    - `servers`：一个服务器列表，每个服务器对应一个端口和协议，以及允许连接的主机名。
        - `port`： 定义服务器监听的端口和协议。
            - `number: 80`：此服务器监听80端口。
            - `name: http`：端口名称定义为http。
            - `protocol: HTTP`：指定此端口使用HTTP协议。
        - `hosts`：允许访问这个Gateway的外部主机名列表。
            - `"httpbin.example.com"`：外部访问访问此服务需要通过`httpbin.example.com`的域名。

总结：这个配置文件定义了一个Gateway（名为httpbin-gateway），该Gateway允许外部请求通过主机名`httpbin.example.com`访问到Istio Service Mesh内部的服务，监听80端口并使用HTTP协议。



创建virtual service 定义路由规则 

```bash
kubectl apply -f istiolabmanual/ingressvs.yaml 
```



查看ingress vs的配置

```bash
nano istiolabmanual/ingressvs.yaml 
```



```bash
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
  - "httpbin.example.com"
  gateways:
  - httpbin-gateway
  http:
  - match:
    - uri:
        prefix: /status
    - uri:
        prefix: /delay
    route:
    - destination:
        port:
          number: 8000
        host: httpbin
```



这是一个Istio VirtualService配置文件。其主要作用是来定义流量路由规则，将访问特定主机名（本例中为httpbin.example.com）的流量导向指定的Istio服务。以下是配置文件各个部分的解析：

1. `apiVersion`：这个配置文件遵循的API版本，为networking.istio.io/v1alpha3。
2. `kind`：配置类型为VirtualService。
3. `metadata`：元数据部分。
   - `name`：VirtualService的名称，为httpbin。
4. `spec`：以下是具体的VirtualService规格配置。
   1. `hosts`：流量路由规则适用的匹配域名列表。本例中，匹配域名为httpbin.example.com。
   2. `gateways`：定义此VirtualService通过哪些Gateway发布。本例中，使用httpbin-gateway。
   3. `http`：定义HTTP路由规则。
       - `match`：用于匹配特定条件的流量。
           - `uri`：根据请求URI进行匹配。
               - 第一个匹配规则：匹配以/status开头的URL路径。
               - 第二个匹配规则：匹配以/delay开头的URL路径。
       - `route`：指定匹配规则后要执行的路由操作。
           - `destination`：设定流量路由目标。
               - `port`：目标服务的端口，本例中为8000。
               - `host`：目标服务的主机名，本例中为httpbin。

综上所述，这个配置文件定义了一个VirtualService，名为httpbin，用于将主机名为httpbin.example.com的流量，通过httpbin-gateway网关，根据相应的URL匹配将流量路由至目标服务httpbin的8000端口。



查看Virtual Service信息，重点关注服务 网关和主机的绑定关系

```bash
kubectl get vs
```



```bash
root@node1:~/istio-1.16.0# kubectl get vs
NAME       GATEWAYS               HOSTS                     AGE
bookinfo   ["bookinfo-gateway"]   ["*"]                     43m
httpbin    ["httpbin-gateway"]    ["httpbin.example.com"]   80s
```





访问已发布的httpin 接口

```bash
curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/status/200

curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/delay/2
```



```bash
root@node1:~/istio-1.16.0# curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/status/200
HTTP/1.1 200 OK
server: istio-envoy
date: Thu, 01 Dec 2022 07:45:37 GMT
content-type: text/html; charset=utf-8
access-control-allow-origin: *
access-control-allow-credentials: true
content-length: 0
x-envoy-upstream-service-time: 28

root@node1:~/istio-1.16.0# curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/delay/2
HTTP/1.1 200 OK
server: istio-envoy
date: Thu, 01 Dec 2022 07:45:50 GMT
content-type: application/json
content-length: 738
access-control-allow-origin: *
access-control-allow-credentials: true
x-envoy-upstream-service-time: 2006
```



访问未经定义的目标

```bash
curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/headers
```



```bash
root@node1:~/istio-1.16.0# curl -I -HHost:httpbin.example.com http://$INGRESS_HOST:$INGRESS_PORT/headers
HTTP/1.1 404 Not Found
date: Thu, 01 Dec 2022 07:46:32 GMT
server: istio-envoy
transfer-encoding: chunked
```



设置规则将headers服务发布到外网 

```bash
kubectl apply -f istiolabmanual/ingressgateway2.yaml 
```



查看ingress gate2的配置文件

```bash
nano istiolabmanual/ingressgateway2.yaml 
```



```bash
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: httpbin-gateway
spec:
  selector:
    istio: ingressgateway # use Istio default gateway implementation
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
  - "*"
  gateways:
  - httpbin-gateway
  http:
  - match:
    - uri:
        prefix: /headers
    route:
    - destination:
        port:
          number: 8000
        host: httpbin
```



这是一个新的Istio Gateway和VirtualService配置文件。

Gateway配置部分：
- `kind: Gateway` 定义了这是一个Gateway资源。
- `metadata.name` 定义了Gateway的名称为：`httpbin-gateway`。
- `spec.selector.istio` 使用默认的Istio ingressgateway。
- `spec.servers` 部分定义了1个服务器，其使用了80端口，协议为HTTP，名称为http。此服务器接受来自任何host的请求（hosts设定为`*`）。

VirtualService配置部分：
- `kind: VirtualService` 定义了这是一个VirtualService资源。
- `metadata.name` 定义了VirtualService的名称为：`httpbin`。
- `spec.hosts` 设置了所有的主机（`*`）。
- `spec.gateways` 将VirtualService绑定到之前创建的`httpbin-gateway`。
- `spec.http.match` 包含一个匹配规则，要求URI路径以`/headers`作为前缀。
- `spec.http.route.destination` 部分定义了匹配规则后的流量将被发送到`httpbin`服务的8000端口。

此配置文件实现的功能是：将所有访问`httpbin.example.com`域名以及URL路径前缀为`/headers`的请求，通过`httpbin-gateway`网关转发至`httpbin`服务的8000端口。



使用浏览器加 `/headers` 在群集外进行访问

![image-20221201154930064](manual.assets/image-20221201154930064.png)



查看Virtual Service信息，重点关注服务 网关和主机的绑定关系

```bash
kubectl get vs
```



```bash
root@node1:~/istio-1.16.0# kubectl get vs
NAME       GATEWAYS               HOSTS   AGE
bookinfo   ["bookinfo-gateway"]   ["*"]   47m
httpbin    ["httpbin-gateway"]    ["*"]   5m52s
```



清理资源

```bash
kubectl delete gateway httpbin-gateway
kubectl delete virtualservice httpbin
kubectl delete --ignore-not-found=true -f samples/httpbin/httpbin.yaml
```



## 6.Egress

查看istio 系统服务，确认egress gateway 组件正常运行

```bash
kubectl get svc -n istio-system | grep egress
```



```bash
root@node1:~/istio-1.16.0# kubectl get svc -n istio-system | grep egress
istio-egressgateway    ClusterIP      10.105.229.85    <none>        80/TCP,443/TCP   
```



查看istio 系统pod

```bash
kubectl get pod -n istio-system | grep egress
```



```bash
root@node1:~/istio-1.16.0# kubectl get pod -n istio-system | grep egress
istio-egressgateway-d84b5f89f-558m4    1/1     Running   0          12m
```



安装sleep应用

```bash
kubectl apply -f samples/sleep/sleep.yaml
```



设置source_pod 变量

```bash
export SOURCE_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
```



这个命令是用于在Kubernetes环境中找到并获取一个名为`sleep`的应用程序的pod名称，然后将名称保存在名为`SOURCE_POD`的环境变量中。

让我逐步解释命令组件：

1. `kubectl get pod -l app=sleep`: 这部分命令使用`kubectl`工具从Kubernetes集群中获取pod信息，并使用`-l app=sleep`参数来过滤仅具有标签`app=sleep`的pod。
2. `-o jsonpath={.items..metadata.name}`: 这部分是一个输出格式化选项，它要求`kubectl`以JSON格式返回结果，并使用jsonpath表达式`{.items..metadata.name}`提取名称字段。这将返回一个名称列表，其中包括所有匹配的pod。
3. `export SOURCE_POD=$(…)`: 此部分将前面提到的命令的输出（即找到的pod名称）保存到名为`SOURCE_POD`的环境变量中，以便于以后在其他命令中使用。

此命令在某些场景中很有用，例如，您需要确定与特定应用程序关联的pod中的一个以便向其发送请求以进行测试。



为外部httpbin服务创建service entry

```bash
kubectl  apply -f  istiolabmanual/egressse.yaml 
```



查看egress service entry配置文件

```bash
nano istiolabmanual/egressse.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: httpbin
spec:
  hosts:
  - httpbin.org
  ports:
  - number: 80
    name: http-port
    protocol: HTTP
  resolution: DNS
```



这是一个Istio的配置文件，定义了一个`ServiceEntry`资源。`ServiceEntry`资源用于为集群外提供的服务在Istio服务网格中创建条目，以便将这些外部服务视为网格中的服务。这使得Istio可以将流量路由到这些外部服务，并对其施加策略。

以下是对这个配置文件的详细解析：

1. `apiVersion: networking.istio.io/v1alpha3` ：这指定了Istio资源的版本以及使用的Istio API子集。
2. `kind: ServiceEntry` ：此字段指定这是一个`ServiceEntry`资源。
3. `metadata` ：包含此资源的元数据。
   - `name: httpbin`： 为资源分配的名称为`httpbin`。
4. `spec` ：定义`ServiceEntry`的具体配置。
   - `hosts`： 列出与此资源关联的外部服务主机。
     - `httpbin.org` ：此`ServiceEntry`将包含`httpbin.org`域。
   - `ports`： 定义与外部服务关联的端口。
     - `number: 80`：端口号为80。
     - `name: http-port`：为端口分配的名称为`http-port`。
     - `protocol: HTTP`：使用的通信协议为HTTP。
   - `resolution: DNS`：指定如何解析外部服务的地址。此字段中使用的`DNS`值表示Istio将使用DNS解析来发现外部服务的IP。

这个`ServiceEntry`配置允许Istio网格内的服务访问`httpbin.org`。这表示Istio将对`http://httpbin.org`的请求执行类似于集群内部服务的操作。因此，该配置可以帮助在Istio服务网格中实现请求的平滑路由、熔断、故障注入等功能。



检查Service Entry

```bash
kubectl get se
```



```bash
root@node1:~/istio-1.16.0# kubectl get se
NAME      HOSTS             LOCATION   RESOLUTION   AGE
httpbin   ["httpbin.org"]              DNS          104s
```



从sleep上访问外网

```bash
kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
```



```bash
root@node1:~/istio-1.16.0# kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
{
  "origin": "20.205.45.50"
}
```



检查sidecar里的proxy日志

```bash
kubectl logs $SOURCE_POD -c istio-proxy | tail
```



```bash
root@node1:~/istio-1.16.0# kubectl logs $SOURCE_POD -c istio-proxy | tail
2022-12-01T07:52:39.719206Z     info    cache   returned workload certificate from cache        ttl=23h59m59.280795108s
2022-12-01T07:52:39.719382Z     info    cache   returned workload trust anchor from cache       ttl=23h59m59.280627706s
2022-12-01T07:52:39.719501Z     info    ads     SDS: PUSH request for node:sleep-75bbc86479-8dv7p.default resources:1 size:4.0kB resource:default
2022-12-01T07:52:39.719557Z     info    ads     SDS: PUSH request for node:sleep-75bbc86479-8dv7p.default resources:1 size:1.1kB resource:ROOTCA
2022-12-01T07:52:39.719666Z     info    cache   returned workload trust anchor from cache       ttl=23h59m59.280338602s
2022-12-01T07:52:40.051272Z     info    Readiness succeeded in 5.666123916s
2022-12-01T07:52:40.051666Z     info    Envoy proxy is ready
2022-12-01T07:52:44.407739Z     warn    HTTP request failed: Get "http://169.254.169.254/metadata/instance?api-version=2019-08-15": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
2022-12-01T07:52:44.407773Z     warn    Could not unmarshal response: unexpected end of JSON input:
[2022-12-01T07:55:06.694Z] "GET /ip HTTP/1.1" 200 - via_upstream - "-" 0 31 608 607 "-" "curl/7.81.0-DEV" "e839b6e0-060c-9c78-9f97-26dda40d94d0" "httpbin.org" "54.166.148.227:80" outbound|80||httpbin.org 10.244.104.16:55424 3.215.37.86:80 10.244.104.16:43594 - default
```

注意观察，此处的`upstream_cluster："outbound|80||httpbin.org"`



查看Virtual Service 和 Destination Rule信息

```bash
kubectl get vs

kubectl get dr
```



```bash
root@node1:~/istio-1.16.0# kubectl get vs
NAME       GATEWAYS               HOSTS   AGE
bookinfo   ["bookinfo-gateway"]   ["*"]   55m
root@node1:~/istio-1.16.0#
root@node1:~/istio-1.16.0# kubectl get dr
NAME          HOST          AGE
details       details       54m
productpage   productpage   54m
ratings       ratings       54m
reviews       reviews       54m
```



创建egress gateway

```bash
kubectl  apply -f  istiolabmanual/egressgw.yaml 
```



查看egress gateway的配置文件

```bash
nano istiolabmanual/egressgw.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: istio-egressgateway
spec:
  selector:
    istio: egressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - httpbin.org
```



这是一个Istio Gateway配置文件。Gateway是一个用于加载入网格或从网格中注入流量的特殊Istio资源。在本例中，这是一个关于Istio egress网关的配置。Istio的egress网关允许你对出站流量进行安全、受控和观察能力的管理。

让我们详细解析一下这个配置文件：

- `apiVersion`: 使用的Istio API版本是`networking.istio.io/v1alpha3`。
- `kind`: 此资源类型是`Gateway`。
- `metadata`: 定义了资源的元数据。
  - `name`: 资源的名称是`istio-egressgateway`。
- `spec`: 描述了Gateway资源的配置：
  - `selector`: 定义了哪些部署工作负载应与此Gateway关联。
    - `istio: egressgateway`: 已选择Istio egressgateway工作负载。
  - `servers`: 定义了与此Gateway关联的端口、协议和允许的主机。
    - `port`: 定义了端口，协议和名称。
      - `number: 80`： 端口号为80。
      - `name: http`： 端口名称为http。
      - `protocol: HTTP`： 协议是HTTP。
    - `hosts`: 列出哪些主机可以使用这个Gateway。
      - `httpbin.org`: 允许从这个网关访问`httpbin.org`。

总结一下，这个Istio Gateway配置允许从网格的工作负载访问外部服务`httpbin.org`，通过Istio egress网关并使用HTTP协议在端口80上发起请求。



查看gateway

```bash
kubectl get gw
```



```bash
root@node1:~/istio-1.16.0# kubectl get gw
NAME                  AGE
bookinfo-gateway      56m
istio-egressgateway   83s
```



创建virtual service，将流量引导到egress gateway

```bash
kubectl  apply -f  istiolabmanual/egressvs.yaml 
```



查看egress virtual service 配置文件

```
nano istiolabmanual/egressvs.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: vs-for-egressgateway
spec:
  hosts:
  - httpbin.org
  gateways:
  - istio-egressgateway
  - mesh
  http:
  - match:
    - gateways:
      - mesh
      port: 80
    route:
    - destination:
        host: istio-egressgateway.istio-system.svc.cluster.local
        subset: httpbin
        port:
          number: 80
      weight: 100
  - match:
    - gateways:
      - istio-egressgateway
      port: 80
    route:
    - destination:
        host: httpbin.org
        port:
          number: 80
      weight: 100
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: dr-for-egressgateway
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
  - name: httpbin
```



这个配置文件的目的是通过Istio Egress网关来控制和监控Kubernetes集群内部服务对外部服务（在这个例子中是`httpbin.org`）的访问。

通过Istio的Egress网关，集群中的服务能够:

1. 通过Istio管理和安全地访问外部服务：所有出站流量都会被引导到Istio Egress网关，从而实现了统一的流量控制和安全策略。
2. 对访问外部服务的流量进行监控和跟踪：因为流量都经过Egress网关，所以Istio可以采集外部服务调用的指标数据，如延迟、错误率等。
3. 灵活地通过配置应用路由规则：例如，在处理过程中对请求或响应进行修改，或者将流量引导到一个备用的外部服务上。
4. 实施细粒度的访问控制策略：通过定义DestinationRule和VirtualService规则，可以有效地实施安全策略，例如为不同的服务设置不同的超时、重试、断路器策略等。

具体到这个配置文件，有两个VirtualService和一个DestinationRule：

1. 第一个VirtualService将集群内部服务发往`httpbin.org`的请求定向到Istio Egress网关。
2. 第二个VirtualService将在Istio Egress网关收到的请求继续定向到`httpbin.org`。
3. DestinationRule定义了访问Istio Egress网关的子集策略，包括负载平衡和连接池设置。这些设置可以为向外部服务发起的请求提供更好的性能和稳定性。



这个配置文件定义了一个Istio VirtualService和一个DestinationRule，在Kubernetes集群中使用Istio egress网关访问外部服务`httpbin.org`。

**VirtualService解析**

1. `apiVersion`: 表示使用了Istio配置的版本，这里是`networking.istio.io/v1alpha3`。
2. `kind`: 表示配置文件的类型，这里是VirtualService。
3. `metadata`: 配置的元数据，其中定义了VirtualService的名称，这里是`vs-for-egressgateway`。
4. `spec.hosts`: 定义了使用这个VirtualService的hosts，这里是`httpbin.org`。
5. `spec.gateways`: 定义了应用此VirtualService的网关，这里包括`istio-egressgateway`（表示Istio egress网关）和`mesh`（表示Istio服务网格）。
6. `spec.http`: 定义了HTTP路由规则。

`spec.http`中有两个路由规则：
* 第一个规则：
  * 当入口网关是mesh，端口是80时，匹配该规则。
  * 将请求路由到`istio-egressgateway.istio-system.svc.cluster.local`的httpbin子集，端口为80。
  * 路由权重为100
* 第二个规则：
  * 当入口网关是istio-egressgateway，端口是80时，匹配该规则。
  * 将请求路由到`httpbin.org`，端口为80。
  * 路由权重为100

首先, 请求从`mesh`中的服务提交至`istio-egressgateway`网关，然后，从网关转发至`httpbin.org`。

**DestinationRule解析**

1. `apiVersion`: 表示使用了Istio配置的版本，这里是`networking.istio.io/v1alpha3`。
2. `kind`: 表示配置文件的类型，这里是DestinationRule。
3. `metadata`: 配置的元数据，其中定义了DestinationRule的名称，这里是`dr-for-egressgateway`。
4. `spec.host`: 定义了DestinationRule应用的主机，这里是`istio-egressgateway.istio-system.svc.cluster.local`（表示Istio egress网关的服务地址）。
5. `spec.subsets`: 定义了主机的子集，这里有一个名为`httpbin`的子集。

这个DestinationRule用于设置访问Istio egress网关时使用的子集策略，可以用于设置连接池、负载均衡等。



查看Virtual Service 和Destination Rule信息

```bash
kubectl get vs
kubectl get dr
```



```bash
root@node1:~/istio-1.16.0# kubectl get vs
NAME                   GATEWAYS                         HOSTS             AGE
bookinfo               ["bookinfo-gateway"]             ["*"]             59m
vs-for-egressgateway   ["istio-egressgateway","mesh"]   ["httpbin.org"]   89s
root@node1:~/istio-1.16.0# kubectl get dr
NAME                   HOST                                                 AGE
details                details                                              59m
dr-for-egressgateway   istio-egressgateway.istio-system.svc.cluster.local   93s
productpage            productpage                                          59m
ratings                ratings                                              59m
reviews                reviews                                              59m
```



从sleep上访问外网

```bash
kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
```



```bash
root@node1:~/istio-1.16.0# kubectl exec -it $SOURCE_POD -c sleep -- curl http://httpbin.org/ip
{
  "origin": "10.244.104.16, 20.205.45.50"
}
```

 	注意：此处的ip地址发生了变化



检查sidecar里的proxy日志，观察新的条目

```bash
kubectl logs $SOURCE_POD -c istio-proxy | tail
```



```bash
root@node1:~/istio-1.16.0# kubectl logs $SOURCE_POD -c istio-proxy | tail
2022-12-01T07:52:39.719382Z     info    cache   returned workload trust anchor from cache       ttl=23h59m59.280627706s
2022-12-01T07:52:39.719501Z     info    ads     SDS: PUSH request for node:sleep-75bbc86479-8dv7p.default resources:1 size:4.0kB resource:default
2022-12-01T07:52:39.719557Z     info    ads     SDS: PUSH request for node:sleep-75bbc86479-8dv7p.default resources:1 size:1.1kB resource:ROOTCA
2022-12-01T07:52:39.719666Z     info    cache   returned workload trust anchor from cache       ttl=23h59m59.280338602s
2022-12-01T07:52:40.051272Z     info    Readiness succeeded in 5.666123916s
2022-12-01T07:52:40.051666Z     info    Envoy proxy is ready
2022-12-01T07:52:44.407739Z     warn    HTTP request failed: Get "http://169.254.169.254/metadata/instance?api-version=2019-08-15": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
2022-12-01T07:52:44.407773Z     warn    Could not unmarshal response: unexpected end of JSON input:
[2022-12-01T07:55:06.694Z] "GET /ip HTTP/1.1" 200 - via_upstream - "-" 0 31 608 607 "-" "curl/7.81.0-DEV" "e839b6e0-060c-9c78-9f97-26dda40d94d0" "httpbin.org" "54.166.148.227:80" outbound|80||httpbin.org 10.244.104.16:55424 3.215.37.86:80 10.244.104.16:43594 - default
[2022-12-01T08:01:34.332Z] "GET /ip HTTP/1.1" 200 - via_upstream - "-" 0 46 614 614 "-" "curl/7.81.0-DEV" "81f48ace-057e-978a-85b4-1bcecdb48e27" "httpbin.org" "10.244.135.13:8080" outbound|80|httpbin|istio-egressgateway.istio-system.svc.cluster.local 10.244.104.16:47570 54.166.148.227:80 10.244.104.16:57680 - -

```

注意观察，启用了`egress gateway`之后此处的`upstream_cluster："outbound|80|httpbin|istio-egressgateway.istio-system.svc.cluster.local"`



这个日志文件来源于名为`istio-proxy`的容器，它是Istio Sidecar代理。关于日志文件里的信息，主要包括以下几点：

1. 在07:52:39.719382Z和07:52:39.719666Z，日志显示工作负载信任锚点从缓存中返回。这意味着Istio已经加载了与服务相关的证书，并缓存了它们。在这里，工作负载信任锚点主要用于验证其他服务的身份。

2. 在07:52:39.719501Z和07:52:39.719557Z，日志显示Istio发送了一个SDS（Secret Discovery Service）推送请求。这里实现了安全通信所需证书的动态分发。

3. 在07:52:40.051272Z和07:52:40.051666Z，日志显示Envoy代理的准备状况检查成功，Envoy代理已准备好处理外部请求。

4. 在07:52:44.407739Z和07:52:44.407773Z，日志显示了一个向169.254.169.254发送的请求失败。这个IP通常是云服务器上的一个元数据服务器，用于从该服务器获取实例的元数据。这个警告可能与该请求的超时有关，但它不会影响我们的配置。

5. 在2022-12-01T07:55:06.694Z和2022-12-01T08:01:34.332Z，本地服务向`httpbin.org`发起了两个请求，请求路径为`/ip`。在这两个请求中，都成功返回了HTTP状态码 200，表示请求已成功处理。注意，在第一个请求中，响应直接从`httpbin.org`（IP地址: 54.166.148.227）返回，在第二个请求中，响应先经过了我们之前配置的Istio Egress网关（Istio Egress Gateway 地址：10.244.135.13:8080），然后再路由至外部服务`httpbin.org`。这说明之前的Istio配置文件已成功生效，将流量导向了正确的路径。



清理

```bash
kubectl delete -f samples/sleep/sleep.yaml
kubectl delete -f  istiolabmanual/egressse.yaml 
kubectl delete -f istiolabmanual/egressgw.yaml
kubectl delete -f istiolabmanual/egressvs.yaml 
```



# Lab 4 弹性能力

## 1.超时重试

（可选）加载default destination rules.

```bash
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
```



将review指向v2版本

```bash
kubectl apply -f istiolabmanual/reviewsv2.yaml 
```



查看配置文件

```yaml
nano istiolabmanual/reviewsv2.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
```



这个配置文件是一个Istio VirtualService资源的定义。它的作用是在Istio服务网格内部定义路由规则，以便将特定的流量引导至不同的服务实例。现在来具体了解一下这个文件的组成部分和功能：

- `apiVersion`: 指定Istio API版本，这里是`networking.istio.io/v1alpha3`。这个版本表明此资源是使用Istio v1alpha3 API创建的。
- `kind`: 这个资源的类型，这里是`VirtualService`，表示我们定义一个Istio虚拟服务。
- `metadata`: 元数据部分，提供这个资源的一些基本信息。在这个例子里，我们只定义了`name`，也就是这个VirtualService的名称，这里叫做`reviews`。
- `spec`: 资源的规格定义，主要包含的内容有：
  - `hosts`: 定义了这个VirtualService生效的目标主机或服务。这里我们指定`reviews`作为目标服务，意味着此VirtualService将应用于`reviews`服务上。
  - `http`: 定义了HTTP路由规则，可以为一个或多个规则。在这个例子中，只有一个规则：
    - `route`: 定义目标路由规则，包括一个或多个目标。在这个例子中，只有一个目标：
      - `destination`: 定义目标服务。这里，`host: reviews`，表示目标服务是`reviews`。`subset: v2`意味着将流量路由至版本为v2的`reviews`服务实例。

简而言之，这个Istio VirtualService资源将所有发送到`reviews`服务的流量路由至版本为v2的`reviews`服务实例。这对于实现流量管理，如蓝绿部署、金丝雀发布等策略非常有用。



查看bookinfo页面，看黑星星



给ratings 服务添加延迟

```bash
kubectl apply -f istiolabmanual/delay.yaml 
```



查看配置文件

```yaml
nano istiolabmanual/delay.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - fault:
      delay:
        percent: 100
        fixedDelay: 2s
    route:
    - destination:
        host: ratings
        subset: v1
```



这是一个Istio VirtualService资源配置文件。它定义了一个名为"ratings"的VirtualService，用于在服务网格内管理流量。以下是各部分的详细解释：

- `apiVersion`: 指定Istio API的版本，这里是`networking.istio.io/v1alpha3`。
- `kind`: 资源类型为`VirtualService`，表明这是一个Istio VirtualService资源。
- `metadata`: 定义了资源的元数据。
  - `name`: 名称为`ratings`的资源。
- `spec`: 描述VirtualService的具体规范。
  - `hosts`: 指定VirtualService所管理的主机或服务，这里是`ratings`。
  - `http`: 定义HTTP流量规则。
    - `fault`: 故障注入规则，用于模拟服务异常，如延迟和故障，帮助测试服务的弹性。
      - `delay`: 延迟注入，表示所有调用"ratings"服务的请求将被延迟。
        - `percent`: 注入延迟的请求百分比。这里是100%，表示所有请求都会受到延迟影响。
        - `fixedDelay`: 固定延迟时间，这里是2秒。表明流量将被延迟2秒。
    - `route`: 定义路由规则。
      - `destination`: 目标服务的信息。
        - `host`: 目标服务的主机名，这里是"ratings"。
        - `subset`: 目标服务的子集（例如版本），这里是"v1"。表示流量将路由至"ratings"服务的"v1"版本。

总结：这个配置文件表示了一个名为"ratings"的VirtualService作用于"ratings"服务，实现了故障注入，对全部请求进行延迟2秒的操作，并将流量路由至"ratings"服务的"v1"版本。这种配置方式在模拟故障以测试服务的弹性时非常有用。



查看bookinfo页面观察延迟
  会观察到页面需要大约2s才能加载完成 



给reviews 服务添加超时策略

```bash
kubectl apply -f istiolabmanual/timeout.yaml 
```



查看bookinfo页面观察快速失败
  延时设置为2s，但是我们的超时是1s，所以就可耻地失败了



给ratings 服务添加重试策略

```bash
kubectl apply -f istiolabmanual/retry.yaml 
```

 

查看超时配置文件

```bash
nano istiolabmanual/retry.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v2
    timeout: 1s
```



这是一个Istio VirtualService资源的配置文件，用于在Istio服务网格内管理服务"reviews"的流量。

详细解析如下：

1. `apiVersion`: 这个参数表示配置文件使用的API版本，本例中为"networking.istio.io/v1alpha3"。

2. `kind`: 表示资源类型，在这里类型是"VirtualService"。

3. `metadata`: 配置文件的元数据信息，包括资源名称等。

   - `name`: 这个参数表示资源的名字，这里为"reviews"。

4. `spec`: 资源配置的具体规格。

   - `hosts`: 表示与此配置相关联的主机。这里的主机是"reviews"。

   - `http`: HTTP 路由规则的配置列表。

     - `route`: 定义流量路由规则。

       - `destination`: 定义流量的目的地。

         - `host`: 流量要发送到的目的主机，这里指定为"reviews"。
         - `subset`: 流量要路由到的目标服务的子集，在本例中为"v2"版本。

     - `timeout`: 设定请求超时时间，在这个例子中，超时时间为1秒。

总结：该配置文件定义了一个Istio VirtualService资源，为在服务网格内名为"reviews"的服务，将所有流量路由至"v2"版本，并设置了1秒的请求超时时间。



从bookinfo页面上刷新一次

![image-20221201165359989](manual.assets/image-20221201165359989.png)



查看日志看是否有两次重试

```bash
kubectl logs -f ratings-v1-xxxxx -c istio-proxy
```



```bash
[2022-12-01T08:53:38.675Z] "GET /ratings/0 HTTP/1.1" 200 - via_upstream - "-" 0 48 1 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0" "7b7596ea-aa34-98f9-9d7a-e17fbd58cdf3" "ratings:9080" "10.244.135.9:9080" inbound|9080|| 127.0.0.6:35157 10.244.135.9:9080 10.244.135.10:50698 outbound_.9080_.v1_.ratings.default.svc.cluster.local default
[2022-12-01T08:53:39.688Z] "GET /ratings/0 HTTP/1.1" 200 - via_upstream - "-" 0 48 1 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0" "7b7596ea-aa34-98f9-9d7a-e17fbd58cdf3" "ratings:9080" "10.244.135.9:9080" inbound|9080|| 127.0.0.6:58597 10.244.135.9:9080 10.244.135.10:49704 outbound_.9080_.v1_.ratings.default.svc.cluster.local default
```

注意观察日志中的两个条目的`path`和`start_time`



这是两条Istio Envoy代理的访问日志，记录了服务之间HTTP请求和响应的信息。每条记录包含了许多字段，分别代表请求处理中的不同信息，我会逐一解释：

1. 日期和时间：日志发生的时间，第一条记录的时间是"2022-12-01T08:53:38.675Z"，第二条记录是"2022-12-01T08:53:39.688Z"。

2. 请求方法和URL："GET /ratings/0 HTTP/1.1"表示使用HTTP/1.1协议发送一个GET请求到"/ratings/0"这个URL。

3. 响应状态码：200表示请求成功，"-"表示没有额外的状态标志。

4. 是否通过上游服务："via_upstream"表示请求是经过上游服务处理的。

5. 后续的"-"表示该字段没有额外信息。

6. 请求的字节数：0表示请求头中没有"Content-Length"字段。

7. 响应的字节数：48表示响应的正文部分包含48个字节。

8. 请求持续时间：1表示请求处理耗时1毫秒。

9. 后续的"0"和"-"表示该字段没有额外信息。

10. 用户代理："Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:107.0) Gecko/20100101 Firefox/107.0"表示请求者使用的浏览器和操作系统信息，即Windows 10系统上的Firefox 107.0版本。

11. 请求ID："7b7596ea-aa34-98f9-9d7a-e17fbd58cdf3"表示该请求的唯一标识符。

12. 目标主机："ratings:9080"表示目标服务的主机名和端口号。

13. 被请求的主机IP和端口："10.244.135.9:9080"表示处理请求的主机IP地址和监听的端口号。

14. 其他相关信息，如路由设置、源IP地址等。

总之，这两条日志记录了发送到"ratings"服务URL "/ratings/0"的两个GET请求。请求方法是HTTP/1.1，请求成功（状态码为200），请求处理时间为1毫秒，请求者使用的浏览器是Windows 10系统上的Firefox 107.0。



清理

```bash
kubectl delete -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```





## 2.熔断

部署httpin服务

```bash
kubectl apply -f samples/httpbin/httpbin.yaml
```



在服务的DestinationRule 中添加熔断设置

```bash
kubectl apply -f istiolabmanual/circuitbreaking.yaml 
```



查看熔断配置文件

```bash
nano istiolabmanual/circuitbreaking.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 1
      http:
        http1MaxPendingRequests: 1
        maxRequestsPerConnection: 1
    outlierDetection:
      consecutive5xxErrors: 1
      interval: 1s
      baseEjectionTime: 3m
      maxEjectionPercent: 100
```



这是一个Istio的`DestinationRule`资源配置。Istio用于管理服务网格中的网络行为。这个特定的资源配置用于定义和调整名为`httpbin`的服务的流量行为。

资源的各部分如下所述：

1. `apiVersion`：指定Istio API的版本，本例中为"networking.istio.io/v1alpha3"。
2. `kind`: 指定要创建的资源类型，本例中为`DestinationRule`。
3. `metadata`：设置资源的元数据，如名称等。在这个案例中，`DestinationRule`的名称是" httpbin "。
4. `spec`：资源的具体规格和配置。
   - `host`: 指定受此规则影响的服务名称，这里是`httpbin`。
   - `trafficPolicy`: 定义与该服务相关的各种流量策略。
     - `connectionPool`: 配置用于连接服务的连接池策略。
       - `tcp`: 针对TCP连接的配置。
         - `maxConnections`: 允许的最大TCP连接数，本例中设置为1。
       - `http`: 针对HTTP连接的配置。
         - `http1MaxPendingRequests`: 允许的最大待处理的HTTP1.x请求，本例中设为1。
         - `maxRequestsPerConnection`: 每个TCP连接允许的最大HTTP请求数，这里设置为1。
     - `outlierDetection`: 配置用于检测异常服务实例的策略。
         - `consecutive5xxErrors`: 连续5xx错误数阈值，达到该数目后服务实例会被短暂逐出。这里设置为1。
         - `interval`: 异常检测的时间间隔，本例中为1秒。
         - `baseEjectionTime`: 逐出服务实例的基础时间，达到该时间后可能会恢复。这里设置为3分钟。
         - `maxEjectionPercent`: 允许最大逐出实例的百分比，这里设置为100。

总之，这个配置定义了一个针对`httpbin`服务的`DestinationRule`，限制其连接池中TCP最大连接为1，HTTP请求的最大等待数量为1，每个TCP连接上的最大HTTP请求数为1。另外，异常检测策略产生效果时，异常的服务实例迅速被逐出。注意这里的资源限制非常严格，实际生产环境中根据需要进行调整。



查看DestinationRule 

```bash
kubectl describe dr httpbin 
```



```bash
root@node1:~/istio-1.16.0# kubectl describe dr httpbin
Name:         httpbin
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  networking.istio.io/v1beta1
Kind:         DestinationRule
Metadata:
  Creation Timestamp:  2022-12-01T09:01:01Z
  Generation:          1
  Managed Fields:
    API Version:  networking.istio.io/v1alpha3
    Fields Type:  FieldsV1
    fieldsV1:
      f:metadata:
        f:annotations:
          .:
          f:kubectl.kubernetes.io/last-applied-configuration:
      f:spec:
        .:
        f:host:
        f:trafficPolicy:
          .:
          f:connectionPool:
            .:
            f:http:
              .:
              f:http1MaxPendingRequests:
              f:maxRequestsPerConnection:
            f:tcp:
              .:
              f:maxConnections:
          f:outlierDetection:
            .:
            f:baseEjectionTime:
            f:consecutive5xxErrors:
            f:interval:
            f:maxEjectionPercent:
    Manager:         kubectl-client-side-apply
    Operation:       Update
    Time:            2022-12-01T09:01:01Z
  Resource Version:  20544
  UID:               8db2a2a8-f121-4707-a8d3-9ce8e8f4f449
Spec:
  Host:  httpbin
  Traffic Policy:
    Connection Pool:
      Http:
        http1MaxPendingRequests:      1
        Max Requests Per Connection:  1
      Tcp:
        Max Connections:  1
    Outlier Detection:
      Base Ejection Time:    3m
      consecutive5xxErrors:  1
      Interval:              1s
      Max Ejection Percent:  100
Events:                      <none>
```



安装测试工具

```bash
kubectl apply -f samples/httpbin/sample-client/fortio-deploy.yaml
```



查看正常访问结果

```bash
FORTIO_POD=$(kubectl get pods -lapp=fortio -o 'jsonpath={.items[0].metadata.name}')
kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -curl http://httpbin:8000/get
```



这段命令的目的是使用`fortio`工具向`httpbin`服务发送一个HTTP GET请求，并输出结果。让我们逐行分解这段命令。

1. `FORTIO_POD=$(kubectl get pods -lapp=fortio -o 'jsonpath={.items[0].metadata.name}')`

   这一行将`FORTIO_POD`变量设置为运行`fortio`应用的Kubernetes pod的名称。该命令通过`-lapp=fortio`标签选择器来查找匹配的pods，然后使用`-o 'jsonpath={.items[0].metadata.name}'`选项提取第一个匹配的pod的名称。

2. `kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -curl http://httpbin:8000/get`

   这一行使用`kubectl exec`命令在运行`fortio`应用的pod中执行一个命令。`-it`选项表示交互式地运行该命令。`-c fortio`选项表示在`fortio`容器中执行命令。

   命令执行的实际内容是：`/usr/bin/fortio load -curl http://httpbin:8000/get`。`fortio`工具位于`/usr/bin/fortio`路径，我们使用`load`子命令向指定的URL发送HTTP请求。`-curl`选项表示只发送一个请求（而不是持续发送请求以进行性能测试）并以curl-like的格式显示输出。`http://httpbin:8000/get`是`httpbin`服务的URL和端口，我们要向其发送GET请求。

总结起来，这段命令首先找到运行`fortio`应用的Kubernetes pod，然后在该pod的`fortio`容器中使用`fortio`工具向`httpbin`服务发送一个HTTP GET请求，并输出结果。



```bash
root@node1:~/istio-1.16.0# FORTIO_POD=$(kubectl get pods -lapp=fortio -o 'jsonpath={.items[0].metadata.name}')
root@node1:~/istio-1.16.0# kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -curl http://httpbin:8000/get
HTTP/1.1 200 OK
server: envoy
date: Thu, 01 Dec 2022 09:03:34 GMT
content-type: application/json
content-length: 594
access-control-allow-origin: *
access-control-allow-credentials: true
x-envoy-upstream-service-time: 27

{
  "args": {},
  "headers": {
    "Host": "httpbin:8000",
    "User-Agent": "fortio.org/fortio-1.17.1",
    "X-B3-Parentspanid": "9d922efa9abce5eb",
    "X-B3-Sampled": "1",
    "X-B3-Spanid": "7ac5bf604849d79b",
    "X-B3-Traceid": "8295319ca4c94cc09d922efa9abce5eb",
    "X-Envoy-Attempt-Count": "1",
    "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/httpbin;Hash=53b68132bb6f26d82a22d779c2506fb18000d56a56f308d67e6756570004b994;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/default"
  },
  "origin": "127.0.0.6",
  "url": "http://httpbin:8000/get"
}
```



这个输出展示了一个来自 `fortio` 客户端向 `httpbin` 服务发送 HTTP GET 请求的结果。以下是输出中各部分的解释：

1. 请求：`kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -curl http://httpbin:8000/get`。这个命令在名为 "FORTIO_POD" 的 Pod 中运行 `fortio` 容器，并向 `http://httpbin:8000/get` 发送 HTTP GET 请求。

2. 响应状态行：`HTTP/1.1 200 OK`。这表示请求成功，服务器返回了 200 OK 状态码。

3. 响应头部信息：
   - server: envoy - 表明响应由 Envoy 代理服务器生成。
   - date: Thu, 01 Dec 2022 09:03:34 GMT - 响应生成的日期和时间。
   - content-type: application/json - 表明响应内容类型为 JSON。
   - content-length: 594 - 响应内容的长度为 594 字节。
   - access-control-allow-origin: * - 跨域资源共享（CORS）相关的头部，允许任何来源访问此资源。
   - access-control-allow-credentials: true - 表明响应可以在浏览器中暴露凭据。
   - x-envoy-upstream-service-time: 27 - Envoy 代理请求上游服务所消耗的时间，单位为毫秒。

4. 响应内容：一个 JSON 对象，包含以下字段：
   - args: 一个空对象，表示没有传递任何查询参数。
   - headers: 包含传递给请求的所有头部信息的对象。
   - origin: 发送请求的来源 IP 地址。
   - url: 请求的完整 URL。

这个输出表明，当 `fortio` 客户端向 `httpbin` 服务发送单个 HTTP GET 请求时，请求已成功完成并返回了预期的响应。在这个实例中，可以了解请求和响应的详细信息，以验证服务是否按预期工作或调试潜在问题。



触发熔断  2个并发，执行20次

```bash
kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -c 2 -qps 0 -n 20 -loglevel Warning http://httpbin:8000/get
```



这个命令同样使用 `kubectl` 命令操作 Kubernetes 集群，与前一个命令类似，但有一些主要区别。让我们逐个分析这个命令的各个部分：

1. `kubectl exec -it "$FORTIO_POD" -c fortio`：`exec` 命令用于在 Kubernetes pod 中执行一个命令。`-it` 标志表示交互式终端模式。`"$FORTIO_POD"` 是运行 fortio 应用的 pod 的名称。`-c fortio` 选项用来指定在 `fortio` 容器中执行命令。
   
2. `-- /usr/bin/fortio load`：`--` 标志表示后面的命令将在指定容器中执行。`/usr/bin/fortio load` 是要在 `fortio` 容器中执行的命令，用于生成负载。

3. `-c 2`：一个重要区别是这里使用 `-c 2` 选项，表示并发发送 2 个请求。前一个命令中只发送了一个请求。

4. `-qps 0`：表示每秒查询（Query Per Second）的速率被设置为 0。当 qps 为零时，Fortio 将以最大速度发送请求，而不限制请求速度。

5. `-n 20`：指定发送 20 个请求。前一个命令中只发送了一个请求。

6. `-loglevel Warning`：设置日志记录级别为 "Warning"，这意味着只会输出警告和更高级别的日志信息。

7. `http://httpbin:8000/get`：这是要发送 HTTP GET 请求的目标 URL，同样指向名为 `httpbin` 的服务，端口 8000 上的 `/get` 路径。

总结：这个命令与先前的命令相比，主要区别在于并发数、请求数量和日志记录级别。这个命令将以最大速度并发发送 2 个请求，共 20 个请求，同时日志记录级别设为 "Warning"。



```bash
...
Sockets used: 11 (for perfect keepalive, would be 2)
Jitter: false
Code 200 : 10 (50.0 %)
Code 503 : 10 (50.0 %)
Response Header Sizes : count 20 avg 115.25 +/- 115.3 min 0 max 231 sum 2305
Response Body/Total Sizes : count 20 avg 532.75 +/- 291.8 min 241 max 825 sum 10655
All done 20 calls (plus 0 warmup) 10.408 ms avg, 185.8 qps
```



这是使用 `fortio` 命令发送请求时得到的统计输出。我们可以从以下几个方面对输出进行分析：

1. **Sockets used**：用于发送请求的套接字数量为 11 个。在理想的 keepalive 状态下，只需要 2 个套接字。这里使用了更多的套接字，可能因为请求是分批进行的，或者部分连接没有被完全重用。

2. **Jitter**：抖动为 false，表示在同一时间发送请求时没有延迟差异。

3. **响应代码**：
   - Code 200：成功的响应数为 10 个，占比 50.0%。
   - Code 503：服务不可用的响应数为 10 个，占比 50.0%。

   这些结果表明，一半的请求已正常处理，另一半遇到了服务不可用的问题，可能是目标服务器接收请求的能力有限，或者负载均衡器将部分流量引向了异常状态的实例。

4. **Response Header Sizes**：
   - count: 20 次响应。
   - avg: 平均头部大小为 115.25 字节。
   - +/- 115.3: 平均头部大小的标准差。
   - min: 最小头部大小为 0 字节。
   - max: 最大头部大小为 231 字节。
   - sum: 总计头部大小为 2305 字节。

   这部分统计信息显示了所收到的响应头部大小的分布。

5. **Response Body/Total Sizes**：
   - count: 20 次响应。
   - avg: 平均响应正文大小为 532.75 字节，包含响应头部和正文的总大小。
   - +/- 291.8: 平均大小的标准差。
   - min: 最小正文大小为 241 字节。
   - max: 最大正文大小为 825 字节。
   - sum: 总计正文大小为 10655 字节。

   这部分统计信息显示了收到的响应正文以及包含响应头部和正文的总大小的分布。

6. **All done**：总共发送了 20 个请求（plus 0 warmup，没有进行预热请求）。
   - 平均每个请求耗时为 10.408 毫秒。
   - 请求发送速率为 185.8 qps（每秒请求数）。

这些统计数据帮助我们了解请求在目标服务器上的执行情况，以及系统的延迟和吞吐量。根据这些信息，我们可以深入探究服务的性能表现，优化请求配置并解决潜在问题。



触发熔断 again 3个并发，执行30次

```bash
kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -c 3 -qps 0 -n 30 -loglevel Warning http://httpbin:8000/get
```



这个命令与前两个命令类似，但并发数和请求数量有所不同。让我们分析这个命令：

1. `kubectl exec -it "$FORTIO_POD" -c fortio`：与前两个命令相同，`exec`  命令用于在 Kubernetes pod 中执行一个命令。使用交互式终端模式 (`-it`)，并在名为 `"$FORTIO_POD"` 的 pod 的 `fortio` 容器中执行命令。

2. `-- /usr/bin/fortio load`：与前两个命令相同，`--` 标志表示后面的命令将在指定容器中执行。`/usr/bin/fortio load` 是要在 `fortio` 容器中执行的命令，用于生成负载。

3. `-c 3`：这次使用 `-c 3` 选项，表示并发发送 3 个请求。与前两个命令相比，这将增加并发请求的数量。

4. `-qps 0`：与前两个命令相同，表示每秒查询（Query Per Second）的速率被设置为 0。当 qps 为零时，Fortio 将以最大速度发送请求，而不限制请求速度。

5. `-n 30`：指定发送 30 个请求。这是另一个主要变化，因为在这个命令中总请求数量增加到了 30 个。

6. `-loglevel Warning`：与前一个命令相同，设置日志记录级别为 "Warning"，这意味着只会输出警告和更高级别的日志信息。

7. `http://httpbin:8000/get`：与前两个命令相同，这是要发送 HTTP GET 请求的目标 URL，仍旧指向名为 `httpbin` 的服务，端口 8000 上的 `/get` 路径。

总结：这个命令的核心区别在于并发数从 2 增加到了 3，同时请求数量从 20 增加到了 30。这意味着这个命令将以最大速度并发发送 3 个请求，共计 30 个请求。日志记录级别依然设为 "Warning"。



```bash
...
Sockets used: 23 (for perfect keepalive, would be 3)
Jitter: false
Code 200 : 8 (26.7 %)
Code 503 : 22 (73.3 %)
Response Header Sizes : count 30 avg 61.5 +/- 102 min 0 max 231 sum 1845
Response Body/Total Sizes : count 30 avg 396.63333 +/- 258.1 min 241 max 825 sum 11899
All done 30 calls (plus 0 warmup) 5.330 ms avg, 452.0 qps
```



查看熔断指标

```bash
kubectl exec "$FORTIO_POD" -c istio-proxy -- pilot-agent request GET stats | grep httpbin | grep pending
```



```bash
root@node1:~/istio-1.16.0# kubectl exec "$FORTIO_POD" -c istio-proxy -- pilot-agent request GET stats | grep httpbin | grep pending
cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.default.remaining_pending: 1
cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.default.rq_pending_open: 0
cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.high.rq_pending_open: 0
cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_active: 0
cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_failure_eject: 0
cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_overflow: 42
cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_total: 29
```

 `overflow`即是被熔断的访问次数

这个输出是通过在 Fortio Pod 的 Istio-Proxy 容器上执行 `pilot-agent request GET stats` 命令来收集有关 `httpbin` 服务的 Envoy 代理统计信息。然后使用 `grep` 命令过滤特定的统计数据，关注于 httpbin 服务的 pending 请求。

以下是每行输出的解释：

1. `cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.default.remaining_pending: 1`：默认熔断器设置下，剩余可处理的 pending 请求数量为 1。

2. `cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.default.rq_pending_open: 0`：默认熔断器设置下，尚未打开的 pending 请求数量为 0。

3. `cluster.outbound|8000||httpbin.default.svc.cluster.local.circuit_breakers.high.rq_pending_open: 0`：高优先级熔断器设置下，尚未打开的 pending 请求数量为 0。

4. `cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_active: 0`：向上游 httpbin 服务发送的当前活动的 pending 请求数量为 0。

5. `cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_failure_eject: 0`：由于连接池的失败导致的从 pending 请求中移除的请求数量为 0。

6. `cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_overflow: 42`：向上游 httpbin 服务的 pending 请求溢出计数为 42。这意味着有关此服务的 42 个请求因连接池已满而被拒绝。

7. `cluster.outbound|8000||httpbin.default.svc.cluster.local.upstream_rq_pending_total: 29`：自上游 httpbin 服务启动以来的所有 pending 请求总数为 29。

这些统计信息有助于了解与 httpbin 服务相关的请求性能，例如熔断器设置、请求排队情况以及由于连接池溢出导致的请求拒绝。



清理

```bash
kubectl delete destinationrule httpbin
kubectl delete deploy httpbin fortio-deploy
kubectl delete svc httpbin fortio
```

 

# Lab 5 调试

## 1.故障注入

启用路由策略

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```



分别使用匿名用户和jason查看bookinfo界面
  Jason同学黑星星
  普通群众无星星



注入延时故障

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```



查看延时故障配置文件

```bash
nano samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```



```yaml

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    fault:
      delay:
        percentage:
          value: 100.0
        fixedDelay: 7s
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
```



这是一个 Istio VirtualService 配置文件，用于定义“ratings”服务的流量路由规则。让我们一一解析配置文件的内容：

1. `apiVersion`: 定义了配置使用的 Istio API 版本，这里为 networking.istio.io/v1alpha3。

2. `kind`: 定义了资源类型，这里是 VirtualService。

3. `metadata` - 包含该资源的元数据：

   - `name`: 资源名称，这里为 ratings。

4. `spec` - 描述了 VirtualService 的详细配置：

   - `hosts`: 定义一个或多个主机名，这里是一个单一的主机名“ratings”，表示仅对“ratings”服务应用。
   
   - `http`: 定义一系列的 HTTP 配置：
   
     - 第一个 `match` 对象用于以下规则：
     
       - 如果 HTTP 请求头包含 `end-user` 键，并且其值为`jason`，则应用此规则。
       
       - 配置故障注入：`fault` 中，为此类请求添加延迟。`fixedDelay` 设置为7秒，`percentage` 设置为 100.0%，表示匹配的请求将会有 100% 的概率经历 7 秒延迟。
       
       - `route`: 描述匹配的请求将被发送到的目标服务。这里，目标是“ratings”服务的“v1”子集（即 v1 版本）。
       
     - 第二个规则没有指定条件，是默认行为：
     
       - `route`: 默认请求将被发送到目标服务。这里，目标是“ratings”服务的“v1”子集（即 v1 版本）。

综上所述，此配置文件执行以下操作：当 HTTP 请求头中的 `end-user` 为 `jason` 时，为请求注入 7 秒延迟，然后将其路由到“ratings”服务的 v1 子集；对于所有其他请求，则直接路由到 v1 子集，而不注入延迟。



分别使用匿名用户和jason查看bookinfo界面
 Jason 踩坑了 

![image-20221201171239118](manual.assets/image-20221201171239118.png)

 普通群众情绪稳定



注入异常中断故障

```bash
kubectl apply -f samples/bookinfo/networking/virtual-service-ratings-test-abort.yaml
```



查看注入故障配置文件

```bash
nano samples/bookinfo/networking/virtual-service-ratings-test-abort.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    fault:
      abort:
        percentage:
          value: 100.0
        httpStatus: 500
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
```



这是一个 Istio VirtualService 配置文件，用于配置 `ratings` 服务的流量路由规则。接下来，我将分析每个部分的作用和含义：

1. `apiVersion`: 指定 Istio 资源配置的版本，这里使用的是 `networking.istio.io/v1alpha3`。
2. `kind`: 指定 Istio 资源类型，这里使用的是 `VirtualService`。
3. `metadata`: 配置元数据，包含一个 `name` 属性，用于指定该 VirtualService 的名称，这里的名称设置为 `ratings`。
4. `spec`: 定义 VirtualService 的具体配置。

`spec` 部分的详细解析：

- `hosts`: 指定与 VirtualService 相关的主机名，这里仅有一个，即 `ratings`。
- `http`: 定义一组 HTTP 路由规则，包含两个路由规则。

第一个路由规则：
  - `match`: 在当前路由规则生效前，需要满足的条件。这里的条件是，请求头（`headers`）中的 `end-user` 值必须等于 `jason`。
  - `fault`: 定义故障注入规则，用于在请求流转过程中注入故障以测试系统的弹性。
    - `abort`: 一种故障注入类型，用于终止请求。
      - `percentage`: 指定出现故障的概率。这里设置为 100.0%。
      - `httpStatus`: 指定返回给客户端的 HTTP 状态码。这里设置为 500。
  - `route`: 定义将流量路由到哪个目标。这里配置了一个目标：
    - `destination`: 指定目标。
      - `host`: 目标主机名。这里设置为 `ratings`。
      - `subset`: 目标子集，分发流量的版本。这里设置为 `v1`。

第二个路由规则：
  - `route`: 与上一个路由规则相似，定义将流量路由到哪个目标。但是，注意这条规则没有 `match` 配置，也没有故障注入。这意味着如果第一个规则不匹配，流量将按照这条规则路由。这里也只定义了一个目标，其 `destination` 设置与上面相同，将流量路由到 `ratings` 服务的 `v1` 子集。

综上所述，这个 VirtualService 配置文件定义了以下流量路由规则：如果请求头中的 `end-user` 为 `jason`，则注入故障（HTTP 500 错误，100.0% 概率）并将流量路由到 `ratings` 服务的 `v1` 子集；对于其他请求，直接路由到 `ratings` 服务的 `v1` 子集，而不注入故障。



分别使用匿名用户和jason查看bookinfo界面 
  Jason 中招 

![image-20221201171514787](manual.assets/image-20221201171514787.png)

  普通群众没事



清理环境

```bash
kubectl delete -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```





## 2.流量镜像

创建`httpbin-v1` 和 `httpbin-v2`

```bash
kubectl apply -f istiolabmanual/httpbin-v1.yaml  
kubectl apply -f istiolabmanual/httpbin-v2.yaml 
```



查看v1的定义文件

```bash
nano istiolabmanual/httpbin-v1.yaml
```



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v1
  template:
    metadata:
      labels:
        app: httpbin
        version: v1
    spec:
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        command: ["gunicorn", "--access-logfile", "-", "-b", "0.0.0.0:80", "httpbin:app"]
        ports:
        - containerPort: 80
```



这是一个 Kubernetes 部署（Deployment）的配置文件，用于部署一个名为 httpbin-v1 的应用。下面是配置文件的详细解析：

- `apiVersion: apps/v1`：指定了这个配置文件使用的 Kubernetes API 版本，当前版本是 apps/v1。
- `kind: Deployment`：声明这是一个 Deployment 资源类型的配置文件。
- `metadata`：为 Deployment 添加了一些元数据。
  - `name: httpbin-v1`：设置了 Deployment 的名字为 httpbin-v1。
- `spec`：描述了 Deployment 的具体规格。
  - `replicas: 1`：设置期望的 Pod 副本数为 1。
  - `selector`：定义了如何匹配和选择 Pod，以使 Deployment 管理这些 Pod。
    - `matchLabels`：定义了匹配的标签。
      - `app: httpbin`：匹配 app 标签的值为 httpbin。
      - `version: v1`：匹配 version 标签的值为 v1。
  - `template`：描述了要创建的 Pod 的模板。
    - `metadata`：为 Pod 添加了一些元数据。
      - `labels`：定义了要附加到 Pod 的标签。
        - `app: httpbin`：设置了 app 标签的值为 httpbin。
        - `version: v1`：设置了 version 标签的值为 v1。
    - `spec`：描述了 Pod 中容器的具体规格。
      - `containers`：列出了 Pod 中的容器。
        - 第一个（也是唯一的）容器：
          - `image: docker.io/kennethreitz/httpbin`：设置了容器的镜像为 docker.io/kennethreitz/httpbin。
          - `imagePullPolicy: IfNotPresent`：设置了镜像拉取策略。如果镜像已存在于本地，则无需拉取镜像。
          - `name: httpbin`：设置了容器的名字为 httpbin。
          - `command`：定义了容器启动时要执行的命令和参数。
            - 启动 gunicorn，将访问日志输出到 stdout，监听 0.0.0.0:80，并运行 httpbin:app。
          - `ports`：设置了容器暴露的端口。
            - `containerPort: 80`：设置暴露的端口为 80。

总结来说，这个配置文件定义了一个名为 httpbin-v1 的 Deployment 资源，部署一个包含单个容器的 Pod，容器运行 kennethreitz/httpbin 镜像，使用 gunicorn 监听在 80 端口。标签选择器确保 Deployment 仅管理应用名为 httpbin，版本为 v1 的 Pod。



发布服务

```bash
kubectl apply -f istiolabmanual/httpbinsvc.yaml
```



查看服务定义文件

```bash
nano istiolabmanual/httpbinsvc.yaml
```



```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 80
  selector:
    app: httpbin
```



这是一个 Kubernetes 配置文件，定义了一个 Service 资源。以下是配置文件的各个部分的解析：

1. `apiVersion: v1`：这是使用的Kubernetes API版本，这里用的是v1。
2. `kind: Service`：该配置文件定义的资源类型是Service。Service是Kubernetes中的一种对象，它可抽象地将一组Pod（如此前配置中的httpbin-v1 Deployment管理的Pod）暴露为一个网络服务。

接下来看 `metadata` 部分：

3. `name: httpbin`：这个Service的名字是httpbin。
4. `labels:`：定义了Service资源的标签，这里只有一个标签：
   - `app: httpbin`：app标签值为httpbin。

进入 `spec` 部分，描述了Service的具体规格和配置：

5. `ports:`：定义了暴露的端口数组，这里只有一个端口：
   - `name: http`：给这个端口取个名字，叫http。
   - `port: 8000`：Service暴露的端口是8000。
   - `targetPort: 80`：映射到Pod内部容器的目标端口为80。也就是说，当网络请求到达8000端口时，Service将请求转发到后端Pod的80端口。

6. `selector:`：定义了Service的标签选择器，该选择器会匹配标签满足特定条件的Pod。这里我们有一个匹配条件：
   - `app: httpbin`：选择app标签值为httpbin的Pod。这匹配了之前提到的httpbin-v1 Deployment创建的带有相同app标签的Pod。

总的来说，这个配置文件定义了一个名为httpbin的Service，它监听8000端口并将请求转发到后端标签为'app: httpbin'的Pod的80端口。



查看服务

```bash
kubectl describe svc httpbin
```



```bash
root@node1:~/istio-1.16.0# kubectl describe svc httpbin
Name:              httpbin
Namespace:         default
Labels:            app=httpbin
Annotations:       <none>
Selector:          app=httpbin
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.106.199.214
IPs:               10.106.199.214
Port:              http  8000/TCP
TargetPort:        80/TCP
Endpoints:         10.244.104.18:80,10.244.135.16:80
Session Affinity:  None
Events:            <none>
```



启动sleep服务

```bash
kubectl apply -f samples/sleep/sleep.yaml
```



设置路由规则

```bash
kubectl apply -f istiolabmanual/httpbinvs.yaml 
```



查看路由规则文件

```bash
nano stiolabmanual/httpbinvs.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
    - httpbin
  http:
  - route:
    - destination:
        host: httpbin
        subset: v1
      weight: 100
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```



这个配置文件定义了两个Istio资源：一个VirtualService和一个DestinationRule。它们都是Istio的流量管理组件，用于控制在服务网格内的流量行为。

第一个资源是VirtualService：

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
    - httpbin
  http:
  - route:
    - destination:
        host: httpbin
        subset: v1
      weight: 100
```

- `apiVersion`为`networking.istio.io/v1alpha3`，表示这是一个Istio资源。
- `kind`为`VirtualService`，表示这是一个虚拟服务定义。
- `metadata.name`为`httpbin`，表示虚拟服务的名称或标识。
- `spec.hosts`定义了虚拟服务应用的目标主机，这里的目标主机是`httpbin`。
- `spec.http`定义了HTTP流量的路由规则。
  - `route`定义了一个路由规则，将流量路由到不同的目标服务。
    - `destination`定义了目标主机和子集。
      - `host`指定虚拟服务发送流量的主机，这里是`httpbin`。
      - `subset`指定了目标服务的子集，这里是`v1`。
    - `weight`为100，表示将100%的流量路由到`subset: v1`。

第二个资源是DestinationRule：

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: httpbin
spec:
  host: httpbin
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

- `apiVersion`为`networking.istio.io/v1alpha3`，表示这是一个Istio资源。
- `kind`为`DestinationRule`，表示这是一个目标规则定义。
- `metadata.name`为`httpbin`，表示目标规则的名称或标识。
- `spec.host`指定规则应用的目标主机，这里是`httpbin`。
- `spec.subsets`定义了目标主机的多个子集，以便在流量路由时使用。
  - 第一个子集`v1`表示带有标签`version: v1`的目标服务实例。
  - 第二个子集`v2`表示带有标签`version: v2`的目标服务实例。

总结：这个配置文件定义了一个Istio的VirtualService和DestinationRule。VirtualService负责将100%的流量路由到httpbin服务的v1子集。DestinationRule为httpbin服务定义了两个子集，分别是带有不同版本标签的服务实例（v1和v2）。这为你提供了对服务的不同版本进行流量管理的能力。



使用sleep访问服务

```bash
export SLEEP_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
kubectl exec -it $SLEEP_POD -c sleep -- sh -c 'curl  http://httpbin:8000/headers' | python3 -m json.tool
```



```bash
root@node1:~/istio-1.16.0# export SLEEP_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl exec -it $SLEEP_POD -c sleep -- sh -c 'curl  http://httpbin:8000/headers' | python3 -m json.tool
{
    "headers": {
        "Accept": "*/*",
        "Host": "httpbin:8000",
        "User-Agent": "curl/7.81.0-DEV",
        "X-B3-Parentspanid": "9d5d287f47ecd220",
        "X-B3-Sampled": "1",
        "X-B3-Spanid": "d6be1d30ff475d60",
        "X-B3-Traceid": "7599f42a01151eab9d5d287f47ecd220",
        "X-Envoy-Attempt-Count": "1",
        "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/default;Hash=0eba4506484820359556030d829573051b0f525ef9b6205fa5d88eed040e131b;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/sleep"
    }
}
```



命令行分解：

1. `export SLEEP_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})`：此命令获取`app=sleep`标签的pod的名称，并将其存储在名为SLEEP_POD的环境变量中。`-o jsonpath={.items..metadata.name}`以JSONPath格式输出pod的名称。

2. `kubectl exec -it $SLEEP_POD -c sleep -- sh -c 'curl http://httpbin:8000/headers' | python3 -m json.tool`：此命令在之前定义的SLEEP_POD环境变量中指定的pod上执行一个命令。这里我们使用curl从'httpbin'服务的8000端口获取'/headers'路径的内容，并将结果传递给python的json.tool模块，以便对输出进行格式化。

输出解释：
输出展示了'httpbin'服务对'/headers'请求的响应结果。主要包括以下HTTP头：

- Accept：接受的内容类型，这里是`*/*`，表示任何类型。
- Host：目标主机和端口，这里是`httpbin:8000`。
- User-Agent：发出请求的用户代理，这里是`curl/7.81.0-DEV`。
- X-B3-*：Zipkin分布式追踪系统用于追踪请求的标头。（如 X-B3-Parentspanid，X-B3-Sampled，X-B3-Spanid，X-B3-Traceid）
- X-Envoy-Attempt-Count：请求已经尝试了几次，默认为1。
- X-Forwarded-Client-Cert：包含有关用于双向TLS的客户端证书身份信息的标头。

从输出中可以看到，请求成功到达'httpbin'服务，并通过Istio组件进行了透明处理，因为X-B3-* 和 X-Envoy-Attempt-Count等Envoy特定的标头已经添加到了响应中。



查看v1和v2的日志

```bash
export V1_POD=$(kubectl get pod -l app=httpbin,version=v1 -o jsonpath={.items..metadata.name})
kubectl logs -f $V1_POD -c httpbin
```



```bash
root@node1:~/istio-1.16.0# export V1_POD=$(kubectl get pod -l app=httpbin,version=v1 -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl logs -f $V1_POD -c httpbin
[2022-12-01 09:16:59 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2022-12-01 09:16:59 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2022-12-01 09:16:59 +0000] [1] [INFO] Using worker: sync
[2022-12-01 09:16:59 +0000] [9] [INFO] Booting worker with pid: 9
127.0.0.6 - - [01/Dec/2022:09:29:11 +0000] "GET /headers HTTP/1.1" 200 527 "-" "curl/7.81.0-DEV"
```



首先，让我们分析这两个命令：

1. `export V1_POD=$(kubectl get pod -l app=httpbin,version=v1 -o jsonpath={.items..metadata.name})`

   这行命令使用`kubectl get pod`命令通过标签（`app=httpbin, version=v1`）获取与httpbin v1相关的pod，然后通过jsonpath表达式（`{.items..metadata.name}`）获取pod的名称（metadata.name）。得到的结果被存储在环境变量`V1_POD`中。

2. `kubectl logs -f $V1_POD -c httpbin`

   这行命令使用`kubectl logs`来获取名为`V1_POD`的pod的日志。`-f`（或`--follow`）参数表示持续监听日志输出，当有新的日志产生时实时输出，类似于`tail -f`命令。`-c httpbin`表示要获取的container是名为`httpbin`的container。

接下来，让我们分析日志输出：

日志显示了httpbin服务v1 在 pod 中启动并成功接收到请求。以下是关于输出的解释：

1. Gunicorn服务器启动信息: 
```bash
[2022-12-01 09:16:59 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2022-12-01 09:16:59 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2022-12-01 09:16:59 +0000] [1] [INFO] Using worker: sync
[2022-12-01 09:16:59 +0000] [9] [INFO] Booting worker with pid: 9
```
   这些信息显示了Gunicorn服务器（一个Python WSGI HTTP服务器）的启动过程，使用sync worker模型在端口80监听请求。

2. 接收到的请求和响应信息:
```bash
127.0.0.6 - - [01/Dec/2022:09:29:11 +0000] "GET /headers HTTP/1.1" 200 527 "-" "curl/7.81.0-DEV"
```
   这行日志显示了httpbin服务接收到一个请求，请求的来源IP为127.0.0.6。请求方法是GET，路径是`/headers`，使用的HTTP协议版本是1.1。响应状态码是200（成功），响应内容大小为527字节。请求头中的User-Agent是`curl/7.81.0-DEV`。从这里我们可以看出，之前执行的curl命令已经成功地访问了httpbin服务的v1子集。



```bash
export V2_POD=$(kubectl get pod -l app=httpbin,version=v2 -o jsonpath={.items..metadata.name})
kubectl logs -f $V2_POD -c httpbin
```



```bash
root@node1:~/istio-1.16.0# export V2_POD=$(kubectl get pod -l app=httpbin,version=v2 -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl logs -f $V2_POD -c httpbin
[2022-12-01 09:17:18 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2022-12-01 09:17:18 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2022-12-01 09:17:18 +0000] [1] [INFO] Using worker: sync
[2022-12-01 09:17:18 +0000] [10] [INFO] Booting worker with pid: 10
```



设置镜像规则

```bash
kubectl apply -f istiolabmanual/mirror.yaml --validate=false
```



查看镜像规则文件

```bash
nano istiolabmanual/mirror.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
    - httpbin
  http:
  - route:
    - destination:
        host: httpbin
        subset: v1
      weight: 100
    mirror:
      host: httpbin
      subset: v2
    mirrorPercent: 100
```

这个配置文件是一个Istio VirtualService资源，用于定义HTTP流量的路由规则。让我们按部分解析它：

- `apiVersion`: 声明了资源的API版本。在这种情况下，版本为`networking.istio.io/v1alpha3`。
- `kind`: 指定了资源类型。这里是一个Istio的`VirtualService`资源。
- `metadata`: 包含元数据信息，例如资源的名称。在这个例子里，资源名为`httpbin`。

下面是VirtualService的特定规范（`spec`）:

- `hosts`: 这个部分定义了规则适用的服务。在这个例子里，规则应用于名为`httpbin`的服务。
- `http`: 包含HTTP流量的Route规则。在这个例子里，我们为httpbin服务定义了一个路由规则，具体包括：
  - `route`: 定义了流量应该转发到哪里。
    - `destination`: 指定了流量的目标。这里，将流量路由到名为`httpbin`的服务的`v1`子集。 
    - `weight`: 指定了流量百分比。在这个例子里，`weight: 100`表示全部100%的流量都会被路由到`httpbin`的`v1`子集。
  - `mirror`: 配置镜像目标，将流量的副本发送到另一个服务或子集。在这个例子里，流量的副本将被发送到`httpbin`服务的`v2`子集。
  - `mirrorPercent`: 指定要镜像的流量百分比。这里，`mirrorPercent: 100`表示100%的流量将会被复制并发送到镜像目标。

综合上面的解释，这个配置文件表示所有流量将被发送到`httpbin`服务的`v1`子集。与此同时，所有流量的副本也会被镜像到`httpbin`服务的`v2`子集。这种设置在实际应用中可以用于服务版本的A/B测试或新版本的验证。



使用sleep访问服务

```bash
export SLEEP_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
kubectl exec -it $SLEEP_POD -c sleep -- sh -c 'curl  http://httpbin:8000/headers' | python3 -m json.tool
```



```bash
root@node1:~/istio-1.16.0# export SLEEP_POD=$(kubectl get pod -l app=sleep -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl exec -it $SLEEP_POD -c sleep -- sh -c 'curl  http://httpbin:8000/headers' | python3 -m json.tool
{
    "headers": {
        "Accept": "*/*",
        "Host": "httpbin:8000",
        "User-Agent": "curl/7.81.0-DEV",
        "X-B3-Parentspanid": "f5000d3241a11cbb",
        "X-B3-Sampled": "1",
        "X-B3-Spanid": "b0a32f1cbca43d0d",
        "X-B3-Traceid": "0bf35512fb2b57abf5000d3241a11cbb",
        "X-Envoy-Attempt-Count": "1",
        "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/default/sa/default;Hash=0eba4506484820359556030d829573051b0f525ef9b6205fa5d88eed040e131b;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/sleep"
    }
}
```



查看v1和v2的日志

```bash
export V1_POD=$(kubectl get pod -l app=httpbin,version=v1 -o jsonpath={.items..metadata.name})
kubectl logs -f $V1_POD -c httpbin
```



```bash
root@node1:~/istio-1.16.0# export V1_POD=$(kubectl get pod -l app=httpbin,version=v1 -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl logs -f $V1_POD -c httpbin
[2022-12-01 09:16:59 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2022-12-01 09:16:59 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2022-12-01 09:16:59 +0000] [1] [INFO] Using worker: sync
[2022-12-01 09:16:59 +0000] [9] [INFO] Booting worker with pid: 9
127.0.0.6 - - [01/Dec/2022:09:29:11 +0000] "GET /headers HTTP/1.1" 200 527 "-" "curl/7.81.0-DEV"
127.0.0.6 - - [01/Dec/2022:09:32:03 +0000] "GET /headers HTTP/1.1" 200 527 "-" "curl/7.81.0-DEV"
```



```bash
export V2_POD=$(kubectl get pod -l app=httpbin,version=v2 -o jsonpath={.items..metadata.name})
kubectl logs -f $V2_POD -c httpbin
```



```bash
root@node1:~/istio-1.16.0# export V2_POD=$(kubectl get pod -l app=httpbin,version=v2 -o jsonpath={.items..metadata.name})
root@node1:~/istio-1.16.0# kubectl logs -f $V2_POD -c httpbin
[2022-12-01 09:17:18 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2022-12-01 09:17:18 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2022-12-01 09:17:18 +0000] [1] [INFO] Using worker: sync
[2022-12-01 09:17:18 +0000] [10] [INFO] Booting worker with pid: 10
127.0.0.6 - - [01/Dec/2022:09:32:03 +0000] "GET /headers HTTP/1.1" 200 567 "-" "curl/7.81.0-DEV"
```



清理

```bash
kubectl delete virtualservice httpbin
kubectl delete destinationrule httpbin
kubectl delete deploy httpbin-v1 httpbin-v2 sleep
kubectl delete svc httpbin 
```





# Lab 6 验证：配置TLS安全网关

## 1.为单Host配置 TLS ingress gateway

创建根证书和为证书签名的私钥

```bash
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=example Inc./CN=example.com' -keyout example.com.key -out example.com.crt
```

这是一个使用`openssl`命令生成自签名SSL证书的示例。下面简要解释这个命令的各个部分：

1. `openssl req`：`openssl`是一个强大的安全工具，`req`是用于创建和处理证书签名请求（CSR）的命令。
2. `-x509`：用于生成自签名的X.509证书，而不是生成证书签名请求（CSR）。
3. `-sha256`：指定使用SHA-256哈希算法。
4. `-nodes`：这意味着私钥不用密码进行加密。如果省略此选项，会提示您输入密码来保护私钥。
5. `-days 365`：证书的有效期是365天。
6. `-newkey rsa:2048`：要为证书和私钥创建一个新的RSA密钥对，密钥长度为2048位。
7. `-subj '/O=example Inc./CN=example.com'`：设置证书的主题字段。其中，`O`代表组织名称，`CN`代表通用名称（即域名）。
8. `-keyout example.com.key`：将生成的私钥存储到名为`example.com.key`的文件中。
9. `-out example.com.crt`：将生成的自签名证书存储到名为`example.com.crt`的文件中。

通过运行这个命令，您将在当前目录下创建两个文件：`example.com.key`（私钥）和`example.com.crt`（自签名证书）。这些文件可以用于部署到Web服务器，以便支持HTTPS连接。但是，自签名证书在浏览器中通常会引发安全警告，因为它们没有受到可信认证机构（CA）的签署。在生产环境中，建议使用由可信任的认证机构签署的证书。



为httpbin.example.com创建证书和私钥：

```bash
openssl req -out httpbin.example.com.csr -newkey rsa:2048 -nodes -keyout httpbin.example.com.key -subj "/CN=httpbin.example.com/O=httpbin organization"
openssl x509 -req -sha256 -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 0 -in httpbin.example.com.csr -out httpbin.example.com.crt
```

这些命令用于为`httpbin.example.com`域名创建一个SSL证书，并用先前生成的自签名根证书（`example.com.key`和`example.com.crt`）对其进行签名。以下是对这两个命令的逐项解释：

第一个命令：

1. `openssl req`：`openssl`是一个强大的安全工具，`req`是用于创建和处理证书签名请求（CSR）的命令。
2. `-out httpbin.example.com.csr`：将生成的证书签名请求存储到名为`httpbin.example.com.csr`的文件中。
3. `-newkey rsa:2048`：要为证书和私钥创建一个新的RSA密钥对，密钥长度为2048位。
4. `-nodes`：这表示私钥不进行密码保护。如果省略此选项，会提示您输入密码以保护私钥。
5. `-keyout httpbin.example.com.key`：将生成的私钥存储在名为`httpbin.example.com.key`的文件中。
6. `-subj "/CN=httpbin.example.com/O=httpbin organization"`：设置证书的主题字段。其中，`CN`代表通用名称（即域名），`O`代表组织名称。

第二个命令：

1. `openssl x509`：命令用于处理x509证书。
2. `-req`：表示下一个输入文件是一个证书签名请求（CSR）。
3. `-sha256`：指定使用SHA-256哈希算法。
4. `-days 365`：证书的有效期为365天。
5. `-CA example.com.crt`：使用名为`example.com.crt`的自签名根证书对CSR进行签名。
6. `-CAkey example.com.key`：指定与根证书关联的私钥文件`example.com.key`。
7. `-set_serial 0`：为新签名的证书设置序列号。在这种情况下是0，但建议使用唯一的序列号。
8. `-in httpbin.example.com.csr`：从名为`httpbin.example.com.csr`的CSR文件读取。
9. `-out httpbin.example.com.crt`：将生成的已签名证书存储在名为`httpbin.example.com.crt`的文件中。

运行这两个命令后，您将创建以下文件：

- `httpbin.example.com.key`：私钥
- `httpbin.example.com.csr`：证书签名请求
- `httpbin.example.com.crt`：已签名证书

这些文件可部署到Web服务器以支持HTTPS连接（使用自签名根证书对证书进行签名）。然而，在生产环境中，建议使用经可信认证机构（CA）签署的证书，以避免浏览器安全警告。



启动 httpbin 用例

```bash
kubectl apply -f istiolabmanual/sdshttpbin.yaml 
```



为ingress gateway创建 secret

```bash
kubectl create -n istio-system secret tls httpbin-credential --key=httpbin.example.com.key --cert=httpbin.example.com.crt
```

这个命令行使用 `kubectl`（Kubernetes命令行工具）在特定的命名空间（`istio-system`）下创建一个新的TLS secret。它用于将您已经创建的SSL证书（httpbin.example.com.crt）和私钥（httpbin.example.com.key）导入Kubernetes集群。下面我们来详细解释这个命令的各个部分。

- `kubectl`: Kubernetes命令行工具，用于与集群进行交互。
- `create`: 创建一个新的Kubernetes资源。
- `-n istio-system`: 指定要在其中创建TLS secret的命名空间。在这里，命名空间是`istio-system`。
- `secret`：指定要创建的Kubernetes资源类型，即 secret（密钥）。
- `tls`：指定创建的是一个TLS类型的secret，用于包含SSL证书和私钥。
- `httpbin-credential`：这是您将创建的TLS secret的名称。可以根据需要为它们指定名称，但是这里我们已经给出了一个示例名称。
- `--key=httpbin.example.com.key`：指定私钥文件。这里是先前生成的httpbin.example.com.key文件。
- `--cert=httpbin.example.com.crt`：指定证书文件。这里是先前生成的httpbin.example.com.crt文件。

当您运行这个命令时，Kubernetes集群将在`istio-system`命名空间下创建一个名为`httpbin-credential`的secret，然后您可以将其用于在Kubernetes上部署带有SSL的服务。需要注意的是，在使用任何与TLS相关的Kubernetes资源之前，请确保您已通过OpenSSL或其他受信任的认证机构生成了相应的证书和私钥。



创建 Gateway ，可以打开 yaml文件重点关注servers以及TLS部分的定义

```bash
kubectl apply -f istiolabmanual/sdsgateway.yaml
```



查看gateway配置文件

```bash
nano istiolabmanual/sdsgateway.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: mygateway
spec:
  selector:
    istio: ingressgateway # use istio default ingress gateway
  servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: httpbin-credential # must be the same as secret
    hosts:
    - httpbin.example.com
```

这是一个Istio Gateway资源的YAML配置文件。Gateway用于配置入口流量网关，以便处理进入Istio服务网格的流量。下面我们分别解释此配置文件的各个部分：

- `apiVersion: networking.istio.io/v1alpha3`: 定义了我们正在使用的Istio API版本，这里使用的是v1alpha3版本。
- `kind: Gateway`: 表示我们要创建的Kubernetes资源类型是一个Istio Gateway。
- `metadata:`：包含有关Kubernetes资源的元数据。
  - `name: mygateway`: 指定Gateway资源的名称，这里使用了名称`mygateway`。
- `spec:`：包含Gateway资源的规范定义。
  - `selector:`：用于指定此Gateway应用于哪些工作负载。在这里，我们选择Istio的默认Ingress网关。
    - `istio: ingressgateway`: 使用Istio默认的ingressgateway。
  - `servers:`: 在Gateway中定义了一个或多个服务器。
    - `- port:`: 配置服务器监听的端口。
      - `number: 443`: 监听的端口号，这里实例使用了HTTPS的默认端口443。
      - `name: https`: 给端口一个名称。在这里，我们使用`https`作为名称。
      - `protocol: HTTPS`: 指定服务器使用的协议。这里设置为HTTPS。
    - `tls:`: 配置TLS，定义HTTPS的TLS设置。
      - `mode: SIMPLE`: 设置TLS模式为SIMPLE，即一个普通的单向TLS，客户端与服务器之间建立加密通信。
      - `credentialName: httpbin-credential`: 指定要使用的Kubernetes secret的名称，其中包含证书和私钥。这里的名称`httpbin-credential`必须与先前创建的secret名称相同。
    - `hosts:`：指定监听哪些主机名。可以使用特定的域名或通配符匹配多个域名。
      - `- httpbin.example.com`: 在这个示例中，Gateway配置为使用`httpbin.example.com`这个域名。

此配置文件创建了一个名为`mygateway`的Istio Gateway，该Gateway将处理到`httpbin.example.com`的HTTPS流量。流量通过Istio默认的ingressgateway，在端口443上监听，启用具有普通模式的TLS，并使用`httpbin-credential`secret下载证书和私钥。



配置网关的ingress traffic routes 定义相应的虚拟服务

```bash
kubectl apply -f istiolabmanual/sdsvirtualserver.yaml 
```



查看virtual server配置文件

```bash
nano istiolabmanual/sdsvirtualserver.yaml 
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: httpbin
spec:
  hosts:
  - "httpbin.example.com"
  gateways:
  - mygateway
  http:
  - match:
    - uri:
        prefix: /status
    - uri:
        prefix: /delay
    route:
    - destination:
        port:
          number: 8000
        host: httpbin
```

此配置定义了一个VirtualService，用于将httpbin.example.com的流量路由至特定路径（/status和/delay）的httpbin服务实例上。这个资源必须与一个Gateway资源（在本例中是 "mygateway"）协同工作，才能正确地处理入口流量。



设置ingress主机和端口变量

```bash
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].nodePort}')
```

这些命令行是用于从Kubernetes集群中获取与Istio Ingress Gateway相关的信息。下面是每个命令的解析：

1. `export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')`

这个命令获取Istio Ingress Gateway对应的Pod（使用标签istio=ingressgateway和命名空间istio-system进行过滤），然后通过JSONPath表达式提取Pod的Host IP地址。这个值被存储在环境变量INGRESS_HOST中。

2. `export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')`

这个命令从命名空间istio-system中获取名为istio-ingressgateway的Kubernetes服务，并查找名称为"http2"的端口。然后使用JSONPath提取NodePort值，并将其存储在环境变量INGRESS_PORT中。

3. `export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')`

这个命令类似于上一个命令，但是它查找名称为"https"的端口，并将NodePort值存储在环境变量SECURE_INGRESS_PORT中。

4. `export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].nodePort}')`

这个命令类似于上两个命令，但是它查找名称为"tcp"的端口，并将NodePort值存储在环境变量TCP_INGRESS_PORT中。

总之，这些命令提取了与Istio Ingress Gateway相关的信息，包括Host IP地址，HTTP2端口，HTTPS端口和TCP端口。这些值将存储在相应的环境变量中，以便在后续的操作中使用。



发送HTTPS请求访问httpbin服务

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    此处应该有茶壶

```bash
root@node1:~/istio-1.16.0# curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
> --cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
* Added httpbin.example.com:32696:192.168.1.233 to DNS cache
* Hostname httpbin.example.com was found in DNS cache
*   Trying 192.168.1.233:32696...
* TCP_NODELAY set
* Connected to httpbin.example.com (192.168.1.233) port 32696 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: example.com.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=httpbin.example.com; O=httpbin organization
*  start date: Dec  2 01:06:59 2022 GMT
*  expire date: Dec  2 01:06:59 2023 GMT
*  common name: httpbin.example.com (matched)
*  issuer: O=example Inc.; CN=example.com
*  SSL certificate verify ok.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* Using Stream ID: 1 (easy handle 0x562ada683e30)
> GET /status/418 HTTP/2
> Host:httpbin.example.com
> user-agent: curl/7.68.0
> accept: */*
>
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* Connection state changed (MAX_CONCURRENT_STREAMS == 2147483647)!
< HTTP/2 418
< server: istio-envoy
< date: Fri, 02 Dec 2022 01:13:11 GMT
< access-control-allow-credentials: true
< x-more-info: http://tools.ietf.org/html/rfc2324
< access-control-allow-origin: *
< content-length: 135
< x-envoy-upstream-service-time: 18
<

    -=[ teapot ]=-

       _...._
     .'  _ _ `.
    | ."` ^ `". _,
    \_;`"---"`|//
      |       ;/
      \_     _/
        `"""`
* Connection #0 to host httpbin.example.com left intact
```

这个命令行是用`curl`工具向Istio Ingress Gateway发起一个使用自定义主机头（Host: httpbin.example.com）的HTTP请求。请求的目标是https协议下的/status/418端点。由于Istio使用TLS，所以需要提供一个根证书文件（example.com.crt）来验证服务器证书。

在命令行中：
1. `-v`参数表示输出详细的调试信息。
2. `-HHost:httpbin.example.com`设置了一个自定义的主机头（Host header）。
3. `--resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST"`参数是告诉curl使用指定的IP地址和端口号，而不是通过DNS解析httpbin.example.com。
4. `--cacert`参数提供了根证书文件（example.com.crt），以正确验证服务器证书。

输出中，首先显示了请求相关的信息，例如DNS解析、连接建立、TLS握手、协议协商等。然后在输出中看到发送了一个GET请求到/status/418端点，使用HTTP/2协议并设置了Host头为`httpbin.example.com`。

服务器响应的状态码为418，表示"I'm a teapot"。这个状态码是在RFC 2324（超文本咖啡壶控制协议，HTCPCP）中定义的，用于在服务器不是一个咖啡壶的情况下返回。响应中还包含了一个ASCII艺术作品，表现了一个茶壶的形象。

最后，`* Connection #0 to host httpbin.example.com left intact`表明与服务器的连接完好无损。

回滚日志查看 TSL 握手过程，在这个TLS握手的例子里，客户端（curl命令）访问了一个通过Istio Envoy服务的HTTP服务器。这是一个典型的TLS 1.3握手过程。根据提供的curl输出，我们可以用Mermaid表示这个完整的过程。以下是用Mermaid编写的时序图代码及对应步骤的解释：

时序图代码：
```mermaid
sequenceDiagram
    Participant Client
    Participant Server
    Client->>Server: Client Hello (Offering TLSv1.3, ALPN: h2, http/1.1)
    Server->>Client: Server Hello (TLSv1.3, ALPN: h2)
    Server->>Client: Encrypted Extensions
    Server->>Client: Server Certificate
    Server->>Client: CERT Verify (with server's public key)
    Server->>Client: Finished
    Client->>Server: Change Cipher Spec
    Client->>Server: Finished
    Client->>Server: GET /status/418 HTTP/2 (encrypted with negotiated cipher)
    Server->>Client: HTTP/2 418 Response (encrypted with negotiated cipher)
```

步骤解释：
1. 客户端（Client）发送 "Client Hello" 消息，提供支持的TLS版本（在这里是TLSv1.3），同时携带一个ALPN（Application-Layer Protocol Negotiation）协议列表（h2和http/1.1）。

2. 服务器（Server）回应 "Server Hello" 消息，确认采用的TLS版本（TLSv1.3）和选择的ALPN协议（在这里是h2）。

3. 服务器发送 "Encrypted Extensions" 消息。这是TLS 1.3的新特性，将所有握手扩展都置于加密的握手消息中，以提高安全性。

4. 服务器提供其数字证书，其中包含服务器的公钥信息。

5. 服务器发送 "CERT Verify" 消息（带有服务器的公钥），客户端需要使用这个消息验证服务器的身份。

6. 服务器发送 "Finished" 消息，表明其握手部分已完成。

7. 客户端发送 "Change Cipher Spec" 消息，告知服务器将切换到协商好的加密套件进行通信。

8. 客户端发送 "Finished" 消息，表明其握手部分已完成。

9. 客户端使用协商好的加密套件发送加密的请求(GET /status/418 HTTP/2)。

10. 服务器使用同样的加密套件发送加密的HTTP响应（HTTP/2 418）。

这个示例展示了典型的TLSv1.3握手过程，包括版本协商、证书验证、加密套件选择以及加密通信。在这个过程中，客户端和服务器建立了安全的通信环境。



删除网关的secret并创建一个新 secret以更改ingress gateway的凭据

```bash
kubectl -n istio-system delete secret httpbin-credential
```



```bash
mkdir new_certificates
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=example Inc./CN=example.com' -keyout new_certificates/example.com.key -out new_certificates/example.com.crt
openssl req -out new_certificates/httpbin.example.com.csr -newkey rsa:2048 -nodes -keyout new_certificates/httpbin.example.com.key -subj "/CN=httpbin.example.com/O=httpbin organization"
openssl x509 -req -sha256 -days 365 -CA new_certificates/example.com.crt -CAkey new_certificates/example.com.key -set_serial 0 -in new_certificates/httpbin.example.com.csr -out new_certificates/httpbin.example.com.crt
kubectl create -n istio-system secret tls httpbin-credential \
--key=new_certificates/httpbin.example.com.key \
--cert=new_certificates/httpbin.example.com.crt
```



使用新证书进行访问

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert new_certificates/example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    此处还是有茶壶



```bash
< HTTP/2 418
< server: istio-envoy
< date: Fri, 02 Dec 2022 01:14:48 GMT
< access-control-allow-credentials: true
< x-more-info: http://tools.ietf.org/html/rfc2324
< access-control-allow-origin: *
< content-length: 135
< x-envoy-upstream-service-time: 8
<

    -=[ teapot ]=-

       _...._
     .'  _ _ `.
    | ."` ^ `". _,
    \_;`"---"`|//
      |       ;/
      \_     _/
        `"""`
* Connection #0 to host httpbin.example.com left intact
```



尝试使用旧证书访问

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    吃瘪



```bash
root@node1:~/istio-1.16.0# curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
> --cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
* Added httpbin.example.com:32696:192.168.1.233 to DNS cache
* Hostname httpbin.example.com was found in DNS cache
*   Trying 192.168.1.233:32696...
* TCP_NODELAY set
* Connected to httpbin.example.com (192.168.1.233) port 32696 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: example.com.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (OUT), TLS alert, decrypt error (563):
* error:0407008A:rsa routines:RSA_padding_check_PKCS1_type_1:invalid padding
* Closing connection 0
curl: (35) error:0407008A:rsa routines:RSA_padding_check_PKCS1_type_1:invalid padding
```

在这个输出中，我们的curl命令试图向Istio Ingress Gateway（httpbin.example.com）发送一个HTTPS请求，访问/status/418端点，同时提供Host头和自定义证书。这是逐步解析各个部分：

1. `* Added httpbin.example.com:32696:192.168.1.233 to DNS cache` - Curl将httpbin.example.com及其对应的端口和IP地址添加到DNS缓存中。

2. `* Hostname httpbin.example.com was found in DNS cache` - Curl在DNS缓存中找到了httpbin.example.com域名。

3. `*   Trying 192.168.1.233:32696...` - Curl尝试连接到给定的IP地址和端口。

4. `* TCP_NODELAY set` - Curl设置TCP_NODELAY，禁用Nagle算法以提高传输速度。

5. `* Connected to httpbin.example.com (192.168.1.233) port 32696 (#0)` - Curl成功地连接到了目标地址。

6. 下面一系列端展示了curl与服务器进行TLS握手的过程。
   * `* ALPN, offering h2`
   * `* ALPN, offering http/1.1`
   * `* successfully set certificate verify locations:` 等。

7. `* TLSv1.3 (OUT), TLS alert, decrypt error (563):` - 在与服务器完成握手过程的某个时候，TLS连接遇到了解密错误。

8. `* error:0407008A:rsa routines:RSA_padding_check_PKCS1_type_1:invalid padding` - 连接中出现了一个错误，使用RSA填充检查方法时返回了无效的填充错误。这可能是由于证书的错误导致的。

9. `* Closing connection 0` - 由于错误，Curl关闭了连接ID为0的连接。

10. `curl: (35) error:0407008A:rsa routines:RSA_padding_check_PKCS1_type_1:invalid padding` - 最后，curl命令由于错误而退出，并将RSA例程错误的详细信息显示给用户。

总之，在这里的输出中，Curl试图连接到Istio Ingress Gateway并访问指定的端点。但是，在TLS握手过程中，遇到了一个无效填充的错误，导致连接无法成功建立。这可能是由于证书错误。要解决此问题，请检查提供的证书是否正确。



## 2.为多Host配置TLS ingress gateway

重新创建httpbin凭据

```bash
kubectl -n istio-system delete secret httpbin-credential
kubectl create -n istio-system secret tls httpbin-credential \
--key=httpbin.example.com.key \
--cert=httpbin.example.com.crt
```



启用helloworld-v1样例

```bash
kubectl apply -f istiolabmanual/helloworld-v1.yaml
```



为helloworld-v1.example.com创建证书和私钥：

```bash
openssl req -out helloworld-v1.example.com.csr -newkey rsa:2048 -nodes -keyout helloworld-v1.example.com.key -subj "/CN=helloworld-v1.example.com/O=helloworld organization"
openssl x509 -req -sha256 -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 1 -in helloworld-v1.example.com.csr -out helloworld-v1.example.com.crt
```



创建helloworld-credential secret

```bash
kubectl create -n istio-system secret tls helloworld-credential --key=helloworld-v1.example.com.key --cert=helloworld-v1.example.com.crt
```



创建指向两个服务的gateway

```bash
kubectl apply -f istiolabmanual/mygatewayv1.yaml
```



查看gateway配置文件

```bash
nano istiolabmanual/mygatewayv1.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: mygateway
spec:
  selector:
    istio: ingressgateway # use istio default ingress gateway
  servers:
  - port:
      number: 443
      name: https-httpbin
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: httpbin-credential
    hosts:
    - httpbin.example.com
  - port:
      number: 443
      name: https-helloworld
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: helloworld-credential
    hosts:
    - helloworld-v1.example.com
```

这是一个Istio的`Gateway`资源配置文件。它指定了如何将外部流量路由到在Istio服务网格里的服务。让我解释一下具体的配置内容：

- `apiVersion`: 这是API版本，这里使用的是`networking.istio.io/v1alpha3`。
- `kind`: Kubernetes资源类型，这里的类型是`Gateway`。
- `metadata`：资源的配置元数据。
  - `name`：指定Gateway的名称，此处为`mygateway`。

`spec`字段描述了`Gateway`的配置内容：

- `selector`：选择器，定义要使用的Istio网关。
  - `istio: ingressgateway`：这里选择了Istio默认的ingress gateway。

`servers`字段定义了不同的服务器配置。这个示例包括两个服务器配置：

1. 第一个服务器（httpbin）:

   - `port`：端口配置。
     - `number`: 443 - 使用HTTPS协议的默认端口。
     - `name`: https-httpbin - 为端口命名。
     - `protocol`: HTTPS - 使用HTTPS协议。
   - `tls`: TLS配置。
     - `mode`: SIMPLE - 简单的TLS模式，即客户端和Gateway之间的单向加密。
     - `credentialName`: httpbin-credential - TLS证书的密钥名称。
   - `hosts`: 路由规则支持的主机名列表。
     - httpbin.example.com - 当请求的Host header匹配此域名时，流量将路由到这个服务器配置。

2. 第二个服务器（helloworld）:

   - `port`：端口配置。
     - `number`: 443 - 使用HTTPS协议的默认端口。
     - `name`: https-helloworld - 为端口命名。
     - `protocol`: HTTPS - 使用HTTPS协议。
   - `tls`: TLS配置。
     - `mode`: SIMPLE - 简单的TLS模式，即客户端和Gateway之间的单向加密。
     - `credentialName`: helloworld-credential - TLS证书的密钥名称。
   - `hosts`: 路由规则支持的主机名列表。
     - helloworld-v1.example.com - 当请求的Host header匹配此域名时，流量将路由到这个服务器配置。

总之，这个Gateway配置定义了两个使用HTTPS协议的服务器。其中，一个处理域名为`httpbin.example.com`的流量，并使用`httpbin-credential`证书；另一个处理域名为`helloworld-v1.example.com`的流量，并使用`helloworld-credential`证书。这些服务器都使用简单的TLS模式，即客户端和Gateway之间的单向加密。



创建helloworld-v1的vs

```bash
kubectl apply -f istiolabmanual/helloworld-v1vs.yaml
```



查看virtual server配置文件

```bash
nano istiolabmanual/helloworld-v1vs.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: helloworld-v1
spec:
  hosts:
  - helloworld-v1.example.com
  gateways:
  - mygateway
  http:
  - match:
    - uri:
        exact: /hello
    route:
    - destination:
        host: helloworld-v1
        port:
          number: 5000
```

该配置针对向`helloworld-v1.example.com`发送的请求，将匹配URI为`/hello`的流量，并将这些流量路由到`helloworld-v1`服务的5000端口。



向v1发起访问

```bash
curl -v -HHost:helloworld-v1.example.com --resolve "helloworld-v1.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt "https://helloworld-v1.example.com:$SECURE_INGRESS_PORT/hello"
```

​    此处收获HTTP/2 200



```yaml
...
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* Connection state changed (MAX_CONCURRENT_STREAMS == 2147483647)!
< HTTP/2 200
< content-type: text/html; charset=utf-8
< content-length: 60
< server: istio-envoy
< date: Fri, 02 Dec 2022 01:20:51 GMT
< x-envoy-upstream-service-time: 113
<
Hello version: v1, instance: helloworld-v1-5f44dd8565-zplmv
* Connection #0 to host helloworld-v1.example.com left intact
```



向httpbin.example.com发起访问

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    此处还是有茶壶



```bash
< HTTP/2 418
< server: istio-envoy
< date: Fri, 02 Dec 2022 01:21:55 GMT
< access-control-allow-credentials: true
< x-more-info: http://tools.ietf.org/html/rfc2324
< access-control-allow-origin: *
< content-length: 135
< x-envoy-upstream-service-time: 3
<

    -=[ teapot ]=-

       _...._
     .'  _ _ `.
    | ."` ^ `". _,
    \_;`"---"`|//
      |       ;/
      \_     _/
        `"""`
* Connection #0 to host httpbin.example.com left intact
```



## 3.配置交互TLS ingress gateway

更新证书

```bash
kubectl -n istio-system delete secret httpbin-credential
kubectl create -n istio-system secret generic httpbin-credential --from-file=tls.key=httpbin.example.com.key \
--from-file=tls.crt=httpbin.example.com.crt --from-file=ca.crt=example.com.crt
```



把mygateway切换到`mutual`模式

```bash
kubectl apply -f istiolabmanual/mygatewayv2.yaml
```



查看gateway配置文件

```bash
nano istiolabmanual/mygatewayv2.yaml
```



```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
 name: mygateway
spec:
 selector:
   istio: ingressgateway # use istio default ingress gateway
 servers:
 - port:
     number: 443
     name: https
     protocol: HTTPS
   tls:
     mode: MUTUAL
     credentialName: httpbin-credential # must be the same as secret
   hosts:
   - httpbin.example.com
```

这个配置文件定义了一个Istio Gateway资源，它用来配置Istio代理（Envoy）以执行基于L7协议层（如HTTP/HTTPS）的流量入口。以下是关于这个具体配置文件的解释：

- `apiVersion: networking.istio.io/v1alpha3`：定义了资源的API版本，使用Istio网络API库中的`v1alpha3`版本。
- `kind: Gateway`：指定这是一个Gateway资源。
- `metadata`字段包含以下信息：
  - `name: mygateway`：为这个Gateway定义一个名为`mygateway`的具体名称。

- `spec`字段描述了Gateway的配置内容，包括：
  - `selector`字段定义了应用于此Gateway的代理（Envoy）工作负载标签。当前的选择器是：
    - `istio: ingressgateway`：使用默认的Istio ingress gateway。
  - `servers`字段定义了一组服务器配置，每个服务器为一个或多个主机名启用不同协议。当前的Gateway有一个服务器配置：
    - `port`字段定义了服务器运行的端口和协议类型：
      - `number: 443`：监听端口号为443，通常用于HTTPS。
      - `name: https`：给监听端口命名为“https”。
      - `protocol: HTTPS`：指定这个服务器用于HTTPS流量。
    - `tls`字段包含TLS相关设置，如模式和证书：
      - `mode: MUTUAL`：指定服务器为双向TLS（mTLS）模式。要求客户端和服务器端都提供证书以进行相互认证。
      - `credentialName: httpbin-credential`：指定使用名为`httpbin-credential`的证书与密钥。该名称应与相应的Kubernetes Secret名称相同。
    - `hosts`字段包含此服务器应接受的主机名列表：
      - `httpbin.example.com`：此服务器应处理发往`httpbin.example.com`的流量。

总之，这个Gateway定义了一个监听443端口的HTTPS服务器，处理发往`httpbin.example.com`的流量。它使用双向TLS（需要客户端和服务器证书）进行连接，并使用名为`httpbin-credential`的证书与密钥。此外，它使用Istio的默认ingress gateway。



尝试使用之前的方式进行访问

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    吃瘪

```bash
root@node1:~/istio-1.16.0# curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
> --cacert example.com.crt "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
* Added httpbin.example.com:32696:192.168.1.233 to DNS cache
* Hostname httpbin.example.com was found in DNS cache
*   Trying 192.168.1.233:32696...
* TCP_NODELAY set
* Connected to httpbin.example.com (192.168.1.233) port 32696 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: example.com.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Request CERT (13):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Certificate (11):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=httpbin.example.com; O=httpbin organization
*  start date: Dec  2 01:06:59 2022 GMT
*  expire date: Dec  2 01:06:59 2023 GMT
*  common name: httpbin.example.com (matched)
*  issuer: O=example Inc.; CN=example.com
*  SSL certificate verify ok.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* OpenSSL SSL_write: Broken pipe, errno 32
* Failed sending HTTP2 data
* nghttp2_session_send() failed: The user callback function failed(-902)
* Connection #0 to host httpbin.example.com left intact
curl: (16) OpenSSL SSL_write: Broken pipe, errno 32
```

这段输出描述了使用 curl 命令请求一个 HTTP 服务的过程。在这个例子中，请求的是在 httpbin.example.com 上的 `/status/418` 路径。下面是对输出的简单描述：

1. curl 首先将域名 httpbin.example.com，端口号，和 IP 地址（192.168.1.233）添加到 DNS 缓存。
2. curl 连接到目标 IP（192.168.1.233）和端口号（32696）的服务器。
3. 启动 TLS（Transport Layer Security，传输层安全性）协议过程。
4. 进行 TLSv1.3 握手，双方协商加密算法，确认服务器证书等。
5. 获得服务器证书的详细信息（通常包括证书的主题，颁发者，有效期，等）。
6. 确认 SSL 证书已验证。
7. 使用 HTTP/2 协议与服务器通信。
8. 在尝试发送请求时，出现一个错误：OpenSSL SSL_write: Broken pipe, errno 32。 这意味着在client尝试向服务器发送请求数据时，连接意外断开。

至此，请求没有成功执行，原因是在发送数据过程中出现了错误。这可能是由于服务器端的问题或者网络问题。建议检查服务器端日志以了解可能的错误原因。



生成新的客户端证书和私钥

```bash
openssl req -out client.example.com.csr -newkey rsa:2048 -nodes -keyout client.example.com.key -subj "/CN=client.example.com/O=client organization"
openssl x509 -req -sha256 -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 1 -in client.example.com.csr -out client.example.com.crt
```



向httpbin.example.com发起访问

```bash
curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
--cacert example.com.crt --cert client.example.com.crt --key client.example.com.key \
"https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
```

​    此处还是有茶壶



```bash
root@node1:~/istio-1.16.0# curl -v -HHost:httpbin.example.com --resolve "httpbin.example.com:$SECURE_INGRESS_PORT:$INGRESS_HOST" \
> --cacert example.com.crt --cert client.example.com.crt --key client.example.com.key \
> "https://httpbin.example.com:$SECURE_INGRESS_PORT/status/418"
* Added httpbin.example.com:32696:192.168.1.233 to DNS cache
* Hostname httpbin.example.com was found in DNS cache
*   Trying 192.168.1.233:32696...
* TCP_NODELAY set
* Connected to httpbin.example.com (192.168.1.233) port 32696 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: example.com.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Request CERT (13):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Certificate (11):
* TLSv1.3 (OUT), TLS handshake, CERT verify (15):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=httpbin.example.com; O=httpbin organization
*  start date: Dec  2 01:06:59 2022 GMT
*  expire date: Dec  2 01:06:59 2023 GMT
*  common name: httpbin.example.com (matched)
*  issuer: O=example Inc.; CN=example.com
*  SSL certificate verify ok.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* Using Stream ID: 1 (easy handle 0x563d03e87e30)
> GET /status/418 HTTP/2
> Host:httpbin.example.com
> user-agent: curl/7.68.0
> accept: */*
>
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* Connection state changed (MAX_CONCURRENT_STREAMS == 2147483647)!
< HTTP/2 418
< server: istio-envoy
< date: Fri, 02 Dec 2022 01:27:09 GMT
< access-control-allow-credentials: true
< x-more-info: http://tools.ietf.org/html/rfc2324
< access-control-allow-origin: *
< content-length: 135
< x-envoy-upstream-service-time: 1
<

    -=[ teapot ]=-

       _...._
     .'  _ _ `.
    | ."` ^ `". _,
    \_;`"---"`|//
      |       ;/
      \_     _/
        `"""`
* Connection #0 to host httpbin.example.com left intact
```

与之前的输出相比，这次的请求已经成功访问了httpbin.example.com的"/status/418"路径，并通过端口32696建立了安全连接。在这次尝试中，TLSv1.3握手成功完成，并验证了服务器的证书。客户端像服务器一样也使用了自己的证书和密钥。此次请求使用了HTTP/2协议进行通信，没有再出现Broken pipe错误。

成功访问的原因可能有以下几点：

1. 服务器证书问题已解决：在这次的输出中，可以看到服务器证书已经得到验证，证书的颁发者、起始日期和过期日期都是有效的。并且与目标域名`httpbin.example.com`匹配。

2. 客户端证书和密钥正确：此次的请求中，客户端正确地提供了自己的证书（client.example.com.crt）和密钥（client.example.com.key），这在之前的尝试中可能未正确提供。

3. 正确处理HTTP/2协议：此次的尝试成功使用了HTTP/2协议，与服务器建立了连接，没有出现Broken pipe错误。这表明客户端和服务器之间的协议版本已就绪，并且协议处理正确。

综上所述，第二次尝试已解决了之前遇到的问题，导致可以正常访问目标路径。这些变化有可能是因为修复了证书问题、提供了正确的客户端证书和密钥，以及正确处理了HTTP/2协议。

对比此前的握手验证过程,这一次明显增多，我们可以用Mermaid 时序图描述TLS握手过程如下：

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server

    C->>S: ClientHello (TLSv1.3)
    S->>C: ServerHello (TLSv1.3)
    S->>C: Encrypted Extensions
    S->>C: Request CERT
    S->>C: 提供服务器证书
    S->>C: CERT verify
    S->>C: Finished
    C->>S: ChangeCipherSpec
    C->>S: 提供客户端证书
    C->>S: CERT verify
    C->>S: Finished
    C->>S: 使用HTTP/2发起请求
    S->>C: HTTP/2 响应 (418)
```

1. 客户端向服务器发送ClientHello消息，指明使用TLSv1.3。
2. 服务器回复ServerHello消息，确认使用TLSv1.3。
3. 服务器发送Encrypted Extensions消息。
4. 服务器请求客户端证书（Request CERT消息）。
5. 服务器提供服务器证书。
6. 服务器发送CERT verify消息，要求客户端验证服务器证书。
7. 服务器发送Finished消息。
8. 客户端发送ChangeCipherSpec消息，告知服务器将开始加密所有后续的通信。
9. 客户端提供客户端证书。
10. 客户端发送CERT verify消息，证明客户端证书有效。
11. 客户端发送Finished消息。
12. 之后，客户端使用HTTP/2向服务器发起请求。
13. 服务器通过HTTP/2发回响应（418代码，表示“我是一个茶壶”）。



清理环境

```bash
kubectl delete gateway mygateway
kubectl delete virtualservice httpbin
kubectl delete --ignore-not-found=true -n istio-system secret httpbin-credential \
helloworld-credential
kubectl delete --ignore-not-found=true virtualservice helloworld-v1
```

```bash
rm -rf example.com.crt example.com.key httpbin.example.com.crt httpbin.example.com.key httpbin.example.com.csr helloworld-v1.example.com.crt helloworld-v1.example.com.key helloworld-v1.example.com.csr client.example.com.crt client.example.com.csr client.example.com.key ./new_certificates
```

```bash
kubectl delete deployment --ignore-not-found=true httpbin helloworld-v1
kubectl delete service --ignore-not-found=true httpbin helloworld-v1
```



# Lab 7 认证：为应用生成双向TLS

可选,重新声明istioctl路径

```bash
  export PATH=$PWD/bin:$PATH
```



创建两个名称空间foo和bar，并在它们两个上部署httpbin 和 sleep 并启用sidecar注入：

```bash
kubectl create ns foo
kubectl apply -f <(istioctl kube-inject -f samples/httpbin/httpbin.yaml) -n foo
kubectl apply -f <(istioctl kube-inject -f samples/sleep/sleep.yaml) -n foo
kubectl create ns bar
kubectl apply -f <(istioctl kube-inject -f samples/httpbin/httpbin.yaml) -n bar
kubectl apply -f <(istioctl kube-inject -f samples/sleep/sleep.yaml) -n bar
```



创建另一个 legacy 名称空间，不启用sidecar注入的情况下部署sleep：

```bash
kubectl create ns legacy
kubectl apply -f samples/httpbin/httpbin.yaml -n legacy
kubectl apply -f samples/sleep/sleep.yaml -n legacy
```



查看这三个名称空间的相互访问情况

```bash
for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
```

  这是一个用于测试连接和HTTP响应状态的Shell命令。这个命令在几个不同的命名空间（foo、bar和legacy）中运行sleep Pod，并尝试向HTTP服务器（httpbin）发送请求。以下是对这个命令的详细解释：

1. 外层for循环：遍历"foo" "bar" "legacy"三个命名空间。${from}变量代表当前循环的命名空间名称。
2. 内层for循环：遍历"foo" "bar"两个命名空间。${to}变量代表当前循环的目标命名空间名称。
3. kubectl get pod：在${from}命名空间中获取带有'app=sleep'标签的Pod，并用`-o jsonpath={.items..metadata.name}`选项来提取Pod名称。
4. kubectl exec：在${from}命名空间中执行指定的Pod（带有'app=sleep'标签的Pod）和sleep容器。后面的`--`表示执行的命令将在Pod内部运行。
5. curl命令：执行HTTP请求。访问目标URL：http://httpbin.${to}:8000/ip。使用-s选项使输出无声，-o /dev/null将返回的内容重定向到/dev/null，最后，-w "sleep.${from} to httpbin.${to}: %{http_code}\n"将显示每次请求的源和目标以及返回的HTTP状态代码。

因此，这个命令的目的是检查在不同命名空间中的sleep Pod与其他命名空间中的httpbin服务之间的连接情况。最终，每个请求的源和目标以及HTTP状态代码将被输出。



两两互通，和谐

```bash
root@node1:~/istio-1.16.0# for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
sleep.foo to httpbin.foo: 200
sleep.foo to httpbin.bar: 200
sleep.bar to httpbin.foo: 200
sleep.bar to httpbin.bar: 200
sleep.legacy to httpbin.foo: 200
sleep.legacy to httpbin.bar: 200
```



检查authentication policies 和 destination rules

```bash
kubectl get peerauthentication --all-namespaces

kubectl get destinationrule --all-namespaces
```



```bash
root@node1:~/istio-1.16.0# kubectl get peerauthentication --all-namespaces
No resources found
root@node1:~/istio-1.16.0#
root@node1:~/istio-1.16.0# kubectl get destinationrule --all-namespaces
NAMESPACE   NAME          HOST          AGE
default     details       details       18h
default     productpage   productpage   18h
default     ratings       ratings       18h
default     reviews       reviews       18h
```



在整个网格上启用PERMISSIVE模式的认证策略

```bash
kubectl apply -n istio-system -f istiolabmanual/mtlspermissive.yaml 
```



查看认证策略配置文件

```bash
nano istiolabmanual/mtlspermissive.yaml
```



```yaml
apiVersion: "security.istio.io/v1beta1"
kind: "PeerAuthentication"
metadata:
  name: "default"
spec:
  mtls:
    mode: PERMISSIVE
```

这是一个Istio的`PeerAuthentication`资源配置文件。它用于定义服务之间的认证策略，主要用于配置mTLS（双向TLS）行为。下面是关于这个配置文件的详细解释：

1. `apiVersion: "security.istio.io/v1beta1"`：指定资源对象使用的API版本，这里用的是Istio Security API的v1beta1版本。
2. `kind: "PeerAuthentication"`：资源对象的Kind（类型），表示这是一个`PeerAuthentication`资源。
3. `metadata`：元数据部分，其中包含资源对象的名称和其他相关信息。
   - `name: "default"`：资源对象的名称为"default"。
4. `spec`：配置的具体细节：
   - `mtls`：定义mTLS的配置。
     - `mode: PERMISSIVE`：设置mTLS模式为"PERMISSIVE"。在这种模式下，服务可以同时接收使用TLS加密或者未加密的流量。这将允许任何能与服务通信的来源发送请求，而无论其是否使用TLS。

总之，这个配置文件定义了一个名为"default"的`PeerAuthentication`资源，该资源将服务的mTLS模式设置为"PERMISSIVE"，允许服务接收来自其他使用或不使用TLS加密的服务的请求。这通常用于平滑升级和过渡，但请注意，这样的设置可能会降低安全性。在需要保证较高安全性的场景下，可以考虑使用更严格的模式，如"STRICT"。



查看这三个名称空间的相互访问情况

```bash
for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
```

  还是很和谐，因为策略比较宽松



```bash
root@node1:~/istio-1.16.0# for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
sleep.foo to httpbin.foo: 200
sleep.foo to httpbin.bar: 200
sleep.bar to httpbin.foo: 200
sleep.bar to httpbin.bar: 200
sleep.legacy to httpbin.foo: 200
sleep.legacy to httpbin.bar: 200
```



在整个网格上启用STRICT模式的认证策略

```bash
kubectl  apply -n istio-system -f istiolabmanual/mtlsstrict.yaml 
```



查看认证策略配置文件

```bash
nano istiolabmanual/mtlsstrict.yaml
```



```yaml
apiVersion: "security.istio.io/v1beta1"
kind: "PeerAuthentication"
metadata:
  name: "default"
spec:
  mtls:
    mode: STRICT
```

在这个配置文件中，唯一不同的部分是`spec`下的`mtls`设置。`mode`的值从"PERMISSIVE"变为了"STRICT"。

这意味着在Istio服务网格中，mTLS（双向TLS）策略已经升级为"STRICT"模式。在此模式下，只有使用TLS加密的流量才能通过认证。与刚才的"PERMISSIVE"模式相比，"STRICT"模式提供了更高的安全性，因为它要求所有流量都进行加密。在"STRICT"模式下，不允许未加密的流量进入服务。



查看这三个名称空间的相互访问情况

```bash
for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
```

  策略一旦收紧，legacy 被发现是裸泳的了，悲剧



```bash
root@node1:~/istio-1.16.0# for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
sleep.foo to httpbin.foo: 200
sleep.foo to httpbin.bar: 200
sleep.bar to httpbin.foo: 200
sleep.bar to httpbin.bar: 200
sleep.legacy to httpbin.foo: 000
command terminated with exit code 56
sleep.legacy to httpbin.bar: 000
command terminated with exit code 56
```



重建legacy 名称空间的服务，请启用sidecar注入

```bash
kubectl apply -f <(istioctl kube-inject -f samples/httpbin/httpbin.yaml) -n legacy
kubectl apply -f <(istioctl kube-inject -f samples/sleep/sleep.yaml) -n legacy
```



查看legacy 名称空间pod重建情况

```bash
kubectl get pod -n legacy
```



```bash
root@node1:~/istio-1.16.0# kubectl get pod -n legacy
NAME                      READY   STATUS    RESTARTS   AGE
httpbin-dddb978d5-wg7pq   2/2     Running   0          26s
sleep-7786869f6c-56q75    2/2     Running   0          24s
```



查看这三个名称空间的相互访问情况

```bash
for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
```

  注入sidecar之后，legacy又可以和小伙伴们一起玩耍了



```bash
root@node1:~/istio-1.16.0# for from in "foo" "bar" "legacy"; do for to in "foo" "bar"; do kubectl exec $(kubectl get pod -l app=sleep -n ${from} -o jsonpath={.items..metadata.name}) -c sleep -n ${from} -- curl http://httpbin.${to}:8000/ip -s -o /dev/null -w "sleep.${from} to httpbin.${to}: %{http_code}\n"; done; done
sleep.foo to httpbin.foo: 200
sleep.foo to httpbin.bar: 200
sleep.bar to httpbin.foo: 200
sleep.bar to httpbin.bar: 200
sleep.legacy to httpbin.foo: 200
sleep.legacy to httpbin.bar: 200
```



查看验证策略

```bash
kubectl get peerauthentication --all-namespaces
```



```bash
root@node1:~/istio-1.16.0# kubectl get peerauthentication --all-namespaces
NAMESPACE      NAME      MODE     AGE
istio-system   default   STRICT   6m22s
```



清理

```bash
kubectl delete peerauthentication --all-namespaces –all
kubectl delete ns foo bar legacy
```



# Lab 8 授权：实现JWT身份的认证和授权

创建包含 httpbin 和sleep样例的名称空间foo

```bash
kubectl create ns foo
kubectl apply -f <(istioctl kube-inject -f samples/httpbin/httpbin.yaml) -n foo
kubectl apply -f <(istioctl kube-inject -f samples/sleep/sleep.yaml) -n foo
```



检查httpbin和sleep的通讯情况

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl http://httpbin.foo:8000/ip -s -o /dev/null -w "%{http_code}\n"
```



```bash
root@node1:~/istio-1.16.0# kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl http://httpbin.foo:8000/ip -s -o /dev/null -w "%{http_code}\n"
200
```

这个命令行包含了几个子命令，组合在一起完成了在命名空间`foo`下的`sleep` Pod内部检查`http://httpbin.foo:8000/ip`的HTTP响应状态的操作。让我们逐段分解这个命令行。

1. `$(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name})`：这个子命令用于获取命名空间`foo`下的带有`app=sleep`标签的Pod的名称。`-o jsonpath={.items..metadata.name}`表示仅输出Pod的名称。

2. `kubectl exec`：这是一个用于在当前运行的Kubernetes Pod中执行命令的命令行工具。

3. `-c sleep`：这个选项表示在`sleep`容器中执行命令。

4. `-n foo`：这个选项表示在命名空间`foo`下执行操作。

5. `-- curl http://httpbin.foo:8000/ip -s -o /dev/null -w "%{http_code}\n"`：这是在`sleep`容器中执行的curl命令。该命令访问`http://httpbin.foo:8000/ip`并返回响应状态。`-s`表示静默模式，用于不显示输出的进度指示或错误消息。`-o /dev/null`表示将响应的主体输出重定向到`/dev/null`。`-w "%{http_code}\n"`表示输出HTTP响应的状态码，后跟换行符。

输出：
`200`

这表示请求成功，在访问`http://httpbin.foo:8000/ip`时返回的HTTP响应状态码为200，表示操作成功。  一切正常，本来就是by default的吗



为httpbin创建 request authentication policy

```bash
kubectl apply -f istiolabmanual/jwtra.yaml
```



查看验证策略

```
nano istiolabmanual/jwtra.yaml
```



```yaml
apiVersion: "security.istio.io/v1beta1"
kind: "RequestAuthentication"
metadata:
  name: "jwt-example"
  namespace: foo
spec:
  selector:
    matchLabels:
      app: httpbin
  jwtRules:
  - issuer: "testing@secure.istio.io"
    jwksUri: "https://raw.githubusercontent.com/istio/istio/release-1.5/security/tools/jwt/samples/jwks.json"
```

这是一个Istio RequestAuthentication资源的YAML配置文件。RequestAuthentication资源用于配置请求的认证策略，对于符合策略的请求，Istio将要求客户端提供有效的JWT（JSON Web Token）令牌。以下是配置文件的详细解释：

1. apiVersion: 配置文件版本，security.istio.io/v1beta1 表示这是Istio security 的 v1beta1 版本。
2. kind: 资源类型，这里是 RequestAuthentication 用于配置请求认证策略。
3. metadata: 包括资源的名称和命名空间。
   - name: 资源名称，这里是 "jwt-example"
   - namespace: 资源所在的命名空间，这里是 "foo"

4. spec: RequestAuthentication资源的具体配置信息。
   - selector: 定义策略适用的目标工作负载，通过指定匹配标签来选择目标。
     - matchLabels: 目标工作负载的标签选择器，这里需要标签 "app: httpbin" 的工作负载才能匹配。
   - jwtRules: 定义处理JWT的规则。
     - issuer: JWT发行人，这里是 "testing@secure.istio.io"
     - jwksUri: JWT签名密钥集的URL，这里是 "https://raw.githubusercontent.com/istio/istio/release-1.5/security/tools/jwt/samples/jwks.json"

这个配置文件定义了一个请求认证策略，要求具有标签 "app: httpbin" 的工作负载的请求提供有效的JWT，发行人为 "testing@secure.istio.io"，同时通过提供的jwksUri验证JWT的签名。



检查持有无效JWT的访问

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer invalidToken" -w "%{http_code}\n"
```

  理应不能访问，401没毛病

``` bash
root@node1:~/istio-1.16.0# kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer invalidToken" -w "%{http_code}\n"
401
```



检查不持有JWT的访问， 

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -w "%{http_code}\n"
```

  这居然也能成功，尴尬

```bash
root@node1:~/istio-1.16.0# kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -w "%{http_code}\n"
200
```



在foo上启用 authorization policy

```bash
kubectl apply -f istiolabmanual/jwtap.yaml 
```



查看验证策略配置文件

```bash
nano istiolabmanual/jwtap.yaml
```



```yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: require-jwt
  namespace: foo
spec:
  selector:
    matchLabels:
      app: httpbin
  action: ALLOW
  rules:
  - from:
    - source:
       requestPrincipals: ["testing@secure.istio.io/testing@secure.istio.io"]
```

这个配置文件定义了一个Istio的AuthorizationPolicy资源。AuthorizationPolicy用于在服务间通信上应用访问控制策略。具体来说，这个策略允许具有特定JWT令牌的请求访问"httpbin"服务。下面是配置文件各部分的详细解释：

1. `apiVersion: security.istio.io/v1beta1` - 表示这是一个Istio的安全相关资源。
2. `kind: AuthorizationPolicy` - 表示这个资源是一个授权策略。
3. `metadata` - 包括策略的名称（`name: require-jwt`）和命名空间（`namespace: foo`）。
4. `spec` - 描述策略的具体设置。
   a. `selector` - 选择要应用此策略的工作负载。这里，策略适用于具有标签`app: httpbin`的工作负载。
   b. `action: ALLOW` - 表示允许满足规则条件的请求。
   c. `rules` - 规定允许哪些请求访问服务。在此示例中，有一个规则：
      - `from` - 定义请求来源：
        i. `source` - 指定来源的标识或属性。
          - `requestPrincipals: ["testing@secure.istio.io/testing@secure.istio.io"]` - 允许具有指定请求主体的请求。

这个AuthorizationPolicy与之前的RequestAuthentication配置文件有关联。在前一个RequestAuthentication策略中，我们要求所有发往具有标签 `app: httpbin` 的工作负载的请求提供一个有效的JWT。而在这个AuthorizationPolicy中，我们定义了允许哪些已验证的JWT请求访问"httpbin"服务。这里我们允许请求主体为"testing@secure.istio.io/testing@secure.istio.io"的JWT令牌通过。

总之，这两个策略结合在一起可以达到这样的效果：如果向具有 "app: httpbin" 标签的工作负载发起请求，请求者需要提供一个有效的JWT令牌，且令牌中的请求主体必须为 "testing@secure.istio.io/testing@secure.istio.io"，只有满足这些条件的请求才会被允许通过。



设置指向JWT的Token变量

```bash
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.5/security/tools/jwt/samples/demo.jwt -s) && echo $TOKEN | cut -d '.' -f2 - | base64 --decode -
```

这个命令是一种在Unix或Linux终端中执行的多步骤过程，旨在从Istio的GitHub仓库获取一个示例的JWT（JSON Web Token），并对其进行解码。让我们逐步解析这个命令：

1. `TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.5/security/tools/jwt/samples/demo.jwt -s)`: 使用curl命令从Istio的GitHub仓库下载一个示例的JWT，并将其内容保存在名为`TOKEN`的变量中。`-s`参数使curl操作在静默模式下进行，这意味着它不会显示进度或错误消息。

2. `echo $TOKEN`: 输出`TOKEN`变量的值。这将打印下载的JWT字符串。

3. `cut -d '.' -f2 -`: 使用cut命令从JWT字符串中提取第二部分（以'.'分隔）。一个典型的JWT由三部分组成：header、payload和signature，它们用点号分隔。这个命令将JWT解析为其净荷部分。

4. `base64 --decode -`: 使用base64命令对提取后的净荷部分进行解码。这将把Base64编码的净荷部分解码为一个JSON对象。

整个命令通过管道将这些步骤连接起来，使输出能够顺利流转。最后，你会看到JWT的净荷部分作为一个易读的JSON对象。



使用有效JWT进行访问

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer $TOKEN" -w "%{http_code}\n"
```

这个命令行是一个用于发送请求到"httpbin"服务的复合命令。它在名为 "foo" 的命名空间中执行，并向"httpbin"服务发送带有JWT令牌的请求。让我们逐步解析它：

1. `kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}`：这个子命令用于获取在 "foo" 命名空间中标签为 "app=sleep" 的pod的名称。使用 `-o jsonpath={.items..metadata.name}` 选择输出仅包含pod名称的子集。

2. `kubectl exec $(...) -c sleep -n foo --`: 这一部分的命令使用 `kubectl exec` 在 "foo" 命名空间中找到具有 "app=sleep" 标签的Pod，并在其中的sleep容器中执行后续的命令。`$(...)` 部分会被之前子命令的结果（即对应的Pod的名称）替换。

3. `curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer $TOKEN" -w "%{http_code}\n"`：这一部分的命令在sleep容器内使用curl向"httpbin"服务发送请求。它请求"httpbin"服务的 "/headers" 接口，使用之前获取的TOKEN作为发送请求的JWT令牌。`-s` 参数表示静默模式，`-o /dev/null` 将响应主体发送到/dev/null, 然后 `-w "%{http_code}\n"` 参数输出HTTP状态码并以换行符结尾。

这个命令与之前的命令有关。之前的命令获取并解码了JWT令牌的净荷部分。而这个命令使用获取的JWT令牌（保存在TOKEN变量中）作为身份验证信息向"httpbin"服务发送请求。结合之前分析的Istio授权策略，这意味着只有请求包含有效的JWT令牌且JWT的请求主体合法时，请求才会被 "httpbin" 服务接受，并返回HTTP状态码。



正常,200

```bash
root@node1:~/istio-1.16.0# kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer $TOKEN" -w "%{http_code}\n"
200
```



再次验证不持有JWT的访问

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -w "%{http_code}\n"
```

  授权策略启用之后，就没办法浑水摸鱼了，403了

```bash
root@node1:~/istio-1.16.0# kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -w "%{http_code}\n"
403
```



清理环境

```bash
kubectl delete namespace foo
```





# Lab 9 监控

## 1.日志

模拟一次页面访问,为了防止被不必要的katacode页面元素“污染“，我们最好从内部用命令行执行一次curl访问

```bash
kubectl get svc

curl http://10.108.91.118:9080/productpage
```



```bash
root@node1:~# kubectl get svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
details       ClusterIP   10.97.72.135    <none>        9080/TCP   19h
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP    222d
productpage   ClusterIP   10.108.91.118   <none>        9080/TCP   19h
ratings       ClusterIP   10.96.212.103   <none>        9080/TCP   19h
reviews       ClusterIP   10.102.110.0    <none>        9080/TCP   19h
root@node1:~# curl http://10.108.91.118:9080/productpage
<!DOCTYPE html>
<html>
  <head>
    <title>Simple Bookstore App</title>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">

<!-- Optional theme -->
<link rel="stylesheet" href="static/bootstrap/css/bootstrap-theme.min.css">

  </head>
  <body>
...
```



查看istio proxy日志

```
kubectl get pods
```



```bash
kubectl logs -f productpage-xxx istio-proxy
```



观察到两个outbond条目，分别指向details和reviews，还有一个inbound条目，指向productpage

```bash
2022-12-01T07:08:05.712268Z     info    xdsproxy        connected to upstream XDS server: istiod.istio-system.svc:15012
[2022-12-02T02:36:05.715Z] "GET /details/0 HTTP/1.1" 200 - via_upstream - "-" 0 178 2 2 "-" "curl/7.68.0" "7b923129-a124-9cf9-9c32-8fbe97cb3f57" "details:9080" "10.244.135.8:9080" outbound|9080||details.default.svc.cluster.local 10.244.135.11:49730 10.97.72.135:9080 10.244.135.11:34060 - default
[2022-12-02T02:36:05.721Z] "GET /reviews/0 HTTP/1.1" 200 - via_upstream - "-" 0 438 791 791 "-" "curl/7.68.0" "7b923129-a124-9cf9-9c32-8fbe97cb3f57" "reviews:9080" "10.244.104.11:9080" outbound|9080||reviews.default.svc.cluster.local 10.244.135.11:48624 10.102.110.0:9080 10.244.135.11:47568 - default
```

这个日志包含了两条记录，分别表示两个HTTP请求的信息。每条记录包括请求时间、响应状态、相关元数据等。让我们逐条分析。

1. 第一条日志：

```bash
[2022-12-01T07:08:05.712268Z]     info    xdsproxy        connected to upstream XDS server: istiod.istio-system.svc:15012
```

记录了在2022年12月1日07:08:05（UTC）时, 代理成功连接到了上游XDS服务器 istiod.istio-system.svc，端口15012。XDS是Istio中用来分发配置信息的协议。

2. 第二条日志：

```bash
[2022-12-02T02:36:05.715Z] "GET /details/0 HTTP/1.1" 200 - via_upstream - "-" 0 178 2 2 "-" "curl/7.68.0" "7b923129-a124-9cf9-9c32-8fbe97cb3f57" "details:9080" "10.244.135.8:9080" outbound|9080||details.default.svc.cluster.local 10.244.135.11:49730 10.97.72.135:9080 10.244.135.11:34060 - default
```

这条记录显示了一个使用HTTP/1.1协议的GET请求，请求路径为/details/0，发生在2022-12-02T02:36:05.715Z。请求得到了200的响应状态码，表示请求成功。请求的其他信息如下：

- 通过上游服务器（via_upstream）处理请求。
- 请求头部没有Referer字段（"-"）。
- 请求体大小为0字节。
- 响应体大小为178字节。
- 请求响应时间为2毫秒。
- 请求端使用的工具是curl/7.68.0。
- 请求的唯一标识符（UUID）为7b923129-a124-9cf9-9c32-8fbe97cb3f57。
- 请求的目标服务是details，端口号为9080。
- 请求被路由到了内部IP 10.244.135.8，端口号为9080。
- 请求来源与目标间的连接信息：outbound|9080||details.default.svc.cluster.local 10.244.135.11:49730 10.97.72.135:9080 10.244.135.11:34060 - default。

3. 第三条日志：

```bash
[2022-12-02T02:36:05.721Z] "GET /reviews/0 HTTP/1.1" 200 - via_upstream - "-" 0 438 791 791 "-" "curl/7.68.0" "7b923129-a124-9cf9-9c32-8fbe97cb3f57" "reviews:9080" "10.244.104.11:9080" outbound|9080||reviews.default.svc.cluster.local 10.244.135.11:48624 10.102.110.0:9080 10.244.135.11:47568 - default
```

这条记录与第二条类似，不过请求的目标路径为/reviews/0，响应体大小为438字节，请求响应时间为791毫秒，请求被路由到了内部IP 10.244.104.11，端口号为9080。请求来源与目标间的连接信息：outbound|9080||reviews.default.svc.cluster.local 10.244.135.11:48624 10.102.110.0:9080 10.244.135.11:47568 - default。





为获取更多信息，设置日志以JSON格式显式

```bash
istioctl manifest apply --set profile=demo --set values.meshConfig.accessLogFile="/dev/stdout" --set values.meshConfig.accessLogEncoding=JSON
```



（可选）查看日志设置

```bash
kubectl describe configmap istio -n istio-system | less
```



配置文件有以下输出

```bash
Data
====
mesh:
----
accessLogEncoding: JSON #格式
accessLogFile: /dev/stdout #日志输入位置
defaultConfig:
  discoveryAddress: istiod.istio-system.svc:15012
  proxyMetadata: {}
  tracing:
    zipkin:
      address: zipkin.istio-system:9411
enablePrometheusMerge: true
extensionProviders:
- envoyOtelAls:
    port: 4317
    service: opentelemetry-collector.istio-system.svc.cluster.local
  name: otel
rootNamespace: istio-system
trustDomain: cluster.local
meshNetworks:
----
networks: {}
```



再次查看istio proxy日志

```bash
kubectl logs -f productpage-xxx istio-proxy
```



分别指向details和reviews 的outbound条目

```bash
{"downstream_remote_address":"10.40.0.12:41654","authority":"details:9080","path":"/details/0","protocol":"HTTP/1.1","upstream_service_time":"9","upstream_local_address":"10.40.0.12:48508","duration":"15","upstream_transport_failure_reason":"-","route_name":"default","downstream_local_address":"10.99.18.67:9080","user_agent":"curl/7.47.0","response_code":"200","response_flags":"-","start_time":"2020-05-15T06:30:26.459Z","method":"GET","request_id":"e2de9f03-38ac-924d-ae2b-176d868c56ab","upstream_host":"10.40.0.8:9080","x_forwarded_for":"-","requested_server_name":"-","bytes_received":"0","istio_policy_status":"-","bytes_sent":"178","upstream_cluster":"outbound|9080||details.default.svc.cluster.local"}

{"upstream_cluster":"outbound|9080||reviews.default.svc.cluster.local","downstream_remote_address":"10.40.0.12:43866","authority":"reviews:9080","path":"/reviews/0","protocol":"HTTP/1.1","upstream_service_time":"1786","upstream_local_address":"10.40.0.12:36048","duration":"1787","upstream_transport_failure_reason":"-","route_name":"default","downstream_local_address":"10.98.163.167:9080","user_agent":"curl/7.47.0","response_code":"200","response_flags":"-","start_time":"2020-05-15T06:30:26.480Z","method":"GET","request_id":"e2de9f03-38ac-924d-ae2b-176d868c56ab","upstream_host":"10.40.0.11:9080","x_forwarded_for":"-","requested_server_name":"-","bytes_received":"0","istio_policy_status":"-","bytes_sent":"379"}
```

指向productpage的inbound条目

```bash
{"upstream_cluster":"inbound|9080|http|productpage.default.svc.cluster.local","downstream_remote_address":"10.32.0.1:40938","authority":"10.109.209.72:9080","path":"/productpage","protocol":"HTTP/1.1","upstream_service_time":"1839","upstream_local_address":"127.0.0.1:51264","duration":"1840","upstream_transport_failure_reason":"-","route_name":"default","downstream_local_address":"10.40.0.12:9080","user_agent":"curl/7.47.0","response_code":"200","response_flags":"-","start_time":"2020-05-15T06:30:26.446Z","method":"GET","request_id":"e2de9f03-38ac-924d-ae2b-176d868c56ab","upstream_host":"127.0.0.1:9080","x_forwarded_for":"-","requested_server_name":"-","bytes_received":"0","istio_policy_status":"-","bytes_sent":"5183"}
```



使用JSON Handler查看详细日志尤其是五元组信息和Flag

```bash
`"outbound|9080||details.default.svc.cluster.local"`

`"outbound|9080||reviews.default.svc.cluster.local"`

`"inbound|9080|http|productpage.default.svc.cluster.local"
```



## 2.指标和可视化

安装仪表板： 

```bash
kubectl apply -f samples/addons
```



开放监控工具的NotePort端口

```bash
kubectl patch svc -n istio-system prometheus -p '{"spec":{"type": "NodePort"}}'
kubectl patch service prometheus --namespace=istio-system --type='json' --patch='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value":31120}]'

kubectl patch svc -n istio-system grafana  -p '{"spec":{"type": "NodePort"}}'
kubectl patch service grafana --namespace=istio-system --type='json' --patch='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value":31121}]'

kubectl patch svc -n istio-system tracing -p '{"spec":{"type": "NodePort"}}'
kubectl patch service tracing --namespace=istio-system --type='json' --patch='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value":31122}]'

kubectl patch svc -n istio-system kiali -p '{"spec":{"type": "NodePort"}}'
kubectl patch service kiali --namespace=istio-system --type='json' --patch='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value":31123}]'
```



仪表板组件打开方式

- prometheus: [http://node1:31120/]( http://node1:31120/)
- grafana: [http://node1:31121/](http://node1:31121/)

- tracing: [http://node1:31122/]( http://node1:31122/)

- kiali: [http://node1:31123/



查看ingress gateway的端口号

```bash
 kubectl get svc -n istio-system | grep ingress
```



```bash
root@node1:~/istio-1.16.0# kubectl get svc -n istio-system | grep ingress
istio-ingressgateway   LoadBalancer   10.105.85.107    <pending>     15021:31215/TCP,80:30193/TCP,443:32696/TCP,31400:30573/TCP,15443:30572/TCP   23h
```



在另外一个窗口执行压测脚本

```
while true; do curl http://node3:30193/productpage; done  
```



查看Prometheus监控指标:服务指标

![image-20221202142137147](manual.assets/image-20221202142137147.png)



查看Prometheus监控指标: Envoy 代理指标

![image-20221202142211924](manual.assets/image-20221202142211924.png)



查看Prometheus监控指标: 控制平面指标,Status：runtime and build



![image-20221202142308737](manual.assets/image-20221202142308737.png)



查看Prometheus监控指标:  Status：configuration

![image-20221202142349481](manual.assets/image-20221202142349481.png)



使用Grafana查看系统整体状态

打开Grafana页面，从报表列表中可以看到istio相关的报表，这里最关键的两个报表分别是

- 查看应用（服务）数据的Mesh Dashboard
- 查看Istio 自身（各组件）数据的Performance Dashboard



![image-20221202142627379](manual.assets/image-20221202142627379.png)



打开mesh dashboard我们可以观察到网格数据总览

![image-20221202142734251](manual.assets/image-20221202142734251.png)





如果需要查看应用层面信息可以从service列表下面进行选择进入相关的服务视图（Service Dashboard）

![image-20221202143001982](manual.assets/image-20221202143001982.png)



如果需要查看部署层面信息可以从workload列表下面进行选择进入相关的工作负载（workload dashboard）视图

![image-20221202143139584](manual.assets/image-20221202143139584.png)



打开Performance Dashboard可以查看Istio 自身以及各个组件的数据

Istio 系统总览

![image-20221202143301597](manual.assets/image-20221202143301597.png)



控制平面视图

![image-20221202143555608](manual.assets/image-20221202143555608.png)



## 3.查看应用拓扑信息

Overview界面

![image-20221202144107999](manual.assets/image-20221202144107999.png)





Graph页面：查看应用拓扑

![image-20221202144342898](manual.assets/image-20221202144342898.png)



尝试选择不同的构图和指标查看拓扑信息，比如在下图中可以观察流量在不同版本的reviews服务之间的分配及延时参数

![image-20221202144534745](manual.assets/image-20221202144534745.png)



Application以拓扑和出入站流量为监控核心

![image-20221202144817611](manual.assets/image-20221202144817611.png)



Service：侧重观察服务定义

![image-20221202144947810](manual.assets/image-20221202144947810.png)



Workload： 侧重观察部署和基础架构：

![image-20221202145126393](manual.assets/image-20221202145126393.png)





## 4.分布式追踪

在Jeager页面中，从search中选择服务和相关的参数

![image-20221202145440346](manual.assets/image-20221202145440346.png)



从右侧选择一个trace进行分析，（建议选择饱满一点的，比如有8个span）

![image-20221202145554461](manual.assets/image-20221202145554461.png)





对比bookinfo的访问过程进行访问链的调查

![image-20221202145727546](manual.assets/image-20221202145727546.png)

展开某个span查看访问过程，可以看到envoy日志中的关键信息 比如response_flag quest-id和无元组信息

![image-20221202145824316](manual.assets/image-20221202145824316.png)



亦可使用Trace Graph方式查看

![image-20221202145921407](manual.assets/image-20221202145921407.png)





# 备注

## 国内安装方法

如果直接下载istio失败，可以使用这个步骤下载1.16.0安装包并解压：

```bash
wget https://chengzhstor.blob.core.windows.net/k8slab/istio-1.16.0-linux-amd64.tar.gz
tar xf istio-1.16.0-linux-amd64.tar.gz
```



进入下载目录，随着产品的迭代，此处的版本号可能不同，请大家依据屏幕提示进行后两步操作

```bash
cd istio-1.16.0/
```



设置环境变量

```bash
export PATH="$PATH:/root/istio-1.16.0/bin"
```



## 清理整个环境



清理 Bookinfo

```bash
samples/bookinfo/platform/kube/cleanup.sh
```



卸载istio

```bash
kubectl delete -f samples/addons
istioctl manifest generate --set profile=demo | kubectl delete --ignore-not-found=true -f -
```

​    Istio 卸载程序按照层次结构逐级的从 istio-system 命令空间中删除 RBAC 权限和所有资源。对于不存在的资源报错，可以安全的忽略掉，毕竟他们已经被分层的删除了。



删除命名空间 istio-system 

```bash
kubectl delete namespace istio-system
```



指示 Istio 自动注入 Envoy 边车代理的标签默认也不删除。 不需要的时候，使用下面命令删掉它。

```bash
kubectl label namespace default istio-injection-
```

