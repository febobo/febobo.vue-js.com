---
title: 用slack和hubot搭建你自己的运维机器人
date: 2016-12-02 17:33:51
tags:
description: 对于运维人员来说，用ssh登录自己的服务器进行各种操作几乎成了家常便饭。每次开发人员提交代码后，登录服务器执行git pull
---

### 场景

对于运维人员来说，用`ssh`登录自己的服务器进行各种操作几乎成了家常便饭。每次开发人员提交代码后，登录服务器执行`git pull`，或者把数据备份后压缩并下载也成为了日常工作。能不能省掉这些烦琐的步骤，让开发人员自己完成呢？现在好了，你可以用`slack`和`hubot`搭建一个自己的运维机器人，再也不用`ssh`，开发人员只需要在聊天群里对着机器人喊一声，它就乖乖地做事去了，多么省心！在整个场景中，`hubot`是那个做事情的机器人，而`slack`是聊天工具，其实也有一种搭配方式是用微信配合`hubot`，但是为了这个微信，还要注册一个单独的账号，还要用二维码扫码登录，种种不便，所以我们最后选择的还是`slack`。另外一个方案是`github`自带的`webhooks`也可以实现开发人员提交后服务器自动拉取，但是不知什么原因，配置起来总是有点麻烦，而且不稳定。所以最终我们还是选择了`slack+hubot`的方案。

### 安装hubot

...省略

### 连接hubot和slack

那么怎么和slack打通呢？其实也很简单，你只需要在`slack`上安装好`hubot`的插件，它会自动生成一个`token`，把这个`token`记下来，输入：
`env HUBOT_SLACK_TOKEN=xoxb-你的token ./bin/hubot --adapter slack`

这时候hubot就启动起来，等待接收命令了。但是由于hubot缺省加入的是slack的#general频道，如果你改掉了频道名字，或者删掉了这个频道的话，你需要重新邀请小机器人进入一个新的频道，否则没法对话。

现在你可以在slack的客户端和小机器人对话了

### 让hubot保持在线
但是如果这时候我们关掉ssh的话，小机器人就掉线了，怎么办呢？可以用nohup的方法让它留在后台，也可以用tmux：
`yum install tmux`
`tmux new -s hubot`

在tmux里执行上面的命令，然后按ctrl+b，然后再按d退出。如果再想进去，可以执行：

`tmux a -t hubot`

### 可选操作
由于每次启动hubot时，都需要执行以下一串长长的命令：

`env HUBOT_SLACK_TOKEN=xoxb-你的token ./bin/hubot --adapter slack`
如果你不想这么麻烦的话，可以把这个环境变量放在fish的config里，路径是：`~/.config/fish/config.fish`。

添加以下一行命令：

`set -x HUBOT_SLACK_TOKEN xoxb-你的token`
这样你每次启动hubot时，就只需要执行以下这句就行了：
`./bin/hubot --adapter slack`

同样，如果你嫌shellcmd太长的话，也可以加下这一句：
`set -x HUBOT_SHELLCMD_KEYWORD run`

这样你以后再需要hubot帮你执行命令的时候，只要说这一句就行了：
`hubot run update`
