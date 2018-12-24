---
title: HTTP安全相关响应首部
date: 2017-06-29 13:09:41
tags:
- HTTP安全
- 响应首部
---

  上一篇文章记录了一下X-Frame-Options响应首部，这次记录下其他的与HTTP安全相关的首部字段与信息。

  ### X-XSS-Protection

  [X-XSS-Protection MDN解释](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/X-XSS-Protection)

  > HTTP X-XSS-Protection 响应头是Internet Explorer，Chrome和Safari的一个功能，当检测到跨站脚本攻击 (XSS)时，浏览器将停止加载页面。虽然这些保护在现代浏览器中基本上是不必要的，当网站实施一个强大的Content-Security-Policy来 禁用内联的JavaScript ('unsafe-inline')时, 他们仍然可以为尚不支持 CSP 的旧版浏览器的用户提供保护。

  > X-XSS-Protection: 0 禁止XSS过滤。
  X-XSS-Protection: 1 启用XSS过滤（通常浏览器是默认的）。 如果检测到跨站脚本攻击，浏览器将清除页面（删除不安全的部分）。
  X-XSS-Protection: 1; mode=block 启用XSS过滤。 如果检测到攻击，浏览器将不会清除页面，而是阻止页面加载。
  X-XSS-Protection: 1; report=<reporting-uri> 启用XSS过滤。 如果检测到跨站脚本攻击，浏览器将清除页面并使用CSP report-uri指令的功能发送违规报告。

  Nginx配置方法：add_header X-XSS-Protection "1; mode=block";

  ### X-Content-Type-Options

  [X-Content-Type-Options MDN解释](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/X-Content-Type-Options)

  > X-Content-Type-Options 响应首部相当于一个提示标志，被服务器用来提示客户端一定要遵循在 Content-Type 首部中对  MIME 类型 的设定，而不能对其进行修改。这就禁用了客户端的 MIME 类型嗅探行为，换句话说，也就是意味着网站管理员确定自己的设置没有问题。

  > X-Content-Type-Options: nosniff
  nosniff
  假如请求类型为以下两种，那么阻止请求的发生：
  "style" 但是 MIME 类型不是 "text/css"，
  "script" 但是 MIME 类型不是  JavaScript MIME 类型。

  这个首部主要解决的问题是MIME嗅探问题。

  MIME嗅探是什么呢？MIME嗅探是浏览器对接收到的文件类型（可能传输说明的是text，但是实际上却是image）进行解析，然后按照自己解析出来的文件类型进行处理，目的是为了解决有些文件类型错误导致无法正确显示或者执行的问题。

  好心但是带来了大问题，因为按照自己嗅探的结果执行那就有了被攻击的可能，比如一个上传图片的过程，图片上传时实际上是一个js，MIME嗅探完毕后认为这个是JS，然后就执行js代码，js为XSS等攻击代码，导致问题出现。

  有了X-Content-Type-Options的设置，要求客户端一定遵循content-type的设定，那么就能够比较好的解决这个问题，不让坏人有机可乘。

  Nginx配置：add_header X-Content-Type-Options nosniff;

  ### Strict-Transport-Security

  [Strict-Transport-Security MDN解释](https://developer.mozilla.org/zh-CN/docs/Security/HTTP_Strict_Transport_Security)

  > HTTP Strict Transport Security (通常简称为HSTS) 是一个安全功能，它告诉浏览器只能通过HTTPS访问当前资源, 禁止HTTP方式.

  HTTPS比HTTP安全这个就不用说了，嘿嘿，启用这个的好处也就不多说了，本博客也是启用了HSTS的，强制不允许HTTP访问。

  语法：
  > Strict-Transport-Security: max-age=expireTime [; includeSubdomains]
  
  max-age设置过期时间，单位是秒，includeSubdomains是是否包含过子域名的意思。

  Nginx配置: add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

  ### Content-Security-Policy

  这个叫“内容安全政策”，最终设置的东西也会展示在Response Header里，内容比较多，建议直接上MDN学习，给出的很详细，网址如下:

  [Content-Security-Policy MDN解释](https://developer.mozilla.org/zh-CN/docs/Web/Security/CSP/Using_Content_Security_Policy)


