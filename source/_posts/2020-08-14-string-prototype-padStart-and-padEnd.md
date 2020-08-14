---
title: JavaScript 内置对象 String 的 padStart 和 padEnd 方法
date: 2020-08-14 23:56:36
tags:
  - padStart
  - padEnd
categorites:
 - [学习笔记 - JavaScript]
excerpt: padStart 和 padEnd 方法是 String 对象 prototype 上挂载的方法, 两个方法都是返回新的字符串, 不会改变原来的值...
---

## `padStart()`

`padStart`方法是 `String` 对象 `prototype` 上挂载的一个方法, 该方法会返回新的字符串, 不会改变原来的值

**语法**

```js
str.padStart(targetLength [, padString])
```

**作用**: 用另一个字符串(`padString`)从当前字符串(`str`)左侧开始填充, 以便产生的字符串达到给定的长度(`targetLength`)

例子:

```js
const str = '2'
const newStr = str.padStart(2, '0')
console.log(str) // 
console.log(newStr) // 
```

**规则**

- r如果 **targetLength** 这个数值小于当前字符串 `str` 的长度, 则返回当前字符串


