---
author: cosin
pubDatetime: 2023-09-17T10:02:45+08:00 
title: java 学习
featured: false
draft: false
tags:
  - color-schemes
  - docs
description:
  java 学习记录
---

# volatile
volatile 关键字在 Java 中是一个非常重要的关键字，它可以确保变量的可见性。在多线程环境中，当一个线程修改了一个 volatile 变量时，其他线程能够立即看到这个变化。

volatile 关键字还可以防止指令重排序。在某些情况下，编译器可能会对指令进行重排序，以提高性能。但是，如果一个变量是 volatile 的，那么编译器就不能对其进行重排序。

volatile 关键字主要用于在多线程环境中确保变量的可见性和防止指令重排序。