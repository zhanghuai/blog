---
title: 两数相加
toc: true
copyright: true
date: 2020-07-06 11:11:54
tags: 
categories: 
  - leetcode
  - 面试题
---

**题目描述：** 两个字符串（数字可能超过安全数字）相加，可以假设字符串除了0之外，不会以0开头

eg:

| str1 | str2 | ouput |
| :-----| :---- | :---- |
| 999 | 111 | 1110 |
| 99 | 1 | 100 |
| 8 | 22 | 30 |

<!--more-->
### 解法
**注意：** 两数相加时候的进位，最好加完之后的进位
```typescript
/**
 * @description 两个字符串（数字可能超过安全数字）相加，可以假设字符串除了0之外，不会以0开头
 * @param {string} str1
 * @param {string} str2 
 * @returns {string}
 */
function addTowStrings(str1: string, str2: string): string {
  let str1Len = str1.length - 1;
  let str2Len = str2.length - 1;
  let ret = "";
  let carry = 0;
  while (str1Len >= 0 || str2Len >= 0) {
    const s1 = str1Len >= 0 ? str1[str1Len] : 0;
    const s2 = str2Len >= 0 ? str2[str2Len] : 0;
    const num = +s1 + +s2 + carry;
    carry = Math.floor(num / 10);
    ret = `${num % 10}${ret}`;
    str1Len--;
    str2Len--;
  }
  return carry === 1 ? `1${ret}` : ret;
}
```
### 参考链接
[两个字符串数字相加](https://github.com/tinytot1/blog/issues/4)
[两数相加](https://leetcode-cn.com/problems/add-two-numbers/)
[两数相加 II](https://leetcode-cn.com/problems/add-two-numbers-ii/)
