---
title: 原型与原型链
date: 2019-08-29 15:38:45
toc: true
copyright: true
tags: 
  - 原型与原型链
categories:
  - JavaScript
---

### 理解原型
无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 `prototype` 属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得一个 `constructor` (构造函数)属性，这个属性包含一个指向 `prototype` 属性所在函数的指针。当调用构造函数创建一个新实例后，该实例的内部将包含一个指针(内部 属性)，指向构造函数的原型对象。
<!--more-->

#### prototype
每个函数都有一个 prototype 属性，就是我们经常在各种例子中看到的那个 prototype ，比如：
```js
function Person() {

}
// prototype是函数才会有的属性
Person.prototype.name = 'test';
var person = new Person();
console.log(person.name) // test
```
让我们用一张图表示构造函数和实例原型之间的关系：
![原型和prototype图](https://ftp.bmp.ovh/imgs/2020/06/f4b289e3ad22f3d0.png)

#### constructor
在默认情况下，所有原型对象都会自动获得一个 `constructor` (构造函数)属性，这个属性包含一个指向 `prototype` 属性所在函数的指针:
![原型和constructor图](https://ftp.bmp.ovh/imgs/2020/06/609bc84d24abdc38.png)

#### __proto__(内部属性)
每个实例对象都有一个私有属性，称之为 `__proto__`（浏览器实现），指向它的构造函数的原型对象
![原型和constructor图](https://ftp.bmp.ovh/imgs/2020/06/cc90c831fa2eca8a.png)

**注：** Object.getPrototypeOf(person)可以得到实例对象的原型对象

### 原型链
简单回顾一下构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。而所有函数的原型对象都是 `Object` 的实例，因此原型对象都会包含一个内部指针，指向 `Object.prototype`。

完整的原型链如下图所示：
![原型和constructor图](https://ftp.bmp.ovh/imgs/2020/06/583639db4e051f80.png)


#### 实例与原型
当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。
```js
function Person() {
}
// prototype是函数才会有的属性
Person.prototype.name = 'test';
var person = new Person();
person.name = 'person';
console.log(person.name); // person
delete person.name;
console.log(person.name); // test
```