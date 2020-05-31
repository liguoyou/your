---
title: 基于 Hexo 搭建 Gitee 个人静态博客
date: 2020-05-29 00:04:28
tags:
top: true
excerpt: 基于 Hexo 搭建静态博客的过程记录及问题解决方案, 本文选择的是 Ocean 主题
---

## 首次搭建博客

_本文默认你已经安装了 Node.js 环境以及 npm 或者 yarn 资源管理工具_

### 1. 全局安装 Hexo

本文使用的是 `yarn`

```bash
yarn global add hexo-cli
# 或者
npm install -g hexo-cli
```

### 2. 初始化文件夹

创建文件夹 ~~hexo~~ your (**后面我把文件夹名改成了 your, 保持和访问的根目录一直**), 

在 ~~hexo~~ your 中打开 git Bash, 执行:

```bash
# 网速不好的话, 这里可能需要点时间
hexo init
```

{% asset_img image-20200526204940892.png "init 成功" %}

init 后目录如下, 关于目录及文件的说明请查看[官方文档(中文)](https://hexo.io/zh-cn/docs/)

{% asset_img image-20200526205207269.png "init 生成的目录" %}

### 3. 编译&运行

打开代码, 从 `package.json` 文件中可以看到:

```bash
# 编译
yarn build
# 启动服务
yarn server
```

{% asset_img image-20200526210826688.png 编译&运行 %}

当然, 更便捷的操作是执行

```bash
hexo generate && hexo server
```

简写

```bash
hexo g && hexo s
```

{% asset_img image-20200526211114174.png hexo g && hexo s %}

### 4. 首次运行如图

默认主题 landscape

{% asset_img image-20200526210938239.png 首次运行 %}

## 安装主题

### 一. 选择主题

本文选择的主题是 [Ocean](https://zhwangart.github.io/),

在[官方的主题](https://hexo.io/themes/)里面选择一个你喜欢的主题, 按照主题的[文档](https://zhwangart.github.io/2018/11/30/Ocean/)来就好

下载好慢....等等等...

### 遇到的问题:

#### (1) Cannot GET /js/search.js 404 这个问题搞了好久...下面是历程:

{% blockquote %}
一开始按照作者的文档操作配置

安装插件依赖
`yarn add hexo-generator-searchdb`

在 `./_config.yml` 中添加配置 (注意: 这不是 ocean 中的)
```bash
# 本地检索 hexo-generator-searchdb
search:
  path: search.xml
  field: post
  content: true
```
{% endblockquote %}


然后就报错 Cannot GET /js/search.js 404!!

**首先**, 按照 [关于搜索问题 sevilen 同学给了一个详细的步骤](https://github.com/zhwangart/gitalk/issues/7)

在 `\themes\ocean\layout\_partial\after-footer.ejs` 中配置
```bash
# 不管是这个
<% if (theme.search){ %>
  <%- js('js/search') %>
<% } %>
# 还是这个
<% if (theme.local_search.enable){ %>
  <%- js('/js/search') %>
<% } %>
```
然而都没有起作用. 甚至因为我没有 local_search 配置而导致报错..

**然后** 直接找到加载 js 的地方修改路径

{% codeblock lang:bash themes\ocean\source\js\ocean.js %}
# 原代码
# $.getScript('/js/search.js',
# 修改为
$.getScript('js/search.js',

# 原代码
# searchFunc("/search.xml",
# 修改为
searchFunc("search.xml",
{% endcodeblock %}

首页可以了! 内页还是不行啊, 一看请求失败的链接, 居然把路径拼在了整个地址的最后, 好无奈:

```
http://localhost:4000/your/2020/05/29/%E5%9F%BA%E4%BA%8E-Hexo-%E6%90%AD%E5%BB%BA-Gitee-%E4%B8%AA%E4%BA%BA%E9%9D%99%E6%80%81%E5%8D%9A%E5%AE%A2/js/search.js?_=1590852138728
```


**最后使用了一种不是很好的办法解决了**

在文件路径前面添加根路径`/your/` (_config.yml 中的 `root: /your/`)

{% codeblock lang:bash themes\ocean\source\js\ocean.js %}
# 原代码
# $.getScript('/js/search.js',
# 修改为
$.getScript('/your/js/search.js',

# 原代码
# searchFunc("/search.xml",
# 修改为
searchFunc("/your/search.xml",
{% endcodeblock %}

就**ok**了!

#### (2) 首页的视频 / 图片 / 图标 / logo / favicon.ico 等无法正常显示

图片的问题和上面类似, 直接写解决方案: 直接查看官方文档添加[辅助函数 url_for](https://hexo.io/zh-cn/docs/helpers#url-for)就可以顺利解决: 

**修改以下文件**

{% codeblock lang:html themes\ocean\layout\_partial\sidebar.ejs %}
<!-- 原代码 -->
<%- theme.brand %>

<!-- 修改为 -->
<%- url_for(theme.brand) %>
{% endcodeblock %}

{% codeblock lang:html themes\ocean\layout\_partial\head.ejs %}
<!-- 原代码 -->
<%- theme.favicon %>

<!-- 修改为 -->
<%- url_for(theme.favicon) %>
{% endcodeblock %}

{% codeblock lang:html themes\ocean\layout\_partial\ocean.ejs %}
<!-- 原代码 有五处 -->
theme.ocean.path
<!-- 修改为 -->
url_for(theme.ocean.path)

<!-- 原代码 -->
<%- theme.ocean.brand %>
<!-- 修改为 -->
<%- url_for(theme.ocean.brand) %>
{% endcodeblock %}

#### (3) 修改文件, 比如修改样式部署之后不生效?

一般是缓存文件问题, 试试:
```bash
hexo clean && hexo g
``` 

#### (4) 关于很多的问题

直接查看作者的这篇文章 [关于 Ocean 使用中的问题](https://zhwangart.github.io/2019/07/02/Ocean-Issues/), 以及阅读 [Hexo 的文档](https://hexo.io/zh-cn/docs/), 非常详细...

**切记, 遇事冷静思考, 不要烦躁.**

### 二. 编译&重启

```bash
hexo clean && hexo g && hexo s
```

新的主题:

{% asset_img image-20200526221210487.png 新主题 %}

## 部署博客到 Gitee 上

### (1) 首先到 Gitee 注册账号并创建仓库, 例如: your, 并开启 Gitee pages 服务

{% asset_img image-20200526235114597.png 创建仓库及开启服务 %}

### (2) 配置 `_config.yml`

```yml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://liguoyou.gitee.io/your/
root: /your/
```

```yml
deploy:
  type: git
  repo: git@gitee.com:liguoyou/your.git
  branch: master
```

### (3) 安装自动部署发布工具

```bash
yarn add hexo-deployer-git
```

发布博客

```bash
hexo clean && hexo g && hexo d
```

进入 Gitee Pages 点击更新按钮, 等待部署完成即可

**值得注意的是**

如果你只想把编译完成后的 public 文件中的所有文件 push 至仓库中:

```bash
hexo d
```

如图:

{% asset_img image-20200526235831345.png hexo d %}

**但是, 我想把整个本地的仓库 push 上去怎么做呢**

那就将编译好的整个仓库的内容提交(`git push`)上去, ([更多关于 git 的操作]())

{% asset_img image-20200527224538977.png "git push" %}

然后**设置部署目录为 public** 即可

{% asset_img image-20200527000632686.png "设置部署目录为 public" %}

**每次提交后, 都需要点击更新按钮(付费用户听说会自动更新)**

**参考**
[Hexo 官方文档, 真的很详细](https://hexo.io/zh-cn/docs/)
[Ocean 中文文档 - zhwangart](https://zhwangart.github.io/2018/11/30/Ocean/)
[关于 Ocean 使用中的问题 - zhwangart](https://zhwangart.github.io/2019/07/02/Ocean-Issues/)
[Gitalk 的使用 - zhwangart](https://zhwangart.github.io/2018/12/06/Gitalk/)
[基于Gitee+Hexo搭建个人博客 - segmentfault](https://segmentfault.com/a/1190000018662692)
[使用Gitee+Hexo搭建个人博客 - 简书](https://www.jianshu.com/p/5014133ba61a)
