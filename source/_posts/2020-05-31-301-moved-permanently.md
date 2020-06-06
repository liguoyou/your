---
title: 301 Moved Permanently (from disk cache)
date: 2020-05-31 23:10:42
tags:
  - 301
  - 浏览器缓存
categories:
  - [问题解决, 缓存]
excerpt: 301 Moved Permanently (from disk cache) 清除浏览器的301缓存
---

不得不记录下这个奇葩的遭遇,

本来一切好好的, 然后我把 vue.config.js 的

```js
devServer: {
    proxy: {
      '/api': {
        target: '测试 api 地址',
        // code...
      }
    }
},
```

把这个测试 api 地址修改成了生产环境 api 地址, 为了检查某个问题

然后再改了回来, 重启了服务, 然后就发生了不正常的事情: 获取导航的接口, 居然发生了 301 重定向到了生产环境 api 地址

一开始我觉得是不是后端改了什么东西, 后面想着先自己排查, 然后花了宝贵的一小时查找答案和后端沟通, baidu 了也没找到解决方案

然后去 google 搜了下, '301 Moved Permanently (from disk cache)', 找到了这篇文章,

{% asset_img 01.jpg "上方文章截图" %}

成功解决了. 原来是缓存的问题, 要清除浏览器的 301 缓存:

**把鼠标按住浏览器刷新按钮不放或者右键, 在弹出来的菜单中选择"清空缓存并硬性重新加载"**

**如果没有弹出菜单, 可能需要先打开浏览器的控制台再操作**

**参考**
[Chrome 浏览器清除 301 缓存 - 老谭](https://laotan.net/clear-chrome-301-disk-cache/)
上方文章来源于
[How can I make Chrome stop caching redirects? - superuser](https://superuser.com/questions/304589/how-can-i-make-chrome-stop-caching-redirects)
