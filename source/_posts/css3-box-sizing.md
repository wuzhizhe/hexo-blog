---
title: css3-box-sizing
date: 2017-05-15 11:31:07
tags:
- css3
- box-sizing
- 盒子模型
---

## box-sizing

  之前一直没有发现这个东西，今天研究时发现的。原来写css样式时，元素的padding border margin的值在元素中计算元素最终的宽高值是需要再次相加的，因此如果想要元素总高度不变的话，需要设置border padding的时候需要重新设置里面元素的高度，这个是个很苦恼的事情，用css检测工具检测的时候经常会提醒这样写代码会导致元素宽高不受控制表现。

  但是现在有了box-sizing可以完美解决这个问题。

  目前的box-sizing有以下两个主要属性：

### box-sizing: content-box

  这个属性与之前不设置时是一样的，就是宽度高度只包括内容，不包括margin padding border等值。

### box-sizing: border-box

  border-box值就是一个比较神奇的东西了，在盒子模型里，你如果使用了box-sizing:boder-box;那么所有的这些属性加起来的值才是最终设置的值。
  >width = border + padding + width of the content;
  height = border + padding + height of the content;

## 实战展示

具体的效果以及代码见下面：

灰色部分是使用了border-box属性的div,而蓝紫色部分是使用了content-box属性的div，上面的两个50% div能完美的包含在父元素中，而下面的div因为border padding等属性成为两行。

以下展示具体代码运行示例见: [Jsfiddle box-sizing](https://jsfiddle.net/dazhizhemu/4qhnyq95/)

![使用box-sizing](/images/box-sizing/box-sizing.png)

具体代码：

```html

<!DOCTYPE html> 
<html> 
<head> 
<style> 
* {
    font-size: 16px;
}
div.border-box-container {
    height: 5em;
    width: 30em;
    border: 1em solid red;
}
div.content-box-container {
    margin-top: 1.2em;
    height: 5em;
    width: 30em;
    border: 1em solid red;
}
div.border-box-container  div.box { 
    box-sizing:border-box;
    -moz-box-sizing:border-box; 
    -webkit-box-sizing:border-box;
    width:50%; 
    height: 100%;
    border:0.8em solid gray; 
    text-align: center;
    float:left;
    display: flex;
    align-items: center;
    justify-content: center; 
}
div.content-box-container div.box {
    box-sizing:content-box;
    -moz-box-sizing:content-box; 
    -webkit-box-sizing:content-box;
    width:50%; 
    height: 100%;
    border:0.8em solid #8181d2; 
    text-align: center;
    float:left; 
}

</style> 
</head> 
<body> 
    <div class="border-box-container">
      <div class="box">这个 div 占据左半部分。</div> 
      <div class="box">这个 div 占据右半部分。</div> 
    </div>
    <div class="content-box-container">
      <div class="box">这个 div 占据左半部分。</div> 
      <div class="box">这个 div 占据右半部分。</div> 
    </div>
</body> 
</html>

```
