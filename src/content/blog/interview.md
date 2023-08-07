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
- 函数内部创建另一个函数 函数能够访问其定义时的环境中变量的方式得以实现。

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

Cache-Control:
no-store和no-cache都是用来禁止缓存的，但它们的含义不同。no-store表示浏览器不应该缓存任何内容，无论是临时还是永久的。no-cache表示浏览器可以缓存内容，但如果内容发生了变化，浏览器应该重新获取最新的内容。

no-store是更严格的缓存策略，它可以防止浏览器缓存任何内容，即使是临时的。no-cache是一个更灵活的缓存策略，它允许浏览器缓存内容，但如果内容发生了变化，浏览器应该重新获取最新的内容。

在大多数情况下，no-cache是足够的，因为它可以防止浏览器缓存过时的内容。但是，如果您需要确保浏览器不缓存任何内容，则可以使用no-store。

不建议随意授予 no-store，因为你失去了 HTTP
和浏览器所拥有的许多优势，包括浏览器的后退/前进缓存。

# react 协调阶段 reconcile

## mount 阶段

beginWork -> reconcileChildren (创建子节点) -> 打上react side effect
tags（按位或） -> completeWork -> current === null(判断是mount还是update) ->
createInstance -> instance 会被挂载到workInProgress.stateNode上 ->
finalizeInitialChildren (挂载属性 src alt等)

- hostsComponent div span html原生标签

## flushSync 和 useLayoutEffect

flushSync 和 useLayoutEffect 都是 React 提供的方法，用于在 DOM
上执行更新。但是，它们有不同的用途和行为。

flushSync 用于强制 React 立即更新 DOM。这通常用于在用户输入后立即更新
UI，或者在需要在用户看不到的地方更新 DOM 时。

useLayoutEffect 用于在 DOM
布局发生变化时更新组件。这通常用于在组件的大小或位置发生变化时更新 UI。

以下是 flushSync 和 useLayoutEffect 的比较表：

属性	 flushSync	 useLayoutEffect 什么时候使用	 立即更新 DOM	 在 DOM
布局发生变化时更新组件 是否阻止后续更新	 是	 否 是否影响性能	 是	 否
总体而言，flushSync 是一个强大的工具，可以用于在 React
应用程序中进行即时更新。但是，它应该谨慎使用，因为它会影响性能。useLayoutEffect
是更常用的选择，因为它不会阻止后续更新，也不会影响性能。

## vue3 生命周期 diff算法和react的区别

## https链接 tcp握手 网络层

基于链表的插入排序。 买卖股票最入门版。 实现 LRU。 实现一个 Hash 表。
最大频率栈。 归并排序。 快速排序。 非递归的中序排序。 用两个栈模拟队列。
树的层序 S 形遍历。 合并 K 个有序链表。

## useDeferredValue

在 React 中，useDeferredValue 是 React Hooks
的一个函数，它可以帮助我们延迟一个值的计算，直到它被需要时才计算。这可以让我们在需要时提高性能，因为我们可以避免在不需要时计算值。

要使用
useDeferredValue，我们需要传递一个函数作为第一个参数，该函数将在我们需要值时计算它。第二个参数是可选的，它可以用来指定一个默认值，如果在指定的时间内没有计算出值，则使用该默认值。

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
const start = Date.now();

const mySetInterVal = (fn: Function, a: number, b: number) => {
  if (a < 0 || b < 0) throw new Error("Time Error");
  let timer: number | null = null;
  const task = (timeOut: number) => {
    timer = setTimeout(() => {
      fn();
      task(timeOut + b);
      console.log(`Duration is ${(Date.now() - start) / 1000}`);
    }, timeOut);
  };
  task(a);
  return () => clearTimeout(timer);
};

const clearFn = mySetInterVal(() => console.log("timeout"), 1000, 1000);

