---
title: Git 常用操作命令总结
date: 2020-05-31 23:38:41
tags:
excerpt: Git 常用操作命令总结, 远程仓库管理 git push, git remote -v, 分支操作 git checkout -b devel, 打标签 git tag, 记录回滚 git reset --hard commit_id 等
---

## 场景:
小 G 去了一家公司, 公司使用了 git 作为源代码的管理工具

## 工具

第一步先下载一个敲命令的工具: Git Bash

## 使用技巧:

键盘 **上箭头** 和 **下箭头** 可以快速切换到历史命令

在对应的目录右键, 选择 Git Bash Here, 即可直接在当前目录下打开命令界面

{% asset_img git_01.png "右键, 选择 Git Bash Here" %}

---

## 准备工作: 获取项目源代码

**生成 SSH 公钥, 发给 Git 服务器的管理员, 获得代码授权**

```bash
# 生成 SSH 公钥
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"

# 查看公钥, 全部复制粘贴
cat ~/.ssh/id_rsa.pub
```

**配置 Git 使用者信息**

```bash
git config --global user.name "GuoYou.Li"
git config --global user.email GuoYou.Li@foxmail.com
```

**注:** 执行了上面的命令之后, 以后的每个项目的每次 Git 提交时, 都是使用了 这两条信息来说明是谁提交了更新

**克隆项目到本地指定的文件夹内**

```bash
# git clone 你的项目地址
git clone git@github.com:liguoyou/vue-demo.git
```

进入文件夹: cd 项目文件夹名

```bash
cd vue-demo
```

**注:** cd .. 返回上一层; cd ../../ 返回两层; cd view/home 进入文件夹

**npm 安装依赖包**

更推荐使用 `yarn`

```bash
npm install
# cnpm install (如果你安装了淘宝镜像, 速度会更快些)

# 运行项目 (具体命令还需要查看 package.json 里面的 scripts)
npm run serve
```

{% asset_img git_02.png "git reflog" %}

**注:** Ctrl + c 可以结束项目运行

---

## 项目开发过程的 Git 使用

**流程**

远程库<`git pull`> 工作区 <`git add`> 暂存区 <`git commit`> 版本区<`git push`> 远程库

**查看当前状态**

```bash
git status
```

**把修改的文件提交到暂存区**

```bash
# git add 文件名
git add vue.config.js
```

**把修改的所有文件提交到暂存区**

```bash
git add .
```

**把暂存区的内容提交到版本区, -m 为 message 的意思**

```bash
# git commit -m 备注信息
git commit -m 'create vue.config.js'
```

**也可以一步直接提交暂存区和版本区, -a 为 add 的意思**

```bash
# 新增的文件第一次提交, 不能使用
git commit -a -m '这里填写修改的备注信息'
```

**查看提交的历史记录**

```bash
git log
```

{% asset_img git_03.png "查看提交的历史记录" %}

**注:** 回车可以往下查看更多记录, 输入 q 可以退出当前 log 界面

**回滚指定版本, 先从 git log 中找到要回滚的 commit id**

```bash
# git reset --hard commit_id
git reset --hard 33cb2b7ec310f5ed
```

**回到上一个 commit 版本**

```bash
git reset --hard HEAD^

# 再上一个
git reset --hard HEAD^1
```

**查看所有操作日志, 包含刚刚 reset 的 commit 日志**

```bash
git reflog
```

{% asset_img git_04.png "git reflog" %}

**提交代码到代码库**

```bash
git push

# 相当于, origin 默认, 可省略, master 是分支, 也可以省略
git push origin master
```

***

## 多个远程仓库

假设我们需要推送到另一个远程仓库

**关联远程仓库**

```bash
# gitee 是给这个远程的一个命名, 和 origin 一样是一个远程的别称, origin 为默认
git remote add gitee git@gitee.com:liguoyou/vue-demo.git

# 查看远程
git remote -v
```

**推送代码到码云这个远程仓库**

```bash
git push gitee
```

***

## 分支相关

**查看当前所有分支**

```bash
git branch
```

**创建一个分支 devel, 并切换到该分支**

```bash
git branch devel

git checkout devel

# 快捷操作, 创建分支的同时切换到该分支, -b 是 branch 的意思
git checkout -b devel2
```

**删除分支 devel2**

```bash
git branch -d devel2

# 如果 devel2 有内容修改没有合并, 则需要 -D 才能完成删除
git branch -D devel2
```

**合并 devel 的内容到 master 分支上**

```bash
# 提交 devel 分支上的修改内容
git commit -a -m 'update test.vue'

# 切换到 master
git checkout master

# 执行合并
git merge devel
```

**切换到 devel 分支, 并 push 到远程**

```bash
# 切换到 devel 分支
git checkout devel

git push
```

会提示你当前分支在远程没有对应的上游分支

**设置上游分支**

```bash
git push --set-upstream origin devel
```

{% asset_img git_05.png "设置上游分支" %}


**如果发现 push 失败, 很有可能是因为其他人修改提交了代码, 你需要先拉取最新的代码下来, 合并之后再提交**

```bash
git pull
```

***

## 打版本标签

**添加标签**

```bash
# 打标签
git tag 1.0.0

# add一个标签 -a,  并写 message -m
git tag -a 1.1.0 -m "v 1.1.0"
```

**查看标签**

```bash
git tag
```

**同步标签到远程**

```bash
git push 1.0.0

# 或者, 注意是两个 -
git push --tags
```

{% asset_img git_06.png "打版本标签" %}

暂时这么多.