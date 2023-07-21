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

## 原型定义
为其他对象提供共享属性的对象
