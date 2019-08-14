# 05 - 使用Istio对微服务进行流量管控

## 使用规则进行流量管控
Istio中用来完成流量管控的核心组件是Pilot.  Pilot 使用三种配置资源来完成流量管控，他们分别是  Virtual Services, Destination Rules和 Service Entries.

### Virtual Services
一个 [VirtualService](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService) 定义了一组路由规则。每个路由规则都定义了一些路由的条件，当这些条件被满足时，流量会被发送到正确的目的地[destination](https://istio.io/docs/reference/config/istio.networking.v1alpha3.html#Destination).

### Destination Rules
一个[DestinationRule](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Destination) 定义了在路由之后目标服务的相关规则。 可以在其中指定负载均衡，熔断策略等等。每个在`VirtualService`规则中指定的`host` 和 `subset` 都必须对应的定义在`DestinationRule`中。


### Service Entries
一个 [ServiceEntry](https://istio.io/docs/reference/config/istio.networking.v1alpha3.html#ServiceEntry) 配置可以使得网格内的服务可以访问网格外的服务。

## Guestbook 应用简介
在Guestbook应用中定义了一个guestbook服务。 这个服务有两个不同的版本(version 1)和(version 2)。 每个版本根据[guestbook-deployment.yaml](https://github.com/linsun/examples/blob/master/guestbook-go/guestbook-deployment.yaml)和 [guestbook-v2-deployment.yaml](https://github.com/linsun/examples/blob/master/guestbook-go/guestbook-v2-deployment.yaml)中的定义都有多个实例。默认的如果不创建任何规则，Istio会轮询的把请求平均分布在这两个版本上。然而现实中，新版本可能有一些问题，所以在上线之前最好进行A/B测试并遵循金丝雀部署原则。


### 使用进行A/B testing 
定义下面的规则 (found in [istio101/workshop/plans](https://github.com/IBM/istio101/tree/master/workshop/plans))来把所有流量导入到version 1:

```shell
kubectl create -f guestbook-destination.yaml
```
查看规则:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: destination-guestbook
spec:
  host: guestbook
  subsets:
    - name: v1
      labels:
        version: '1.0'
    - name: v2
      labels:
        version: '2.0'
```

```shell
kubectl replace -f virtualservice-all-v1.yaml
```
查看规则:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: virtual-service-guestbook
spec:
  hosts:
    - '*'
  gateways:
    - guestbook-gateway
  http:
    - route:
        - destination:
            host: guestbook
            subset: v1
```

这个 `VirtualService` 通过Istio ingress gateway - `guestbook-gateway`来获取进入的网格的流量，把所有的流量都发送到带有"version: v1"标签的pods中。 你可以通过在浏览器中输入guestbook的访问地址来访问guestbook并观察这个行为。 guestbook的访问地址获取方法请参考 [04-ExpostSvcViaIngressGateway](../04-ExpostSvcViaIngressGateway/README.md)。


替换`VirtualService` 规则，使得来自Firefox的流量进入带有"version: v2"标签的pods中，其他流量进入带有"version: v1"标签的pods中：

```shell
kubectl replace -f virtualservice-test.yaml
```
Let's examine the rule:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: virtual-service-guestbook
spec:
  hosts:
    - '*'
  gateways:
    - guestbook-gateway
  http:
    - match:
        - headers:
            user-agent:
              regex: '.*Firefox.*'
      route:
        - destination:
            host: guestbook
            subset: v2
    - route:
        - destination:
            host: guestbook
            subset: v1
```



### 金丝雀部署
在 `Canary Deployments`中我们需要逐渐的把流量导入新版本的服务中。替换新的 `VirtualService` 规则来体验金丝雀部署:

```shell
kubectl replace -f virtualservice-80-20.yaml
```
查看规则:
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: virtual-service-guestbook
spec:
  hosts:
    - '*'
  gateways:
    - guestbook-gateway
  http:
    - route:
        - destination:
            host: guestbook
            subset: v1
          weight: 80
        - destination:
            host: guestbook
            subset: v2
          weight: 20
```
通过这个规则，我们把80%的流量导入v1版本， 20%的流量导入v2版本。
你可以在浏览器中多次访问guestbook来查看。请注意在Mac上通过command + Shift + R，或者通过 Ctrl + F5 在windows上来确保刷新应用访问。

恭喜！你已经完成了所有的实验，现在可以通过https://cognitiveclass.ai/badges/beyond-the-basics-istio-and-ibm-cloud-kubernetes-service/ 申请一个
Badge了！
