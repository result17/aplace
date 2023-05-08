---
author: cosin
pubDatetime: 2023-05-08T02:39:14Z
title: 为什么日志总以std err输出？
postSlug: ""
featured: false
draft: false
tags:
  - color-schemes
  - docs
ogImage: ""
description:
  日志输出
---

## 优点一：不影响正常stdout
```shell
$ grep something file | your_filter | sort | uniq -c
```
当标准输出需要多个管道，比较耗费性能和时间时，日志通过stderr输出不会影响stdout。

## 优点二：没有行缓冲，立即flush
缓冲就意味不够实时，会产生丢失。
