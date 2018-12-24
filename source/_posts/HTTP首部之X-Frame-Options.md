---
title: HTTP首部之X-Frame-Options
date: 2017-06-29 09:16:44
tags:
- HTTP首部
- 盗链
- X-Frame-Options
---

  这几天在阅读HTTP相关的书籍，先从简单的入手，先看别人推荐的《图解HTTP》。
  
  看到HTTP首部的讲解，看到了一个首部名为X-Frame-Options，这个字段是响应首部字段，也就是说是在我们浏览器调试中显示的response headers里面显示的。

  那么这个首部字段是做什么的呢?定义就不展示了直接贴上MDN的定义：
  > X-Frame-Options HTTP 响应头是用来给浏览器指示允许一个页面可否在frame,iframe或者object 中展现的标记。网站可以使用此功能，来确保自己网站的内容没有被嵌到别人的网站中去，也从而避免了点击劫持 (clickjacking) 的攻击。

  >X-Frame-Options 有三个值:
  DENY
  表示该页面不允许在 frame 中展示，即便是在相同域名的页面中嵌套也不允许。
  SAMEORIGIN
  表示该页面可以在相同域名页面的 frame 中展示。
  ALLOW-FROM uri
  表示该页面可以在指定来源的 frame 中展示。  
  
  这个说的更直白一点就是可以防止直接盗链，大家可以找相关的网址测试。

  如果不设置X-Frame-Options这个字段，默认的情况就是这个网站可以被frame引用，也就是可以被连接嵌入，这样就产生了盗链问题。

  以下面的例子展示一下：（用了百度跟Google，嘿嘿）

  ```html

  <iframe src="https://www.baidu.com" width="100%" height="300px"></iframe>
  <iframe src="https://www.google.com" width="100%" height="200px"></iframe>

  ```

  代码运行[jsfiddle](https://jsfiddle.net/dazhizhemu/1z8cd9mg/)

  想起之前的新闻，乐视与优酷之间的因为盗链引发的官司，优酷状告乐视盗链，我看了下两家的网站都没有启用这个选项，不知道为什么，可能是为了分享视频方便？我觉得应该不是这个问题。
  去看了一下youtube的视频外链处理方式，首先播放页面是不允许盗链的，设置了X-Frame-Options同源可以引入。外站要引入视频是 "https://www.youtube.com/embed/0nhrzAUeOVo" 这种格式的，有上下文/embed/,这个网址的X-Frame-Options没有设置，可以在任何网站嵌入。

  所以说吧有些问题可能就不是单纯的从技术角度可以考虑或者理解的了，我真的不信优酷、乐视等网站不懂这些东西，可能有别的原因。

  另外就是有了X-Frame-Options就能解决盗链问题了？

  找到了chrome extension的一个工具可以解决这个不准盗链的问题，这个扩展直接把返回的请求头里面的X-Frame-options给移除了，哈哈哈，不过这个是利用工具了，也就是浏览器行为了，不是网站可以处理的。

  忘了写Nginx如何添加X-Frame-Options支持了，在server里：add_header X-Frame-Options "SAMEORIGIN";
  其他的Apache IIS等在 [MDN网址](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) 上有相关说明。
