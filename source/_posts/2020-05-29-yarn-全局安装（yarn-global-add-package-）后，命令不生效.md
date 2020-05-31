---
title: "yarn 全局安装（yarn global add [package]）后，命令不生效"
date: 2020-05-29 00:07:52
tags:
excerpt: 使用 yarn 全局安装 `@vue/cli`, `hexo-cli` 等, 安装成功, 但是使用命令却不生效, 一般是环境变量配置问题
---


## 背景

最近使用 npm/cnpm 很不爽, 尝试将自己的项目全部转成 yarn

## 问题描述

使用 yarn 全局安装 `@vue/cli`, `hexo-cli` 等, 安装成功, 但是使用命令却不生效...

`yarn global add hexo-cli`

## 解决过程

第一个感觉就是环境变量问题了, 本文是 win10 下的截图

(1) 找到 yarn 的全局安装目录 `yarn global bin`

{% asset_img image_01.png yarn global bin %}

(2) 将该目录地址拷贝至环境变量-系统变量-path 中:

{% asset_img image_02.png 将该目录地址拷贝至环境变量-系统变量-path %}

(3) 重启终端 `git bash`, 执行命令, **应该**就可以了.

**好吧, 我的还不可以**

卸载全局依赖包 `yarn global remove hexo` , 再重新安装 `yarn global add hexo`

**还是不行**

{% asset_img image_03.png hexo init 失败 %}

然后我发现我的安装文件在 `C:\Users\guoyo\AppData\Local\Yarn\Data\global\node_modules\.bin` 中,

尝试将刚刚的环境变量地址修改成这个, ok, 重启终端成功了(**win7 可能需要重启电脑!!**)

## 原因

1. 可能是从 npm/cnpm 切换为 yarn 某个环节不对
2. 可能是安装 yarn 的时候缺少了某个操作步骤造成的

**参考**
https://blog.csdn.net/weixin_41643133/article/details/83829235
