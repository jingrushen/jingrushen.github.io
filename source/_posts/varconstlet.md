---
title: 关于变量提升var const let的知识讲解
date: 2018-10-10
updated: 2018-10-10
tags:
  - javascript
  - es6
categories:
  es6
---

预编译四部曲：
① 创建AO对象 activeobject 也就是函数的执行器上下文
② 创建变量和形参的声明，赋值undefined
③ 给形参赋值
④ 函数声明（不包括函数表达式）

<!--more-->
```
// var hoisting
num;     // => undefined  
var num;  
num = 10;  
num;     // => 10  
// function hoisting
getPi;   // => function getPi() {...}  
getPi(); // => 3.14  
function getPi() {  
  return 3.14;
}
```

#### var variable
var 生命周期：声明阶段  ----  初始化阶段(undefined)  ----  （遇到赋值语句）分配阶段
var 语句出现的位置不影响声明和初始化阶段

#### function
function 生命周期：声明初始化赋值同一阶段
function在任何位置调用都不依赖于它的声明位置

#### let
let生命周期：声明阶段(放置暂时死亡区，未初始化) --- （遇到赋值语句）初始化和分配阶段
注意：
1.当在块级作用域中声明了let，那么它所声明的变量就绑定在这个作用域中，不再用受到外部的影响，此时变量处于暂时死亡区，在它初始化赋值之前不能被访问;
2.在作用域内部用let声明了变量， 之后不得重新声明，只能重新赋值
const与let具有一样的生命周期，但是它被赋值只能发生一次，之后不能改变(对于引用类型来说地址不能改变)

```
let condition = true;  
if (condition) {  
  // console.log(number); // => Throws ReferenceError
  let number;
  console.log(number); // => undefined
  number = 5;
  console.log(number); // => 5
}
///////分析：当执行if语句时，number会被声明，放置与暂时死亡区，，在初始化之前访问会报错number is not defined !! 当执行到let number语句时，初始化undefined， 最后赋值语句number = 5 
```

## 总结：
进行不可变的绑定用const， 否则用let， 少暴露未初始化变量