setTimeout(() => {
  clearFn();
  console.log(`Duration is ${(Date.now() - start) / 1000}. END!`);
}, 7000);
```

## 原型

为其他对象提供共享属性的对象

## 原型链

原型也是一个对象，所以它也具有自己的原型，以此往复直至null。
所有对象都有原型，所有函数(除了箭头函数外)都有prototype。

## OSI模型

物理层（Physical
Layer）：负责传输比特流，即将数据转换成比特流并将其发送到物理介质上。
数据链路层（Data Link
Layer）：负责将比特流转换成帧（Frame），并在物理介质上发送和接收帧。
网络层（Network
Layer）：负责为数据包（Packet）选择最佳的路径，并将其从源主机发送到目标主机。
传输层（Transport
Layer）：负责在源主机和目标主机之间建立端到端的连接，并提供可靠的数据传输服务。
会话层（Session Layer）：负责管理应用程序之间的会话和数据交换。
表示层（Presentation
Layer）：负责将数据转换为应用程序可以理解的格式，并提供加密、压缩和解压缩等服务。
应用层（Application
Layer）：提供各种网络应用程序，如电子邮件、文件传输和远程登录等。
### 为什么表现层、会话层具体实现很少
[为什么表现层、会话层实现很少](https://www.zhihu.com/question/58798786)
SDP
会话描述协议Session Description Protocol (SDP) 是一个描述多媒体连接内容的协议，例如分辨率，格式，编码，加密算法等。所以在数据传输时两端都能够理解彼此的数据。本质上，这些描述内容的元数据并不是媒体流本身。

## TCP/IP的网络分层模型

网络接口层（Network Interface
Layer）：负责将数据转换成帧，并在物理介质上发送和接收帧。 网际层（Internet
Layer）：负责为数据包选择最佳的路径，并将其从源主机发送到目标主机。
传输层（Transport
Layer）：负责在源主机和目标主机之间建立端到端的连接，并提供可靠的数据传输服务。
应用层（Application
Layer）：提供各种网络应用程序，如电子邮件、文件传输和远程登录等。

## 非受控组件

在React中，非受控组件（uncontrolled
components）是指表单元素的值不受React组件的state或props变化控制的组件。相反，表单元素的值由DOM自身维护。
根本原因，不同应用程序会话各异，难于抽象到具体协议。

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
type ReactNode =
  | ReactChild
  | ReactFragment
  | ReactPortal
  | boolean
  | null
  | undefined;
type ReactChild = ReactElement | ReactText;

type ReactText = string | number;
interface ReactElement<
  P = any,
  T extends string | JSXElementConstructor<any> =
    | string
    | JSXElementConstructor<any>,
> {
  type: T;
  props: P;
  key: Key | null;
}
declare global {
  namespace JSX {
    interface Element extends React.ReactElement<any, any> {}
  }
}

export namespace JSX {
  export interface Element extends VNode {}
  export interface ElementClass {
    $props: {};
  }
  export interface ElementAttributesProperty {
    $props: {};
  }
  export interface IntrinsicElements extends NativeElements {
    // allow arbitrary elements
    // @ts-ignore suppress ts:2374 = Duplicate string index signature.
    [name: string]: any;
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

_reactInternals 属性来访问组件对应的 fiber 对象。在 fiber 对象上，可以通过
stateNode 来访问当前 fiber 对应的组件实例

## React.PureComponent 注意事项

### 避免使用箭头函数。

```jsx
class Index extends React.PureComponent {}

