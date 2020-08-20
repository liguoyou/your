---
title: JavaScript 内置对象 String 的 padStart() 和 padEnd() 方法
date: 2020-08-14 23:56:36
tags:
  - padStart
  - padEnd
categories:
 - [学习笔记, JavaScript]
excerpt: padStart 和 padEnd 方法是 String.prototype 上定义的方法, 在 ECMAScript 2017 (ES7) 中首次被定义; 两个方法都是返回新的字符串, 并且不会改变原来的值...
---

## `padStart()`

`padStart` 和 `padEnd` 方法是 `String.prototype` 上定义的方法, 在 `ECMAScript` 2017 (`ES7`) 中首次被定义; 两个方法都是返回新的字符串, 并且不会改变原来的值

### 语法

```js
str.padStart(targetLength [, padString])
```

### 作用: 

用另一个字符串(`padString`)从当前字符串(`str`)**左侧**开始填充, 直到字符串(`str`)达到给定的长度(`targetLength`), 然后将新的字符串返回

例子:

```js
const str = '2'
const newStr = str.padStart(2, '0')
console.log(str) // '2'
console.log(newStr) // '02'
```

### 规则

**1.** 如果 `targetLength` 这个数值小于当前字符串(`str`)的长度, 则返回当前字符串

示例:

```js
const str = '08' // str.length = 2
const newStr = str.padStart(1, 'LLLL') // targetLength: 1
console.log(newStr) // '08'
```

**2.** 如果填充字符串(`padString `)太长，使填充后的字符串长度超过了目标长度(`targetLength`)，则只保留最左侧的部分，其他部分会被截断

示例:

```js
const str = 'abc'
const newStr = str.padStart(6, '0123')
console.log(newStr) // '012abc'
```

### 兼容性

兼容性并不理想, 在 ECMAScript 2017 (ES7) 中首次被定义。

查看 https://caniuse.com/#search=padStart

{% asset_img 20200815145303_start.jpg "padStart 兼容性" %}

**如果原生环境不支持该方法，在其他代码之前先运行下面的代码，将创建 `String.prototype.padStart()` 方法。**

```js
// https://github.com/uxitten/polyfill/blob/master/string.polyfill.js
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart
if (!String.prototype.padStart) {
    String.prototype.padStart = function padStart(targetLength,padString) {
        targetLength = targetLength>>0; //floor if number or convert non-number to 0;
        padString = String((typeof padString !== 'undefined' ? padString : ' '));
        if (this.length > targetLength) {
            return String(this);
        }
        else {
            targetLength = targetLength-this.length;
            if (targetLength > padString.length) {
                padString += padString.repeat(targetLength/padString.length); //append to original to ensure we are longer than needed
            }
            return padString.slice(0,targetLength) + String(this);
        }
    };
}
```

### 使用场景

目前常见用于日期和时间字符串实现补0

```js
const date = new Date()
let month = `${date.getMonth() + 1}`
if (month.length === 1) month = month.padStart(2, '0') // 用得好像鸡肋的. 哈哈..
console.log('month:', month) // month: 08
```

## `padEnd()`

用另一个字符串(`padString`)从当前字符串(`str`)**右侧**开始填充, 直到字符串(`str`)达到给定的长度(`targetLength`), 然后将新的字符串返回

`padEnd` 和 `padStart` 语法, 作用, 规则基本上一致的, 不同的是 `padEnd`, 从**右侧**开始填充

### 语法

```js
str.padEnd(targetLength [, padString])
```

例子: 

```js
const str = '23'
const newStr = str.padEnd(4, 'ABC')
console.log(newStr) // 23AB
```

### 兼容性

兼容和 `padStart` 一致, 查看 https://caniuse.com/#search=padEnd

**如果原生环境不支持该方法，在其他代码之前先运行下面的代码，将创建 `String.prototype.padEnd()` 方法。**

```js
// https://github.com/uxitten/polyfill/blob/master/string.polyfill.js
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padEnd
if (!String.prototype.padEnd) {
    String.prototype.padEnd = function padEnd(targetLength,padString) {
        targetLength = targetLength>>0; //floor if number or convert non-number to 0;
        padString = String((typeof padString !== 'undefined' ? padString: ''));
        if (this.length > targetLength) {
            return String(this);
        }
        else {
            targetLength = targetLength-this.length;
            if (targetLength > padString.length) {
                padString += padString.repeat(targetLength/padString.length); //append to original to ensure we are longer than needed
            }
            return String(this) + padString.slice(0,targetLength);
        }
    };
}
```

**总结**

翻看 `uView ui` 源码才知道有 `padStart` 这么一个方法的存在, 去 MDN 查到了相关的文档学习, 目前不推荐使用.

看源码很有意思, 可以学习到一些开发骚技巧来补充自己的不足.

**参考**
[String.prototype.padStart() - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)
[String.prototype.padEnd() - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/padEnd)