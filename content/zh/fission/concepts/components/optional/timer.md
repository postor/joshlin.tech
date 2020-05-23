---
title: "Timer（计时器）"
weight: 5
description: >
  周期的调用函数
---

# 简介

计时器工作方式和 kubernetes CronJob 类似但不同点在于计时器不像 CronJob 那样创建一个 Pod 来执行任务，而是
发送一个请求到路由去调用函数。它适用于需要周期性执行的后台任务。

# 图解

{{< img "../assets/timer.png" "Fig.1 计时器触发器" "30em" "1" >}}

1. 如果到了计划的时间，计时器调用定义的函数。
