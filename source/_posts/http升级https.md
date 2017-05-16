---
title: http升级https
date: 2017-05-16 14:11:56
tags: http,https
categories: http,https
description: 最近维护的vue中文社区群里有不少热心的同学call me 说社区被挂马了？被举报了？ 我心想啥玩意，好好的非商业网站怎么会被和谐，接下来我们一起来一探究竟
---


## 原起

最近维护的vue中文社区群里有不少热心的同学call me 说社区被挂马了？被举报了？ 我心想啥玩意，好好的非商业网站怎么会被和谐，接下来我们一起来一探究竟

## 想法

即然你们腾迅好端端的报出来我的网站不安全，那么我就将http升级为https变成安全网站再让你来检测，说做就做，再做之前，我们先来简单复习一下http及https

## HTTP && https

### HTTP(HyperText Transfer Protocol，缩写：HTTP)

HTTP是一个客户端终端（用户）和服务器端（网站）请求和应答的标准（TCP）。通过使用网页浏览器、网络爬虫或者其它的工具，客户端发起一个HTTP请求到服务器上指定端口（默认端口为80）。我们称这个客户端为用户代理程序（user agent）。应答的服务器上存储着一些资源，比如HTML文件和图像。我们称这个应答服务器为源服务器（origin server）。在用户代理和源服务器中间可能存在多个“中间层”，比如代理服务器、网关或者隧道（tunnel

### HTTP(Hypertext Transfer Protocol Secure，缩写：HTTPS)

HTTPS是一种网络安全传输协议。在计算机网络上，HTTPS经由超文本传输协议进行通信，但利用SSL/TLS来加密数据包。HTTPS开发的主要目的，是提供对网络服务器的身份认证，保护交换数据的隐私与完整性

### 与HTTP的差异

与HTTP的URL由“http://”起始且默认使用端口80不同，HTTPS的URL由“https://”起始且默认使用端口443。
HTTP是不安全的，且攻击者通过监听和中间人攻击等手段，可以获取网站帐户和敏感信息等。HTTPS被设计为可防止前述攻击，并（在没有使用旧版本的SSL时）被认为是安全的。

## 撸起袖子干

我们如上已经简单的概述了一下http及https，那么我们的http如何能完整的升级为https呢 ？
其实很简单，只需要简单的几步操作即可完成。

首先我们来划分一下步骤

- 1.1 首先我们来了解一下必不可少的CA证书
即：CA证书、SSL 证书。是指由电子证书认证机构（CA, Certificate Authority）所版发的数字证书，可用于认证网站身份、加密HTTPS（基于SSL/TLS）通讯双方（客户端/服务器）的通讯内容。证书是HTTPS服务器的身份证明，证书中数据会使用不可破译格式，证书中内容包括：

所有者的身份信息
证书序列号及到期时间
加密公钥
证书颁发机构的数字签名
使用SSL 证书后，加密流程大致如下：

客户端(浏览器)通过握手获取证书，验证服务器身份，并基于SSL/TLS建立加密连接
验证服务器身份后，会浏览器地址栏中会出现一个绿色的挂锁图标，并使用https前缀
浏览器/服务器之间往来传递的信息均经过加密处理，保障传输安全
从技术角度来讲，SSL 证书主要解决了两个问题：身份认证、信息传输安全。另外，CA厂商为了提高可信度，在发生安全事故后也会给与一定数额的赔偿


- 1.2 获取证书
[https://vue-js.com](https://vue-js.com)目前使用的[腾迅云](https://www.qcloud.com/product/ssl)为期一年的免费证书，当我们拿到我们的证书以后就可以进行文档上的安装证书

- 1.3 安装证书

将域名 `www.domain.com` 的证书文件`1_www.domain.com_bundle.crt` 、私钥文件`2_www.domain.com.key`保存到同一个目录，例如`/usr/local/nginx/conf`目录下。
更新Nginx根目录下 `conf/nginx.conf` 文件如下：

```bash
server {
        listen 443;
        server_name www.domain.com; #填写绑定证书的域名
        ssl on;
        ssl_certificate 1_www.domain.com_bundle.crt;
        ssl_certificate_key 2_www.domain.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
        ssl_prefer_server_ciphers on;
        location / {
            root   html; #站点目录
            index  index.html index.htm;
        }
    }
```

- 1.4 安装完证书以后先执行`nginx -t` 来检查一下conf配置是否正确，没有问题再执行`service nginx restart`重启你的`nginx`服务,这个时候不出意外你的网站已经能正常的使用https来进行访问了


- 1.5 问题来了，如果你的网站我刚好是从http转成https 的， 那么用户并不知道你的网站已经升级成https 了怎么办 ？用户还停留在原来的http上怎么办 ？ 有些同学应该已经想到了， 最常见的办法就是rewirte(重定向) ，如下只需要在你的`nginx`配置的`server`里加上一句 `rewrite ^(.*) https://$host$1 permanent;`就ok了


## 结语

如果你的网址在浏览器的地址还没有亮绿灯的话，那么此时你需要点击小灰点，照着他的提示一步一步去修正， 一般都是网站引用非https资源造成的