export default class Father extends React.Component {
  render = () => <Index callback={() => {}} />;
}
```

### PureComponent 的父组件是函数组件的情况，绑定函数要用 useCallback 或者 useMemo 处理。

```jsx
class Index extends React.PureComponent {}
export default function () {
  const callback =
    function handerCallback() {}; /* 每一次函数组件执行重新声明一个新的callback，PureComponent浅比较会认为不想等，促使组件更新  */
  return <Index callback={callback} />;
}
```

## wasm想要使用sharedArrayBuffer的条件是什么？

响应头要加上

```http
Cross-Origin-Embedder-Policy: require-corp
Cross-Origin-Opener-Policy: same-origin
```

## npm version

~1.0.2 可以升级至 1.0.* ^1.0.2 可以升级至 1._._

## tree-shaking

Tree-Shaking指的是消除永远不会被执行的代码（dead code）

## Map和WeakMap有什么不同点

JavaScript中的map和weakMap都是用于存储键值对的数据结构，但它们之间有几个重要的不同点：

- 引用类型的处理方式：map可以存储任意类型的键和值，而weakMap只能存储对象作为键，并且这些对象是弱引用的，也就是说，如果一个对象没有被其他地方引用，它将被垃圾回收，无法被weakMap访问。

- 大小的追踪：map具有size属性，可以轻松地确定其中存储了多少个键值对，而weakMap没有size属性，并且无法轻松确定其中存储了多少个键值对。

- 迭代器：map具有迭代器，可以使用for...of循环或forEach方法对其中的键值对进行迭代，而weakMap没有迭代器，因为无法确定其中存储了多少个键值对。

性能：由于weakMap使用了弱引用，因此它的性能可能会比map略差一些，尤其是在存储大量对象时。

## ts中的！

! 在 TypeScript 中是用于强制类型转换的。例如，如果您有一个 number
类型的值，您可以使用 ! 将其强制转换为 string 类型：

```ts
const number = 123;
const string = number!;
```

## vue中的生命周期

## vue useForceUpdate

```ts
import { getCurrentInstance } from "vue";
export function useForceUpdate() {
  const instance = getCurrentInstance();
  return () => instance.proxy.$forceUpdate();
}
```

## typescript 中类型和接口区别

- 类型能联合和交叉，接口不能
- 类型能定义对象和其他类型，接口不能
- 接口可以继承，而类型不能。
- 可以实现多个接口，而类型不可以。
- 接口可以拓展，而类型不可以。

## xss

XSS 是跨站点脚本攻击（Cross-Site Scripting）的简称 常见类型分为

- 反射型xss：常见在搜索中，接受用户输入并编码到URL中，并在页面中渲染出来。此时

```
?id=<script>alert(1)</script>
```

就有注入恶意脚本机会。

- 存储型xss：常见于带评论功能的网站中，攻击者上传恶意代码脚本至服务器数据库，当正常用户浏览到该帖子时就有机会执行恶意代码。
- DOM型xss：常见于一些用hash锚点的网站中，hash锚点传入恶意代码，js将恶意代码作为拼接dom结构的字符串插入至页面就会触发。
  DOM XSS
  是由于浏览器解析机制导致的漏洞，服务器不参与，而存储型与反射型都需要服务器响应参与

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

移动节点，获取当前vnode节点的前一个vnode的el作为锚点，使用浏览器的dom api
insertBefore插入。

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
    patch(oldStartVNode, newStartVNode, container);
    // 更新相关索引，指向下一个位置
    oldStartVNode = oldChildren[++oldStartIdx];
    newStartVNode = newChildren[++newStartIdx];
  } else if (oldEndVNode.key === newEndVNode.key) {
    patch(oldEndVNode, newEndVNode, container);
    oldEndVNode = oldChildren[--oldEndIdx];
    newEndVNode = newChildren[--newEndIdx];
  } else if (oldStartVNode.key === newEndVNode.key) {
    patch(oldStartVNode, newEndVNode, container);
    insert(oldStartVNode.el, container, oldEndVNode.el.nextSibling);
    oldStartVNode = oldChildren[++oldStartIdx];
    newEndVNode = newChildren[--newEndIdx];
  } else if (oldEndVNode.key === newStartVNode.key) {
    patch(oldEndVNode, newStartVNode, container);
    insert(oldEndVNode.el, container, oldStartVNode.el);
    oldEndVNode = oldChildren[--oldEndIdx];
    newStartVNode = newChildren[++newStartIdx];
  }
}
```

### 时间复杂度

理想情况 o(n) 相比简单 Diff 算法，双端 Diff
算法的优势在于，对于同样的更新场景，执行的DOM 移动操作次数更少。

## vue3 快速diff

### 预处理fdfadfd

首先patch相同的前置和后置节点。

### source数组和最长递增子序列

根据剩下的vnode节点数量构建source
数组，将用来存储新的一组子节点中的节点在旧的一组子节点中的位置索引，后面将会使用它计算出一个最长递增子序列，并用于辅助完成
DOM 移动的操作。 正常两次遍历构建source数组需要o(n *
n)，使用索引表Map<vnode.key, new
vnode.index>讲构建source数组时间复杂度降低o(n)。
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

