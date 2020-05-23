---
title: "KubeWatcher"
weight: 3
description: >
  监视 Kubernetes 集群中资源变化的鹰眼
---

# 简介

Kubewatcher 监视 Kubernetes API 并调用和监视相对应的函数，发送监视时间到函数中。

控制器保持跟踪用户请求的监视及其对应的函数。Kubewatcher 监视基于这些请求的 API；
当一个监视事件发生的时候，它吧对象序列化并通过路由调用对应函数。

经过一些尝试，目前 Kubewatcher 和函数之间还没有一个可靠的消息通道。此项工作在
事项 #64 上跟踪。

# 图解

{{< img "../assets/kubewatcher.png" "Fig.1 KubeWatcher 触发器" "30em" "1" >}}

1. KubeWatcher 监视用户定义的资源变化。
2. 如果发生辩护则调用函数。
