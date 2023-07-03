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