- 引用计数，每当一个对象被引用时，它的引用计数就会增加
  1；每当一个对象不再被引用时，它的引用计数就会减少
  1。当为0时，则可以进行内存回收。
  引用计数法回收内存会产生碎片，当然碎片不是只有引用计数法才有的问题，所有的 GC
  都需要面对碎片。
- 标记清除-算法
  垃圾收集器首先标记内存中的所有对象，然后从根节点开始遍历，清除被引用对象和运行环境中对象的标记，剩下的标记对象不可访问，等待回收对象。
- 新生代老生代内存回收方法。新生代：分为from和to部分。v8在64操作系统中有32mb的新生代内存空间，首先gc检查from部分，查出存活对象，并将零散的内存，重新排列拷贝到to部分，不存活对象会被回收，最后from和to交换区域。
- 老生代：空间较大。保存由to提升的持久化引用类型数据。使用标记清楚来回收内存对象，同样将将零散的内存，重新排列成紧密的线性空间。

## vue Keepalive组件

KeepAlive 组件本身并不会渲染额外的内容，它的渲染函数最终只返回需要被 KeepAlive
的组件，我们把这个需要被 KeepAlive 的组件称为“内部组件

- shouldKeepAlive：该属性会被添加到“内部组件”的 vnode
  对象上，这样当渲染器卸载“内部组件”时，可以通过检查该属性得知“内部组件”需要被KeepAlive
  -keepAliveInstance：“内部组件”的 vnode 对象会持有 KeepAlive 组件实例，在
  unmount 函数中会通过 keepAliveInstance 来访问 _deActivate 函数
  失活的本质就是将组件所渲染的内容移动到隐藏容器中，而激活的本质是将组件所渲染的内容从隐藏容器中搬运回原来的容器。
- keptAlive：“内部组件”如果已经被缓存，则还会为其添加一个 keptAlive
  标记。这样当“内部组件”需要重新渲染时，渲染器并不会重新挂载它，而会将其激活

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

## 为什么 script 标签要放在body后面？

由于请求并执行script标签会阻碍页面渲染，此时dom树还没构建好，页面没法渲染完整。

## 解决是 script 标签内添加 async 和 defer 属性

- async 并行请求，加载完成尽快解析和执行
- defer 并行请求，加载完成在DOMContentLoaded
  事件之前执行的，加载ESM脚本时会自动延迟处理。

## 动态加载模块

```js
squareBtn.addEventListener("click", () => {
  import("/js-examples/modules/dynamic-module-imports/modules/square.js").then(
    (Module) => {
      let square1 = new Module.Square(
        myCanvas.ctx,
        myCanvas.listId,
        50,
        50,
        100,
        "blue",
      );
    },
  );
});
```

## vite 进执行.ts文件的转译工作

Vite 仅执行 .ts 文件的转译工作，并不执行 任何类型检查。并假定类型检查已经被你的
IDE 或构建过程处理了。

## @import和Link引入css文件

@import 和 Link 的区别如下：

@import 是 CSS 的内部引入语法，而 Link 是 HTML 的引入语法。 @import 只能引入 CSS
文件，而 Link 可以引入任何类型的文件。 @import 引入的 CSS 文件会在当前 CSS
文件中被解析，而 Link 引入的 CSS 文件会在 HTML 文档中被解析。 @import 引入的 CSS
文件会覆盖当前 CSS 文件中定义的同名的样式，而 Link 引入的 CSS 文件不会覆盖当前
CSS 文件中定义的同名的样式。 因此，在大多数情况下，我们应该使用 Link 引入 CSS
文件，而不是使用 @import。但是，如果我们需要在 CSS 文件中引入其他 CSS
文件，那么我们可以使用 @import。

## css优先级

样式来源	顺序	类型

- 内联样式	1	ID 选择器
- 内部样式	2	类选择器
- 外部样式	3	元素选择器
- 属性选择器	4	属性选择器
- 伪类选择器	5	伪类选择器
- 伪元素选择器	6	伪元素选择器

## redux的compose狗屎代码

自以为是：充分利用函数式编程，将闭包函数当作参数互相传递，恶心至极

