---
title: JavaScript中Array的some等方法对比学习记录
date: 2017-04-20 18:08:31
tags:
---
### JavaScript的方法

1\. [Array.prototype.some方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
  some方法是用来对数组中的元素进行判断，只要有一个元素满足判断条件就返回true。
  代码示例，来自MDN
  
```javascript
function isBigEnough(element, index, array) { 
  return element >= 10; 
} 

[12, 5, 8, 130, 44].every(isBigEnough);   // false 
[12, 54, 18, 130, 44].every(isBigEnough); // true
```

2\. [Array.prototype.every方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

  every方法与上面的some正好相反，对于数组元素进行判断，要求所有的元素都能满足条件才返回true。
  代码示例，来自MDN：
  
```javascript
function isBigEnough(element, index, array) { 
  return element >= 10; 
} 

[12, 5, 8, 130, 44].every(isBigEnough);   // false 
[12, 54, 18, 130, 44].every(isBigEnough); // true
```
想起了其他几个类似的方法，都在这里记录下：

3\. [Array.prototype.find方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)

  find方法找到**第一个**满足要求的数组元素，然后返回该元素。
  代码示例，来自MDN；
  
```javascript
function isBigEnough(element) {
  return element >= 15;
}

[12, 5, 8, 130, 44].find(isBigEnough); // 130
```
4\. [Array.prototype.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)

  filter方法将会返回所有满足条件的数组元素，元素在一个数组中存储。
  代码示例，来自MDN：
  
```javascript
var words = ["spray", "limit", "elite", "exuberant", "destruction", "present"];

var longWords = words.filter(function(word){
  return word.length > 6;
})

// Filtered array longWords is ["exuberant", "destruction", "present"]
```

5\. [Array.prototype.map方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

  map方法是对数组中的所有元素按照某种方式做修改，然后返回一个修改后的新数组。
  代码示例，来自MDN：
  
```javascript
var numbers = [1, 5, 10, 15];
var roots = numbers.map(function(x) {
   return x * 2;
});
// roots is now [2, 10, 20, 30]
// numbers is still [1, 5, 10, 15]
```

这里就是做一个集中的记录，没有涉及每个方法的详细用法，只做最基础的介绍与整理，想要详细看的可以进入上面每个方法的MDN链接中进行学习，也可以搜索其他的资料深入学习。

