---
author: cosin
pubDatetime: 2024-08-02T06:47:07.139Z
title: java 学习
featured: false
draft: false
tags:
  - color-schemes
  - docs
description:
  java 学习记录
---
## Java 后端中Entity、Model和POJO区别
### Entity（实体）
实体通常对应数据库中的一个表，它包含了表中字段的映射以及相关的业务逻辑。在 JPA（Java Persistence API）中，实体类会用注解来标记，例如 @Entity 和 @Table。
### Model（模型）
模型通常是一个简单的数据传输对象（DTO），用于表示数据结构，不包含业务逻辑。在一些项目中，"Model" 这个词可能被用来泛指用于数据表示的类，包括但不限于数据库表的映射。
### POJO（Plain Old Java Object，普通老式Java对象）
POJO 是一个简单的Java对象，不遵循特定的Java规范，不实现任何特定的接口或继承自特定的类。在 MyBatis 中，POJO 通常用于映射数据库表的行，只包含属性和标准的 getter/setter 方法。

## volatile
volatile 关键字在 Java 中是一个非常重要的关键字，它可以确保变量的可见性。在多线程环境中，当一个线程修改了一个 volatile 变量时，其他线程能够立即看到这个变化。

volatile 关键字还可以防止指令重排序。在某些情况下，编译器可能会对指令进行重排序，以提高性能。但是，如果一个变量是 volatile 的，那么编译器就不能对其进行重排序。

volatile 关键字主要用于在多线程环境中确保变量的可见性和防止指令重排序。