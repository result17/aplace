## bind
```js
Function.prototype.bind = function (context, ...args) {
  const fn = this; // the target function
  return function (...innerArgs) {
    return fn.apply(context, [...args, ...innerArgs]);
  };
};
```