```js
// 计算一个柯里化函数？，函数执行顺序是从右到左
const compose = (...funcs) => {
  return funcs.reduce((f, g) => (x) => f(g(x)));
};
function logMiddleware() {
  /* 第二层在reduce中被执行 */
  return (next) => {
    /* 返回增强后的dispatch */
    console.log(next);
    return (action) => {
      const { type } = action;
      console.log("第一个中间件:", type);
      return next(action);
    };
  };
}
function logMiddleware2() {
  /* 第二层在reduce中被执行 */
  return (next) => {
    console.log("事件参数");
    /* 返回增强后的dispatch */
    console.log(next);
    // 用返回值作为下一个函数的参数
    console.log("用返回值作为下一个中间件的参数");
    return (action) => {
      const { type } = action;
      console.log("第二个中间件:", type);
      return next(action);
    };
  };
}

// 联合函数
const cf = compose(logMiddleware(), logMiddleware2());

// 函数参数
cf(({ type }) => {
  console.log(`what is ${type}`);
})({
  type: 1,
});
```

## redux三大原则

- 单向数据流
- state只读，不可变 每次返回新state
- 纯函数执行 dispatch reducer纯函数

## redux思想

- 发布订阅模式
- 中间件思想

## redux缺点

- 不可能将每一个需要状态的组件都用 subscribe / unSubscribe 来进行订阅
- 状态改变，所有订阅状态的子组件更新

## react-redux 核心要点

- 子组件能只订阅所需的state
- 避免不必要的组件更新

### 提供Provider组件

- Provider 作用就是保存 redux 中的 store ，分配给所有需要 state 的子孙组件。

### 提供connect高阶组件

- 将store所需的state映射为组件的props
- 自动传入一个dispatch函数用于改变store的state
- 当需要的state，有变化的时候

## vite

### 使用模块路径重写支持了裸模块导出

### 文件变动时vite做了什么

- 文件变动vite是使用chokidar进行监控的

```ts
const onHMRUpdate = async (file: string, configOnly: boolean) => {
  if (serverConfig.hmr !== false) {
    try {
      await handleHMRUpdate(file, server, configOnly);
    } catch (err) {
      ws.send({
        type: "error",
        err: prepareError(err),
      });
    }
  }
};

const onFileAddUnlink = async (file: string) => {
  file = normalizePath(file);
  await handleFileAddUnlink(file, server);
  await onHMRUpdate(file, true);
};

watcher.on("change", async (file) => {
  file = normalizePath(file);
  // invalidate module graph cache on file change
  moduleGraph.onFileChange(file);

  await onHMRUpdate(file, false);
});

watcher.on("add", onFileAddUnlink);
watcher.on("unlink", onFileAddUnlink);
```

handleHMRUpdate最终会调用restartServer，重新进行ws连接，最后发送

```ts
 ws.send({
    type: 'update',
    updates,
  })
}
```

## 内存泄漏

- 使用performance标签页，如果gc后，内存还没有降低，可以排查代码
- 全局变量 使用严格模式 ide检查
- 事件处理函数没有卸载
- 错误使用闭包
- 可以举weakMap作为例子
- 遗忘的定时器

## webpack流程

- 读取配置
- 创建compiler对象，加载plugin插件
- 读取文件入口
- 开始编译，执行loader
- 执行loader,编译完成
- 根据入口和依赖关系，组装chunk，添加输出列表
- 输出列表输出至文件系统

## source map

source map
是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要
soucre map。

## ReactDOM.render 看初始化流程

- 创建FiberRoot节点
- updateContainer调用

```ts
// 非批量更新
unbatchedUpdates(() => {
  /*  开始更新  */
  updateContainer(children, fiberRoot, parentComponent, callback);
});
```

- 首先计算优先级lane，react17之前使用过期时间
- 创建update对象，将update对象放进更新队列updateQueue
- 调用scheduleUpdateOnFiber，进入调度更新流程

## scheduleUpdateOnFiber

- unbatch(同步)直接进入performSyncWorkOnRoot，进入调和流程，render和commit
- 否则是useState和setState进入enSureRootIsScheduled调度流程
- 当前的执行任务类型为 NoContext ，说明当前任务是非可控的，那么会调用
  flushSyncCallbackQueue 方法。 
