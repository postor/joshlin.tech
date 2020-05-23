---
title: "安装 Fission"
linkTitle: 安装 Fission
weight: 20
description: >
  Fission 的安装指引
---

欢迎！本指引会帮你在 Kubernetes 集群上安装并运行 Fission。

# 集群准备工作

首先，我们确认下你已经安装了 Kubernetes CLI 和 Helm。如果你已经安装了 helm，[跳过并转到 fission 安装](#install-fission).

如果你不会（或者不想）用 Helm，也有备选安装方法；详见 [无 Helm 安装](#without-helm).

## Kubernetes 集群

如果你没有 Kubernetes 集群, [使用官方文档来安装一个](https://kubernetes.io/docs/setup/).

{{% notice info %}}
Fission 需求 Kubernetes 1.9 或更高版本
{{% /notice %}}

## Kubectl

Kubectl 是一个用来在 Kubernetes 集群上运行命令的命令行接口，访问 [这里](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 查看如何使用它

查看 [如何在不同平台上配置设置]({{% relref "../trouble-shooting/setup/kubernetes.md" %}}#kubeconfig-for-connecting-to-cluster) 下一步，确保你具有集群的权限。运行这个命令来检查你的 Kubernetes 版本

```sh
$ kubectl version
```

## Helm

Helm 是 Kubernetes 的一个安装器。 如果你已经使用 helm，[跳到下一节](#install-fission).

要安装 Helm，首先你需要 helm CLI。访问 [这里](https://helm.sh/docs/intro/install/) 查看如何安装。

{{% notice info %}}
你可以跳过一下内容并转到 [Fission 安装](#install-fission) 如果你已经使用 Helm **v3**.
{{% /notice %}}

下一步，在你的 Kubernetes 集群上安装 Helm 服务。在做这件事之前，你需要给 Helm 服务安装软件到集群的权限。

例如，你可以使用一个专用的具有完全集群管理权限的用户按以下步骤安装 helm。

(译注：这些应该用不到了，helm 3.0 后没有 init 命令了， 也不需要 tiller)

```sh
$ kubectl create serviceaccount --namespace kube-system tiller
$ kubectl create clusterrolebinding tiller-cluster-rule \
    --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
$ helm init --service-account tiller
```

或者如果你的集群已经配置好了有权限的服务账号（这和版本以及你的 Kubernetes 安装方式有关），你只需要运行：

```sh
$ helm init
```

# 安装 Fission

## 如何选择：完整版 vs 核心版

* fission-all（完整版） **(推荐)**
    * 安装一个完整的服务集合包含 NATS 消息队列，用于日志的 influxDB，等。并支持所有的 Fission 特性。这适用于
    想要尝试所有 Fission 特性的的人和需要使用核心版不包含特性的企业。

* fission-core（核心板）
    * 安装用于创建和服务函数的核心组件

接下来，我们会使用 `fission-all` 来演示如何安装 Fission

`fission-all` 和 `fission-core` 的所有配置项表格可以在 [这里](https://github.com/fission/fission/tree/master/charts#configuration) 查看。

## 使用 Helm

### 不支持负载均衡的集群

类似 Minikube 这样不支持 `LoadBalancer` 服务类型的环境应使用 `NodePort` 代替。你可以通过创建一个 LoadBalancer 类型的服务
看它是否显示 `<pending>` 不再变化来确认。

```sh
$ kubectl get svc
NAME                                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
router                                  LoadBalancer   10.109.61.85     <pending>     80:31972/TCP   25h
```

{{< tabs "fission-install" >}}
{{< tab "Minikube, Docker Desktop" >}}

* Helm v2

```sh
$ helm install --name fission --namespace fission \
    --set serviceType=NodePort,routerServiceType=NodePort \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

* Helm v3

```sh
$ export FISSION_NAMESPACE="fission"
$ kubectl create namespace $FISSION_NAMESPACE
$ helm install --namespace $FISSION_NAMESPACE --name-template fission \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

{{< /tab >}}
{{< tab "OpenShift without LoadBalancer" >}}

请访问 [OpenShift]({{%relref "_index.zh.md" %}}) 了解详情。

* Helm v2

```sh
$ helm install --name fission --namespace fission \
    --set serviceType=NodePort,routerServiceType=NodePort,logger.enableSecurityContext=true,prometheus.enabled=false \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

* Helm v3

```sh
$ export FISSION_NAMESPACE="fission"
$ kubectl create namespace $FISSION_NAMESPACE
$ helm install --namespace $FISSION_NAMESPACE --name-template fission \
    --set serviceType=NodePort,routerServiceType=NodePort,logger.enableSecurityContext=true,prometheus.enabled=false \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

{{< /tab >}}
{{< /tabs >}}

serviceType 变量允许配置 Kubernetes 类型集群外的服务。如果你不想暴露任何东西到集群外你可以用 `ClusterIP`。

### 云服务集群 (GKE, AKS, EKS)

如果你无法连接到集群查看 [如何添加 token 到 kubeconfig]({{% relref "../trouble-shooting/setup/kubernetes.md" %}} #kubeconfig-for-connecting-to-cluster)。

{{< tabs "fission-install-cloud-provider" >}}
{{< tab "GKE, AKS, EKS" >}}

* Helm v2

```sh
$ helm install --name fission --namespace fission \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

* Helm v3

```sh
$ export FISSION_NAMESPACE="fission"
$ kubectl create namespace $FISSION_NAMESPACE
$ helm install --namespace $FISSION_NAMESPACE --name-template fission \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

{{< /tab >}}
{{< tab "OpenShift" >}}

请访问 [OpenShift]({{%relref "_index.zh.md" %}}) 查看详情

* Helm v2

```sh
$ helm install --name fission --namespace fission \
    --set logger.enableSecurityContext=true,prometheus.enabled=false \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

* Helm v3

```sh
$ export FISSION_NAMESPACE="fission"
$ kubectl create namespace $FISSION_NAMESPACE
$ helm install --namespace $FISSION_NAMESPACE --name-template fission \
    --set logger.enableSecurityContext=true,prometheus.enabled=false \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.tgz
```

{{< /tab >}}
{{< /tabs >}}

## 不使用 helm

此方法使用 `kubectl apply` 来安装 Fission。如果你需要任何的调整，你可以在应用到你的集群之前编辑 YAML 文件。
给 fission 安装创建命名空间。

```sh
$ kubectl create namespace fission 
```
  
{{% notice info %}}
* 如果你想安装到另一个命名空间，请考虑使用 `helm`。
{{% /notice %}}

选择一下命令 _之一_ 运行：

* 你可以选择使用 `fission-all` 或 `fission-core`。 这里我们使用 `fission-all` 来演示安装。

{{< tabs "fission-install-without-helm" >}}
{{< tab "Basic" >}}
```bash
$ kubectl -n fission apply -f \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}.yaml
```
{{< /tab >}}
{{< tab "Minikube" >}}
```bash
$ kubectl -n fission apply -f \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-all-{{% release-version %}}-minikube.yaml
```
{{< /tab >}}
{{< tab "OpenShift" >}}

请访问 [OpenShift]({{%relref "_index.zh.md" %}}) 查看详情

```bash 
$ kubectl -n fission apply -f \
    https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-core-{{% release-version %}}-openshift.yaml
```
{{< /tab >}}
{{< /tabs >}}

# 安装 Fission CLI

{{< tabs "fission-cli-install" >}}
{{< tab "MacOS" >}}
```sh
$ curl -Lo fission https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-cli-osx \
    && chmod +x fission && sudo mv fission /usr/local/bin/
```
{{< /tab >}}
{{< tab "Linux" >}}
```sh
$ curl -Lo fission https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-cli-linux \
    && chmod +x fission && sudo mv fission /usr/local/bin/
```
{{< /tab >}}
{{< tab "Windows" >}}
对于 windows，你可以在 WSL（linux子系统）中使用 linux binary。或者你可以下载 windows 可执行程序： [fission.exe](https://github.com/fission/fission/releases/download/{{% release-version %}}/fission-cli-windows.exe)
{{< /tab >}}
{{< /tabs >}}

# 运行示例

终于，你可以好用 Fission 了！

```sh
$ fission env create --name nodejs --image fission/node-env:{{% release-version %}}

$ curl -LO https://raw.githubusercontent.com/fission/fission/master/examples/nodejs/hello.js

$ fission function create --name hello --env nodejs --code hello.js

$ fission function test --name hello
Hello, world!
```

了解更多语言教程，访问 [语言]({{% relref "../languages/" %}})。

# 接下来是？

如果遇到问题，我们乐于帮忙 -- 请 [到
slack channel](https://join.slack.com/t/fissionio/shared_invite/enQtOTI3NjgyMjE5NzE3LTllODJiODBmYTBiYWUwMWQxZWRhNDhiZDMyN2EyNjAzMTFiYjE2Nzc1NzE0MTU4ZTg2MzVjMDQ1NWY3MGJhZmE) 
并请求帮助

迁出 [示例](https://github.com/fission/fission/tree/master/examples) 查看一些例子函数。
for some example functions.
