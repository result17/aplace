---
author: cosin
pubDatetime: 2023-08-17T21:32:05+08:00 
title: JavasScript 设计模式
featured: false
draft: false
tags:
  - color-schemes
  - docs
description:
  JavaScript设计模式与开发实践 读书笔记
---
## Javascript 原型继承的原则
JavaScript设计模式与开发实践 1.4
- 所有数据都是对象
- 要得到一个对象，不是通过实例化类，而是找到一个对象作为原型并克隆它。
- 对象会记住他的原型
- 如果对象无法响应某个请求，它会把这个请求委托给它自己的原型

## 代理类实现单例模式
```js
var CreateDiv = function( html ){
  this.html = html;
  this.init();
};

CreateDiv.prototype.init = function(){
  var div = document.createElement( 'div' );
  div.innerHTML = this.html;
  document.body.appendChild( div );
};

var ProxySingletonCreateDiv = (function(){

  var instance;
  return function( html ){
    if ( !instance ){
      instance = new CreateDiv( html );
    }

    return instance;
   }

})();

var a = new ProxySingletonCreateDiv( 'sven1' );
var b = new ProxySingletonCreateDiv( 'sven2' );

alert ( a === b );
```
通过代理类实现透明的单例模式（不必用getInstance）
```js
const ProxyConstructor = new Proxy(Constructor, {
  construct(target, args) {
    // 拦截构造函数的 new 操作

    console.log('before construct');
    
    // 使用 Reflect.construct 调用原始构造函数
    const obj = Reflect.construct(target, args);

    console.log('after construct');

    return obj;
  }  
});

// 代理后使用
new ProxyConstructor(args); 
```

## 策略模式
一个基于策略模式的程序至少由两部分组成。第一个部分是一组策略类，策略类封装了具体的算法，并负责具体的计算过程。第二个部分是环境类Context, Context接受客户的请求，随后把请求委托给某一个策略类。要做到这点，说明Context中要维持对某个策略对象的引用。
```js
var strategies = {
    "S": function( salary ){
      return salary * 4;
    },
    "A": function( salary ){
      return salary * 3;
    },
    "B": function( salary ){
      return salary * 2;
    }
};

var calculateBonus = function( level, salary ){
    return strategies[ level ]( salary );
};

console.log( calculateBonus( 'S', 20000 ) );     // 输出：80000
console.log( calculateBonus( 'A', 10000 ) );     // 输出：30000
```
