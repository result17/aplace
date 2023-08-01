---
author: cosin
pubDatetime: 2023-07-23T03:58:38+08:00 
title: 面试题目合集
postSlug: interview
featured: true
tags:
  - release
description: 前端面试题
---

## 闭包
- 能够读取其他函数内部变量的函数
- 函数内部创建另一个函数
函数能够访问其定义时的环境中变量的方式得以实现。

## BFC创建条件
- 根元素 html
- float 不为none
- position为absolute或fixed
- display为inline-block，table-cell，table-caption， flex，inline-flex
- overflow不为visible

## 如何避免搜索引擎抓取网页内容
```shell
# robots.txt
     User-agent: *
     Disallow: /path/to/page
     Disallow: /path/to/directory/
```
```html
<meta name="robots" content="noindex">
```
## 输入一个URL后到用户完整看到网页发生了什么?
- 从url到http请求

## vue2和vue3的diff算法有什么异同？

## react-hook-form

## HTTP 请求
- 起始行 如 GET / http/1.1
- 标头 headers
- 空行
- body

http2压缩表头，将数据分为多个数据帧。目的时：
- 加快传输速度
- 能优先传输优先级高的帧

## HTTP 缓存
- cache-control: private 私有缓存
- 代理缓存 cache-control: no-cache no-store
### no-cache 和 no-store区别
Cache-Control: no-store和no-cache都是用来禁止缓存的，但它们的含义不同。no-store表示浏览器不应该缓存任何内容，无论是临时还是永久的。no-cache表示浏览器可以缓存内容，但如果内容发生了变化，浏览器应该重新获取最新的内容。

no-store是更严格的缓存策略，它可以防止浏览器缓存任何内容，即使是临时的。no-cache是一个更灵活的缓存策略，它允许浏览器缓存内容，但如果内容发生了变化，浏览器应该重新获取最新的内容。

在大多数情况下，no-cache是足够的，因为它可以防止浏览器缓存过时的内容。但是，如果您需要确保浏览器不缓存任何内容，则可以使用no-store。

不建议随意授予 no-store，因为你失去了 HTTP 和浏览器所拥有的许多优势，包括浏览器的后退/前进缓存。

# react 协调阶段 reconcile
## mount 阶段
beginWork -> reconcileChildren (创建子节点) -> 打上react side effect tags（按位或） -> completeWork -> current === null(判断是mount还是update) -> createInstance -> instance 会被挂载到workInProgress.stateNode上 -> finalizeInitialChildren (挂载属性 src alt等)
- hostsComponent div span html原生标签

## flushSync 和 useLayoutEffect
flushSync 和 useLayoutEffect 都是 React 提供的方法，用于在 DOM 上执行更新。但是，它们有不同的用途和行为。

flushSync 用于强制 React 立即更新 DOM。这通常用于在用户输入后立即更新 UI，或者在需要在用户看不到的地方更新 DOM 时。

useLayoutEffect 用于在 DOM 布局发生变化时更新组件。这通常用于在组件的大小或位置发生变化时更新 UI。

以下是 flushSync 和 useLayoutEffect 的比较表：

属性	            flushSync	            useLayoutEffect
什么时候使用	     立即更新 DOM	        在 DOM 布局发生变化时更新组件
是否阻止后续更新	      是	                  否
是否影响性能	         是	                   否
总体而言，flushSync 是一个强大的工具，可以用于在 React 应用程序中进行即时更新。但是，它应该谨慎使用，因为它会影响性能。useLayoutEffect 是更常用的选择，因为它不会阻止后续更新，也不会影响性能。

## vue3 生命周期 diff算法和react的区别

## https链接 tcp握手 网络层

基于链表的插入排序。
买卖股票最入门版。
实现 LRU。
实现一个 Hash 表。
最大频率栈。
归并排序。
快速排序。
非递归的中序排序。
用两个栈模拟队列。
树的层序 S 形遍历。
合并 K 个有序链表。

