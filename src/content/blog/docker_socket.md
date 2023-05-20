---
author: cosin
pubDatetime: 2023-05-20T13:42:30+08:00
title: docker ip地址问题
postSlug: ""
featured: false
draft: false
tags:
  - color-schemes
  - docs
ogImage: ""
description:
  docker容器ip地址区别
---
遇到容器内监听<code>127.0.0.1</code>的web服务无法连接问题。
### docker ip地址
在Docker容器中运行的Web服务应该监听容器内部的网络接口地址，通常是0.0.0.0或者127.0.0.1。

如果监听0.0.0.0，表示该Web服务可以通过容器的网络接口地址（通常是类似172.17.0.x的IP地址，docker 虚拟网卡地址）访问，这样可以使得该服务可以被容器内外的其他服务或者主机访问，从而提供服务。

如果监听127.0.0.1，表示该Web服务只能通过容器内部的网络接口地址（即localhost）访问，这样该服务只能在容器内部提供服务，无法被容器外的其他服务或者主机访问。

需要注意的是，如果Web服务需要对外提供服务，容器的网络配置需要进行相应的设置，例如将容器的网络端口映射到主机的网络端口上，以便外部网络可以访问该服务。
