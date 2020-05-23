---
title: "函数 Pod"
weight: 5
description: >
  加载和执行用户函数的地方
---

# 简介

函数 Pod 是用来处理客户端 HTTP 请求的。它包含两个容器：获取器和环境容器
containers: Fetcher and Environment Container. 

# 图解

{{< img "../assets/function-pod.png" "Fig.1 函数 Pod" "50em" "1" >}}

1. 获取器从自定义资源获取函数信息。
2. 从 StorageSvc 拉取部署 archive。
3. 保存 archive 到共享 volume。
4. 调用指定的环境容器的地址来启动函数配置。
5. 环境容器加载从 volume 用户函数。
6. 开始服务来自路由的请求。

# 环境容器

环境容器运行用户定义的函数并且是语言相关的。 
每个环境容器必须包含一个 HTTP 服务和一个函数加载器。

# 获取器

获取器负责从 StorageSvc 拉取部署 archive 并验证检查和以保证文件完整性。
