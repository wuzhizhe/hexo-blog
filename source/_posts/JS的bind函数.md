---
title: JS的bind函数
date: 2017-04-17 11:06:03
tags:
---
  今天看到谷歌developer的官方文档，看到了对bind方法的运用，这个函数可以创建一个新的函数，并且通过传递参数修改原函数的this对象，可以修改this对象也就表示可以传递原来函数本身，bind函数的第一个函数必须是要修改的对象，而后面的参数才是原来的函数使用的参数。

  MDN官网的解释：
> bind()方法会创建一个新函数。当这个新函数被调用时，bind()的第一个参数将作为它运行时的 this, 之后的一序列参数将会在传递的实参前传入作为它的参数。

  [Function.prototype.bind链接](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

这里插入下之前部分代码示例：

```javascript
function spawn(generatorFunc) {
  function continuer(verb, arg) {
    var result;
    try {
      result = generator[verb](arg);
    } catch (err) {
      return Promise.reject(err);
    }
    if (result.done) {
      return result.value;
    } else {
      return Promise.resolve(result.value).then(callback, errback);
    }
  }
  var generator = generatorFunc();
  var callback = continuer.bind(continuer, "next");
  var errback = continuer.bind(continuer, "throw");
  return callback();
}

spawn(function *() {
  "use strict";
  try {
    // 'yield' effectively does an async wait, returning the result of the promise
    let story = yield getJson('story.json');
    addHtmlToPage(story.heading);
    // Map our array of chapter urls
    // to an array of chapter json promises.
    // This makes sure they all download parallel.
    let chapterPromises = story.chapterUrls.map(getJson);
    // Can't use chapterPromises.forEach, because yielding within doesn't work
    for (let i = 0, chapterPromise; chapterPromise = chapterPromises[i]; i++) {
      // Wait for each chapter to be ready, then add it to the page
      let chapter = yield chapterPromise;
      addHtmlToPage(chapter.html);
    }
    addTextToPage("All done");
  }
  catch (err) {
    // try/catch just works, rejected promises are thrown here
    addTextToPage("Argh, broken: " + err.message);
  }
  document.querySelector('.spinner').style.display = 'none';
});
```
  代码出现在promise文章里，[链接](https://developers.google.com/web/fundamentals/getting-started/primers/promises)


  有很多文章分析js方法的call apply bind的区别，这里就做简单的记录，call apply都是直接执行原来的方法，然后传递新对象以及参数，bind是生成一个新的函数。call与apply都是执行，但是还是有区别，主要是参数传递的不同，call用于传递确定个数的参数，而apply用于传递个数不确定的参数，用类似于数组的形式将参数传递进去，然后进行处理。

