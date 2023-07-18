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
