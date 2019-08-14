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

## Connect Istio Ingress Gateway to the IBM Cloud Kubernetes Service NLB Host Name

NLB host names are the DNS host names you can generate for each IBM Cloud Kubernetes deployment exposed with the Network LoadBalancer(NLB) service. These host names come with SSL certificate, the DNS registration, and health checks so you can benefit from them for any deployments that you expose via the NLB on IBM Cloud Kubernetes Service.

You can run the IBM Cloud Kubernetes Service ALB, an API gateway of your choice, an Istio ingress gateway, and an MQTT server in parallel in your IBM Cloud Kubernetes Service cluster. Each one will have its own:

    1. Publicly available wildcard host name
    2. Wildcard SSL certificate associated with the host name
    3. Health checks that you can configure if you use multizone deployments. 
    
Let's leverage this feature with Istio ingress gateway:

1. Let's first check if you have any NLB host names for your cluster:

    ```shell
    ibmcloud ks nlb-dnss --cluster $MYCLUSTER
    ```
   If you haven't used this feature before, you will get an empty list.

2. Obtain the Istio ingress gateway's external IP. Get the EXTERNAL-IP of the istio-ingressgateway service via output below:

    ```shell
    kubectl get service istio-ingressgateway -n istio-system
    ```
    
3. Create the NLB host with the Istio ingress gateway's public IP address:

    ```shell
    ibmcloud ks nlb-dns-create --cluster $MYCLUSTER --ip $INGRESS_IP
    ```

4. List the NLB host names for your cluster:

    ```shell
    ibmcloud ks nlb-dnss --cluster $MYCLUSTER
    ```

    Example output:
    ```
   Retrieving host names, certificates, IPs, and health check monitors for network load balancer (NLB) pods in cluster <cluster_name>...
    OK
    Hostname                                                                             IP(s)               Health Monitor   SSL Cert Status   SSL Cert Secret Name   
    mycluster-85f044fc29ce613c264409c04a76c95d-0001.us-east.containers.appdomain.cloud   ["169.1.1.1"]   None             created           mycluster-85f044fc29ce613c264409c04a76c95d-0001   
    ```

5. Make note of the NLB host name (<nlb_host_name>), as it will be used to access your Guestbook app in later parts of the course. Create an environment variable for it and test using curl

    Example:
    ```
    export NLB_HOSTNAME=mycluster-85f044fc29ce613c264409c04a76c95d-0001.us-east.containers.appdomain.cloud
    
    curl $NLB_HOSTNAME
    ```

6. Enable health check of the NLB host for Istio ingress gateway:

    ```shell
    ibmcloud ks nlb-dns-monitor-configure --cluster $MYCLUSTER --nlb-host $NLB_HOSTNAME --type HTTP --description "Istio ingress gateway health check" --path "/healthz/ready" --port 15020 --enable
    ```

7. Monitor the health check of the NLB host for Istio ingress gateway:

    ```shell
    ibmcloud ks nlb-dns-monitor-status --cluster $MYCLUSTER
    ```
    
    After waiting for a bit, you should start to see the health monitor's status changed to Enabled.
    
    Example output:
    ```
    Retrieving health check monitor statuses for NLB pods...
    OK
    Hostname                                                                             IP              Health Monitor   H.Monitor Status   
    mycluster-85f044fc29ce613c264409c04a76c95d-0001.us-east.containers.appdomain.cloud   169.1.1.1   Enabled          Healthy
    ```

恭喜! 你成功的通过Istio ingress gateway把guestbook暴露到了网格外.



#### [继续进行05-TrafficManagement](../05-TrafficManagement/README.md)