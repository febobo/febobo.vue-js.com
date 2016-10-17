---
title: hexo自动布署到个人服务器
date: 2016-10-13 17:35:01
tags: 布署,git hook
categories: 布署,git hook
description: 这些天突然想静下心好好写博客，以前的cms不太想用了（因为后台太丑），然后就找到了hexo，因为什么，因为好看啊，markdown语法更友好，海量主题随意替换，只要你喜欢每天一个主题都不分分钟的事，不对，好像说的有点跑题了。
---

这些天突然想静下心好好写博客，以前的cms不太想用了（因为后台太丑），然后就找到了hexo，因为什么，因为好看啊，markdown语法更友好，海量主题随意替换，只要你喜欢每天一个主题都不分分钟的事，不对，好像说的有点跑题了。

言归正传，咱们回到博文上来，为什么要自动布暑呢，这个道理大家都懂，应该没有人愿意每写完一个文章然后打个包再传去服务器解压吧，说归说，做归做，那么如何做呢

## 服务器生成一个git服务器
```bash
mkdir demo && cd demo && mkdir demo.git
git init --bare
```
--bare 是指新建的这个仓库为一个裸仓，直白的来讲就是不包含工作区，只有版本控制的结构

## 配置git hooks
关于 hooks 的详情内容可以[参考这里](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)
现在我们回到刚刚新建的.git目录下的hooks
```bash
cd demo.git
vim post-receive
```
在 post-receive 文件中写入如下内容：

```bash
#!/bin/sh
git --work-tree=/var/www/demo --git-dir=/var/repo/demo.git checkout -f
```

注意
--git-dir 表示仓库所在的位置
--work-tree 表示实际文件被存放的位置
通过这个实现仓库与实际文件的分离

## 回到hexo
我们可以配置使得hexo博客方便的提交到服务器，省去了ftp的步骤。
修改hexo目录下的_config.yml文件，找到[deploy]添加一个条目

```bash
deploy:
- type: git
  repo: github仓库地址
- type: git
  repo: user@mydomain.com:/var/repo/demo.git
```
这样通过hexo deploy命令可以同时部署到github pages和服务器上了。

## 非hexo实现自动布署
进入工作目录，然后添加服务器的remote，push提交
```
git remote add deploy ssh://user@mydomain.com:/var/repo/demo.git
git push deploy master
```
