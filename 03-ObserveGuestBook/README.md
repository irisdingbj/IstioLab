# 03 - 观察微服务的遥测数据： metrics 和 tracing

### Istio 遥测


[Istio mixer enables telemetry reporting](https://istio.io/docs/concepts/policy-and-control/mixer.html).

### 配置 Istio 来获得 telemetry 数据

1. 验证  Grafana, Prometheus, Kiali 和 Jaeger 成功部署. 他们都被安装在  `istio-system` 名称空间下面.

    ```shell
    kubectl get pods -n istio-system
    kubectl get services -n istio-system
    ```

2. 配置 Istio 自动收集遥测数据.
    ```shell
    cd ../../plans/
    kubectl create -f guestbook-telemetry.yaml
    ```

1. 获得访问 guestbook的地址，使用下面输出的 EXTERNAL-IP来访问:

    ```shell
    kubectl get service guestbook -n default
    ```

在浏览器中打开 external ip 地址来访问 guestbook.

4. 模拟一些到guestbook的访问负载.

    ```shell
    for i in {1..20}; do sleep 0.5; curl http://<guestbook_IP>/; done
    ```

## 查看 guestbook 遥测数据

#### Jaeger

1. 为Jaeger启动 port forwarding :

    ```shell
    kubectl port-forward -n istio-system \
      $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') \
      16686:16686 &
    ```
2. 在浏览器中输入 `http://127.0.0.1:16686`
3. 在 **Services** 菜单中选择 **guestbook** 或者 **analyzer** 服务.
4. 下啦到窗口底部并点击 **Find Traces** 按钮来查看trace.

更多可参考 [Jaeger文档](https://www.jaegertracing.io/docs/)

#### Grafana

1. 为Grafana启动 port forwarding :

    ```shell
    kubectl -n istio-system port-forward \
      $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') \
      3000:3000 &
    ```

2. 在浏览器中打开 http://localhost:3000 并进入 `Istio Service Dashboard` 

3. 从下拉列表中选择 guestbook 服务.

4. 打开另外一个浏览器窗口来多次访问guestbook应用。或者也可以运行上边的脚本来产生一些工作负载。然后切换回到grafana的窗口。

Grafana dashboard 可以看到有关每个工作负载的metrics. 也可以浏览一下其他的dashborad. 

更多 [Grafana的参考文档](http://docs.grafana.org/).

#### Prometheus

1. 为Prometheus启动 port forwarding.

    ```shell
    kubectl -n istio-system port-forward \
      $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') \
      9090:9090 &
    ```
2. 打开浏览器并访问 http://localhost:9090/graph, 在 “Expression” 输入框里输入: `istio_request_bytes_count`. 点击 Execute 然后选择Graph.

3. 尝试另一个查询: `istio_requests_total{destination_service="guestbook.default.svc.cluster.local", destination_version="2.0"}`

#### Kiali

Kiali 是一个开源的项目，它可以安装在Istio之上并为Istiot中的微服务提供可视化界面。 通过Kiala你可以详细的查看你的微服务以及他们之间的调用关系。它还提供一些设置熔断和限流的功能。

1. 为 Kiali 启动 port forwarding.

    ```shell
    kubectl -n istio-system port-forward \
    $(kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}') \
    20001:20001 &
    ```

2. 打开浏览器并访问[http://localhost:20001/kiali/](http://localhost:20001/kiali/), 用 `admin` 做为用户名和密码来登陆.
3. 选择 Graph and 并选择 `default` 名称空间. 你可以查看到部署到Istio中的微服务拓扑图。
4. 从 `Edge Labels` 下拉列表框中选择a `Traffic rate per second` 来查看访问速率.
5. Kiali 还有很多其他试图来帮组你观察你的微服务。点击这些不同的试图来查看他们。



#### [继续进行04-ExpostSvcViaIngressGateway - 通过Istio Ingress Gateway暴露微服务](../04-ExpostSvcViaIngressGateway/README.md)