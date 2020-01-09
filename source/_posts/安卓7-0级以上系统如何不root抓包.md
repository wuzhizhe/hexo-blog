---
title: 安卓7.0级以上系统如何不root抓包
date: 2020-01-09 11:18:33
tags:
categories:
---
  安卓升级了7.0以后app可以不信任用户自己添加的证书了，这让抓包变得困难，找了那么多资料也没个好的解决方法，今天又搜索了一下，看到一篇博客里面的方法，可以实现7.0以上app抓包，博客原文[android-7-0以上使用virtualxposedjusttrustme抓包](http://www.iliili.cn/2019/02/06/android-7-0%E4%BB%A5%E4%B8%8A%E4%BD%BF%E7%94%A8virtualxposedjusttrustme%E6%8A%93%E5%8C%85/)

  原理很简单，利用virtualXposed工具，里面按照一个叫[JustTrustMe](https://github.com/Fuzion24/JustTrustMe)的模块，就能让软件信任用户证书了，问题解决。