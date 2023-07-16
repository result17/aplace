---
author: cosin
pubDatetime: 2023-06-30T11:12:57+08:00 
title: js基本功能函数实现
postSlug: handwrite0js
featured: true
tags:
  - release
description: js基本功能函数简单实现
---
## bind
```js
Function.prototype.bind = function (context, ...args) {
  const fn = this; // the target function
  return function (...innerArgs) {
    return fn.apply(context, [...args, ...innerArgs]);
  };
};
```

## 千分位分割
```js
const format = (n) => {
  let ans = ''
  let count = 0
  while (n) {
    let rest = n % 10
    n = Math.floor(n / 10)
    ans += rest
    count++
    if (count % 3 === 0 && n > 0) ans += ','
  }
  let res = '' 
  let i = ans.length
  while (i > 0) {
    res += ans[--i]
  }
  return res
}
```

## debounce
```js
const debounce = (fn, duration, imd) => {
  let timer = null;
  return function (...args) {
    const context = this
    const f = function () {
      timer = null
      if (!imd) fn.apply(context, args)
    }
    const callNow = imd && !timer
    clearTimeout(timer)
    timer = setTimeout(f, duration)
    if (callNow) fn.apply(context, args)
  }
}

const cat = {
  name: 'rose',
  count: 0,
  print: function() {
    this.count++
    console.log(`${this.name}: ${this.count}`)
  }
}

setInterval(debounce(() => cat.print(), 500), 1000)
```

## throttle
```js
const throttle = function (fn, limit) {
  let inThrottle = false
  let timer = null
  let lastRun = 0

  return function(...args) {
    const context = this
    if (!inThrottle) {
      fn.apply(context, args)
      inThrottle = true
      lastRun = Date.now()
    } else {
      clearTimeout(timer)
      timer = setTimeout(() => {
        const now = Date.now()
        if ((now - lastRun) >= limit) {
          fn.apply(context, args)
          lastRun = now
          inThrottle = false
        }
      }, limit - (Date.now() - lastRun))
    }
  }
}
```

## package.json 中的 peerDependencies 
在我们进行一些插件开发的时候会经常用到，比如 jQuery-ui 的开发依赖于 jQuery，html-webpack-plugin 的开发依赖于 webpack等。

## vue core hasOwn
```ts
const hasOwnProperty = Object.prototype.hasOwnProperty
export const hasOwn = (
  val: object,
  key: string | symbol
): key is keyof typeof val => hasOwnProperty.call(val, key)
```

## NaN !== NaN
```ts
const a = NaN;
const b = a
// false
console.log(a === b)
```

## Promise
```ts
enum IPromiseState {
  Pending,
  Fulfilled,
  Rejected,
}

const runAsync = (cb: () => void) => {
  setTimeout(cb, 0);
};

const isThenable = (obj: any): obj is IPromise => {
  return typeof obj?.then === "function";
};

type Handler = {
  handleOnFulfilled: (value?: any) => void;
  handleOnRejected: (reason?: any) => void;
}

class IPromise {
  private state: IPromiseState = IPromiseState.Pending

  private result?: any

  private handlersList: Handler[] = [];

  constructor(
    executor: (
      resolve: (val: any) => void,
      reject: (reason: any) => void
    ) => void
  ) {
    try {
      executor(this.resolve, this.reject)
    } catch (error) {
      console.log(error)
      this.reject(error)
    }
  }

  private setResultAndState(value: any, state: IPromiseState) {
    if (this.state !== IPromiseState.Pending) {
      throw new Error("Promise is not pending.")
    }
    if (isThenable(value)) {
      value.then(this.resolve, this.reject);
      return;
    }
    this.result = value
    this.state = state
    this.executorHandlers()
  }

  private resolve = (val: any | IPromise) => {
    this.setResultAndState(val, IPromiseState.Fulfilled)
  }

  private reject = (reason?: any) => {
    this.setResultAndState(reason, IPromiseState.Rejected)
  }

  private executorHandlers = () => {
    if (this.state === IPromiseState.Pending) {
      throw new Error("Promise is pending, when executing handlers.")
    }

    runAsync(() => {
      for (const handler of this.handlersList) {
        const isFulFilled = this.state === IPromiseState.Fulfilled
        isFulFilled ? handler.handleOnFulfilled(this.result) : handler.handleOnRejected(this.result)
      }
      this.handlersList.length = 0
    })
    
  }
  
  then(
    onFullfilled?: (value: any) => any | IPromise,
    onRejected?: (reason: any) => any | IPromise
  ) {
    return new IPromise((resolve: any, reject: any) => {
      const handler: Handler = {
        handleOnFulfilled: (value: any) => {
          if (!onFullfilled || typeof onFullfilled !== 'function') {
            resolve(value)
          } else {
            try {
              resolve(onFullfilled(value as any))
            } catch (error) {
              reject(error)
            }
          }
        },
        handleOnRejected: (reason: any) => {
          if (!onRejected || typeof onRejected !== "function") {
            reject(reason)
          } else {
            try {
              resolve(onRejected(reason as any | IPromise))  
            } catch (error) {
              reject(error)
            }
          }
        }
      }

      this.handlersList.push(handler)
      this.executorHandlers()
    })
  }

  catch(onRejected) {
    this.then(undefined, onRejected)
  }
  
  finally(onFinally: () => void) {
    this.then(
      (value) => {
        onFinally()
        return value
      },
      (reason) => {
        onFinally()
        throw reason
      }
    )
  }

}
```