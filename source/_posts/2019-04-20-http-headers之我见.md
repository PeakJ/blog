---
title: http headers之我见
date: 2019-04-20 03:10:59
tags:
categories:
---
最近看了一些关于HTTP headers的知识，平时与服务端联调接口，查看headers必不可少。
> headers（请求头）允许客户端和服务器通过 **request**和 **response**传递附加信息。一个请求头由名称（不区分大小写）后跟一个冒号“：”，冒号后跟具体的值（不带换行符）组成。该值前面的引导空白会被忽略。

<!-- more -->
每一个AJAX请求，都可以设置header的相关属性，可以说headers是HTTP请求必不可少的部分。HTML中`<script src="http://example.com/test.js"></script>` 
`<link rel="stylesheet" type="text/css" href="https://cdn.test.com/theme.css" />` 的src和href属性对应的资源文件，也是通过HTTP请求加载的，还有
`<img src="http://example.jpg"/>`图片的请求，也是HTTP的。

> **既然图片、JS、CSS都是HTTP请求，那么如何在请求这些资源的时候设置headers呢？**

如果直接给src 和 href 属性赋值，肯定是没办法设置header的。
关于图片的设置，是有办法可行的，但是对于js css 等资源，目前没有什么特别好的方式（当然，日常开发也几乎不会有这样的应用场景）。

### header --img:src
图片设置header
```javascript
var oReq = new XMLHttpRequest();
oReq.open("GET", "https://s2.ax1x.com/2019/07/30/eJHI81.png", true);
oReq.setRequestHeader("Your-Header-Here", "Value");
// use multiple setRequestHeader calls to set multiple values
oReq.responseType = "arraybuffer";
oReq.onload = function (oEvent) {
  
  var arrayBuffer = oReq.response; // Note: not oReq.responseText
  if (arrayBuffer) {
    var u8 = new Uint8Array(arrayBuffer);
    var b64encoded = btoa(arrayBufferToString(u8));
    var mimetype="image/png"; // or whatever your image mime type is
    document.getElementById("testImg").src="data:"+mimetype+";base64,"+b64encoded;
  }
};
oReq.send(null);
function arrayBufferToString(buffer) {
  var bufView = new Uint16Array(buffer)
  var length = bufView.length
  var result = ''
  var addition = Math.pow(2, 16) - 1

  for (var i = 0; i < length; i += addition) {
    if (i + addition > length) {
      addition = length - i
    }
    result += String.fromCharCode.apply(null, bufView.subarray(i, i + addition))
  }
  return result
}
```

