---
title: 数字的千位分隔符表示法
toc: true
copyright: true
date: 2020-06-28 11:23:30
tags:
  - 正则表达式
categories: 
  - 编程题
  - 面试题
---
**题目描述：** 数字的千位分隔符表示法,多种实现方式。

eg:

| 输入 | 输出 |
| :-----| :---- |
| 10000000000 | 10,000,000,000 |
| 100000000000 | 100,000,000,000 |
| 100000000 | 100,000,000 |

<!--more-->
### 解法 -- 正则表达式

- `\d{3}`： 三个数为一组
- `(?=\d{3})`： (?=x) 匹配位置，匹配三个数字前面的位置
- `(?=\d{3}$)`： 匹配最后三个数前面的位置
- `(\d{3})+`： 一组三个数出现一次或者多次
- `(?=(\d{3})+$)`： 匹配多组三个数前面的位置
- `(?!^)`： 匹配位置，匹配非 `^` 前面的位置，即此位置后面不匹配 `^`

**注意：** 开头一组三位数字前面。

```ts
/**
 * @description 数字的千位分隔符表示法
 * @param {number} num - 输入数字
 * @returns {string}
 */
function numformat1(num: number): string {
  return num.toLocaleString();
}
```
### 解法 -- toLocaleString
具有多种数字格式化功能。参考链接：[Number.prototype.toLocaleString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString#Checking_for_support_for_locales_and_options_arguments)
**注意：**当格式化大量数字时，最好建立一个 `NumberFormat` 对象并且使用它提供的 `NumberFormat.format` 方法。
```typescript
/**
 * @description 数字的千位分隔符表示法
 * @param {number} num - 输入数字
 * @returns {string}
 */
function numformat1(num: number): string {
  return num.toLocaleString();
}
```

### 解法 -- 函数
函数解法有各种写法，都要注意开头三位数情况，这里实现一个从后向前遍历，每三位数为一组。
```typescript
/**
 * @description 数字的千位分隔符表示法
 * @param {number} num - 输入数字
 * @returns {string}
 */
function numformat2(num: number): string {
  const str = num.toString();
  const arr = [];
  for (let len = str.length, i = len - 1; i >= 0; i = i - 3) {
    let start = i - 2;
    if (start < 0) {
      start = 0;
    }
    arr.push(str.slice(start, i + 1));
  }
  return arr.reverse().join(',');
}
```
### 参考链接
[数字的千位分隔符表示法](https://github.com/tinytot1/blog/issues/3)