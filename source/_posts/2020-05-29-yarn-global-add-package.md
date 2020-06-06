---
title: "yarn 全局安装（yarn global add [package]）成功后，执行命令不生效"
date: 2020-05-29 00:07:52
tags:
  - Yarn
categories:
  - [问题解决, Yarn]
excerpt: 使用 yarn 全局安装 `@vue/cli`, `hexo-cli` 等, 安装成功, 但是使用命令却不生效, 一般是环境变量配置问题
---

## 背景

最近使用 npm/cnpm 很不爽, 尝试将自己的项目全部转成 yarn

## 问题描述

使用 yarn 全局安装 `@vue/cli`, `hexo-cli` 等

```bash
yarn global add hexo-cli
```

安装成功, 但是使用命令却不生效...

## 解决过程

第一个感觉就是环境变量问题了, 本文是 win10 下的截图

(1) 找到 yarn 的全局安装目录

```bash
yarn global bin
```

{% asset_img image_01.png yarn global bin %}

(2) 将该目录地址拷贝至系统属性-环境变量-系统变量-path 中:

{% asset_img image_02.png "将该目录地址拷贝至系统属性-环境变量-系统变量-path" %}

(3) 重启终端, **应该**就可以了.

**好吧, 我的还不可以**

{% asset_img image_03.png "hexo init 失败" %}

上图可以看到我的安装 Hexo 文件并不在 D 盘, 而在 C 盘, 尝试将刚刚新增的环境变量地址修改成图中 C 盘的地址:

```
C:\Users\guoyo\AppData\Local\Yarn\Data\global\node_modules\.bin
```

ok, 重启终端成功了(**win7 可能需要重启电脑!!**)

{% asset_img 20200531155353.jpg "修改环境变量地址" %}

## 原因

1. 可能是从 npm/cnpm 切换为 yarn 某个环节不对
2. 可能是安装 yarn 的时候缺少了某个操作步骤造成的

**参考**
[yarn 全局安装（yarn global add [package]）后，命令不生效；- CSDN](https://blog.csdn.net/weixin_41643133/article/details/83829235)
