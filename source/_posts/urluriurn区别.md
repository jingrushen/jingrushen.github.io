---
title: URL URI URN区别
date: 2018-10-02
updated: 2018-10-02
tags:
    - 计算机
categories:
    计算机基础
---

- URL ： uniform resource locator  统一资源定位符，是互联网上用来标识某一处资源的地址，组成部分：协议 ，ip地址（域名部分）， 端口号（有或无） ， 虚拟目录部分， 文件名， 锚部分， 参数部分
- URI ： uniform resource identifier  统一资源标识符， 每种资源如HTML文档，图像，视频等都是一个URI来定位的，组成部分：命名机制， 主机名， 自身的名称
- URN： uniform resource name 统一资源命名， 通过名字来标识资源

<!--more-->
总结：
* URL和URN都是一种URI，具体的资源标识方式
* URI是一种抽象的资源标识，包括URL和URN以及其他统一资源
* URL是URI的一个子集，是特殊类型的URI， 包含了用于查找某个资源的足够信息


url uri 区别：
uri 是统一资源标识符， 是一种相对或者绝对定位资源，包含的是服务器的路径和资源
url 是统一资源定位符， 是一种绝对定位于资源，能精确的匹配到资源的访问