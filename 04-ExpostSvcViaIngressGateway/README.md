# 04 - 通过Istio Ingress Gateway暴露微服务


### 通过Istio Ingress Gateway暴露guestbook服务

1. 为guestbook应用配置Istio Ingress Gateway.  `guestbook-gateway.yaml` 文件位于 (istio101) 的 `workshop/plans` 目录下.

```shell
kubectl create -f guestbook-gateway.yaml
```

2. 获得 Istio Ingress Gateway的**EXTERNAL-IP**.

```shell
kubectl get service istio-ingressgateway -n istio-system
```
期待输出:
```shell
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
istio-ingressgateway   LoadBalancer   172.21.254.53    169.6.1.1       80:31380/TCP,443:31390/TCP,31400:31400/TCP    1m
2d
```

3. 记录下上面获得的external IP. 在后边的练习中我们会一直使用这个IP. 创建一个名字为$INGRESS_IP的环境变量并把它的值设为上面获得的external IP。

样例:
```
export INGRESS_IP=169.6.1.1
```

恭喜! 你成功的通过Istio ingress gateway把guestbook暴露到了网格外.



#### [继续进行05-TrafficManagement](../05-TrafficManagement/README.md)