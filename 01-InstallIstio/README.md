# 01-安装Istio

IBM公有云上提供的Kubernetes集群可以一键安装Istio，省去安装的烦恼。

## 前提

* 分配到一个IBM Kubernetes Cluster；
* 启动CloudShell云端命令行窗口，本次试验的所有命令行输入都在CloudShell窗口中完成；
* 通过kubectl连接到了云端的Kubernetes集群。

## 第一步：使用IBM Cloud命令行工具安装

在CloudShell窗口中执行下面的命令，这个命令会自动安装Istio和Knative。

```text
ibmcloud ks cluster-addon-enable istio-extras --cluster $MYCLUSTER
```

整个安装过程大约需要几分钟，请耐心等待，可以通过下面步骤检查安装进程。

## 第二步：检查安装后的Istio

在CloudShell窗口中执行下面的命令，观察Istio的安装的组件。

1. 列出所有的名称空间，其中istio-system是Istio安装的名称空间：

   ```text
   kubectl get namespace
   ```
   期待输出：
   ```
   NAME                 STATUS    AGE
   default              Active    30m
   ibm-cert-store       Active    20m
   ibm-system           Active    28m
   istio-system         Active    5m20s
   kube-public          Active    30m
   kube-system          Active    30m
   ```

2. 检查Istio相关的服务已经成功部署。

   ```text
   kubectl get svc -n istio-system
   ```
   期待输出：
   ```
   NAME                                     READY     STATUS    RESTARTS   AGE
    NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                                                                                                                                      AGE
 grafana                  ClusterIP      172.21.248.16    <none>          3000/TCP                                                                                                                                     2s
 istio-citadel            ClusterIP      172.21.86.151    <none>          8060/TCP,15014/TCP                                                                                                                           6m56s
 istio-egressgateway      ClusterIP      172.21.197.125   <none>          80/TCP,443/TCP,15443/TCP                                                                                                                     6m56s
 istio-galley             ClusterIP      172.21.29.234    <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   6m56s
 istio-ingressgateway     LoadBalancer   172.21.161.24    169.61.10.106   15020:31167/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30889/TCP,15030:31326/TCP,15031:30961/TCP,15032:31491/TCP,15443:30967/TCP   6m55s
 istio-pilot              ClusterIP      172.21.168.110   <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       6m55s
 istio-policy             ClusterIP      172.21.147.132   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 6m55s
 istio-sidecar-injector   ClusterIP      172.21.57.81     <none>          443/TCP                                                                                                                                      6m55s
 istio-telemetry          ClusterIP      172.21.231.56    <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       6m55s
 jaeger-agent             ClusterIP      None             <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   2s
 jaeger-collector         ClusterIP      172.21.0.87      <none>          14267/TCP,14268/TCP                                                                                                                          2s
 jaeger-query             ClusterIP      172.21.157.12    <none>          16686/TCP                                                                                                                                    2s
 kiali                    ClusterIP      172.21.156.209   <none>          20001/TCP                                                                                                                                    2s
 prometheus               ClusterIP      172.21.159.166   <none>          9090/TCP                                                                                                                                     6m55s
 tracing                  ClusterIP      172.21.210.248   <none>          80/TCP                                                                                                                                       1s
 zipkin                   ClusterIP      172.21.214.67    <none>          9411/TCP                                                                                                                                     1s
   ```

1. 观察Istio相关的的pod，直到都进入running或者completed状态：

   ```text
   watch kubectl get pods -n istio-system
   ```
   期待输出：
   ```
    NAME                                     READY   STATUS    RESTARTS   AGE
    grafana-6c89cb48cf-v767v                 1/1     Running   0          33s
    istio-citadel-66dff76d4-r9gsf            1/1     Running   0          7m27s
    istio-egressgateway-55fc547574-svkkr     1/1     Running   0          7m27s
    istio-galley-7d9dbfd4b9-fw2qk            1/1     Running   0          7m27s
    istio-ingressgateway-9c4856497-rpxvs     1/1     Running   0          7m27s
    istio-pilot-7ff6949955-d9hbw             2/2     Running   0          7m27s
    istio-policy-6b88dd467b-hxhqx            2/2     Running   1          7m27s
    istio-sidecar-injector-bc8dddd65-bwhbq   1/1     Running   0          7m27s
    istio-telemetry-5f9df6d9cc-wppmf         2/2     Running   1          7m26s
    istio-tracing-5777dc949f-k2lhc           1/1     Running   0          33s
    kiali-8c696cc97-2cwk2                    1/1     Running   0          33s
    prometheus-65c985bf4c-g8ch5              1/1     Running   0          7m26s
   ```

   输入`ctrl+c`结束观察。

恭喜您已经完成了Istio的安装！可以进行下面的实验了。

