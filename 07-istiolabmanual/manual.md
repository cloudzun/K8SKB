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



为外部httpbin服务创建service entry

```bash
kubectl  apply -f  istiolabmanual/egressse.yaml 
```



查看egress service entry配置文件

```bash
nano istiolabmanual/egressse.yaml 
```



```bash
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

​	

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



触发熔断  2个并发，执行20次

```bash
kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -c 2 -qps 0 -n 20 -loglevel Warning http://httpbin:8000/get
```



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



触发熔断 again 3个并发，执行30次

```bash
kubectl exec -it "$FORTIO_POD"  -c fortio -- /usr/bin/fortio load -c 3 -qps 0 -n 30 -loglevel Warning http://httpbin:8000/get
```



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



为httpbin.example.com创建证书和私钥：

```bash
openssl req -out httpbin.example.com.csr -newkey rsa:2048 -nodes -keyout httpbin.example.com.key -subj "/CN=httpbin.example.com/O=httpbin organization"
openssl x509 -req -sha256 -days 365 -CA example.com.crt -CAkey example.com.key -set_serial 0 -in httpbin.example.com.csr -out httpbin.example.com.crt
```



启动 httpbin 用例

```bash
kubectl apply -f istiolabmanual/sdshttpbin.yaml 
```



为ingress gateway创建 secret

```bash
kubectl create -n istio-system secret tls httpbin-credential --key=httpbin.example.com.key --cert=httpbin.example.com.crt
```



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



设置ingress主机和端口变量

```bash
export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].nodePort}')
export TCP_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="tcp")].nodePort}')
```



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

回滚日志查看 TSL 握手过程



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

对比此前的握手验证过程,这一次明显增多



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

  一切正常，本来就是by default的吗



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



设置指向JWT的Token变量

```bash
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.5/security/tools/jwt/samples/demo.jwt -s) && echo $TOKEN | cut -d '.' -f2 - | base64 --decode -
```



使用有效JWT进行访问

```bash
kubectl exec $(kubectl get pod -l app=sleep -n foo -o jsonpath={.items..metadata.name}) -c sleep -n foo -- curl "http://httpbin.foo:8000/headers" -s -o /dev/null -H "Authorization: Bearer $TOKEN" -w "%{http_code}\n"
```

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

```
samples/bookinfo/platform/kube/cleanup.sh
```



卸载istio

```
kubectl delete -f samples/addons
istioctl manifest generate --set profile=demo | kubectl delete --ignore-not-found=true -f -
```

​    Istio 卸载程序按照层次结构逐级的从 istio-system 命令空间中删除 RBAC 权限和所有资源。对于不存在的资源报错，可以安全的忽略掉，毕竟他们已经被分层的删除了。



删除命名空间 istio-system 

```
kubectl delete namespace istio-system
```



指示 Istio 自动注入 Envoy 边车代理的标签默认也不删除。 不需要的时候，使用下面命令删掉它。

```
kubectl label namespace default istio-injection-
```

