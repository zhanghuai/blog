---
title: 作用域与作用域链
date: 2019-07-31 16:54:43
toc: true
copyright: true
tags:
  - 作用域
categories:
  - JavaScript基础
---

### 作用域
作用域是指程序源代码中定义变量的区域。作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。`JavaScript` 采用词法作用域(`lexical scoping`)，也就是静态作用域。因为 `JavaScript` 采用的是词法作用域，函数的作用域在函数定义的时候就决定了。
<!--more-->
```js
var value = 1;
function foo() {
  console.log(value);
}
function bar() {
  var value = 2;
  foo();
}

bar();

// 结果是 1
```
下面，让我们以一个函数的创建和激活两个时期来讲解作用域链是如何创建和变化的。
### 函数创建
函数有一个内部属性 [[scope]]，当函数创建的时候，就会保存所有父变量对象到其中，你可以理解 [[scope]] 就是所有父变量对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！
``` js
var value = 1;
function foo() {
  console.log(value);
}
function bar() {
  var value = 2;
  foo();
}

bar();
```
函数创建时，各自的[[scope]]为：
```js
foo.[[scope]] = [
  globalContext.VO
];

bar.[[scope]] = [
  globalContext.VO
];
``` 

### 函数激活
当函数激活时，进入函数上下文，创建 `VO/AO` 后，就会将活动对象添加到作用链的前端。

这时候执行上下文的作用域链，我们命名为 `Scope`：
```js
Scope = [AO].concat([[Scope]]);
```
在 `bar`函数执行时，`bar`的上下文对象如下:

```js
barContext = {
  AO:{
    value: 2
  },
  Scope: [barContext.AO,globalContext.VO]
}
```
在 `foo`函数执行时，`foo`的上下文对象如下:
```js
fooContext = {
  AO:{},
  Scope: [fooContext.AO,globalContext.VO]
}
value的查找过程：
- fooContext.AO//没有找到
- globalContext.VO //找到 10
```

### 参考资料
[JS中的作用域链是在什么时候建立的？](https://www.zhihu.com/question/36751764)
[JavaScript深入之作用域链](https://github.com/yy9306/yy9306.github.io/issues/15)