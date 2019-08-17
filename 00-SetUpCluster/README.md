# 准备Kubernetes集群环境

Istio Lab使用了IBM公有云上的Kubernetes集群，以及一个云上的命令行窗口CloudShell。您只需要拥有IBM Cloud的注册账号，就可以进行下面的操作。

## 前提

* 拥有一个IBM Cloud账号，也被称为IBM ID。如果没有注册，请到[http://cloud.ibm.com](http://cloud.ibm.com)上注册。
* 准备一个可以联网的浏览器，推荐Chrome，Firefox，和Safari。

## 第一步：分配Kuberntes集群

我们预先为这次实验创建了若干个多节点的Kubernetes集群，请您到IBM工作人员那里分配一个Kubernetes集群。 分配到集群后，请记住您的集群的名字。

## 第二步：准备CloudShell

一，访问[CloudShell](https://cloudshell-console-ikslab.us-south.cf.cloud.ibm.com/)，点击左上角的Login按钮，用IBM Cloud 账号登陆。

![](https://github.com/daisy-ycguo/knativelab/raw/master/images/cloudshell-overview.png)

二，登陆后，出现一个页面，要求输入CloudShell的访问密码。咨询IBM工作人员获取访问密码。输入密码后，就进入CloudShell页面。

![](https://github.com/daisy-ycguo/knativelab/raw/master/images/cloudshell-passw.png)

三，在CloudShell页面中，点击右上角您的用户名，会弹出一个下拉框，选择IBM。 

![](https://github.com/daisy-ycguo/knativelab/raw/master/images/cloudshell-account.png)

四，点击上图中右上角的“IBM”左侧的命令行窗口图标，页面会开始刷新。首次使用需要等待1-5分钟（等待时间与网速有关），一个云上的命令行窗口就创建好了。

五，在命令行窗口中输入几条命令，如`git`或者`kubectl`，看到正确返回后，就可以开始使用了。

![](https://github.com/daisy-ycguo/knativelab/raw/master/images/cloudshell-terminal.png)

## 第三步：连接到您的Kubernetes集群

一，您领取到的集群名称大约为`jtc-workshop**`，其中`**`部分为您的集群编号，如`jtc-workshop01`。把这个集群名称记录在环境变量中。

   在CloudShell页面，输入：
   ```text
   export MYCLUSTER=<your_cluster_name>
   ```

二，获取你的集群的更多信息：

运行命令：
```text
ibmcloud ks cluster-get $MYCLUSTER
```

期待输出：
```
Retrieving cluster jtc-workshop01...
OK


Name:                           jtc-workshop01
ID:                             blbhfbas0es199ogrfgg
State:                          normal
Created:                        2019-08-16T20:54:25+0000
Location:                       syd01
Master URL:                     https://c2.au-syd.containers.cloud.ibm.com:28329
Public Service Endpoint URL:    https://c2.au-syd.containers.cloud.ibm.com:28329
Private Service Endpoint URL:   -
Master Location:                Sydney
Master Status:                  Ready (8 hours ago)
Master State:                   deployed
Master Health:                  normal
Ingress Subdomain:              jtc-workshop01.au-syd.containers.appdomain.cloud
Ingress Secret:                 jtc-workshop01
Workers:                        3
Worker Zones:                   syd01
Version:                        1.13.9_1532
Owner:                          Mike.Petersen@ibm.com
Monitoring Dashboard:           -
Resource Group ID:              5eb57fd577b64b51beb832c2e9d5287a
Resource Group Name:            Default
```

***注意*** 如果返回错误`The specified cluster could not be found.`，请检查
- CloudShell右上角，用户名那里是否换到为`IBM`
- 集群的名字是否正确

三，下载你的集群的配置文件到CloudShell终端：

   运行命令：
   ```text
   ibmcloud ks cluster-config $MYCLUSTER
   ```
   期待输出：
   ```
   OK
   The configuration for jtc-workshop01 was downloaded successfully.

   Export environment variables to start using Kubernetes.

   export KUBECONFIG=/usr/shared-data/cloud-ibm-com-e2b54d0c3bbe4180b1ee63a0e2a7aba4-1/.bluemix/plugins/container-service/clusters/jtc-workshop01/kube-config-syd01-jtc-workshop01.yml
   ```

四，上面一条命令输出的最后一行是黄色高亮的export命令，在CloudShell中拷贝该命令，并黏贴执行：

   ```text
   export KUBECONFIG=/usr/shared-data/cloud-ibm-com-e2b54d0c3bbe4180b1ee63a0e2a7aba4-1/.bluemix/plugins/container-service/clusters/jtc-workshop01/......
   ```

五，验证您已经可以用kubectl连接到云端的Kubernetes集群：

   运行命令：
   ```text
   kubectl get nodes
   ```
   期待输出：
   ```
   NAME           STATUS   ROLES    AGE   VERSION
   10.138.95.33   Ready    <none>   8h    v1.13.8+IKS
   10.138.95.39   Ready    <none>   8h    v1.13.8+IKS
   10.138.95.42   Ready    <none>   8h    v1.13.8+IKS
   ```

这里，`kubectl get nodes`能够得到正确返回，看到您的集群中的节点，那么您就可以继续下面的实验了。

继续 [01-InstallIstio](../01-InstallIstio/README.md).

