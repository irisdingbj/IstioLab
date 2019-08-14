# 02-部署Guestbook样例应用并注入Istio side car



### 下载Guestbook样例应用
1. Clone the Guestbook app into the `workshop` directory.

    ```shell
    git clone https://github.com/IBM/guestbook.git ../guestbook
    ```

2. 进入应用目录.

    ```shell
    cd ../guestbook/v2
    ```

### 为default名称空间启用自动Istio side car注入


1. 为 default 名称空间添加label:
    
    ``` shell
    kubectl label namespace default istio-injection=enabled
    ```
    
2. 验证自动注入的标签已经成功添加到default名称空间:
    
    ``` shell
    kubectl get namespace -L istio-injection
    ```
    
    期待输出:
    ``` shell
    NAME             STATUS   AGE    ISTIO-INJECTION
    default          Active   271d   enabled
    istio-system     Active   5d2h
    ...
    ```

### 创建 Redis 数据库


1. 部署Redis主，从数据库.

    ``` shell
    kubectl create -f redis-master-deployment.yaml
    kubectl create -f redis-master-service.yaml
    kubectl create -f redis-slave-deployment.yaml
    kubectl create -f redis-slave-service.yaml
    ```

2. 验证部署Redis主，从数据库部署成功.

    ```shell
    kubectl get deployment
    ```
    期待输出:
    ```shell
    NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    redis-master   1         1         1            1           5d
    redis-slave    2         2         2            2           5d
    ```

3. 验证主，从数据库服务部署成功

    ```shell
    kubectl get svc
    ```
    期待输出:
    ```shell
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    redis-master   ClusterIP      172.21.85.39    <none>          6379/TCP       5d
    redis-slave    ClusterIP      172.21.205.35   <none>          6379/TCP       5d
    ```

4. 验证主，从数据库对应的pod部署成功.

    ```shell
    kubectl get pods
    ```
    期待输出:
    ```shell
    NAME                            READY     STATUS    RESTARTS   AGE
    redis-master-4sswq              2/2       Running   0          5d
    redis-slave-kj8jp               2/2       Running   0          5d
    redis-slave-nslps               2/2       Running   0          5d
    ```

## 安装 Guestbook 样例应用

1. 在guestbook pods中注入Istio Envoy sidecar，并把guestbook应用部署到cluster中（包含v1和v2版本）： 

    ```shell
    kubectl apply -f ../v1/guestbook-deployment.yaml
    kubectl apply -f guestbook-deployment.yaml
    ```


2. 创建guestbook 服务.

    ```shell
    kubectl create -f guestbook-service.yaml
    ```

3. 验证 guestbook服务成功部署.

    ```shell
    kubectl get svc
    ```
    期待输出:
    ```shell
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    guestbook      LoadBalancer   172.21.36.181   169.61.37.140   80:32149/TCP   5d
    ```

4. 验证 guestbook Pod成功部署.

    ```shell
    kubectl get pods
    ```
    期待输出:
    ```shell
    NAME                            READY     STATUS    RESTARTS   AGE
    guestbook-v1-89cd4b7c7-frscs    2/2       Running   0          5d
    guestbook-v2-56d98b558c-mzbxk   2/2       Running   0          5d
    ```

    注意：每个 guestbook pod 中都包含有2个containers. 一个是 guestbook container, 另一个是 Envoy proxy sidecar.

### 使用 Watson Tone Analyzer
Watson Tone Analyzer 检测用户在guestbook应用中输入反馈的语气，并把这些语气转化成对应的表情符号返回给用户。

1. 在你的IBM cloud账户中创建 Watson Tone Analyzer服务.

    ```shell
    ibmcloud resource service-instance-create my-tone-analyzer-service tone-analyzer lite us-south
    ```

2. 为你的Tone Analyzer服务创建API key. 记录下面命令输出中的 **apikey** 和 **url** .

    ```shell
    ibmcloud resource service-key-create tone-analyzer-key Manager --instance-name my-tone-analyzer-service
    ```

3. 也可以通过下面的命令查看**apikey** 和 **url**:

    ```shell
    ibmcloud resource service-key tone-analyzer-key
    ```

4. 打开 `analyzer-deployment.yaml` 并在文件底部找到 env 的部分. 用你自己的API key来代替 `YOUR_API_KEY` ， 用你自己的url来代替 `YOUR_URL`. YOUR_URL 应该是 `https://gateway.watsonplatform.net/tone-analyzer/api`的格式. 保存文件的修改。

5. 使用`guestbook/v2`目录下的`analyzer-deployment.yaml` 和 `analyzer-service.yaml`部署 analyzer pods 和service。 analyzer 服务会发送请求到 Watson Tone Analyzer 来分析消息中的语气.

    ```shell
    kubectl apply -f analyzer-deployment.yaml
    kubectl apply -f analyzer-service.yaml
    ```

恭喜! 你的guestbook 应用已经部署并配置成功. 进入 03-ObserveGuestBook,来观测你的微服务。

#### [继续进行 03-ObserveGuestBook](../03-ObserveGuestBook/README.md)