## useDeferredValue 
在 React 中，useDeferredValue 是 React Hooks 的一个函数，它可以帮助我们延迟一个值的计算，直到它被需要时才计算。这可以让我们在需要时提高性能，因为我们可以避免在不需要时计算值。

要使用 useDeferredValue，我们需要传递一个函数作为第一个参数，该函数将在我们需要值时计算它。第二个参数是可选的，它可以用来指定一个默认值，如果在指定的时间内没有计算出值，则使用该默认值。
```js
const [value, setValue] = useState(null);
const deferredValue = useDeferredValue(() => {
  // 在这里计算值
  return someValue;
});

// 使用值
console.log(value); // 在需要时计算值
```

## 精确判断类型
Object.prototype.toString.call 用于获取变量的字符串表示，其返回值为：
- "[object Number]"：表示变量是数字类型。
- "[object String]"：表示变量是字符串类型。
- "[object Boolean]"：表示变量是布尔类型。
- "[object Undefined]"：表示变量是undefined类型。
- "[object Object]"：表示变量是对象类型。
- "[object Symbol]"：表示变量是Symbol。
- "[object Null]"：表示变量是null。

## 写一个 mySetInterVal(fn, a, b),每次间隔 a,a+b,a+2b 的时间，然后写一个 myClear，停止上面的 mySetInterVal
```ts
const start = Date.now()

const mySetInterVal = (fn: Function, a: number, b: number) => {
  if (a < 0 || b < 0) throw new Error('Time Error')
  let timer: number | null = null;
  const task = (timeOut: number) => {
    timer = setTimeout(() => {
      fn()
      task(timeOut + b)
      console.log(`Duration is ${(Date.now() - start) / 1000}`)
    }, timeOut)
  }
  task(a)
  return () => clearTimeout(timer)
}

const clearFn = mySetInterVal(() => console.log('timeout'), 1000, 1000)

setTimeout(() => {
  clearFn()
  console.log(`Duration is ${(Date.now() - start) / 1000}. END!`)
}, 7000)

```

## 原型
为其他对象提供共享属性的对象

## 原型链
原型也是一个对象，所以它也具有自己的原型，以此往复直至null。
所有对象都有原型，所有函数(除了箭头函数外)都有prototype。

## OSI模型
物理层（Physical Layer）：负责传输比特流，即将数据转换成比特流并将其发送到物理介质上。
数据链路层（Data Link Layer）：负责将比特流转换成帧（Frame），并在物理介质上发送和接收帧。
网络层（Network Layer）：负责为数据包（Packet）选择最佳的路径，并将其从源主机发送到目标主机。
传输层（Transport Layer）：负责在源主机和目标主机之间建立端到端的连接，并提供可靠的数据传输服务。
会话层（Session Layer）：负责管理应用程序之间的会话和数据交换。
表示层（Presentation Layer）：负责将数据转换为应用程序可以理解的格式，并提供加密、压缩和解压缩等服务。
应用层（Application Layer）：提供各种网络应用程序，如电子邮件、文件传输和远程登录等。

## TCP/IP的网络分层模型
网络接口层（Network Interface Layer）：负责将数据转换成帧，并在物理介质上发送和接收帧。
网际层（Internet Layer）：负责为数据包选择最佳的路径，并将其从源主机发送到目标主机。
传输层（Transport Layer）：负责在源主机和目标主机之间建立端到端的连接，并提供可靠的数据传输服务。
应用层（Application Layer）：提供各种网络应用程序，如电子邮件、文件传输和远程登录等。

## 非受控组件
在React中，非受控组件（uncontrolled components）是指表单元素的值不受React组件的state或props变化控制的组件。相反，表单元素的值由DOM自身维护。

## useEffect和useLayoutEffect区别
在React中，useEffect和useLayoutEffect都是用于处理副作用的Hooks，但它们在执行时机和优先级上存在一些区别。

执行时机：
useEffect会在组件渲染完成后异步执行，而useLayoutEffect会在组件渲染完成后同步执行。

优先级：
useEffect的优先级较低，它不会阻塞浏览器渲染，因此可能会出现闪烁等问题。而useLayoutEffect的优先级较高，它会在浏览器渲染前同步执行，因此可以确保副作用的执行在浏览器渲染之前完成，避免出现闪烁等问题。

