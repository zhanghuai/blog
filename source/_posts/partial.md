---
title: 偏函数(partial application)
date: 2019-09-05 10:29:51
toc: true
copyright: true
tags: 
  - 偏函数
categories: 
  - JavaScript基础
---

### 偏函数的概念
维基百科中对偏函数 (Partial application) 的定义：
>In computer science, partial application (or partial function application) refers to the process of fixing a number of arguments to a function, producing another function of smaller arity. 

翻译就是：在计算机科学中，部分应用（或部分功能应用）是指将一些参数固定到一个函数，产生另一个较小的`arity`函数的过程。
<!--more-->

### 偏函数与bind函数
`bind()`的一个最简单的用法是使一个函数拥有预设的初始参数。只要将这些参数（如果有的话）作为`bind()`的参数写在`this`后面。当绑定函数被调用时，这些参数会被插入到目标函数的参数列表的开始位置，传递给绑定函数的参数会跟在它们后面。
```js
function list() {
  return Array.prototype.slice.call(arguments);
}

// 创建一个函数，它拥有预设参数列表。
const leadingThirtysevenList = list.bind(null, 37);

leadingThirtysevenList(); // [37]

leadingThirtysevenList(1, 2, 3); // [37, 1, 2, 3]
```
然而使用 `bind` 会改变了 `this` 指向，我们要写一个不改变 `this` 指向的方法，即偏函数。

### Partial application
```js
function partial(fn, ...argsBound) {
  return function(...args) {
      return fn.call(this, ...argsBound, ...args);
  };
}
```
验证 `this` 指向的 `demo`
```js
function add(a, b) {
    return a + b + this.value;
}
// const addOne = add.bind(null, 1);
const addOne = partial(add, 1);

const value = 1;
const obj = {
    value: 2,
    addOne: addOne
}
obj.addOne(2); // ???
// 使用 bind 时，结果为 4
// 使用 partial 时，结果为 5
```

### partial 函数占位符
提供占位符，可以等到需要时使用，例如当前还不知道需要传的的具体值时候，可以用一个占位符
```js
const PLACEHOLDER = Symbol('PLACEHOLDER');
function partial(fn, ...argsBound) {
  return function(...args) {
    let position = 0, len = argsBound.length;
    for(var i = 0; i < len; i++) {
        argsBound[i] = argsBound[i] === PLACEHOLDER ? args[position++] : argsBound[i]
    }
    return fn.call(this, ...argsBound, ...args.slice(position));
  };
}
```
我们验证一下
```js
const subtract = function(a, b) { return b - a; };
const subFrom20 = partial(subtract, PLACEHOLDER, 20);
subFrom20(5); //15
```