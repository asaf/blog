---
tags:
- js
- javascript
- es6
- spread
comments: true
date: 2015-09-13T00:00:00Z
title: Add additional properties to a result of a function or a fucntion map using
  ES6 spread operator.
url: /2015/09/13/add-additional-properties-to-a-result-of-a-function-or-a-fucntion-map-using-es6-spread-operator/
---

Sometimes it makes sense to add additional properties to a result of a function,

Examples could be enhancing an operation result with some meta data, extending with a timestamp, run time, etc,

Using ES6, we could easily achieve this by:


```js
bindData(oper, props) {
  if (typeof oper === 'function') {
    return (...args) => ({
      ...oper(...args),
      ...props
    });
  }
  if (typeof oper === 'object') {
    return Object.keys(oper).reduce((prev, key) => ({
      ...prev,
      [key]: bindData(oper[key], props)
    }), {});
  }
  return oper;
}
```

Here are some clarifications:

1. If `oper` is a function, we decorate the original function by a closure,
`return (...args)` is the closure (read more about the [spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) operator.

In general, `myFunction(...args);` is equelevent to `(args) { oper.apply(null, args); }`,

1. When the returned function is invoked, the result of the original function `...oper(...args)` is then merged with the `props` object, in other words:

`...oper(), ...props` is equelevent to: `Object.assign({}, oper(), props);`
Read more about [Object.assign](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)


1. If `oper` is an object, we bind each of its key's recursively (assuming each property is a function).
