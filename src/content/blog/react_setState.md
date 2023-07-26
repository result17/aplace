---
author: cosin
pubDatetime: 2023-07-24T23:55:09+08:00 
title: setState在react17和18的差异
featured: true
draft: false
tags:
  - color-schemes
  - docs
description:
  setState在legacy和concurrent Mode的差异
---
## react18默认开启concurrent模式
```js
ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```
## setState输出
```js
import React from "react";
export default class Home extends React.Component {
  state = { number: 0 };
  handleClick = () => {
    setTimeout(() => {
      this.setState({ number: this.state.number + 1 }, () => {
        console.log("callback1", this.state.number);
      });
      console.log(this.state.number);
      this.setState({ number: this.state.number + 1 }, () => {
        console.log("callback2", this.state.number);
      });
      console.log(this.state.number);
      this.setState({ number: this.state.number + 1 }, () => {
        console.log("callback3", this.state.number);
      });
      console.log(this.state.number);
    });
  };
  render() {
    return (
      <div>
        {this.state.number}
        <button onClick={this.handleClick}>number++</button>
      </div>
    );
  }
}

```
在react17（legacy mode）会同步setState会进行批处理操作，合并多个setState。但在Promise或setState中，函数出逃出批处理操作，进行多次setState，多次render。
```js
function batchedEventUpdates(fn,a){
    /* 开启批量更新  */
   isBatchingEventUpdates = true;
  try {
    /* 这里执行了的事件处理函数， 比如在一次点击事件中触发setState,那么它将在这个函数内执行 */
    return batchedEventUpdatesImpl(fn, a, b);
  } finally {
    /* try 里面 return 不会影响 finally 执行  */
    /* 完成一次事件，批量更新  */
    isBatchingEventUpdates = false;
  }
}
```
所以在react17（legacy mode）上面的输出
```shell
 callback1 1 , 1, callback2 2 , 2,callback3 3 , 3
```
若想在异步中进入批处理操作，可以通过React-Dom 中 unstable_batchedUpdates，可以去手动批量更新。
```js
setTimeout(()=>{
    unstable_batchedUpdates(()=>{
        this.setState({ number:this.state.number + 1 })
        console.log(this.state.number)
        this.setState({ number:this.state.number + 1})
        console.log(this.state.number)
        this.setState({ number:this.state.number + 1 })
        console.log(this.state.number) 
    })
})

```
flushSync 中的 setState > 正常执行上下文中 setState > setTimeout ，Promise 中的 setState。
在不是 pureComponent 组件模式下， setState 不会浅比较两次 state 的值，只要调用 setState，在没有其他优化手段的前提下，就会执行更新。但是 useState 中的 dispatchAction 会默认比较两次 state 是否相同，然后决定是否更新组件。