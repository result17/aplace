---
author: cosin
pubDatetime: 2023-07-27T20:42:14+08:00 
title: vue设计与实现读书笔记
postSlug: interview
featured: true
tags:
  - release
description: vue设计与实现读书笔记
---

## vue如何提升用户开发体验
### 内部捕获当前发生错误的组件栈信息调用warn函数
```ts
export function warn(msg: string, ...args: any[]) {
  console.warn(`[Vue warn] ${msg}`, ...args)
}
```
### 优化log，自定义 formatter
打开 DevTools 的设置，然后勾选“Console”→“Enable custom formatters
```ts
const count = ref(0)
// Ref<0>
console.log(count)
```
### __DEV__ 既提高了开发体验，又减少正式环境的体积(基于rollup.js)
```ts
if (__DEV__) warn('smt')
```

##  特性开关
 __VUE_OPTIONS_API__
 使vue3能支持vue2组件选项API，而不是 Composition API

## 错误处理
捕获用户错误，并允许用户传递错误处理函数
```js
// utils.js
let handleError = null
export default {
  foo(fn) {
    callWithErrorHandling(fn)
  },
  // 用户可以调用该函数注册统一的错误处理函数
  registerErrorHandler(fn) {
    handleError = fn
  }
}
function callWithErrorHandling(fn) {
  try {
    fn && fn()
  } catch (e) {
    // 将捕获到的错误传递给用户的错误处理程序
    handleError(e)
  }
}
```
## 类型支持
使用typescript开发，并在runtime-core/src/apiDefineComponent.ts封装类型。

## 响应式系统
响应式系统的根本目的是劫持值的变化，在读取时保存副作用函数（effect），在更改时执行副作用函数。
```ts
// 存储副作用函数的桶
const bucket = new Set()
// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 将副作用函数 effect 添加到存储副作用函数的桶中
    bucket.add(effect)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 把副作用函数从桶里取出并执行
    bucket.forEach(fn => fn())
    // 返回 true 代表设置操作成功
    return true
  }
})
```
```ts
// 副作用函数
function effect() {
  document.body.innerText = obj.text
}
// 执行副作用函数，触发读取
effect()
// 1 秒后修改响应式数据
setTimeout(() => {
  obj.text = 'hello vue3'
}, 1000)
```
## 改进响应结构
```ts
// 存储副作用函数的桶
const bucket = new WeakMap()
```
```ts
const obj = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 没有 activeEffect，直接 return
    if (!activeEffect) return target[key]
    // 根据 target 从“桶”中取得 depsMap，它也是一个 Map 类型：key --> effects
    let depsMap = bucket.get(target)
    // 如果不存在 depsMap，那么新建一个 Map 并与 target 关联
    if (!depsMap) {
      bucket.set(target, (depsMap = new Map()))
    }
    // 再根据 key 从 depsMap 中取得 deps，它是一个 Set 类型，
    // 里面存储着所有与当前 key 相关联的副作用函数：effects
    let deps = depsMap.get(key)
    // 如果 deps 不存在，同样新建一个 Set 并与 key 关联
    if (!deps) {
      depsMap.set(key, (deps = new Set()))
    }
    // 最后将当前激活的副作用函数添加到“桶”里
    deps.add(activeEffect)
    // 返回属性值
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    // 设置属性值
    target[key] = newVal
    // 根据 target 从桶中取得 depsMap，它是 key --> effects
    const depsMap = bucket.get(target)
    if (!depsMap) return
    // 根据 key 取得所有副作用函数 effects
    const effects = depsMap.get(key)
    // 执行副作用函数
    effects && effects.forEach(fn => fn())
  }
})
```

## 分支管理与cleanup
```ts
const data = { ok: true, text: 'hello world' }
const obj = new Proxy(data, { /* ... */ })
effect(function effectFn() {
  document.body.innerText = obj.ok ? obj.text : 'not'
})
```
当obj.ok = true时依赖收集关系如下：
```
data
    └── ok
        └── effectFn
    └── text
        └── effectFn
```
但当obj.ok = false时，上面的代码无法正确处理此时的依赖关系，还是上图的情况。
```ts
// 用一个全局变量存储被注册的副作用函数
let activeEffect
function effect(fn) {
  const effectFn = () => {
    // 调用 cleanup 函数完成清楚工作
    cleanup(effectFn)
    // 当 effectFn 执行时，将其设置为当前激活的副作用函数
    activeEffect = effectFn
    fn()
  }
  // activeEffect.deps 用来存储所有与该副作用函数相关联的依赖集合
  effectFn.deps = []
  // 执行副作用函数
  effectFn()
}

function track(target, key) {
  // 没有 activeEffect，直接 return
  if (!activeEffect) return
  let depsMap = bucket.get(target)
  if (!depsMap) {
    bucket.set(target, (depsMap = new Map()))
  }
  let deps = depsMap.get(key)
  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  // 把当前激活的副作用函数添加到依赖集合 deps 中
  deps.add(activeEffect)
  // deps 就是一个与当前副作用函数存在联系的依赖集合
  // 将其添加到 activeEffect.deps 数组中
  activeEffect.deps.push(deps) // 新增
}
```
```ts
function cleanup(effectFn) {
  // 遍历 effectFn.deps 数组
  for (let i = 0; i < effectFn.deps.length; i++) {
    // deps 是依赖集合
    const deps = effectFn.deps[i]
    // 将 effectFn 从依赖集合中移除
    deps.delete(effectFn)
  }
  // 最后需要重置 effectFn.deps 数组
  effectFn.deps.length = 0
}
```
