---
author: cosin
pubDatetime: 2023-08-04T02:21:37+08:00 
title: 2023面试记录
postSlug: 面试记录
featured: true
tags:
  - release
description: 面试记录
---

# 京北方 中邮外包
## umi版本
项目使用的版本是UMI^3.5.1，ant design pro自带。

## 事件循环
首先event loop不是ECMA Script的语言规范，它取决运行JavaScript的宿主环境，可分为浏览器和Node.js。

javascript是一门单线程的非阻塞的脚本语言。
单线程意味着，javascript代码在执行的任何时候，都只有一个主线程来处理所有的任务。

而非阻塞则是当代码需要进行一项异步任务（无法立刻返回结果，需要花一定时间才能返回的任务，如I/O事件）的时候，主线程会挂起（pending）这个任务，然后在异步任务返回结果的时候再根据一定规则去执行相应的回调，此规则就被称为事件循环。

目的是使得Javascript能够处理大量并发事件，并保证事情处理的顺序。

1. 执行一个宏任务（栈中没有就从事件队列中获取）
2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
3. 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
4. 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
5. 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

事件循环有一个或多个宏任务队列，不同宏任务队列之间有优先级。

常见的宏任务
``` 
script
I/O
setTimeout
setInterval
setImmediate
requestAnimationFrame
messageChannel
```
常见的微任务
```
process.nextTick
MutationObserver
Promise.then catch finally
```

```js
// https://juejin.cn/post/7202211586676064315
setTimeout(() => {
  console.log('setTimeout')
}, 0);

const { port1, port2 } = new MessageChannel();
port2.onmessage = function () {
  console.log('MessageChannel');
};
port1.postMessage('ping');

Promise.resolve().then(() => {
  console.log('Promise');
}); 
```

```js
setTimeout(() => console.log('jack'), 1000)  

new Promise((res) => setTimeout(() => res('jane'), 1000))
.then(res => console.log(`who is ${res}`)) 
// jack
// who is jane
```

### 渲染
透過 parse HTML 變成 DOM Tree 以及 parse CSS 變成 CSSOM Tree
並且把 DOM Tree 跟 CSSOM 進行合成變成最後的 Render Tree
並根據這個 Render Tree 去計算節點的位置去對整個畫面進行 Paint (繪製)
這整個過程就是 rendering
另外在修改 DOM 的狀況下，也會出現 Reflow (重排/回流) 或是 Repaint (重繪) 的現象
另外在規範上面有提到每一輪的 event loop task 結束後不一定會需要 rendering
原因是為了要達到每秒 60 fps 的效果 (60 frams per second)
每次瀏覽器繪出一個 frame 的間隔時間為 16.7 ms
如果在 16.7ms 內進行兩次 DOM 操作的話，是有可能不會出現兩次渲染的
另一個發生的原因是在畫面上如果沒有可見的影響的渲染的話，這次就是不必要的渲染

## es6 特性
- let const
- import export
- symbol
- 格式字符串
- async await es7
- 箭头函数
- for of 迭代器
- proxy reflect
- set map
- weakSet weakMap
- promise
- 生成器函数
- bigInt
- class extends super static
- （类装饰等）还是提案不算
- Array.from Array.of Array.isArray Array.includes Array.every Array.any Array.map Array.reduce
- Number.isNaN Object.is
- Object.setPrototypeOf Object.getPrototypeOf
- 解构赋值
- 函数默认值
- ES module
- script标签 defer async属性
- 函数剩余参数
- 对象简便写法 <code>{fn() {}}</code> {val}
```js
const f = (val) => val + 1
const g = (val) => val - 1
// '11'
console.log(f`1`)
// 0
console.log(g`1`)
```

## react常用hook
- useRef
- useState
- useReducer
- useContext
- useMemo
- useCallback
- useEffect
- useLayoutEffect
- useImperativeHandle

## useMemo作用
在初次渲染时，useMemo 返回不带参数调用 calculateValue 的结果。

在接下来的渲染中，如果依赖项没有发生改变，它将返回上次缓存的值；否则将再次调用 calculateValue，并返回最新结果。

### 作用
- 跳过代价昂贵的重新计算 
- 跳过组件的重新渲染 
- 记忆另一个 Hook 的依赖
```js
function Dropdown({ allItems, text }) {
  const searchOptions = useMemo(() => {
    return { matchMode: 'whole-word', text };
  }, [text]); // ✅ 只有当 text 改变时才会发生改变

  const visibleItems = useMemo(() => {
    return searchItems(allItems, searchOptions);
  }, [allItems, searchOptions]); // ✅ 只有当 allItems 或 serachOptions 改变时才会发生改变
  // ...
```
- 记忆一个函数 不推荐，应直接使用useCallback

## react fiber
是react16经过底层重构的新的虚拟dom结构，是react最小的执行更新单位，目的是使React实现基于优先级（react17的）可中断的更新

## 离职原因
旧项目进入维护状态，不再开发。因为个人技术发展原因而选择离职。

## 为啥转行
因为最初接触时有兴趣，社区很多攻略和文章供于学习。

## promise作用
Promise是异步容器，能表示异步操作状态，是现在JavaScript异步操作和核心，能力是在状态改变前继续执行代码，状态改变后执行后续的回调函数。
- 将嵌套的回调地狱改为链式调用
- await Promise （本质是使用闭包保存函数执行上下文）
- Promise.all 并发进行异步操作 使用一个timeout Promise限制最长请求时间
- Promise.allSettled 进行并发限制

```js
const rejectList = [];

const p1 = new Promise((resolve, reject) => {
  const timer = setTimeout(() => {
    resolve("can i continue ?");
    console.log("yes, I am continue");
  }, 3000);
  rejectList.push(reject);
  rejectList.push(() => clearTimeout(timer));
}).then((val) => console.log(val)).catch((err) => console.log("I was failed"));

const limit = new Promise((_, reject) => {
  setTimeout(() => reject("Timeout"), 2000);
});

Promise.all([p1, limit]).then((list) => {
  console.log(list);
}).catch((err) => {
  console.log(err);
  for (const reject of rejectList) {
    reject();
  }
});

```

## 单页应用spa路由

## 重绘重排
