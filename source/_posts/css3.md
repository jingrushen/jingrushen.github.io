---
title: css3新特性---animation
date: 2018-09-26
updated: 2018-09-26
tags:
  - css
categories:
  css
---

@keyframes name 动画的名称应用在animation-name
定义规则
```
from {}
to {}
or
0% {}
25% {}
50% {}
75% {}
100% {}
```
animation : `animation-name` `animation-duration` `animation-timing-function` `animation-delay` `animation-iteration-count` `animation-direction`
<!--more-->
1.`animation-name `
2.`animation-duration` 指定动画的持续时间
3.`animation-timing-function` 指定动画的缓冲函数 linear ease-in ...
4.`animation-delay` 指定动画的延迟时间，当为负值时，会忽略需要的时间的动画
5.`animation-iteration-count` 指定动画的执行次数 infinite 无限次
6.`animation-direction` 指定动画的执行方向 normal (正常向前) | reverse (从100% to 0%) | alternate-reverse (先从100% to 0% 再从0% to 100%) | alternate (从0% to 100%) 该属性配合animation-iteration-count
7.`animation-fill-mode` 指定动画的填充模式 动画执行结束之后保留的样式 none | forwards (保留最后一个关键帧设置的样式) | backwards (保留第一个关键帧设置的样式) | both