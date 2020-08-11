---
title: "华为鲲鹏云安装 docker 和 docker-compose"
date: 2020-07-08T18:04:48+08:00
draft: false
---

## 什么是鲲鹏云？

https://www.huawei.com/cn/events/huaweiconnect2019/video-list/learn-about-huawei-cloud-kunpeng-cloud-services

安全是重中之重，抖音个娱乐软件都能威胁美国安全，那你说aws谷歌什么的，是吧

在华为云买主机的那里有个tab是鲲鹏主机，是arm架构的价格可以和默认tab的x86对比下，便宜又安全是吧

## 连上鲲鹏主机

习惯用 ubuntu，所以上 ubuntu18 系统，然后这一步很重要

```
百度搜索： 鲲鹏 apt 源
```

然后找到了这么一篇 


```
wget -O /etc/apt/sources.list https://repo.huaweicloud.com/repository/conf/Ubuntu-Ports-bionic.list
# 不行可以试试这个
wget -O /etc/apt/sources.list http://repo.huaweicloud.com/repository/conf/Ubuntu-Ports-bionic.list

apt-get update
```

然后就简单了

```
apt install docker.io

apt install docker-compose
```