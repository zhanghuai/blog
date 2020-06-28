---
title: 执行上下文
date: 2019-08-01 10:20:19
toc: true
copyright: true
tags:
  - 执行上下文
categories:
  - JavaScript基础
---
如果要问到 `JavaScript` 代码执行顺序的话，想必写过 `JavaScript` 的开发者都会有个直观的印象，那就是顺序执行。但是变量提升，函数提升应该怎么解释呢？这是因为 `JavaScript` 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一段代码的时候，会进行一个“准备工作”。
<!--more-->
### 执行上下文
`JavaScript` 引擎遇到以下代码时才会做“准备工作”：
1. 全局代码：代码默认运行的环境，最先会进入到全局环境中
2. 函数代码：在函数的局部环境中运行的代码
3. Eval代码：在Eval()函数中运行的代码

举个例子，当执行到一个函数的时候，就会进行准备工作，这里的“准备工作”，让我们用个更专业一点的说法，就叫做"执行上下文(`execution context，EC`)"。

### 执行上下文栈
由于有太多的执行上下文， `JavaScript` 引擎创建了执行上下文栈`（Execution context stack，ECStack）`来管理执行上下文。

当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。

看一段代码：
```js
function fn1() {  
  var fn1Context = 'fn1 context';  
  function fn2() {  
      var fn2Context = 'fn2 context';  
      function fn3() {  
          var fn3Context = 'fn3 context';  
          console.log(fn3Context);  
      }  
      fn3();  
      console.log(fn2Context);  
  }  
  fn2();  
  console.log(fn1Context);  
}  
fn1();
```
为了模拟执行上下文栈的行为，让我们定义执行上下文栈是一个数组：
```js
ECStack = [];

// 伪代码
// 在初始化代码时会先进入全局上下文中， 全局上下文压入栈
ECStack.push(globalContext);

// fn1()
ECStack.push(<fn1> fn1Context);

// fn1中竟然调用了fn2，还要创建fn2的执行上下文
ECStack.push(<fn2> fn2Context);

// 擦，fn2还调用了fn3！
ECStack.push(<fn3> fn3Context);

// fn3执行完毕
ECStack.pop();

// fn2执行完毕
ECStack.pop();

// fn1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext，在应用关闭时销毁
```

### VO/AO

#### VO(变量对象)
创建执行上下文时与之关联的会有一个变量对象，该上下文中的所有变量和函数全都保存在这个对象中。

#### AO(活动对象)
进入到一个执行上下文时，此执行上下文中的变量和函数都可以被访问到，可以理解为被激活

谈到了上下文的创建和执行，我们来看看EC建立的过程：

    建立阶段：(函数被调用，但是还未执行函数中的代码)
    1. 创建变量，参数，函数，arguments对象
    2. 建立作用域链
    3. 确定this的值

    执行阶段：变量赋值，函数引用，执行代码

执行上下文为一个对象，包含`VO`，作用域链和`this`
```js
executionContextObj = {    
    this: {}    
} 
```
具体过程：
1. 找到当前上下文调用函数的代码
1. 执行代码之前，先创建执行上下文
1. 创建阶段：
  + 1 创建变量对象(VO)：  
    - 1 创建arguments对象，检查当前上下文的参数，建立该对象下的属性和属性值
    - 2 扫描上下文的函数申明：
      + 1 每扫描到一个函数什么就会在VO里面用函数名创建一个属性，为一个指针，指向该函数在内存中的地址
      + 2 如果函数名在VO中已经存在，对应的属性值会被新的引用覆盖
    - 3 扫描上下文的变量申明：
      1. 每扫描到一个变量就会用变量名作为属性名，其值初始化为undefined
      2. 如果该变量名在VO中已经存在，则直接跳过继续扫描
  + 2 初始化作用域链
  + 3 确定上下文中this的指向
1. 代码执行阶段
  1. 执行函数体中的代码，给VO中的变量赋值

看代码理解：
```js
function foo(i) {    
    var a = 'hello';    
    var b = function privateB() {};    
    function c() {}    
}    
foo(22);
```
调用foo(22)时创建上下文包括`VO`，作用域链，`this`值。以函数名作为属性值，指向该函数在内存中的地址；变量名作为属性名，其初始化值为`undefined`
**注意：函数申明先于变量申明**
```js
fooExecutionContext = {    
  variableObject: {    
    arguments: {    
      0: 22,    
      length: 1    
    },    
    i: 22,    
    c: pointer to function c(),    
    a: undefined,    
    b: undefined    
  },    
  scopeChain: { ... },    
  this: { ... }    
} 
```
创建阶段结束后就会进入代码执行阶段，给`VO`中的变量赋值:
```js
fooExecutionContext = {    
  variableObject: {    
    arguments: {    
        0: 22,    
        length: 1    
    },    
    i: 22,    
    c: pointer to function c(),    
    a: 'hello',    
    b: pointer to function privateB()    
  },    
  scopeChain: { ... },    
  this: { ... }    
}
```

### 变量提升
```js
function foo() {  
  console.log(f1);    //f1() {}  
  console.log(f2);    //undefined  
  var f1 = 'hosting';  
  var f2 = function() {}  
  function f1() {}  
}  
foo();
```
调用`foo()`时会创建`VO`，初始`VO`中变量值等有一系列的过程，所有变量初始化值为`undefined`，所以`console.log(f2)`的值为`undefined`。并且函数申明先于变量申明，所以`console.log(f1)`的值为`f1()`函数而不为`hosting`。

### 参考资料
[聊一聊javascript执行上下文](https://juejin.im/post/5ac301d151882510fd3fcf3a#heading-5)