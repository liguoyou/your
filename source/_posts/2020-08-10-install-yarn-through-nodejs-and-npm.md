---
title: 使用 Node.js 和 npm 安装和配置 Yarn
date: 2020-08-10 00:03:27
tags:
  - Yarn
  - npm
categories:
  - [问题解决, Yarn]
excerpt: 由于种种令人心酸的原因, 我决定卸载 Node.js, npm, 以及 Yarn, 再重新安装配置, 并将过程记录了下来 
---

## 背景

由于种种令人心酸的原因, 我决定卸载 `Node.js`, `npm`, 以及 `Yarn`, 再重新安装

***

## 安装 Nodejs 和 npm

**安装**

[下载 Node.js](http://nodejs.cn/download/) 安装程序, 过程基本都是点击 next 按钮即可, 不再赘述

我的安装目录 `D:\Program Files\Nodejs`, 安装完成后, `npm` 也被安装到了电脑上

可以通过命令查看是否已经正确安装

```bash
# 查看版本
node -v

# 查看版本
npm -v
```

**配置**

为了方便管理和不占用电脑的C盘容量, 我需要修改 npm 全局依赖包下载保存目录和缓存目录

在 `Node.js` 的安装目录下新建文件夹 `node_cache` 和 `node_global`

然后执行命令

```bash
# 全局依赖包缓存目录
npm config set cache "D:\Program Files\Nodejs\node_cache"

# 全局依赖包下载保存目录
npm config set prefix "D:\Program Files\Nodejs\node_global"

# 查看配置列表
npm config list
```

看到下面的东西就代表成功了

```bash
# ...
userconfig C:\Users\guoyo\.npmrc
cache = "D:\\Program Files\\Nodejs\\node_cache"
prefix = "D:\\Program Files\\Nodejs\\node_global"
# ...
```

当然, 也可以直接打开目录 `C:\Users\[your-computer-name]\.npmrc`, 找到 `.npmrc` 这个文件, 查看里面的内容

***

## 安装与配置 Yarn

执行命令全局安装 `yarn`

```bash
# 全局安装
npm i -g yarn

# 查看版本
yarn version
```

**修改 yarn 全局安装依赖包目录与缓存目录**

```bash
# 查看全局安装依赖包保存目录
yarn global dir
# 默认 C:\Users\[your-computer-name]\AppData\Local\Yarn\Data\global

# 查看全局安装依赖包缓存目录
yarn cache dir
```

在 `Yarn` 的安装目录(D:\Program Files\Nodejs\node_global)下新建文件夹 `yarn_cache` 和 `yarn_global`

```bash
# 配置 yarn 全局安装依赖包保存目录
yarn config set global-folder "D:\Program Files\Nodejs\node_global\yarn_global"

# 配置缓存目录
yarn config set cache-folder "D:\Program Files\Nodejs\node_global\yarn_cache"

# 查看保存目录配置
yarn global dir

# 查看缓存目录配置
yarn cache dir
```

**同 npm, 也可以直接打开目录 `C:\Users\[your-computer-name]\.yarnrc`, 找到 `.yarnrc` 这个文件, 查看里面的内容**

## 测试

```bash
# 全局安装 rimraf
yarn global add rimraf
```

**特别介绍: 用 `rimraf` 删除超级大, 层级深的目录, 比如 node_modules, 非常方便**

可以看到在 `D:\Program Files\Nodejs\node_global\yarn_global\node_modules\.bin`

{% asset_img 2020-08-10_00-51-00.png "yarn global 安装目录" %}

测试: 桌面新建一个文件夹 aaa_modules, 在桌面打开 `git bash` 终端: 

```bash
# 删除文件夹
rimraf aaa_modules
```

**报错**: bash: rimraf: command not found

这时, 还需要配置环境变量, 才能使 `yarn` 全局安装的依赖包命令生效

将路径URL:  `D:\Program Files\Nodejs\node_global\yarn_global\node_modules\.bin` 复制到: 

控制面板 - 系统属性 - 高级系统设置 - 高级 - 环境变量 - 系统变量 - Path - 编辑 - 新增 - **输入内容 URL** - 确定 - 确定 - 确定

{% asset_img 2020-08-10_00-59-38.png "yarn global 环境变量配置" %}

**重启终端, 再次输入命令, 即可**

`Yarn` 就重新安装完成了!

文章很短, 但是过程很坎坷, 遇事不要烦躁, 慢慢来

**参考**
[修改Yarn的全局安装和缓存位置 - cnblogs](https://www.cnblogs.com/Jimc/p/11121632.html)
[Nodejs安装及环境配置 - 简书](https://www.jianshu.com/p/13f45e24b1de)