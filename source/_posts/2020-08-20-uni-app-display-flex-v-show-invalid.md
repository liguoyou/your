---
title: uni-app 给 view 设置 display:flex 后, v-show="false" 在微信小程序中失效
date: 2020-08-20 23:40:28
tags:
  - uni-app
  - v-show
  - flex
  - 微信小程序
categorites:
 - [问题解决, uni-app]
 - [问题解决, 微信小程序]
---

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

`v-show="false"` 在 H5 浏览器中解析为 `style="display: none;"`
