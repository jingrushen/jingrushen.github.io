---
title: js的隐式装箱(小黑屋)
date: 2018-08-14
updated: 2018-08-14
tags:
    - web前端
    - javascript
categories:
    javascript
---

**原始值：number, string, boolean, undefined, null, symbol**
**对象值：object**

### ToPrimitive(input, PreferredType)：转换规则：(当不是原始值)
1. PreferredType是String，则先调用toString()，结果不是原始值的话再调用valueOf()，还不是原始值的话则抛出错误；
2. PreferredType是Number，则先调用valueOf()再调用toString()。
3. 当没有给ToPrimitive方法传类型时，通常的表现就像是传递了Number类型，先调用valueOf(),再调用toString()


### ToNumber：Number()
### ToString：String()
<!--more-->
* **ToNumber** 

|input|result|
|:-|:-:|
|null|+0|
|undefined|NaN|
|number| 不转换|
|boolean| true->1 false->+0
|string| Number(string)|
symbol| typeError
object| ToNumber(ToPrimitive(input, Number))

* **ToString**

|input|result|
|:-|:-:|
|null|'null'|
|undefined|'undefined'|
|number|String(number)|
|boolean| 'true' \| 'false"
|string| 不转换
symbol| typeError
object| ToNumber(ToPrimitive(input, Number))


### 装箱规则：
* 单目运算符：
1. +a 
return ToNumber(a) 强制转换成number
2. !a
return ToBoolean(a) 强制转换成boolean值

* 双目运算符：
1. a + b
ToPrimitive(a)
ToPrimitive(b)
若有string类型，就把两个原值都进行转化字符串ToString()操作，否则把两个原值都进行转化数字ToNumber()操作
2. a == b
① x，y如果类型相同
这个部分相信有问题的同学百度一下就好。数字的比大小，字符串比大小。里面需要小心的就是NaN != NaN 以及 对象如何比较大小？（[1] != [1]）
重点：x，y类型不同
② x，y一方为布尔值
如果x，y其中一个是布尔值，那么对这个布尔值进行toNumber操作。发现问题了么童鞋们，来看下面代码:
42 == true   // false
不了解规范的会认为，42是真值啊！42会转换为true！你别说如if(42){}这个42确实是真值。但是我们现在在讨论“==”下的转换，那么请记住规范规定了：类型不同时若一方是布尔值，是对布尔值进行类型转化即true => 1，之后我们就可以理解为什么42不等于true了因为1!= 42
③ x，y为数字和字符串
将字符串的一方进行toNumber()操作，这个不难理解哈
④ x，y一方为对象
将对象进行ToPrimitive()操作。如何操作见上文。


#### 栗子
1. [] + [] = ""
step1:( [].valueOf() = [] => [].toString() => "" 是字符串类型，也就是原始值，返回 "" ) 
 所以 ToPrimitive([]) => "" 
step2: ToString(ToPrimitive([])).concat(ToString(ToPrimitive([]))) = "" + "" = ""

2. [] + {} = "[object Object]"
step1:( [].valueOf() = [] => [].toString() => "" 是字符串类型，也就是原始值，返回 "" ) 
 所以 ToPrimitive([]) = "" 
step2:( {}.valueOf() = {} => [].toString() => "[object Object]" 是字符串类型，也就是原始值，返回 "[object Object]" )  所以 ToPrimitive({}) = "[object Object]"
step3: ToString(ToPrimitive([])).concat(ToString(ToPrimitive({}))) = "" +"[object Object]" = "[object Object]"

3. {} + [] = 0
step1: 前面的 {} 会被当做空代码块忽略，所以只剩下 + [] 一个单目运算符，所以直接执行ToNumber([]) = 0

4. {} + {} = "[object Object][object Object]"
step1:( {}.valueOf() = {} => [].toString() => "[object Object]" 是字符串类型，也就是原始值，返回 "[object Object]" )  所以 ToPrimitive({}) = "[object Object]"
step2: ToString(ToPrimitive({})).concat(ToString(ToPrimitive({}))) = "[object Object]" +"[object Object]" = "[object Object][object Object]"
