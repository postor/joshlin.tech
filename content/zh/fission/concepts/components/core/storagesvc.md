---
title: "StorageSvc（存储服务）"
weight: 8
description: >
  源码和部署 archive 的家
---

# 简介

存储服务是所有大于 256KB package 的 archive 的家。构建器从存储服务拉取源码 archive 并上传部署 archive 到存储服务。
在函数 Pod 中的获取器也会针对函数拉取部署 archive。

# 图解

{{< img "../assets/storagesvc.png" "Fig.1 StorageSvc" "50em" "1" >}}

1. 客户端（命令行或 HTTP 请求）连接到控制器上的存储服务代理地址
2. 控制器代理请求到存储服务。
3. 存储服务保存上传的 archive 到 Kubernetes 持久 volume。
4. (A) 要构建源码 archive 到部署 archive，构建器 Pod 中的获取器下载源码 archive 并保存到共享 volume。一旦构建过程
完成，获取器上传部署 archive 到存储服务。
5. (B) 在函数 Pod 中的获取器获取（A）中的部署包给环境容器加载。
