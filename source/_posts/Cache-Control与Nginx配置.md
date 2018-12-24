---
title: Cache-Control与Nginx配置
date: 2017-07-27 15:46:10
tags:
- cache-control
- nginx
- proxy_cache
---
## 缓存相关问题

  项目上最近要处理前端缓存的问题，因为缓存之前一直没有处理好，所以就是时不时的有点问题，经常因为缓存不更新导致访问的都是旧数据。

  这里其实分为两个地方要处理的缓存，第一个是浏览器本身的缓存，第二个是Http代理服务器（这里用Nginx）的缓存问题。


## 浏览器配置缓存策略以及导致的问题

  chrome浏览器默认缓存，如果http服务器没有显式的设置缓存的策略，那么浏览器将会自动缓存get请求的数据，并且下一次请求时如果请求的数据url没有发生变化那么就不会去请求服务器直接用缓存的数据显示到浏览器上。

  这就导致了新数据、图片、CSS等不生效。

  一开始通过前端代码的方式来解决这些问题，请求url后面加上一个时间戳类似的数据串，这样每次请求的数据不一样也就不会有缓存的问题，用此来解决总是不刷新缓存的问题。
  但是现在项目的代码是纯静态代码，HTML的，不是模板生成的，也不是PHP这一类的，所以有些文件不能用动态的字符串来修改请求的URL了，最明显的例子就是HTML中的img标签，或者css里的一些background的url等，这些内容纯静态，只能写死，这种文件一旦更新就会有缓存的问题，缓存更新不及时好像程序有问题。

  ## HTTP SERVER配置浏览器缓存策略

  因为浏览器的缓存策略是有Cache-Control控制的，所以需要在HTTP SERVER的Response Header里设置Cache-Control，HTTP SERVER控制了缓存策略，这样就能处理浏览器的缓存了。
  Cache-Control不在这里详细展开，简单说下，缓存的设置有这几种策略：no-store(不缓存) no-cache(每次请求时检查缓存是否过期，如果过期，获取新内容) 以及其他的一些设置。

  Nginx的配置: add_header Cache-Control 'no-cache';
  最好配置在每个location里面，不同的location有不同的缓存策略。
  MDN上简单介绍了[Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)，与Cache-Control相关的还有Expires Pragma 返回头的指令。不在此展开。

  Nginx HTTP模块相关配置：

  ## HTTP SERVER配置本身的缓存策略

  HTTP SERVER除了可以控制浏览器的缓存策略之外，还可以配置自己作为缓存代理服务器的缓存策略。

  这种模式下的Nginx是一个简单的反向代理服务器并不是一个纯粹的HTTP SERVER。浏览器请求模式大概如下：

  Browser Client ----->  Nginx Proxy -----> Apache (or other) Http server

  这里的Nginx就是一个反向代理，既然是代理就可以设置缓存，这里的缓存就是proxy_cache。
  
  设置如下：

```javascript
  # 以下的在http 模块里

  proxy_buffering on;
  proxy_cache_path D:/comproject/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=10s use_temp_path=off;
  proxy_cache_methods GET HEAD POST;

  # 这些在具体的location模块里：
  proxy_cache my_cache;
  proxy_cache_revalidate on;
  proxy_cache_min_uses 1;
  proxy_cache_key $host$uri$is_args$args;
  proxy_cache_use_stale error timeout updating invalid_header http_500 http_502 http_503 http_504;
  proxy_cache_valid 200 10s;
  expires 10s;
  add_header Cache-Control 'no-cache';
  proxy_pass http://fdp;

```

  上面设置缓存的路径，缓存的方法以及其他的配置都在官网，具体的可以看[nginx官网](http://nginx.org/en/docs/http/ngx_http_proxy_module.html)

  这里有个关键点就是必须有后面的HTTP SERVER，在Nginx用upstream与proxy_pass来配置，这样就是一个代理服务器了，可以缓存数据了。