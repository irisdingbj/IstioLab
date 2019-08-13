# 引言

Istio Lab是为Istio训练营准备的实验，让您轻松掌握[Istio](https://www.ibm.com/cloud/info/istio)的基本概念和使用方法.

## 内容
- 安装Istio
- 部署 Guestbook例子程序
- 使用 metrics, logging and tracing 来观察微服务
- 创建Istio ingress gateway
- 对微服务进行流量管控，例如：A/B 测试， 金丝雀部署等。

## 安装和配置环境
请根据来安装和配置环境

## Workshop setup
- [Exercise 1 - Accessing a Kubernetes cluster with IBM Cloud Kubernetes Service](exercise-1/README.md)
- [Exercise 2 - Installing Istio](exercise-2/README.md)
- [Exercise 3 - Deploying Guestbook with Istio Proxy](exercise-3/README.md)

## Creating a service mesh with Istio

- [Exercise 4 - Observe service telemetry: metrics and tracing](exercise-4/README.md)
- [Exercise 5 - Expose the service mesh with the Istio Ingress Gateway](exercise-5/README.md)
- [Exercise 6 - Perform traffic management](exercise-6/README.md)
- [Exercise 7 - Secure your service mesh](exercise-7/README.md)
- [Exercise 8 - Enforce policies for microservices](exercise-8/README.md)

## Cleaning up the Workshop

We have a script that will remove [ibmcloud](https://console.bluemix.net/docs/cli/index.html#overview) at [here](cleanup/clean_your_local_machine.sh) and unset your `KUBECONFIG` for you.

We have given you a [script](cleanup/clean_your_k8s_cluster.sh) as a conveant way to remove Istio and the guestbook
application from your instance.

**NOTE**: This puts your kubernetes cluster in a empty state, so do not run this on anything other then
a place you are willing to loose everything.
