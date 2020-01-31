---
title: "CORS跨域"
date: 2020-01-26T12:23:26+08:00
draft: false
---

### 什么是跨域？

简单的来说，出于安全方面的考虑，页面中的JavaScript无法访问其他服务器上的数据，即“同源策略”。而跨域就是通过某些手段来绕过同源策略限制，实现不同服务器之间通信的效果。

![跨域](/images/CORS1.jpg)

### CORS

CORS全称“跨域资源共享”(Cross-Origin-Resource-Share)

CORS需要浏览器和服务器同时支持，才可以实现跨域请求，目前几乎所有浏览器都支持CORS，IE则不能低于IE10。CORS的整个过程都由浏览器自动完成，前端无需做任何设置，跟平时发送ajax请求并无差异。所以，实现CORS的关键在于服务器，只要服务器实现CORS接口，就可以实现跨域通信。

请求类型：

#### CORS分为简单请求和非简单请求两种

符合以下条件，为简单请求：

```
请求方式使用下列方法之一：
GET
HEAD
POST
 
Content-Type 的值仅限于下列三者之一：
text/plain
multipart/form-data
application/x-www-form-urlencoded
```

对于简单请求，浏览器会直接发送CORS请求，具体说来就是在header中加入origin请求头字段。同样，在响应头中，返回服务器设置的相关CORS头部字段，Access-Control-Allow-Origin字段为允许跨域请求的源。请求时浏览器在请求头的Origin中说明请求的源，服务器收到后发现允许该源跨域请求，则会成功返回，具体如下：

![简单请求](/images/CORS2.jpg)


在这里，http://localhost:3001为我们当前发送请求的源，如果服务器发现请求在指定的源范围内，则会返回响应的请求结果， 否则会在控制台报错，如下图所示，在这里需要注意的是，尽管请求失败，但返回的状态码依然可能为200。所以在做处理时需要格外注意。

![简单请求](/images/CORS3.png)

* ### 非简单请求(预检请求)

```
使用了下面任一 HTTP 方法：
PUT
DELETE
CONNECT
OPTIONS
TRACE
PATCH
 
Content-Type 的值不属于下列之一:
application/x-www-form-urlencoded
multipart/form-data
text/plain
```

当发生符合非简单请求（预检请求）的条件时，浏览器会自动先发送一个options请求，如果发现服务器支持该请求，则会将真正的请求发送到后端，反之，如果浏览器发现服务端并不支持该请求，则会在控制台抛出错误，如下：

![简单请求](/images/CORS4.png)

如果非简单请求（预检请求）发送成功，则会在头部多返回以下字段

``` 
Access-Control-Allow-Origin: http://localhost:3001  //该字段表明可供那个源跨域
Access-Control-Allow-Methods: GET, POST, PUT        // 该字段表明服务端支持的请求方法
Access-Control-Allow-Headers: X-Custom-Header       // 实际请求将携带的自定义请求首部字段
```