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
<!-- Your.vue template -->
<view v-show="isShow" class="indicator">1/5</view>
```

```js
// Your.vue JavaScript
data() {
  return {
    // 显示控制
    isShow: false
  }
}
```

```scss
// Your.vue style
<style land="scss" scoped>
  .indicator {
    /* 其他... */
    display: flex;
    /* 其他... */
  }
</style>
```

预期表现: **view 不显示**

结果: 在微信浏览器端 `view` 不显示, 在微信小程序中**显示了!**

## 原因分析

`v-show="false"` 在 H5 浏览器中解析为 `style="display: none;"` 从而隐藏元素;

```html
<view v-show="isShow" class="indicator" style="display: none;">1/5</view>
```

在小程序中则通过 css 隐藏元素, 而 display: flex; 的层级覆盖了标签属性的样式, 导致`view` 显示在页面上了

```css
/* 微信小程序生成的 css */
view[hidden] {
  display: none;
}
```

所以根本原因是因为 **CSS 的权重** 不一样导致样式被覆盖造成了元素没有正确的被隐藏:

### `CSS` 的权重等级

{% asset_img 20200821224131.jpg "css 权重" %}

> !important, 权值为最大
> 代表内联样式，如: style=""，权值为 1000。
> 代表 ID 选择器，如：#content，权值为 100。
> 代表类，伪类和属性选择器，如 .content，权值为 10。
> 代表元素和伪元素选择器，如 h2、:before 与 :after，权值为 1。

那么可以得到如下:

微信小程序隐藏元素样式: **权重值 1 + 10 = 11**

```css
/* 设置 v-show="false" 后微信开发者工具中可以看到 */
view[hidden] {
  display: none;
}
```

---

### 情况 1: 使用全局样式

```scss
/* 全局样式 uni.scss */
.your-flex {
  display: flex;
  align-items: center;
}
```

最终生成的样式 `.your-flex { display: flex; align-items: center; }` **权重值 10**

10 < 11 这种情况使用 `v-show="false"` **是生效的**

---

### 情况 2.1: 页面或组件内写样式

```scss
/* scoped 模式下: */
<style land="scss" scoped>
  .indicator {
    /* 其他... */
    display: flex;
    /* 其他... */
  }
</style>
```

最终的 `html` 会生产一串 `css`: 比如: `class="indicator data-v-01011e28"`,

`.indicator.data-v-01011e28 { display: flex; }` **权重值 10 + 10 = 20**

20 > 11 这种情况使用 `v-show="false"` **是无效的**

---

### 情况 2.2: 页面或组件内写样式

```scss
/* 去掉 scoped */
<style land="scss">
  .indicator {
    /* 其他... */
    display: flex;
    /* 其他... */
  }
</style>
```

最终生成的 `css` `.indicator { display: flex; }` **权重值 10**

10 < 11 这种情况使用 `v-show="false"` **是生效的, 但是并不推荐**

---

### 情况 2.2.1: 值得注意的是

如果外层被其他元素包裹了:

```scss
/* 去掉 scoped */
<style land="scss">
  .container {
    /* 其他... */
    .indicator {
      /* 其他... */
      display: flex;
      /* 其他... */
    }
  }
</style>
```

`.container .indicator { display: flex; }`: **权重值 10 + 10 = 20**

20 > 11 这种情况使用 `v-show="false"` **是无效的**

---

## 解决方法

通过上面的分析, 可以得到下面的解决方式

1. 使用全局样式设置 `display: flex;`

```scss
/* 全局样式 uni.scss */
.your-flex {
  display: flex;
  align-items: center;
}
// 比如直接 使用 ui 组件库 uView 的 .u-flex
```

2. 设置 `view[hidden]` 最高权重

```scss
/* #ifdef MP-WEIXIN */
view[hidden] {
  display: none !important;
}
/* #endif */
```

2. 不使用 `v-show`, 通过行内样式 `style` 设置 `display: "none"` 实现隐藏

3. 使用 `v-if` 代替 `v-show`, 两个实现隐藏的机制不一样, 不推荐

**参考**

[微信小程序条件渲染 hidden 与 wx:if 的比较和不生效问题 - CSDN](https://blog.csdn.net/longfeng1234/article/details/94436068)
[CSS：权重 - 简书](https://www.jianshu.com/p/a8cf1e55032c)
