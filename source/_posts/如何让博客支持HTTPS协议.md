---
title: 如何让博客支持HTTPS协议(2017-10-31更新cerbot工具)
date: 2017-10-31 16:16:31
tags: 
- https
- encrypt
---

## 申请https证书

今天突然想搞一下https，因为现在确实是https的天下了，国内很多网站都是https的了，感觉新建个博客还是http的好像有点low，所以就想搞个https支持。

说干就干，其实这个事情还真不是特别麻烦，因为https所以要有ssl的证书，所以首先申请一个ssl证书。

好多可以申请证书网站服务，根据推荐选择了[Let's Encrypt](https://letsencrypt.org/)，原因很简单：免费、快捷、支持多个域名。

证书的申请自己也没搞过，直接找的教程，用了这里的申请教程[SSL证书申请](https://imququ.com/post/letsencrypt-certificate.html)。
我是按照教程里面的申请下来的，中间生成CSR文件时有点差错，openssl.cnf文件的位置与文章里面的不太一样，用find / -name命令找到的我的机器上的该文件的位置，然后其他的就是一样的了。

申请完证书还需要配置，因为博客用的是nginx，所以接下来需要做的就是在nginx上配置ssl，配置证书以及端口、域名等。
直接贴一下nginx的相关配置代码：

```javascript

listen 443;
server_name muxblog.tk;
ssl on;#开启ssl
ssl_certificate /home/ssl/chained.pem;#公钥
ssl_certificate_key /home/ssl/domain.key;#密钥
ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;#协议
ssl_prefer_server_ciphers on;#优先服务端加密
ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;#加密套件/方式
ssl_session_timeout 5m;#超时

```
nginx配置完成之后重启nginx，用https协议就可以访问自己的博客了。

目前发现的问题

1). 用https访问博客，博客内需要的http资源在chrome浏览器会被认为不安全的，浏览器认为https下面的请求都应该是https的协议。
解决方法:查资料获取到[小胡子李靖的一篇文章](http://www.cnblogs.com/hustskyking/p/upgrade-insecure-requests.html)，用文章中的方法来解决问题，发现浏览器不提示问题了，css文件的访问没有问题，但是博客内的外链http图片没法访问了，目前未找到好的解决办法，先在此记录。

问题更新，因为实在不想在博客上存储图片等文件，小磁盘，顶不住，所以就找了下支持HTTPS的图床，发现了一个，名字就是[图床001](https://www.tuchuang001.com)，很简单，注册账户就能使用，支持https外链。
现在是暂时的解决办法，不知道这个图床001的可靠性，能撑多久，解析域名最终是浙江金华，国内图床。

**已经不使用图床001了，反正想着这些东西也是公开的可看的，直接扔github上了。（2017-06-07）**


## 证书过期遇到的问题

  今日(2017-06-07)遇到let's encrypt提醒我证书要过期了，于是想要续期证书，按照之前设置的东西进行处理时发现了问题。

  因为已经有了HTTPS，现在证书还没有过期，只是将要过期，这个时候续期发现无法续期。原因也很简单，现在强制HTTP转到了HTTPS，然后证书生成校验时用web请求就无法通过了。

  然后怎么处理呢？需要先把HTTPS强制跳转关闭，就是说可以用http访问的情况下更新https证书。。。。，这个真的太蛋疼了，这种续期的方式不可以接受，想一下其他的办法来更新解决这个问题。

  等找到了来这里更新。

### 更新此问题的解决办法  (2017-10-31)

  在github上，有cerbot工具，用github上提供的这个工具，使用脚本一键更新，或者使用定时执行脚本来自动更新。
  项目地址：[cerbot](https://github.com/certbot/certbot)
  具体使用详细阅读readme文件