参考链接：[Img src path with header params to pass](https://stackoverflow.com/questions/23609946/img-src-path-with-header-params-to-pass)

### header -- js:src/ css:href
页面中的js和css暂时还没有好的方法

参考链接： [is it possible to set custom headers on js `<script>` requests?](https://stackoverflow.com/questions/4866722/is-it-possible-to-set-custom-headers-on-js-script-requests)

### headers的相关介绍
附上headers的相关说明：

HTTP 头字段根据实际用途被分为以下 4 种类型：
- 通用头字段(英语：General Header Fields)
- 请求头字段(英语：Request Header Fields)
- 响应头字段(英语：Response Header Fields)
- 实体头字段(英语：Entity Header Fields)

#### request请求头字段一览
| 协议头字段名 | 说明 | 示例 | 状态 |
| --- | --- | --- | --- |
| Accept | 能够接受的回应内容类型（Content-Types）。参见[内容协商](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AE%B9%E5%8D%8F%E5%95%86 "内容协商")。 | `Accept: text/plain` | 常设 |
| Accept-Charset | 能够接受的字符集 | `Accept-Charset: utf-8` | 常设 |
| Accept-Encoding | 能够接受的编码方式列表。参考[HTTP压缩](https://zh.wikipedia.org/wiki/HTTP%E5%8E%8B%E7%BC%A9 "HTTP压缩")。 | `Accept-Encoding: gzip, deflate` | 常设 |
| Accept-Language | 能够接受的回应内容的自然语言列表。参考 [内容协商](https://zh.wikipedia.org/wiki/%E5%86%85%E5%AE%B9%E5%8D%8F%E5%95%86 "内容协商") 。 | `Accept-Language: en-US` | 常设 |
| Accept-Datetime | 能够接受的按照时间来表示的版本 | `Accept-Datetime: Thu, 31 May 2007 20:35:00 GMT` | 临时 |
| Authorization | 用于超文本传输协议的认证的认证信息 | `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==` | 常设 |
| [Cache-Control](https://zh.wikipedia.org/wiki/%E7%BD%91%E9%A1%B5%E5%BF%AB%E7%85%A7 "网页快照") | 用来指定在这次的请求/响应链中的所有缓存机制 都必须 遵守的指令 | `Cache-Control: no-cache` | 常设 |
| Connection | 该浏览器想要优先使用的连接类型 | `Connection: keep-alive``Connection: Upgrade`| 常设 |
| Cookie | 之前由服务器通过 Set- Cookie （下文详述）发送的一个 超文本传输协议Cookie | `Cookie: $Version=1; Skin=new;` | 常设: 标准 |
| Content-Length | 以 八位字节数组 （8位的字节）表示的请求体的长度 | `Content-Length: 348` | 常设 |
| Content-MD5 | 请求体的内容的二进制 MD5 散列值，以 Base64 编码的结果 | `Content-MD5: Q2hlY2sgSW50ZWdyaXR5IQ==` | 过时的 |
| Content-Type | 请求体的 多媒体类型 （用于POST和PUT请求中） | `Content-Type: application/x-www-form-urlencoded` | 常设 |
| Date | 发送该消息的日期和时间(按照 RFC 7231 中定义的"超文本传输协议日期"格式来发送) | `Date: Tue, 15 Nov 1994 08:12:31 GMT` | 常设 |
| Expect | 表明客户端要求服务器做出特定的行为 | `Expect: 100-continue` | 常设 |
| From | 发起此请求的用户的邮件地址 | `From: user@example.com` | 常设 |
| Host | 服务器的域名(用于虚拟主机 )，以及服务器所监听的[传输控制协议](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE "传输控制协议")端口号。如果所请求的端口是对应的服务的标准端口，则端口号可被省略。 自超文件传输协议版本1.1（HTTP/1.1）开始便是必需字段。| `Host: en.wikipedia.org:80``Host: en.wikipedia.org` | 常设 |
| If-Match | 仅当客户端提供的实体与服务器上对应的实体相匹配时，才进行对应的操作。主要作用时，用作像 PUT 这样的方法中，仅当从用户上次更新某个资源以来，该资源未被修改的情况下，才更新该资源。 | `If-Match: "737060cd8c284d8af7ad3082f209582d"` | 常设 |
| If-Modified-Since | 允许在对应的内容未被修改的情况下返回304未修改（ 304 Not Modified ） | `If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT` | 常设 |
| If-None-Match | 允许在对应的内容未被修改的情况下返回304未修改（ 304 Not Modified ），参考 超文本传输协议 的[实体标记](https://zh.wikipedia.org/wiki/HTTP_ETag "HTTP ETag") | `If-None-Match: "737060cd8c284d8af7ad3082f209582d"` | 常设 |
| If-Range | 如果该实体未被修改过，则向我发送我所缺少的那一个或多个部分；否则，发送整个新的实体 | `If-Range: "737060cd8c284d8af7ad3082f209582d"` | 常设 |
| If-Unmodified-Since | 仅当该实体自某个特定时间已来未被修改的情况下，才发送回应。 | `If-Unmodified-Since: Sat, 29 Oct 1994 19:43:31 GMT` | 常设 |
| Max-Forwards | 限制该消息可被代理及网关转发的次数。 | `Max-Forwards: 10` | 常设 |
| Origin | 发起一个针对 跨来源资源共享 的请求（要求服务器在回应中加入一个‘访问控制-允许来源’（'Access-Control-Allow-Origin'）字段）。 | `Origin: http://www.example-social-network.com` | 常设: 标准 |
| Pragma | 与具体的实现相关，这些字段可能在请求/回应链中的任何时候产生多种效果。 | `Pragma: no-cache` | 常设但不常用 |
| Proxy-Authorization | 用来向代理进行认证的认证信息。 | `Proxy-Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==` | 常设 |
| Range | 仅请求某个实体的一部分。字节偏移以0开始。参见[字节服务](https://zh.wikipedia.org/w/index.php?title=%E5%AD%97%E8%8A%82%E6%9C%8D%E5%8A%A1&action=edit&redlink=1 "字节服务（页面不存在）")。 | `Range: bytes=500-999` | 常设 |
| [Referer](https://zh.wikipedia.org/wiki/HTTP%E5%8F%83%E7%85%A7%E4%BD%8D%E5%9D%80 "HTTP引用地址") [*[sic](https://zh.wikipedia.org/wiki/Sic "Sic")*]  | 表示浏览器所访问的前一个页面，正是那个页面上的某个链接将浏览器带到了当前所请求的这个页面。 | `Referer: http://en.wikipedia.org/wiki/Main_Page` | 常设 |
| TE | 浏览器预期接受的传输编码方式：可使用回应协议头 Transfer-Encoding 字段中的值；另外还可用"trailers"（与"分块 "传输方式相关）这个值来表明浏览器希望在最后一个尺寸为0的块之后还接收到一些额外的字段。 | `TE: trailers, [deflate](https://zh.wikipedia.org/wiki/DEFLATE "DEFLATE")` | 常设 |
| User-Agent | 浏览器的[浏览器身份标识字符串](https://zh.wikipedia.org/wiki/%E7%94%A8%E6%88%B7%E4%BB%A3%E7%90%86 "用户代理") | `User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/21.0` | 常设 |
| Upgrade | 要求服务器升级到另一个协议。 | `Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11` | 常设 |
| Via | 向服务器告知，这个请求是由哪些代理发出的。 | `Via: 1.0 fred, 1.1 example.com (Apache/1.1)` | 常设 |
| Warning | 一个一般性的警告，告知，在实体内容体中可能存在错误。 | `Warning: 199 Miscellaneous warning` | 常设 |

#### response响应头字段一览
| 字段名 | 说明 | 例子 | 状态 |
| --- | --- | --- | --- |
| Access-Control-Allow-Origin | 指定哪些网站可参与到跨来源资源共享过程中 | `Access-Control-Allow-Origin: *` | 临时 |
| Accept-Patch| 指定服务器支持的文件格式类型。 | `Accept-Patch: text/example;charset=utf-8` | 常设 |
| Accept-Ranges | 这个服务器支持哪些种类的部分内容范围 | `Accept-Ranges: bytes` | 常设 |
| Age | 这个对象在代理缓存中存在的时间，以秒为单位 | `Age: 12` | 常设 |
| Allow | 对于特定资源有效的动作。针对HTTP/405这一错误代码而使用 | `Allow: GET, HEAD` | 常设 |
| [Cache-Control](https://zh.wikipedia.org/wiki/%E7%BD%91%E9%A1%B5%E5%BF%AB%E7%85%A7 "网页快照") | 向从服务器直到客户端在内的所有缓存机制告知，它们是否可以缓存这个对象。其单位为秒 | `Cache-Control: max-age=3600` | 常设 |
| Connection | 针对该连接所预期的选项 | `Connection: close` | 常设 |
| Content-Disposition | 一个可以让客户端下载文件并建议文件名的头部。文件名需要用双引号包裹。 | `Content-Disposition: attachment; filename="fname.ext"` | 常设 |
| Content-Encoding | 在数据上使用的编码类型。参考 超文本传输协议压缩 。 | `Content-Encoding: gzip` | 常设 |
| Content-Language | 内容所使用的语言 | `Content-Language: da` | 常设 |
| Content-Length | 回应消息体的长度，以 字节 （8位为一字节）为单位 | `Content-Length: 348` | 常设 |
| Content-Location | 所返回的数据的一个候选位置 | `Content-Location: /index.htm` | 常设 |
| Content-MD5 | 回应内容的二进制 MD5 散列，以 Base64 方式编码 | `Content-MD5: Q2hlY2sgSW50ZWdyaXR5IQ==` | 过时的|
| Content-Range | 这条部分消息是属于某条完整消息的哪个部分 | `Content-Range: bytes 21010-47021/47022` | 常设 |
| Content-Type | 当前内容的[MIME](https://zh.wikipedia.org/wiki/MIME "MIME")类型 | `Content-Type: text/html; charset=utf-8` | 常设 |
| Date | 此条消息被发送时的日期和时间(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示) | `Date: Tue, 15 Nov 1994 08:12:31 GMT` | 常设 |
| [ETag](https://zh.wikipedia.org/wiki/HTTP_ETag "HTTP ETag") | 对于某个资源的某个特定版本的一个标识符，通常是一个 消息散列 | `ETag: "737060cd8c284d8af7ad3082f209582d"` | 常设 |
| Expires | 指定一个日期/时间，超过该时间则认为此回应已经过期 | `Expires: Thu, 01 Dec 1994 16:00:00 GMT` | 常设: 标准 |
| Last-Modified | 所请求的对象的最后修改日期(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示) | `Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT` | 常设 |
| Link | 用来表达与另一个资源之间的类型关系，此处所说的类型关系是在 RFC 5988 中定义的 | `Link: </feed>; rel="alternate"`| 常设 |
| [Location](https://zh.wikipedia.org/wiki/HTTP_Location "HTTP Location") | 用来 进行重定向，或者在创建了某个新资源时使用。 | `Location: http://www.w3.org/pub/WWW/People.html` | 常设 |
| P3P | 用于支持设置[P3P](https://zh.wikipedia.org/wiki/P3P "P3P")策略，标准格式为“`P3P:CP="your_compact_policy"`”。然而P3P规范并不成功，大部分现代浏览器没有完整实现该功能，而大量网站也将该值设为假值，从而足以用来欺骗浏览器的P3P插件功能并授权给第三方Cookies。 | `P3P: CP="This is not a P3P policy! ``See http://www.google.com/support/accounts/bin/answer.py?hl=en&answer=151657 for more info."` | 常设 |
| Pragma | 与具体的实现相关，这些字段可能在请求/回应链中的任何时候产生多种效果。 | `Pragma: no-cache` | 常设 |
| Proxy-Authenticate | 要求在访问代理时提供身份认证信息。 | `Proxy-Authenticate: Basic` | 常设 |
| [Public-Key-Pins](https://zh.wikipedia.org/wiki/HTTP%E5%85%AC%E9%92%A5%E5%9B%BA%E5%AE%9A "HTTP公钥固定") | 用于缓解[中间人攻击](https://zh.wikipedia.org/wiki/%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB "中间人攻击")，声明网站认证使用的[传输层安全协议](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E5%B1%82%E5%AE%89%E5%85%A8%E5%8D%8F%E8%AE%AE "传输层安全协议")证书的散列值 | `Public-Key-Pins: max-age=2592000; pin-sha256="E9CZ9INDbd+2eRQozYqqbQ2yXLVKB9+xcprMF+44U1g=";` | 常设 |
| Refresh | 用于设定可定时的重定向跳转。右边例子设定了5秒后跳转至“`http://www.w3.org/pub/WWW/People.html`”。 | `Refresh: 5; url=http://www.w3.org/pub/WWW/People.html` | 专利并非标准Netscape实现的扩展，但大部分网页浏览器也支持。 |
| Retry-After | 如果某个实体临时不可用，则，此协议头用来告知客户端日后重试。其值可以是一个特定的时间段(以秒为单位)或一个超文本传输协议日期。| *   Example 1: `Retry-After: 120`*   Example 2: `Retry-After: Fri, 07 Nov 2014 23:59:59 GMT`| 常设 |
| Server | 服务器的名字 | `Server: Apache/2.4.1 (Unix)` | 常设 |
| Set-Cookie | [HTTP cookie](https://zh.wikipedia.org/wiki/Cookie "Cookie") | `Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1` | 常设: 标准 |
| Status | 通用网关接口 协议头字段，用来说明当前这个超文本传输协议回应的 状态 。普通的超文本传输协议回应，会使用单独的“状态行”（"Status-Line"）作为替代，这一点是在 RFC 7230 中定义的。 | `Status: 200 OK` | Not listed as a [registered field name](http://www.iana.org/assignments/message-headers/message-headers.xml) |
| [Strict-Transport-Security](https://zh.wikipedia.org/wiki/HTTP%E4%B8%A5%E6%A0%BC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8 "HTTP严格传输安全") | HTTP 严格传输安全这一头部告知客户端缓存这一强制 HTTPS 策略的时间，以及这一策略是否适用于其子域名。 | `Strict-Transport-Security: max-age=16070400; includeSubDomains` | 常设: 标准 |
| Trailer | 这个头部数值指示了在这一系列头部信息由由[分块传输编码](https://zh.wikipedia.org/wiki/%E5%88%86%E5%9D%97%E4%BC%A0%E8%BE%93%E7%BC%96%E7%A0%81 "分块传输编码")编码。 | `Trailer: Max-Forwards` | 常设 |
| Transfer-Encoding | 用来将实体安全地传输给用户的编码形式。当前定义的方法包括：分块（chunked）、compress、deflate、gzip和identity。 | `Transfer-Encoding: chunked` | 常设 |
| Upgrade | 要求客户端升级到另一个协议。 | `Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11` | 常设 |
| Vary | 告知下游的代理服务器，应当如何对未来的请求协议头进行匹配，以决定是否可使用已缓存的回应内容而不是重新从原始服务器请求新的内容。 | `Vary: *` | 常设 |
| Via | 告知代理服务器的客户端，当前回应是通过什么途径发送的。 | `Via: 1.0 fred, 1.1 example.com (Apache/1.1)` | 常设 |
| Warning | 一般性的警告，告知在实体内容体中可能存在错误。 | `Warning: 199 Miscellaneous warning` | 常设 |
| WWW-Authenticate | 表明在请求获取这个实体时应当使用的认证模式。 | `WWW-Authenticate: Basic` | 常设 |
| X-Frame-Options| [点击劫持](https://zh.wikipedia.org/wiki/%E7%82%B9%E5%87%BB%E5%8A%AB%E6%8C%81 "点击劫持")保护：*   `deny`：该页面不允许在 frame 中展示，即使是同域名内。*   `sameorigin`：该页面允许同域名内在 frame 中展示。*   `allow-from *uri*`：该页面允许在指定uri的 frame 中展示。*   `allowall`：允许任意位置的frame显示，非标准值。 | `X-Frame-Options: deny` | 过时的 |

参考链接：
[wikipedia-HTTP headers](https://zh.wikipedia.org/wiki/HTTP%E5%A4%B4%E5%AD%97%E6%AE%B5)