### 总结
- performSyncOnRoot 会立刻进入调和阶段 里面调用renderRootSync（render阶段 分为 beginWork（向下调和）和commitUnitOfWork（向上递归））和CommitRoot
- enSureRootIsScheduled 调度流程 首先 markUpdateLaneFromFiberToRoot 向上递归更新优先级
- flushSyncCallbackQueue 立即执行更新队列的任务

## 调度过程
### 控制调度入口
入口的函数ensureRootIsScheduled，里面有个关键的判断

```ts
/* 如果两者相等，那么说明是在一次更新中，那么将退出 */
if (existingCallbackPriority === newCallbackPriority) {
  return;
}
```
这个就是批量更新的原理，只有第一次会进入调度

对于<code>legacy sync</code>模式最后的更新任务是performSyncWorkOnRoot。
对于<code>Concurrent</code>模式最后的更新任务是performConcurrentWorkOnRoot

### 进入调度任务
```ts
function scheduleSyncCallback(callback) {
    if (syncQueue === null) {
        /* 如果队列为空 */
        syncQueue = [callback];
        /* 放入调度任务 */
        immediateQueueCallbackNode = Scheduler_scheduleCallback(Scheduler_ImmediatePriority, flushSyncCallbackQueueImpl);
    }else{
        /* 如果任务队列不为空，那么将任务放入队列中。 */
        syncQueue.push(callback);
    }
} 
```
Scheduler_scheduleCallback 就是在调度章节讲的调度的执行方法，本质上就是通过 MessageChannel 向浏览器请求下一空闲帧，在空闲帧中执行更新任务

## fiber
诞生于react16版本，react中最小执行单位。可以理解为react中的vdom。
### 作用
react15采用递归更新，但项目过大，会造成卡顿。react16以后采用过期时间，17采用优先级，让出执行时间给渲染进程，避免卡顿。等待有空闲时间，再恢复执行，实现了可中断调度机制。
### react fiber ReactElement和dom之间的关系
- ReactElement可以由JSX语法创建，是用户视图层的表象，上面挂载了props和children，
- Dom 浏览器表象
- fiber就是连接Dom和ReactElement桥梁。element变化引起更新流程都会使调和改变。然后对于元素，形成新DOM做视图改变。

### fiber机构
```js
function FiberNode(){

  this.tag = tag;                  // fiber 标签 证明是什么类型fiber。
  this.key = key;                  // key调和子节点时候用到。 
  this.type = null;                // dom元素是对应的元素类型，比如div，组件指向组件对应的类或者函数。  
  this.stateNode = null;           // 指向对应的真实dom元素，类组件指向组件实例，可以被ref获取。
 
  this.return = null;              // 指向父级fiber
  this.child = null;               // 指向子级fiber
  this.sibling = null;             // 指向兄弟fiber 
  this.index = 0;                  // 索引

  this.ref = null;                 // ref指向，ref函数，或者ref对象。

  this.pendingProps = pendingProps;// 在一次更新中，代表element创建
  this.memoizedProps = null;       // 记录上一次更新完毕后的props
  // 副作用
  this.updateQueue = null;         // 类组件存放setState更新队列，函数组件存放
  this.memoizedState = null;       // 类组件保存state信息，函数组件保存hooks信息，dom元素为null
  this.dependencies = null;        // context或是时间的依赖项

  this.mode = mode;                //描述fiber树的模式，比如 ConcurrentMode 模式

  this.effectTag = NoEffect;       // effect标签，用于收集effectList
  this.nextEffect = null;          // 指向下一个effect

  this.firstEffect = null;         // 第一个effect
  this.lastEffect = null;          // 最后一个effect

  this.expirationTime = NoWork;    // 通过不同过期时间，判断任务是否过期， 在v17版本用lane表示。

  this.alternate = null;           //双缓存树，指向缓存的fiber。更新阶段，两颗树互相交替。
}
```

## fiberRoot和rootFiber
- 应用根节点
- reactDom.render 渲染出来叫rootFiber，可以多个

## workInProgress 树
是在内存上构建的fiber树，所有更新都发生在workInProgress树，更新完就是最新的状态，此时让fiberRoot.current = workInProgress树（通过alternate属性连接），则渲染视图。

## react架构
Scheduler - reconciler- render（react dom）

