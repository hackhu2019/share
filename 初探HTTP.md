# HTTP 是什么？
**HTTP** : HyperText Transfer Protocol 。根据字面意思我们可以翻译为超文本传输协议。

它是一种位于「**应用层**」，专门为 web浏览器 和 web服务器 之间通信的而设计的协议。因为网页上通常传输的是 HTML 这样的超媒体文档，所以被称超文本传输协议。

**HTTP** 是基于「 TCP协议」之上的，所以它也具有的 「 TCP协议」的特点：**可靠，传输效率相对较低**。
![HTTP基础概念](https://img-blog.csdnimg.cn/20190714090938928.png)
# HTTP 首部
HTTP 首部可以分为 4 个部分：一般头、请求头、响应头、实体头。

我们可以在任意浏览器中「右键 —— 检查」查看服务器与浏览器之间的网络情况。（推荐 Firefox）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190714095455153.png)
以百度为例，一般头里存放的是请求信息，主要包括：请求网址、请求方法、返回状态码、连接采用的协议等。
```
请求网址:https://www.baidu.com/
请求方法:GET
远程地址:180.101.49.11:443
状态码:
200
版本:HTTP/1.1
Referrer 政策:no-referrer-when-downgrade
```

而作为 web 开发，我们需要重点关注的是 **request**（请求头）、**response**（响应头）两部份。

**request**（请求头），会把客户端的信息放在里面发送给服务器。例如：可接收数据、接受编码格式、语言、连接是否保存、请求的主机域名（可以让多台服务器的任意一台响应）、浏览器信息、**Cookie** 、**来源网址**（防盗链）等。

```
Host: www.baidu.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/javascript, application/javascript, application/ecmascript, application/x-ecmascript, */*; q=0.01
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Referer: https://www.baidu.com/
X-Requested-With: XMLHttpRequest
Connection: keep-alive
Cookie: BAIDUID=03E141A2389016553988E825286E0A2F:FG=1;
1
User-Agent	
Mozilla/5.0 (Windows NT 10.0; …) Gecko/20100101 Firefox/67.0
```
**response**（响应头）：服务器接受到请求根据需要发送数据给客户端。会包含：请求的结果（状态码）、返回数据的长度、编码格式、时间等
```
HTTP/1.1 200 OK
Content-Length: 74
Content-Type: text/plain; charset=UTF-8
Date: Sun, 14 Jul 2019 01:45:57 GMT
```
# 常见请求方法
常与后端打交道的两种请求方法有：get、post。

GET 方法，将请求的参数位于 URL 后，例如：https://www.sogou.com/web?query=123&_asf=www.sogou.com，请求的网址为 https://www.sogou.com/web，以「?」来分割请求网址和请求参数信息，query 为参数名称，123 为参数值，以等号来对应参数名称和值，多个参数之间以「&」区分。

POST 方法，将见于：表单提交，带有敏感信息的请求，所发送的数据隐藏在 HTTP 包中，不显式可见。

二者区别：

- **传输数据方面**：「GET 方法」通过 URL 传递数据，而 URL 的长度是有限制的，无法传输图片、视频等大数据。而「POST 方法」数据存在于 HTTP 包中，理论上不受限制。
- **安全性方面**：「GET 方法」数据显示在 URL 中，容易被窃取，而「POST 方法」不显式可见，更不易被窃取，相对安全。

# HTTP 无状态，有会话
**无状态**：同一连接上的任意两个请求没有链接。服务器无法直接通过连接知道上一请求的状态。

HTTP 的解决，通过 **cookie**，来记录上一次请求的状态，服务器通过 cookie 告知上一请求的状态，与客户端建立会话。

**cookie**：
 1. 由服务器建立，存储于客户端。
 2. 客户端每次请求 **自动** 将已有 Cookie 发送给客户端。例：`Cookie: BAIDUID=03E141A2389016553988E825286E0A2F:FG=1`。
 3. 默认存储在内存中，随着浏览器的关闭而销毁。可以手动设置 Cookie 的存活时间（写入本地硬盘 ），例：`Cache-Control: max-age= 300`，单位为秒。
 4. 大小限制在 4KB。

由 cookie 写入本地硬盘引发的问题：
1. **不安全性**。黑客可以通过获取本地的 cookie 获得信息，本地文件可以人为修改。
2. **存储数据量不足**。随着 web服务的升级，会话所需要传递的数据远不止 4KB。

所以，cookie 仅用来存储一些不太重要的信息。

解决方案 session：

session 将数据存储在 **服务器**，客户端仅用 cookie 存储 session 的 id。

# HTTP 状态码
HTTP 状态码可分为 5 种：
1.  1XX。以 1 开头，表示 **该响应尚未完成**。常见的有：101，表示 HTTP 协议的切换。
2. 2XX。以 2 开头，表示 **成功响应**。常见代码：200 OK，表示请求成功。
3. 3XX。以 3 开头，表示**重定向**。

> 常见代码：304：如果客户端发送了一个带条件的 GET
> 请求且该请求已被允许，而文档的内容（自上次访问以来或者根据请求的条件）并没有改变，则服务器应当返回这个状态码

4. 4XX。以 4 开头，通常表示客户端错误。常见代码 404 ，表示客户端所请求的资源并未在服务器上找到。
![404](https://img-blog.csdnimg.cn/20190714111200713.png)
5.  5XX。以 5 开头，通常表示服务端响应错误。常见代码 500，表示服务器运行出错。
![人为制造错误](https://img-blog.csdnimg.cn/2019071411170593.png)
总结，1XX、2XX、3XX 都属于正常的响应，而 4XX、5XX 都是非正常响应，需要程序员去处理，不能直接将错误暴露给用户。

本篇文章，参考了 [MDN官方文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)，感兴趣的朋友可以自己去官网查看文档，也可以评论发表你的看法。