使用场景：
一般来说，当需要对DOM进行操作时，优先考虑使用useLayoutEffect，因为它可以确保DOM操作在浏览器渲染前完成，避免出现闪烁等问题。而当需要进行异步操作时，可以使用useEffect。

## JSX.Element VS ReactNode VS ReactElement
```ts
type ReactNode = ReactChild | ReactFragment | ReactPortal | boolean | null | undefined;
type ReactChild = ReactElement | ReactText;

type ReactText = string | number;
interface ReactElement<P = any, T extends string | JSXElementConstructor<any> = string | JSXElementConstructor<any>> {
        type: T;
        props: P;
        key: Key | null;
  }
declare global {
    namespace JSX {
        interface Element extends React.ReactElement<any, any> { }
        }
    }

export namespace JSX {
  export interface Element extends VNode {}
  export interface ElementClass {
    $props: {}
  }
  export interface ElementAttributesProperty {
    $props: {}
  }
  export interface IntrinsicElements extends NativeElements {
    // allow arbitrary elements
    // @ts-ignore suppress ts:2374 = Duplicate string index signature.
    [name: string]: any
  }
  export interface IntrinsicAttributes extends ReservedProps {}
}

```
jsx.Element在react中ReactElement，在vue为Vnode

## script 标签 async defer

## 事件循环

