---
title: overflow解决父元素因为子元素漂浮没高度的问题
date: 2017-06-13 14:52:42
tags:
- css
- overflow
- float
- height
---

## 子元素漂浮导致父元素没有高度

  HTML DOM元素，当子元素设置了float为left或者right时，元素就漂浮起来了，这个时候它就不占用任何的宽高空间了，所以就导致了该元素的父元素的高度不正常，如果全部漂浮，父元素的高度为零。

  这个时候在其他的一些元素的布局时就会出现问题，比如父元素下面的元素，很可能与父元素重叠，并且在一些需要计算高度的情况下会导致布局错误，界面混乱。


## 如何解决这问题？

  解决这个问题的方法也很简单，在父元素上使用overflow: auto; 这样就可以解决了这个问题，overflow:auto可以清楚子元素的浮动。


## 代码示例

  ```html

    <div class="container1">
      <div class="child1">飘过来</div>
      <div class="child2">浮过去</div>
    </div>
    <div class="clear"></div>
    <div class="container2">
      <div class="child1">飘过来</div>
      <div class="child2">浮过去</div>
    </div>

  ```
  
  ```css

    .child1,
    .child2 {
      float: left;
    }
    .container1 {
      border: 2px solid red;
      width: 50%;
    }
    .clear {
      clear: both;
    }
    .container2 {
      margin-top:20px;
      overflow: auto;
      border: 2px solid red;
      width: 50%;
    }

  ```

  [代码运行JSFIDDLE](https://jsfiddle.net/dazhizhemu/25jwjca5/)

  代码运行效果图：
  ![](/images/overflow-auto.png)