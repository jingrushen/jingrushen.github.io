---
title: HTTP协议
date: 2018-09-02
updated: 2018-09-02
tags:
    - web前端
    - http
categories:
    HTTP
---
### HTTP：超文本传输协议，属于应用层协议，用于浏览器与服务器之间的通信，客户端打开tcp连接，发送请求给服务器，服务器接收请求并作出响应。
* 特点：
1. 无状态，有会话协议，服务器与浏览器之间的请求响应不会保留任何数据，是一种无连接无状态的协议
2. 简单的
3. 可扩展的
4. 连接： 连接由传输层来控制，TCP连接是一种可靠传输，不会丢失信息（即使丢失了也会返回错误）UDP连接是不可靠传输

### HTTP流
step1. 打开一个TCP连接，TCP连接后被用来发送一条或多条请求，以及接受回应的消息；
step2. 客户端发送一个HTTP请求封装在数据帧中
step3. 服务端接收并处理HTTP请求，返回响应
step4. 关闭连接或者为后续请求重用连接
* 请求报文
请求行 ： 请求方法 请求url 协议版本 eg.
请求头部 ： headers
空行
请求数据
<!--more-->
* 回应报文 
响应行： 协议版本 状态码 状态文本
响应头部
空行
响应数据



短连接(Connection: close)：每一个http请求都会独立的连接完成，每个http请求都会有一个tcp连接
长连接(Connection: keep-alive)：一个tcp连接会保持一段时间，重复用于发送一系列请求，节省每一个http请求都要生成一个tcp连接
流水线连接：请求不需要等待上一个请求接收到应答之后就可发出


### HTTP的组件系统
客户端
服务器
代理 ： 作用：缓存（可以是公开的也可以是私有的，像浏览器的缓存） / 过滤（像反病毒扫描，家长控制...） / 负载均衡（让多个服务器服务不同的请求） /  认证（对不同资源进行权限管理） /  日志记录（允许存储历史信息）
（代理缓存拓展：
概念： 重用已获取的资源能有效的提升网站与应用的性能，缓存是一种保存资源副本并在下次请求时直接使用该副本，当浏览器发起请求时发现在缓存中存在着相应资源，则会拦截请求，返回该资源的副本而不必去源服务器重新下载。
好处：能缓解服务器的压力，获取资源的时间变短从而提高性能。
类型： 私有与共享的缓存。
部署：代理缓存 / 网关缓存 / 浏览器缓存 / 负载均衡器 / cdn 
缓存控制： `Cache-control： no-store|no-cache (禁止进行缓存) | public | private | max-age(缓存的保鲜期) | must-revalidate(已经过期的缓存不再使用，缓存校验)`

规则：当一个资源被缓存之后，当超过缓存期或者资源进行了更新，那么对副本进行删除或更新，当客户端发起一个请求时，缓存检索到已有一个对应的陈旧资源（缓存副本），则缓存会先将此请求附加一个 `If-None-Match ` 头，然后发给目标服务器，以此来检查该资源副本是否是依然还是算新鲜的，若服务器返回了 `304`](Not Modified)（该响应不会有带有实体信息），则表示此资源副本是新鲜的，这样一来，可以节省一些带宽。若服务器通过` If-None-Match `或` If-Modified-Since`判断后发现已过期，那么会带有该资源的实体内容返回。

）

### Cookie
概念：服务器将发送用户浏览器保存在本地的数据中，在下一次浏览器再向同一个服务器发起请求被携带并发送到服务器上，用于告知服务器两个请求是否来自同一个浏览器。
Cookie主要用于以下三个方面：
* 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
* 个性化设置（如用户自定义设置、主题等）
*  浏览器行为跟踪（如跟踪分析用户行为等）


### 跨域方法
* 浏览器的同源策略：当一个资源从客户端发起请求的资源所在的服务器跟客户端的协议，域名，和端口不一致时，会拒绝发出请求；限制行为：1. 无法读取非同源网页的Cookie、LocalStorage、IndexedDB；2. 无法接触非同源网页的 DOM；3. 无法向非同源地址发送 AJAX 请求（可以发送，但浏览器会拒绝接受响应）。

##### 一、CORS
* 允许使用跨域的情况：
1. XMLHttpRequest() 或 fetch 发起的跨域请求
2. @font-face 跨域请求字体资源
3. webGL贴图 / canva drawImage

简单请求：
1. Method： GET HEAD POST
2. 不得人为设置该集合之外的其他首部字段：` Accept / Accept-Language / Content-Language / Content-Type / DPR / Downlink / Save-Data / Viewport-Width / Width`
3. `Content-type : text/plain | mulitipart/form-data | application/x-www-form-urlencoded`
非简单请求（需要先预检请求）
先发送预检请求(Method：OPTIONS)给服务器，确认后再发送http请求给服务器


请求头：
`Origin`：发送请求的域名
`Access-Control-Request-Method`：请求方法
`Access-Control-Request-Headers`：请求头部字段
响应头：
`Access-Control-Request-Method`：存在请求头中的方法
`Access-Control-Request-Headers`：存在请求头中的头部字段
`Access-Control-Allow-Origin`：包含请求域 | *
`Access-Control-Max-Age`：表明该响应的有效时间，在有效时间内，浏览器无须为同一请求再次发起预检请求。  
`Access-Control-Allow-Credentials`：
....
满足才能实现跨域

##### 二、JSONP
原理：动态插入script元素，向服务器发出请求，请求查询字符串中有一个毁掉参数，用来指定回调函数的名字，服务器收到请求之后就会调用指定的回调函数，并把返回的数据放在回调函数的参数中
缺点：只能GET方法


<hr>

### 各种协议
* IP协议
* ARP协议 ： ARP查询包中的源IP地址是源主机的IP地址，目标IP地址是目标主机的IP地址，源MAC地址是源主机的MAC地址，ARP査询包中的目的MAC地址是广播MAC地址FF-FF-FF-FF-FF-FF。
* TCP协议：可靠传输协议，接收方会对接收到的数据分段发送确认给发送方，而发送方会将没有确认的数据分段重新传送给接收方，接收方会对数据分段按照正确的顺序重组，并且删除重复的数据分段；TCP的三次握手：






readyState状态： 请求当前所处的状态
0：unsend 代理已经创建，但是未调用open()
1：opened open()方法被调用
2：headers_received send()方法已经被调用，头部状态是可获得
3：loading 下载中，responseText已经包含部分数据
4：done 下载操作已经完成 服务器响应成功或者失败

status 请求结束的状态码
101, 转换协议
200, OK，访问正常
204, No Content，服务器成功处理了请求但是不需要返回实体内容
205, Reset Content，服务器成功处理了请求但是会重置文档
206, 
301, Moved Permanently，永久移动
302, Move temporarily，暂时移动
304, Not Modified，未修改
307, Temporary Redirect，暂时重定向
401, Unauthorized，未授权，当前的请求需要用户身份验证
403, Forbidden，禁止访问
404, Not Found，在服务器中找不到请求的资源
500, Internal Server Error，服务器发生错误
502, 
503, Service Unavailable，服务器没有准备好处理请求，服务器处于维护或者停机的状态

```
        let xhr = new XMLHttpRequest();
        xhr.open('GET', './data.txt');
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4 && xhr.status === 200) {
                console.log(xhr.responseText);
            } else {
                console.log(xhr.statusText);
            }
        }
        xhr.send();
```