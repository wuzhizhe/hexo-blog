---
title: cookie是如何工作的
date: 2017-04-21 09:50:45
tags:
- cookie
- http
- 浏览器
---

Cookie是现在主流网站实现单点登录、记录用户相关信息、网站广告投放的主要技术手段。

  本文简要介绍下cookie的相关知识。

###  Cookie是什么？
简单说，cookie是浏览器维护的、保存在客户端中的纯文本字符串数据。

### Cookie的使用
Cookie最初是被设计用来保存一些服务端希望客户端能保存、每次请求都能发给服务器进行验证的数据的。这也是cookie最大的使用场景。本文将主要介绍此功能。
但是在很多开发场景中，cookie被滥用了，很多开发者对于一些本地的存储需求（并不一定要用于传输给服务端）的数据也用cookie来存储，这个是后来的localStorage的功能。
Cookie的限制：每个域名下的cookie大小基本都限制为4K，每个域名下的的cookie的数量最多为20，不同浏览器实现可能有细微差别。

### Cookie是如何工作的

Cookie是浏览器请求服务器页面时有服务器返回的数据要求进行设定的。浏览器进行http请求时，会先检查本地是否有对应域名下的cookie，如果有，浏览器就会自动的将cookie发送到服务器，这些cookie数据对服务器都是有意义的，服务器对cookie进行解析用来进行用户身份认证等，服务器解析了cookie后可以根据cookie的信息处理数据，也可以要求浏览器更新cookie；对于没有cookie的情况，如果服务端有要求，就会在返回给客户端的响应头重添加”set-cookie”要求，浏览器就会进行相应的cookie设置。

由于cookie是每次http请求都会发送给服务器的，这是一种带宽开销，所以尽量要在cookie中存储必须每次都要传输的数据，最好不要将使用次数少、意义不大的数据存在cookie中。

其他一些常见场景:
1） 自动登录功能
部分开发者做记住密码、自动登录等功能时，就是将用户的用户名、密码加密后存储在cookie中，用户打开页面时向服务器发送数据进行用户验证。
2） 广告投放
很多广告商将用户浏览的商品等相关信息存储到cookie里，在用户进入某些与广告商有合作的网站时，读取cookie信息，投放比较精准的广告。
因为广告投放的原因，很多用户禁止了浏览器的cookie功能。

### Cookie设置与使用

  Cookie的设置是有浏览器进行设置的，具体的设置也区分为由服务器请求浏览器设置与开发者在JavaScript中进行设置两种情况。
#### 服务器用Set-Cookie设置cookie

这是比较常见的方式，浏览器请求网站后，网站返回数据时要求浏览器设置cookie，具体的实现方式就是服务端在返回的response header中设置‘Set-Cookie’请求，设置相应的cookie，可以设置多个。

具体代码示例（NodeJs）：
![](/images/http-cookie/a.png)

  打开浏览器进行请求发现，第一次请求，request headers没有cookie信息，response headers有set-cookie信息，如下图：

![](/images/http-cookie/b.png)

但是第二次请求时，浏览器就会自动发送此域名下的cookie给服务器，如图：

![](/images/http-cookie/c.png)

#### 客户端设置cookie

客户端可以通过document.cookie来获取当前页面上的所有非httponly（下面解释）的cookie，并且可以由此设置cookie。

![](/images/http-cookie/d.png)

通过上图可以看出，document能获取出cookie，但是属性名为language的cookie由于是httponly的所以无法获取。
用document.cookie=”xx=yy”的这种方法可以添加新的cookie。

#### Cookie属性解析
通过上面的图可以看出cookie有以下几个属性：name value domain path expires/max-age size http(only) secure samesite；
A.  Name，cookie的名称
B.  Value，cookie值
C.  Domain，cookie所属的域名
D.  Path，网页url中的上下文比如:/images/png等，path与domain一起决定cookie在什么请求中被发送到服务端。
E.  Expires/max-age，cookie有效期:expires是http1.0协议使用的，值必须是GMT 格式的时间；max-age是http1.1协议开始使用的，有三种值，正数、0、负数，正数就是从cookie创建开始到结束的时间，单位是秒，0表示要删除掉这个cookie，负数就跟上图的session是一个意思，表示session会话内有效，session关闭后失效。
F.  Http，表示cookie是由服务端设置的且只能由服务端修改，不可以由用户客户端修改，
G.  Samesite，定义cookie如何进行跨域发送，可以有两个值：Strict，严格模式，表明这个 cookie 在任何情况下都不可能作为第三方 cookie；Lax，宽松模式，这种模式下，cookie在get请求时可以作为第三方cookie发送。