## react生命周期
[react生命周期](https://postimg.cc/dhxXhq9P)

## react class instance与fiber关系
_reactInternals 属性来访问组件对应的 fiber 对象。在 fiber 对象上，可以通过 stateNode 来访问当前 fiber 对应的组件实例

## React.PureComponent 注意事项
### 避免使用箭头函数。
```jsx
class Index extends React.PureComponent{}

export default class Father extends React.Component{
    render=()=> <Index callback={()=>{}}   />
}

```
###  PureComponent 的父组件是函数组件的情况，绑定函数要用 useCallback 或者 useMemo 处理。
```jsx

class Index extends React.PureComponent{}
export default function (){
    const callback = function handerCallback(){} /* 每一次函数组件执行重新声明一个新的callback，PureComponent浅比较会认为不想等，促使组件更新  */
    return <Index callback={callback}  />
}

```
## wasm想要使用sharedArrayBuffer的条件是什么？
响应头要加上
```http
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```

## npm version
~1.0.2 可以升级至 1.0.*
^1.0.2 可以升级至 1.*.*

## tree-shaking
Tree-Shaking指的是消除永远不会被执行的代码（dead code）

## Map和WeakMap有什么不同点
JavaScript中的map和weakMap都是用于存储键值对的数据结构，但它们之间有几个重要的不同点：

- 引用类型的处理方式：map可以存储任意类型的键和值，而weakMap只能存储对象作为键，并且这些对象是弱引用的，也就是说，如果一个对象没有被其他地方引用，它将被垃圾回收，无法被weakMap访问。

- 大小的追踪：map具有size属性，可以轻松地确定其中存储了多少个键值对，而weakMap没有size属性，并且无法轻松确定其中存储了多少个键值对。

- 迭代器：map具有迭代器，可以使用for...of循环或forEach方法对其中的键值对进行迭代，而weakMap没有迭代器，因为无法确定其中存储了多少个键值对。

性能：由于weakMap使用了弱引用，因此它的性能可能会比map略差一些，尤其是在存储大量对象时。

## ts中的！
! 在 TypeScript 中是用于强制类型转换的。例如，如果您有一个 number 类型的值，您可以使用 ! 将其强制转换为 string 类型：
```ts
const number = 123;
const string = number!;
``` 

## vue中的生命周期

## vue useForceUpdate
```ts
export function useForceUpdate() {
  const instance = getCurrentInstance()
  return () => instance.proxy.$forceUpdate()
}
```

## typescript 中类型和接口区别
- 类型能联合和交叉，接口不能
- 类型能定义对象和其他类型，接口不能
- 接口可以继承，而类型不能。
- 可以实现多个接口，而类型不可以。
- 接口可以拓展，而类型不可以。

## xss
XSS 是跨站点脚本攻击（Cross-Site Scripting）的简称
常见类型分为
- 反射型xss：常见在搜索中，接受用户输入并编码到URL中，并在页面中渲染出来。此时
```
?id=<script>alert(1)</script>
```
就有注入恶意脚本机会。
- 存储型xss：常见于带评论功能的网站中，攻击者上传恶意代码脚本至服务器数据库，当正常用户浏览到该帖子时就有机会执行恶意代码。
- DOM型xss：常见于一些用hash锚点的网站中，hash锚点传入恶意代码，js将恶意代码作为拼接dom结构的字符串插入至页面就会触发。
DOM XSS 是由于浏览器解析机制导致的漏洞，服务器不参与，而存储型与反射型都需要服务器响应参与

防范措施
- HTML转义或将特殊字符过滤
- 接口转义或将特殊字符过滤
- CSP 内容安全策略 限制恶意脚本执行
- 使用专门的xss过滤器

## vue 简单diff策略
- 通过key和vnode的type确定可以复用的dom节点。
- 在有相同key和相同个数的新旧节点比较中，维护变量lastIndex，遍历新节点如果在旧节点寻找到的索引小于lastIndex证明该节点需要移动，否则更新lastIndex。
### 节点复用
vnode被挂载后vnode.el就会保存实际的dom节点。复用就是讲新vnode.el指向旧vnode.el。
### 节点移动
移动节点，获取当前vnode节点的前一个vnode的el作为锚点，使用浏览器的dom api insertBefore插入。
### 新增节点
使用一个外层变量find，表示新vnode是否在旧vode中是否存在。若不存在则会被视为新增节点，找到新vnode的前一个vnode的el，若el存在则在插入至后面。若不存在则作为锚点元素mount的第一个子节点。
## 删除节点
若旧vnode没在新vnode中找到，则使用unmount函数写在旧vnode的el。

### 时间复杂度
o(n * n)

## vue2 双端Diff算法
双端 Diff 算法是一种同时对新旧两组子节点的两个端点进行比较的算法。
```js
while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
  if (oldStartVNode.key === newStartVNode.key) {
    // 调用 patch 函数在 oldStartVNode 与 newStartVNode 之间打补丁
    patch(oldStartVNode, newStartVNode, container)
    // 更新相关索引，指向下一个位置
    oldStartVNode = oldChildren[++oldStartIdx]
    newStartVNode = newChildren[++newStartIdx]
  } else if (oldEndVNode.key === newEndVNode.key) {
    patch(oldEndVNode, newEndVNode, container)
    oldEndVNode = oldChildren[--oldEndIdx]
    newEndVNode = newChildren[--newEndIdx]
  } else if (oldStartVNode.key === newEndVNode.key) {
    patch(oldStartVNode, newEndVNode, container)
    insert(oldStartVNode.el, container, oldEndVNode.el.nextSibling)
    oldStartVNode = oldChildren[++oldStartIdx]
    newEndVNode = newChildren[--newEndIdx]
  } else if (oldEndVNode.key === newStartVNode.key) {
    patch(oldEndVNode, newStartVNode, container)
    insert(oldEndVNode.el, container, oldStartVNode.el)
    oldEndVNode = oldChildren[--oldEndIdx]
    newStartVNode = newChildren[++newStartIdx]
  }
}
```
### 时间复杂度
理想情况 o(n)
相比简单 Diff 算法，双端 Diff 算法的优势在于，对于同样的更新场景，执行的DOM 移动操作次数更少。

## vue3 快速diff
### 预处理fdfadfd
首先patch相同的前置和后置节点。
### source数组和最长递增子序列
根据剩下的vnode节点数量构建source 数组，将用来存储新的一组子节点中的节点在旧的一组子节点中的位置索引，后面将会使用它计算出一个最长递增子序列，并用于辅助完成 DOM 移动的操作。
正常两次遍历构建source数组需要o(n * n)，使用索引表Map<vnode.key, new vnode.index>讲构建source数组时间复杂度降低o(n)。
根据source的最长递增子序列可以哪些元素可以不移动（节点在更新前后顺序没有发生变化）。

## react hook useContext 和 useReducer 使用事项
- 子组件props有引用类型,使用useMemo保存，并使用React.memo()缓存此props
- 单独封装provide，讲组件变为无状态组件
- 拆分多个context

## es module和cjs的相同点和不同点


## js代码执行
V8 和其他引擎之间最显着的区别是它的即时 (JIT) 编译器。
- 语法检查，错误则抛出错误不执行
- 生成AST树，用内存结构来表示代码结构
- 生成字节码
- 生成机器码 如果存在热点代码，v8会将其转化为机器码，直接执行

## v8内存回收
经典的内存回收算法
- 引用计数，每当一个对象被引用时，它的引用计数就会增加 1；每当一个对象不再被引用时，它的引用计数就会减少 1。当为0时，则可以进行内存回收。
引用计数法回收内存会产生碎片，当然碎片不是只有引用计数法才有的问题，所有的 GC 都需要面对碎片。
- 标记清除-算法 垃圾收集器首先标记内存中的所有对象，然后从根节点开始遍历，清除被引用对象和运行环境中对象的标记，剩下的标记对象不可访问，等待回收对象。
- 新生代老生代内存回收方法。新生代：分为from和to部分。v8在64操作系统中有32mb的新生代内存空间，首先gc检查from部分，查出存活对象，并将零散的内存，重新排列拷贝到to部分，不存活对象会被回收，最后from和to交换区域。
- 老生代：空间较大。保存由to提升的持久化引用类型数据。使用标记清楚来回收内存对象，同样将将零散的内存，重新排列成紧密的线性空间。

## vue Keepalive组件
KeepAlive 组件本身并不会渲染额外的内容，它的渲染函数最终只返回需要被 KeepAlive 的组件，我们把这个需要被 KeepAlive 的组件称为“内部组件

- shouldKeepAlive：该属性会被添加到“内部组件”的 vnode 对象上，这样当渲染器卸载“内部组件”时，可以通过检查该属性得知“内部组件”需要被KeepAlive
-keepAliveInstance：“内部组件”的 vnode 对象会持有 KeepAlive 组件实例，在 unmount 函数中会通过 keepAliveInstance 来访问 _deActivate 函数 失活的本质就是将组件所渲染的内容移动到隐藏容器中，而激活的本质是将组件所渲染的内容从隐藏容器中搬运回原来的容器。
- keptAlive：“内部组件”如果已经被缓存，则还会为其添加一个 keptAlive 标记。这样当“内部组件”需要重新渲染时，渲染器并不会重新挂载它，而会将其激活

## vue3 静态提升
将完全静态的vnode提到渲染函数以外。
纯静态的节点提升到渲染函数之外后，在渲染函数内只会持有对静态节点的引用。当响应式数据变化，并使得渲染函数重新执行时，并不会重新创建静态的虚拟节点，从而避免了额外的性能开销。

## TCP报文格式
TCP报文分为首部、数据和检验和
源端口号和目的端口号分别标识TCP报文的源端口和目的端口。

序列号和确认号是TCP报文的序列号和确认号，用于保证TCP报文的可靠传输。

头部长度表示TCP报文的首部长度，以4字节为单位。

## TCP 三次握手
- 客户端向服务器发送请求。SYN置为1，然后序列号为X
- 服务器响应， SYN和ACK置为1，确认号为X + 1，序列号为Y
- 客户端ACK置为1，序列号为X + 1,连接建立

### 三次握手目的
- 避免恶意攻击者大量发送SYN包，导致服务器宕机
- 避免因为网络延迟，错误与另一客户端建立连接

## TCP四次挥手
- 客户端发送FIN包
- 服务器接受FIN包后发送ACK包，表示已经确定收到FIN包
- 继续传完数据后，服务器发送FIN
- 客户端发送ACK包，确认收到FIN包

