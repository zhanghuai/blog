---
title: 闭包
date: 2019-07-30 15:04:01
toc: true
copyright: true
tags: 
  - 闭包
categories: 
  - JavaScript基础
---

### 闭包的概念
来自维基百科的定义：在计算机科学中，闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。
<!--more-->
以下例子就是闭包：
```js
function makeFunc() {
    const name = "closure";
    function getName() {
      return name;
    }
    return getName;
}

const myFunc = makeFunc();
myFunc(); // closure
```

### 闭包的用处
闭包常见的用法，就将围绕这些特点展开。**注意：闭包封住了变量作用域，有效地防止了全局污染；但同时，它也存在内存泄漏的风险**
1. 模仿块级作用域
现在有`let`和`const`，就不举例子了。
2. 存储变量，缓存数据
```js
function mockData() {
  const mem = {};
  return {
    clear: () => (mem = null), // 显式暴露清理接口
    get: page => {
      if (page in mem) {
        return mem[page];
      }
      mem[page] = Math.random();
    }
  };
}
```
3. 封装私有变量

### 思考题
如果你完成下面的题，应该就算理解闭包的运行机制了。
1. 下面的输出都是3，请改成输出 0，1，2
```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0](); // 3
data[1](); // 3
data[2](); // 3 
```
答案
```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function(i) {
    return function(){
      console.log(i);
    }
  })(i)
}

data[0](); // 0
data[1](); // 1
data[2](); // 2
```
2. 解释
```js
var nAdd;
var t = function() {
    var n = 99;
    nAdd = function() {
    	 n++;
    }
    var t2 = function() {
    	console.log(n)
    }
    return t2;
};

var a1 = t();
var a2 = t();

nAdd();

a1(); //99
a2(); //100
```