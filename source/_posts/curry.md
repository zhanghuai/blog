---
title: 柯里化（curry）
date: 2019-09-06 14:17:18
toc: true
copyright: true
tags: 
  - 柯里化
categories: 
  - JavaScript基础
---

### 柯里化的概念
> In mathematics and computer science, currying is the technique of translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument.     
>by wikipedia

把接受多个参数的函数变换成一系列接受单一参数（从最初函数的第一个参数开始）的函数的技术。（注意是单一参数）

<!--more-->
```js
import { curry } from 'lodash'

const add = (x, y) => x + y
const curriedAdd = curry(add)

const increment = curriedAdd(1)
const addTen = curriedAdd(10)

increment(2) // 3
addTen(2) // 12
```

### 柯里化 VS 偏函数应用（partial application）
> In computer science, partial application (or partial function application) refers to the process of fixing a number of arguments to a function, producing another function of smaller arity.     
>by wikipedia

偏函数应用简单来说就是：一个函数，接受一个多参数的函数且传入部分参数后，返回一个需要更少参数的新函数。
柯里化一般和偏函数应用相伴出现，但这两者是不同的概念：
```js
import { curry, partial } from 'lodash'

const add = (x, y, z) => x + y + z

const curriedAdd = curry(add)       // <- 只接受一个函数

const addThree = partial(add, 1, 2) // <- 不仅接受函数，还接受至少一个参数
  === curriedAdd(1)(2)              // <- 柯里化每次都返回一个单参函数

```
简单来说，一个多参函数（n-ary），柯里化后就变成了 n * 1-ary，而偏函数应用了 x 个参数后就变成了 (n-x)-ary

### 柯里化的实现
虽然从理论上说柯里化应该返回的是一系列的单参函数，但在实际的使用过程中为了像偏函数应用那样方便的调用，所以这里柯里化后的函数也能接受多个参数。
```js
// 实现一个函数 curry 满足以下调用、
const f = (a, b, c, d) => { ... }
const curried = curry(f)

curried(a, b, c, d)
curried(a, b, c)(d)
curried(a)(b, c, d)
curried(a, b)(c, d)
curried(a)(b, c)(d)
curried(a)(b)(c, d)
curried(a, b)(c)(d)
```
很明显第一反应是需要使用递归，这样才能返回一系列的函数。而递归的结束条件就是接受了原函数数量的参数，所以重点就是参数的传递~
```js
// ES5
var curry = function curry (fn, arr) {
  arr = arr || []

  return function () {
    var args = [].slice.call(arguments)
    var arg = arr.concat(args)

    return arg.length >= fn.length
      ? fn.apply(null, arg)
      : curry(fn, arg)
  }
}

// ES6
const curry = (fn, arr = []) => (...args) => (
  arg => arg.length >= fn.length
    ? fn(...arg)
    : curry(fn, arg)
)([...arr, ...args])

const curry1 = (fn, arr) => (...args) => {
  const arg = [...arr, ...args];
  return arg.length >= fn.length ? fn(...arg) : curry1(fn, arg)
}
```