### render两个阶段
render会先调用renderRootSync
```js
function renderRootSync(root,lanes){
    workLoopSync();
    /* workLoop完毕后，证明所有节点都遍历完毕，那么重置状态，进入 commit 阶段 */
    workInProgressRoot = null;
    workInProgressRootRenderLanes = NoLanes;
}
```
```js
// legacy mode
function workLoopSync() {
  /* 循环执行 performUnitOfWork ，一直到 workInProgress 为空 */
  while (workInProgress !== null) {
  // concurrent mode
  // while (workInProgress !== null & !sholdYield()) {
    performUnitOfWork(workInProgress);
  }
}
```
- beginWork 逐级向下对children调和，实例类组件，执行函数组件，打上不同的effect tag
- completeUnitOfWork 向上归并，由sibling则返回兄弟，没有则返回父级。期间形成effect链，进行事件收集，处理style，className。

### commit阶段
处理componentDidMount ，函数组件的 useEffect ，useLayoutEffect ；

维护节点状态 添加节点，更新节点，删除节点

- beforeMutation 执行dom操作前 getSnapshotBeforeUpdate 异步调用useEffect
- mutation 执行dom操作  置空 ref ，在 ref 章节讲到对于 ref 的处理。
对新增元素，更新元素，删除元素。进行真实的 DOM 操作。
- layout 执行dom操作后 对类组件来说会执行生命周期，setState回调，函数组件执行useLayoutEffect钩子
如果有 ref ，会重新赋值 ref

### React fiber如何找到state发生变化的组件
假设组件A发生更新，先向上递归更新父级链的childLanes，接下来从RootFiber向下调和时，发现childLanes等于当前更新优先级，说明它的child链有更新任务，否则向下调和。

## useDeferredValue
useDeferredValue 作为性能优化的手段。当你的 UI 某个部分重新渲染很慢、没有简单的优化方法，同时你又希望避免它阻塞其他 UI 的渲染时，使用 useDeferredValue 很有帮助。

## React 生命周期
- componentDidCatch static getDerivedStateFromError
捕获错误用
- componentDidMount() 
React 将会在组件被添加到屏幕上 （挂载） 后调用它。
设置数据获取、订阅监听事件或操作 DOM 节点的常见位置。
- componentDidUpdate(prevProps, prevState, snapshot?)
React 会在你的组件更新了 props 或 state 重新渲染后立即调用它。这个方法不会在首次渲染时调用。
- componentWillUnmount()
React 会在你的组件被移除屏幕（卸载）之前调用它。此方法常常用于取消数据获取或移除监听事件。
- getSnapshotBeforeUpdate(prevProps, prevState)
在渲染前获取dom信息，有点像useLayoutEffect

## setState之后
setState本质是调用enqueueSetState
```js
 const update = createUpdate(eventTime, lane);
    enqueueUpdate(fiber, update, lane);
    const root = scheduleUpdateOnFiber(fiber, lane, eventTime);
```
函数组件useState
```js
function dispatchAction(fiber, queue, action) {
    var lane = requestUpdateLane(fiber);
    scheduleUpdateOnFiber(fiber, lane, eventTime);
}
```
最终都会调用scheduleUpdateOnFiber
- 在 unbatch 情况下，会直接进入到 performSyncWorkOnRoot ，接下来会进入到 调和流程，比如 render ，commit。
- 那么任务是 useState 和 setState，那么会进入到 else 流程，那么会进入到 ensureRootIsScheduled 调度流程。
- 当前的执行任务类型为 NoContext ，说明当前任务是非可控的，那么会调用 flushSyncCallbackQueue 方法。setTimeout Promise.resolve().then()

## node 不是单进程
<!-- https://stackoverflow.com/questions/61550822/why-node-js-spins-7-threads-per-process -->

## 浏览器解释
1. Process HTML markup and build the DOM tree.
2. Process CSS markup and build the CSSOM tree.
3. Combine the DOM and CSSOM into a render tree.
4. Run layout on the render tree to compute geometry of each node.
5. Paint the individual nodes to the screen.

