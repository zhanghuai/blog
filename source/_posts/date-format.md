---
title: 【日期】时间格式化
toc: true
copyright: true
date: 2020-06-18 16:10:07
tags: 正则表达式
categories: 编程题
---
**题目描述：**给定一个时间，按照指定格式返回

eg:

| time | format | output |
| :-----| :---- | :---- |
| 2019-02-02 12:21:21 | yyyy-MM-dd | 2019-02-02 |
| 2019-02-02 12:21:21 | yyyy-M-d | 2019-2-2 |
| 2019-02-02 12:21:21 | yyyy-MM-dd | 2019-02-02 |
| 2019-02-02 12:21:21 | yyyy年M月d日 | 2019年2月2日 |
| 2019-02-02 12:21:21 | yyyy-MM-dd hh:mm | 2019-02-02 12:21 |

### 题解
```ts
/**
 * @description 日期格式化
 * @param time string | number
 * @param format string "yyyy-MM-dd hh:mm:ss"
 * @returns 格式化时间
 */
function format(time: string | number, format: string = "yyyy-MM-dd hh:mm:ss"): string {
  const date = new Date(time);
  const o = {
    "y+": date.getFullYear(),
    "M+": date.getMonth() + 1,                 //月份 
    "d+": date.getDate(),                    //日 
    "h+": date.getHours(),                   //小时 
    "m+": date.getMinutes(),                 //分 
    "s+": date.getSeconds()                 //秒 
  };
  for (const [key, value] of Object.entries(o)) {
    format = format.replace(new RegExp(key), match => {
      if (match.includes("y")) {
        return `${value}`
      }
      return match.length === 1 ? `${value}` : value < 10 ? `0${value}` : `${value}`;
    });
  }
  return format;
}

console.log(format(Date.now()));
console.log(format("2020-05-19 16:05:12", "yyyy-M")); // 2020-5
console.log(format("2020-05-19 16:05:12", "yyyy-MM")); // 2020-05
console.log(format("2020-05-19 16:05:12", "yyyy年M月")); // 2020年5月
console.log(format("2020-05-19 16:05:12", "yyyy年MM月")); //2020年05月
console.log(format("2020-05-19 16:05:12", "yyyy年M月d天")); // 2020年5月19天
console.log(format("2020-05-19 16:05:12", "yyyy-M-d")); //2020-5-19
console.log(format("2020-05-19 16:05:12", "yyyy-MM-d")); //2020-05-19
console.log(format("2020-05-19 16:05:12", "yyyy-MM-dd hh:mm")); // 2020-05-19 16:05
```
### 参考链接
[日期格式化](https://github.com/tinytot1/blog/issues/2)