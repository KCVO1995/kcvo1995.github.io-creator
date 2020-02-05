---
title: "浅析 URL"
date: 2019-12-15T15:23:26+08:00
draft: false
---

## URL组成
URL，统一资源定位符，用于定位互联网上资源，俗称网址。

### URL的组成:

#### URL=协议+域名或IP+端口号+路径+查询字符串+锚点

### URL的写法:

#### scheme://host.domain:port/path/filename

### 举个[栗子](https://www.zhihu.com/explore)
#### https://www.zhihu.com/explore

在上面的网站中
* scheme - 定义因特网服务的类型。常见的协议有 http、https、ftp、file，其中最常见的类型是http，而 https 则是进行加密的网络传输。例子中使用的是https
* host - 定义域主机（http 的默认主机是 www）
* domain - 定义因特网域名，例子中就是zhihu.com
* port - 定义主机上的端口号（http的默认端口号是 80,https 默认是 443，FTP 默 21 端口）,
* 例子中https的默认端口是443(已被省略)
* path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）,例子中的路径就是/explore
* filename - 定义文档/资源的名称

## IP

IP的全称为Internet Protocol(互联网协议),是设备的数字便签,用于互联网定位.常见的IP可分为IPv4。常见的IP地址可分为IPv4和IPv6两类,其中使用最广泛的是IPv4。

IP可以分为内网IP和外网IP
* 外网IP由通讯公司提供,如国内的电信、移动等。外网用于外网之间的通信和定位
* 内网IP由路由器分配,用于区分内网连接设备

## 域名
域名实际上是IP地址的代称,目的是为了方便记忆。例如39.156.69.79这个百度的IP地址,在浏览器输入就可以访问百度,但我相信没人会这样做,因为这样实在太难记了。但baidu.com就十分方便好记,www.baidu.com通过DNS就会返回39.156.69.79这个IP,浏览器就可以访问了。

域名可以分为:
* 顶级域名,例如".com"、".net"、".org"、".edu"、".info"
* 二级域名,例如"xiedaima.com"
* 父域名
* 子域名

一图流总结:

![domain](/images/domain.png)

推荐一个可以查询自己域名的网站:IP138.com



## DNS

DNS，全称 Domain Name System(service)，是一个域名系统。它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。

在bash,输入ping命令即可查询到域名对应的IP地址。

例如:

![IP](/images/ip.png)

