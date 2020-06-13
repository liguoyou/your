---
title: EventTarget.removeEventListener() 取消监听事件不起作用(失效)的原因
date: 2020-06-12 23:24:26
tags:
  - 事件监听
  - addEventListener
  - removeEventListener
categories:
  - [问题解决, JavaScript]
excerpt: 通过 addEventListener() 给 document 对象注册了 click 事件监听, 再通过 removeEventListener() 取消监听事件不起作用(失效)的原因
---

## 问题产生的原因

**没有正确匹配到要删除的事件监听**

## 监听和取消事件

`addEventListener()` 语法

```js
target.addEventListener(type, listener[, options])
target.addEventListener(type, listener[, useCapture])
```

`removeEventListener()` 语法

```js
target.removeEventListener(type, listener[, options])
target.removeEventListener(type, listener[, useCapture])
```

**参数说明**

`type`: 表示监听事件类型的字符串。

`listener`: 目标事件绑定的回调函数

`options`: 一个指定有关 `listener` 属性的可选参数对象

`useCapture`: 指定需要移除的 `EventListener` 函数是否为捕获监听器。`true` 表示“事件捕获”, 默认为 `false`, 表示“事件冒泡”

### `removeEventListener()` 移除成功需要满足

- 需要移除的事件类型必须是一样的, 比如: `click`
- 需要从目标事件移除的 `EventListener` 函数必须和 `addEventListener` 中注册的是**同一个**, 也就是说**引用地址是相同的**
- 指定需要移除的 `EventListener` 函数的`useCapture` 和 `addEventListener` 中注册的相同的

### 举例说明

1. 设置 `useCapture`

```js
element.addEventListener("mousedown", handleMouseDown, true);
```

```js
element.removeEventListener("mousedown", handleMouseDown, false); // 失败
element.removeEventListener("mousedown", handleMouseDown, true); // 成功
```

2. 设置 `options`

```js
element.addEventListener("mousedown", handleMouseDown, { passive: true });
```

注意此时的 `useCapture` 默认为 `false`, 所以 `removeEventListener` 也应该设置 `useCapture` 为 false

```js
element.removeEventListener("mousedown", handleMouseDown, { passive: true }); // 成功
element.removeEventListener("mousedown", handleMouseDown, { capture: false }); // 成功
element.removeEventListener("mousedown", handleMouseDown, { capture: true }); // 失败
element.removeEventListener("mousedown", handleMouseDown, { passive: false }); // 成功
element.removeEventListener("mousedown", handleMouseDown, false); // 成功
element.removeEventListener("mousedown", handleMouseDown, true); // 失败
```

## 可能遇到的问题

### 1.使用了匿名函数

这样写是**无效**的

```js
// index.vue
create (){
  document.addEventListener( "click", function() { console.log("匿名函数, click") }, false )
},

destroyed() {
  // 这样写是无效的
  document.removeEventListener( "click", function() { console.log("匿名函数, click") }, false )
},
```

需要改成:

```js
// index.vue
create (){
  document.addEventListener( "click", this.callBack, false )
},

destroyed() {
  document.removeEventListener( "click", this.callBack, false )
},

methods: {
  callBack() { console.log("匿名函数, click") }
}
```

### 2.忽略了函数的引用地址

这样写是**无效**的

```js
// index.vue
create (){
  this.eventListener("add")
},

destroyed() {
  this.eventListener("remove")
},

methods: {
  eventListener(val) {
    const callBackFn = () => {
      console.log("函数表达式, click")
    }
    if (val === "add") {
      document.addEventListener("click", callBackFn)
    } else if (val === "remove") {
      document.removeEventListener("click", callBackFn)
    }
  },
}
```

因为 `create()` 和 `destroyed()` 两次的调用分别初始化了一个函数 `callBackFn`, 这两个 `callBackFn` 存放在内存中的地址是不一样的, 是两个没有关系的函数, 所以 `removeEventListener()` 是不起作用(失效)的

同理改成下面这样: 把 `callBackFn` 提取到一个公共方法中:

```js
// code...
methods: {
  eventListener(val) {
    if (val === "add") {
      document.addEventListener("click", this.callBackFn)
    } else if (val === "remove") {
      document.removeEventListener("click", this.callBackFn)
    }
  },

  callBackFn() {
    console.log("函数表达式, click")
  }
}
```

归根结底的原因是: **没有正确匹配到要删除的事件监听**

**参考**

[EventTarget.addEventListener - MDN web docs](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)
[EventTarget.removeEventListener - MDN web docs](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/removeEventListener)
