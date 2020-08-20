---
title: uni-app 给 view 设置 display:flex 后, v-show="false" 在微信小程序中失效
date: 2020-08-20 23:40:28
tags:
  - uni-app
  - v-show
  - flex
  - 微信小程序
categories:
 - [问题解决, uni-app]
 - [问题解决, 微信小程序]
---

最近在学习 uni-app 并用来开发新项目,经常用到 `v-show` 用来做元素的显示隐藏控制, 然后神奇的发现给 view 设置 display:flex 后, v-show="false" 在微信小程序中失效, 并没有把元素隐藏掉...

<!-- more -->

## 问题描述

```html
<view v-show="isShowIndicator" class="indicator">1/5</view>
```

```js
data() {
  return {
    // 显示控制
    isShowIndicator: false
  }
}
```

```css
.indicator {
  /* 其他... */
  display: flex;
  /* 其他... */
}
```

预期表现: **view 不显示**

结果: 在微信浏览器端 `view` 不显示, 在微信小程序中**显示了!**

## 原因分析

`v-show="false"` 在 H5 浏览器中解析为 `style="display: none;"` 从而隐藏元素; 

```html
<view v-show="isShowIndicator" class="indicator" style="display: none;">1/5</view>
```

在小程序中则通过 css 隐藏元素, 而 display: flex; 的层级覆盖了标签属性的样式, 导致`view` 显示在页面上了

```css
view[hidden] { display: none; }
```

## 解决方法

可以尝试下面的方式处理

1. 设置 `view[hidden]`

```css
/* #ifdef MP-WEIXIN */
view[hidden] {
  display: none !important;
}
/* #endif */
```

2. 通过不使用 `v-show`, 通过 style 设置 display 实现隐藏

3. 使用 `v-if`
