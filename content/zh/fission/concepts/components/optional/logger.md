---
title: "Logger"
weight: 2
description: >
  记录并持久化函数日志
---

# 简介

日志被作为 DaemonSet 部署以便转发函数日志到一个中心化的数据服务来持久化。目前仅支持 InfluxDB 存储日志。

# 图解

{{< img "../assets/logger.png" "Fig.1 Logger" "45em" "1" >}}

1. 函数 Pod 中的容器向 docker 日志文件写入日志。
2. Logger 监视 Pod 创建/删除 事件
3. 如果 Pod 运行在同一个节点上则创建链接。（链接会在 Pod 消失时删除）。
4. Logger （`Fluentbit`）从连接中读取日志。
5. 日志被管道到 InfluxDB 持久化。

