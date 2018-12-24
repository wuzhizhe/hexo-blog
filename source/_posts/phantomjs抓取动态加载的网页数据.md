---
title: phantomjs抓取动态加载的网页数据
date: 2017-06-01 10:26:47
tags:
- phantom
- 爬虫
- nodejs
- superagent
---

## 为什么要抓取数据

  其实需求来源非常简单，我要看一本小说，发现这个小说只在某个网站上有，可以免费观看，但是网站体验实在太差，有非常多的广告，一直出现，无法屏蔽，并且内容低劣。这就想起来为什么不能自己抓取这里面的内容呢？自己做个像爬虫的工具，把小说内容全部爬取出来就可以了。

## 具体实践

  说做就做，首先查找资料发现python是这方面的最厉害的工具，但是没接触过python，上手比较麻烦，就查找别的工具Node、Java都可以，于是选择了Node。

  用Node的superagent进行http网络请求，获取小说页面的数据（这里也可以用自带的HTTP模块进行也可以，因为比较简单，就是获取页面内容，没有复杂的东西）。

  superagent介绍:
  > SuperAgent is a small progressive client-side HTTP request library, and Node.js module with the same API, sporting many high-level HTTP client features. 

  [Github地址](https://github.com/visionmedia/superagent)

  有了工具能够获取回网页的内容后，就可以对获取到的HTML元素进行DOM解析，获取章节数据，根据章节数据获取每章每节的小说具体内容，然后整理生成小说。

  Node是服务端的Js脚本，如何处理DOM呢？在前端处理DOM的工具特别多，可以直接用JavaScript，也可以用比较常见的工具比如JQuery。

  这里推荐一个Node处理HTML DOM元素的工具，名字是cheerio, [Github地址](https://github.com/cheeriojs/cheerio)
  简介：
  >Fast, flexible & lean implementation of core jQuery designed specifically for the server.

  使用cheerio可以像使用jquery那样在Node端处理DOM，具体的api以及常用方法详见Git。

  至于具体的Node web项目或者什么项目开发就不具体介绍了，如果想做成web型的，可以用express/koa等做中间件，然后找具体的template进行开发，不在此做进一步记录。

## 新的问题

  做到这里就能满足一般的抓取需求了，只需要根据不同的网站做不同的规则解析就可以了。

  但是前几天上了一个网站，发现了一个新的问题，小说网站也有动态加载内容的。。。。，这个真的特别少见，因为基本上都是纯文字，完全没有什么流量压力之类的，但是确实有的网站就是这么做了，网页初次加载完之后会有一个延时的ajax请求，获取完整的小说数据，然后更新当前页面。

  这个就真的比较尴尬，因为一般http请求就是把这次http请求的数据抓取回来就完了，不会在处理后续的数据，这样无法处理上面这种延时加载的情况，于是需要找新的工具支持。

  此时就有了下面的新工具。

## 支持延时获取数据的爬虫工具phantomjs

  phantom是一个神奇的工具，基于webkit的服务器Javscript api工具，这个就很神奇了，它可以支持上面想要的延时功能，当然phantomjs不止是这一点功能，不过是这里的需求只要用它的这个功能而已。

  [官网](http://phantomjs.org/)
  [Git地址](https://github.com/ariya/phantomjs)
  官网简介:
  >PhantomJS is a headless WebKit scriptable with a JavaScript API. It has fast and native support for various web standards: DOM handling, CSS selector, JSON, Canvas, and SVG.

  在这里可以使用延时，延时之后再进行数据获取，这样就能获取到相关的完整内容了。

  可以直接使用phantomjs执行js代码进行数据抓取，因为是node项目，也可以使用node的phantomjs-node模块进行开发。
  [Github phantomjs-node](https://github.com/amir20/phantomjs-node)

  phantomjs-node使用简单示例

  ```javascript
    
  function getContent(url, array, index, cb, length, repeat) {
    const instance = await phantom.create();
    const page = await instance.createPage();
    try {
        await page.on("onResourceRequested", function (requestData) {
        });

        await page.on('onResourceReceived', function(response) {
        });
        //打开页面
        const status = await page.open(url);

        //延时
        await sleep(2000);
        //获取页面内容
        const content = await page.property('content');
        let $ = cheerio.load(content);
        let title = $('.headline').html();
        let novel = $('#content').html();
        handleContent(title, novel);
        cb();
        await instance.exit();
    } catch (err) {
        handleErr(err);
        return;
    }
  }

  ```

  最后这里有个自用的简单小说爬虫程序，代码非常简陋，有点乱，主要用来抓取小说内容，[Git地址](https://github.com/wuzhizhe/my-little-spider)


  ## 新发现的问题

  **2017-06-01 新发现phantomjs没有被杀死的问题**

  用的是node的phantom模块，电脑安装了phantomjs后，node模块可以与之通信。

  今天发现存在一个问题，有些情况下，phantomjs无法退出，进入查看进程没有被杀死一直在那挂着。因为是异步调用用了await等待instance.exit()成功后才能继续，现在显然是失败了，这种情况下需要去手动杀死进程。。。。。，然后返回结果后下面的程序继续执行。

  暂时没有找到好的解决办法，先记录下来，等有了办法了再更新上去。