## 基础类型真的存在栈区？引用类型真的存在堆区吗？
答案是否定的。
ECMA没有规定Javascript内存分配规则。
[JavaScript中变量存储在堆中还是栈中](https://www.zhihu.com/question/482433315)
v8引擎实现:
1. 字符串保存在堆上，栈上保存字符串的引用，相同字符串引用相同地址。
2. 小整数保存在栈上，双精度浮点型保存在堆上。bigint
3. 其他类型：引擎初始化时分配唯一地址，栈中的变量存的是唯一的引用。

## HTTPS握手
通过TCP连接
1. 客户端send client hello（TLS加密套件，TLS版本，第一随机数）
2. 服务器 Server done（TLS加密套件，TLS版本，第二随机数）
- 发送服务器公钥
- 发送CA证书
3. 客户端 client exchange 验证TLS证书，生成随机数，并用公钥加密成预主密钥（第三随机数）。
4. 服务端使用私钥解密预主密钥
5. 客户端和服务端用预主密钥+第一随机数+第二随机数计算会话密钥
握手完成
### 补充
- 性能最好是TLS1.2，安全性最好是TLS1.3
- TLS证书除了记录域名有效期，非对称算法

## 跨域
<!-- https://aws.amazon.com/cn/what-is/cross-origin-resource-sharing/ -->
我们常说的跨域其实是指跨源资源共享，英文简称cors。同源策略是浏览器特有的安全策略，限制不同源资源交换。当两个资源的协议、域名和端口都相等时才同源。

## CORS的作用
早期互联网时代，CSRF（跨站请求伪造）时有发生。在假冒网站向目的网站进行跨站请求时，会携带用户cookie进行请求，假装自己时用户进行操作。
为此，现在所有浏览器都实现同源策略。

## CORS请求分为简单请求和复杂请求
简单请求要满足同时满足
- 请求方法是GET POST HEAD
- 请求头只能为 Accept-Language、Accept 或 Content-Language
- Accept 只能为 multipart/form-data、application/x-www-form-urlencoded 或 text/plain  
否认则称为复杂请求
复杂请求会额外发生一个option请求，称为预检请求，作用是先检查服务器是否允许请求，避免产生意外的更改。
```
Access-Control-Allow-Headers: Content-Type

Access-Control-Allow-Origin: https://news.example.com

Access-Control-Allow-Methods: GET, DELETE, HEAD, OPTIONS
<!-- 允许不发送预检请求 -->
Access-Control-Max-Age: 60(s)
```

## typescript ! 非空断言操作符
具体而言，x! 将从 x 值域中排除 null 和 undefined 。

## HTTP 压缩算法
- LZ77 霍夫曼编码
- DEFLATE
- GZIP
- LZMA
- Brotli 霍夫曼编码

## import * 会做tree-shaking吗?
import * as modules from 'esmodule' 会做 tree-shaking。tree-shaking 是一种优化技术，它可以去除未使用的模块。这可以减少代码大小
1. 浏览器将解析 import * as modules from 'esmodule' 语句。
2. 浏览器将找到所有导入的模块。
3. 浏览器将检查每个模块是否被使用。
4. 浏览器将删除未使用的模块。

## script 标签的 impoerMap
请查阅mdn

## tsup转译的require函数(cjs)
本质是闭包
```js
var __getOwnPropNames = Object.getOwnPropertyNames;
var __commonJS = (cb, mod) => function __require() {
  return mod || (0, cb[__getOwnPropNames(cb)[0]])((mod = { exports: {} }).exports, mod), mod.exports;
};
// require_package是个函数, 运用创建时传入的json,所以是个闭包
var require_package = __commonJS({
  "package.json"(exports, module) {
    module.exports = {
      name: "freshland",
      }
    };
  }
});
```
## Esmodule 和 cjs有什么区别
- cjs允许动态加载
```js
const module = require(`${name}.js`)
```
- cjs同步加载, module异步加载
- cjs发生在运行时, module发生在编译时
- cjs输出值拷贝, module是值引用

## .d.ts 模块类型声明文件
在 TypeScript 中，.d.ts 文件是模块的类型声明文件。它用于声明模块中导出的类型，以便其他模块可以使用这些类型。
```ts
// tsup.config.ts
import { defineConfig } from 'tsup';

export default defineConfig({
  entry: ['src/index.ts'],
  clean: true,
  bundle: true,
  splitting: true,
  outDir: 'dist',
  format: ['cjs', 'esm'],
  shims: true,
  dts: true,
});
```
