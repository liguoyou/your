---
title: 基于 Hexo 搭建 Gitee 个人静态博客
date: 2020-05-29 00:04:28
tags:
---

## 首次搭建博客

**1. 全局安装 Hexo** 这里我使用的是 `yarn`

`yarn global add hexo-cli`

或者

`npm install -g hexo-cli`



**2. 初始化文件夹**

创建 文件夹 hexo, 在 hexo 中打开 git Bash, 执行:

`hexo init` *网速不好的话, 这里可能需要点时间*

{% asset_img image-20200526204940892.png hexo init 成功 %}

init 后目录如下, 关于目录及文件的说明请查看[官方文档(中文)]( https://hexo.io/zh-cn/docs/ )

{% asset_img image-20200526205207269.png hexo init 生成的目录 %}



**3. 编译&运行**

打开代码, 从 `package.json` 文件中可以看到:

执行 `yarn build` 进行编译, 再执行 `yarn server` 可以运行服务

{% asset_img image-20200526210826688.png 编译&运行 %}



当然, 更便捷的操作是执行

`hexo generate` && `hexo server`

简写

`hexo g` && `hexo s`

{% asset_img image-20200526211114174.png hexo g && hexo s %}



**4. 首次运行如图** 默认主题 landscape

{% asset_img image-20200526210938239.png 首次运行 %}



## 安装主题

**1. 选择主题** 我这里选择的主题是 [Ocean](https://zhwangart.github.io/),

在[官方的主题](https://hexo.io/themes/)里面选择一个你喜欢的主题, 按照主题的[文档](https://zhwangart.github.io/2018/11/30/Ocean/)来就好

下载好慢....等等等...

**2. 重启**

`hexo clean && hexo g && hexo s` 新的主题:

{% asset_img image-20200526221210487.png 新主题 %}

## 部署博客到Gitee上

**1. 准备工作**

(1) 首先到 Gitee 注册账号并创建仓库, 例如: your, 并开启 Gitee pages 服务

{% asset_img image-20200526235114597.png 创建仓库及开启服务 %}

(2) 配置 `_config.yml`

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

(3) 安装自动部署发布工具
`yarn add hexo-deployer-git`

发布博客

`hexo clean && hexo g && hexo d` 

进入 Gitee Pages 点击更新按钮, 等待部署完成即可



**值得注意的是**

执行`hexo d`之后, 会将编译后的文件 push 至仓库中

如图: 

{% asset_img image-20200526235831345.png hexo d %}





**但是, 我想把整个本地的仓库 push 上去怎么做呢**

那就将编译后的整个仓库的内容提交(`git push`)上去,  ([更多关于 git 的操作]())

{% asset_img image-20200527224538977.png git push %}

然后设置部署目录为 public 即可

{% asset_img image-20200527000632686.png 设置部署目录为 public %}



**参考**

 https://hexo.io/zh-cn/docs/  官方文档, 参数很详细

 https://zhwangart.github.io/2018/11/30/Ocean/

 https://segmentfault.com/a/1190000018662692 

 https://www.jianshu.com/p/5014133ba61a 