---
author: cosin
pubDatetime: 2023-07-26T19:16:15+08:00 
title: 记录typescript使用技巧
featured: true
draft: false
tags:
  - color-schemes
  - docs
description:
  typescript使用技巧
---

## 获取对象中值为函数的类型
```ts
type GetOnlyFnKeys<T extends object> = {
  [K in keyof T]: T[K] extends Function ? K : never
}[keyof T]
```
将不是函数类型的值转为never再使用keyof
```ts
type GetOnlyFnProps<T extends object> = {
  [K in GetOnlyFnKeys<T>]: T[K]
}
```
## ts 的工具函数 UnGenericPromise<T> ，提取 Promise 中的泛型类型
```ts
type UnGenericPromise<T extends Promise<any>> = T extends Promise<infer U> ? U : never

```