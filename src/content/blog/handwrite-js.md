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
## curry 函数柯里化
```ts
type AnyFunc = (...args: any[]) => any

type Curry<Fn extends AnyFunc> =
  Parameters<Fn> extends [infer FirstArg, ...infer Rest]
  ? (arg: FirstArg) => Curry<(...args: Rest) => ReturnType<Fn>>
  : ReturnType<Fn>

function curry<T extends AnyFunc, TAgg extends unknown[]>(func: T, agg?: TAgg): Curry<T> {
  const aggregatedArgs = agg ?? []
  if (func.length === aggregatedArgs.length) return func(...aggregatedArgs)
  return ((arg: any) => curry(func, [...aggregatedArgs, arg])) as any
}

function add(a: number, b: number, c: number): number {
  return a + b + c;
}

const curriedAdd = curry(add);

const add5 = curriedAdd(5);
const add5And6 = add5(6);
console.log(add5And6(7));
```
函数定义还是有点小问题

## Event bus
```ts
export interface Registry {
  unregister: () => void;
}

export interface Callable {
  // id
  [key: string]: Function;
}

export interface Subscriber {
  // event
  [key: string]: Callable;
}

export interface IEventBus {
  dispatch<T>(event: string, arg?: T): void;
  register(event: string, callback: Function): Registry;
}

export class EventBus implements IEventBus {
  private subscribers: Subscriber;
  private static nextId = 0;

  constructor() {
    this.subscribers = {};
  }

  public dispatch<T>(event: string, arg?: T): void {
    const subscriber = this.subscribers[event];
    if (subscriber === undefined) {
      return;
    }
    Object.keys(subscriber).forEach((key) => subscriber[key](arg));
  }

  public register(event: string, callback: Function): Registry {
    const id = this.getNextId();
    if (!this.subscribers[event]) this.subscribers[event] = {};
    this.subscribers[event][id] = callback;
    return {
      unregister: () => {
        delete this.subscribers[event][id];
        if (Object.keys(this.subscribers[event]).length === 0)
          delete this.subscribers[event];
      },
    };
  }

  private getNextId(): number {
    return EventBus.nextId++;
  }
}
```

## this指向
在非严格模式， 函数执行时没有明确执行上下文，则会指向全局对象（浏览器window, Node或Deno指向Global）
在严格模式，this找不到会报undefined错误

## instanceof
```ts
const instanceOf = (left: Object, right: Function): boolean => {
  if (typeof left !== 'object' || left === null) return false
  let proto = Object.getPrototypeOf(left)
  while (true) {
    if (proto === null) return false
    if (proto === right.prototype) return true
    proto = Object.getPrototypeOf(proto)
  }
}
```

## call
```ts
type AnyFnc = (...args: any[]) => any

const iCall = <T extends AnyFnc>(fn: T, context: Object, ...args: any[]): ReturnType<T> => {
  const uKey = Symbol('fn')
  context[uKey] = fn
  const result = context[uKey](...args)
  delete result[uKey]
  return result
}
```

## apply
```ts
type AnyFnc = (...args: any[]) => any

const iApply = <T extends AnyFnc>(fn: T, context: Object, args: any[]): ReturnType<T> => {
  const uKey = Symbol('fn')
  context[uKey] = fn
  const result = context[uKey](...args)
  delete result[uKey]
  return result
}
```
## bind
Object.setPrototypeOf() 静态方法可以将一个指定对象的原型（即内部的 [[Prototype]] 属性）设置为另一个对象或者 null。
```js
Function.prototype.bind = function (context, ...args) {
  if (typeof this !== "function") {
    throw new Error("Type Error");
  }
  // 保存this的值
  const self = this;

  function F() {
    // 考虑new的情况
    if (this instanceof F) {
      return self.call(this, ...args, ...arguments);
    }
    return self.apply(context, [...args, ...arguments]);
  }

  Object.setPrototypeOf(F.prototype, self.prototype);
  return F;
};

function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return this.x + "," + this.y;
};

var p = new Point(1, 2);
p.toString(); // '1,2'

var emptyObj = {};
var YAxisPoint = Point.bind(emptyObj, 0 /*x*/);
// 以下这行代码在 polyfill 不支持,
// 在原生的bind方法运行没问题:
//(译注：polyfill的bind方法如果加上把bind的第一个参数，即新绑定的this执行Object()来包装为对象，Object(null)则是{}，那么也可以支持)
var YAxisPoint = Point.bind(null, 0 /*x*/);

var axisPoint = new YAxisPoint(5);

console.log(axisPoint instanceof Point); // true
console.log(axisPoint instanceof YAxisPoint);
console.log(axisPoint.toString());

```
```js
Function.prototype.bind = function(content) {
    if(typeof this != "function") {
        throw Error("not a function")
    }
    // 若没问参数类型则从这开始写
    let fn = this;
    let args = [...arguments].slice(1);
    
    let resFn = function() {
        return fn.apply(this instanceof resFn ? this : content,args.concat(...arguments) )
    }
    function tmp() {}
    tmp.prototype = this.prototype;
    resFn.prototype = new tmp();
    
    return resFn;
}
```

## Object.create 
```js
const create = function (obj) {
  function F() {}
  F.prototype = obj;
  return new F();
}
```