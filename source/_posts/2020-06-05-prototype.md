---
title: JS中的构造函数、原型与原型链
date: 2020-06-05 22:05:08
tags:
  - JavaScript
  - 构造函数
  - 原型
  - 原型链
  - hasOwnProperty
  - instanceof
categories:
  - [学习笔记, JavaScript]
excerpt: JS中的构造函数、原型与原型链, 什么是构造函数? hasOwnProperty 以及 instanceof 的用法
---

## 构造函数

```js
// ES5
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.showInfo = function () {
  console.log(this.name + " " + this.age);
};

const zhangsan = new Person("张三", 23);

// ES6
// class Person {
//   constructor(name, age) {
//     this.name = name;
//     this.age = age;
//   }

//   showInfo () {
//     console.log(this.name + " " + this.age);
//   };
// }
```

### 什么是构造函数？

上面的 `Person` 就是构造函数,

> **constructor** 是一种用于创建和初始化 class 创建的对象的特殊方法。
> —— MDN web docs

### 构造函数的特点

{% blockquote %}

- 构造函数的函数名的第一个字母通常大写。

- 函数体内使用 `this` 关键字，代表所要生成的对象实例。

- 生成对象的时候，必须使用 `new` 命令来调用构造函数。

{% endblockquote %}

### new 命令的作用

{% blockquote %}

new 命令的作用，就是执行一个构造函数，并且返回一个对象实例

new 一个对象的过程

- 创建一个空对象，作为将要返回的对象实例。

- 将空对象的原型指向了构造函数的 `prototype` 属性。

- 将空对象赋值给构造函数内部的 `this` 关键字。

- 开始执行构造函数内部的代码。

{% endblockquote %}

最后返回 `this` 赋值给 zhangsan

再看这些：

- `const o = {}` 是 `const o = new Object()` 的语法糖

- `const a = []` 是 `const a = new Array()` 的语法糖

- `function Fn () {}` 是 `const Fn = new Function ()` 的语法糖

也就是说，`Object`、`Array`、`Function` 就是构造函数，而 o, a, Fn 就是它们的一个实例

**那么这里, zhangsan 就是 `new Person` 后得到的一个对象实例**:

{% asset_img prototype_01.png zhangsan %}

从输出的结果上可以看到:
zhangsan 拥有了 `name` 和 `age` 属性, 在原型`__proto__`上还有一个 `showInfo` 的方法

下面看看了解一下原型`__proto__`的规则

## 原型规则

(1) 所有的引用类型(数组、对象、函数)，都具有对象特性，即可自由扩展属性，`null` 除外。

```js
const obj = {};
obj.a = 100;

const arr = [];
arr.b = 100;

const fn = function () {};
fn.c = 100;

const nObj = null;
nObj.d = 100; // 报错
```

(2) 所有的引用类型（数组、对象、函数），都有一个 `__proto__` 属性，属性值是一个普通的对象。

(3) 所有的函数，都有一个 `prototype` 属性，属性值也是一个普通的对象。

_约定 `__proto__` 为隐式原型，`prototype` 为显式原型_

(4) 所有的引用类型(数组、对象、函数)，`__proto__` 属性值指向它的构造函数的 `prototype` 属性值，

在代码例子中，

```js
// 项目中不推荐直接使用 XX.__proto__
console.log(zhangsan.__proto__ === Person.prototype); // true
```

(5) 当试图得到一个对象的某个属性，如果这个对象本身没有这个属性，那么它就会去它的 `__proto__`, 即它的构造函数的 `prototype` 属性值中去寻找。

代码例子:

```js
// 调用了 zhangsan.__proto__ (即 Person.prototype) 的 showInfo 方法
zhangsan.showInfo(); // 张三 23

// 调用了 zhangsan.__proto__.__proto__ 即(Object) 的 toString 方法
console.log(zhangsan.toString()); // [object Object]
```

## 原型链

{% asset_img prototype_02.png 原型链 %}
_图片来源于慕课网 并经过修改处理_

**注解:**

如同上面所说,

将 zhangsan 的原型指向了构造函数(`Person`)的 `prototype` 属性。

而更深一层, 构造函数(`Person`)的原型又指向了 构造函数(`Object`) 的 `prototype` 属性, 一直到 null

## 相关补充

### hasOwnProperty

可以使用 `hasOwnProperty()` 方法来判断是否是自身的属性

```js
zhangsan.eat = function () {
  console.log("正在吃...");
};

// 调用了自身的 eat 方法
zhangsan.eat(); // 正在吃...

// 判断 eat 是否是自身的属性/方法
console.log(zhangsan.hasOwnProperty("eat")); // true

// 判断 showInfo 是否是自身的属性/方法
console.log(zhangsan.hasOwnProperty("showInfo")); // false
```

### instanceof

用于判断引用类型属于哪个构造函数的方法

`instanceof` 的判断逻辑: 通过 `__proto__` 一层一个层往上找,

```js
const obj = {};
console.log(obj instanceof Object); // true

const arr = [];
console.log(arr instanceof Array); // true
console.log(arr instanceof Object); // true

const fn = function () {};
console.log(fn instanceof Function); // true
console.log(fn instanceof Object); // true
```

以上为学习笔记

**参考**
[慕课网](https://coding.imooc.com/class/115.html)
[JavaScript 中的构造函数 - 掘金](https://juejin.im/entry/584a1c98ac502e006c5d63b8)
[`Object.prototype.__proto__` - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)
