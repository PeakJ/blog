---
title: h5 数据上报之SendBeacon
date: 2019-04-15 14:34:41
tags:
- 数据上报
categories:
- 积累
---
### 概述
公司为了精准的了解自己产品的用户使用情况，通常会对用户数据进行统计分析，获取pv、uv、页面留存率、访问设备等信息。与之相关的就是客户端的数据采集，然后上报的服务端。为了保证数据的准确性，就需要保证数据上报不会有差错。

### 常见方案及缺陷
数据发回服务器的常见做法是，将收集好的用户数据，放在unload事件里面，用 AJAX 请求发回服务器。但是这种方法有个弊端，因为AJAX通常应用的场景是异步发送请求，**在 unload 事件中，使用 XHR 异步发送数据。这种做法有可能导致服务端未收到数据，浏览器就已经断开连接**，数据就会丢失。虽然AJAX支持同步请求，但这种做法会阻塞页面的跳转，影响用户体验。

<!-- more -->

### 解决方案
[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API) 的出现就是为了解决这个问题的。使用 sendBeacon() 方法会使用户代理在有机会时异步地向服务器发送数据，同时不会延迟页面的卸载或影响下一导航的载入性能。这就解决了提交分析数据时的所有的问题：数据可靠，传输异步并且不会影响下一页面的加载。

### 使用方法
使用方式也很简单:`navigator.sendBeacon(url, data)`
- url参数表明 data 将要被发送到的网络地址。
- data 参数是将要发送的 ArrayBufferView 或 Blob, DOMString 或者 FormData 类型的数据
- return: 当用户代理成功把数据加入传输队列时，sendBeacon() 方法将会返回 true，否则返回 false。
例如：
```javascript
window.addEventListener('unload', logData, false);

function logData() {
    navigator.sendBeacon("/log", analyticsData);
}
```

### 兼容性
beacon api 的兼容性如下：
![caniuse 兼容性信息](https://upload-images.jianshu.io/upload_images/1231991-2d7681813dfb7893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1024)

在不支持的浏览器中，可以使用以下 fallback 代码解决浏览器不支持 Beancon API 的问题（仅实现了 GET 方法）。
```
function sendBeacon (url) {
  if (typeof navigator !== 'undefined' && navigator.sendBeacon) {
    return navigator.sendBeacon(url);
  }

  try {
    var req = new window.XMLHttpRequest();
    req.open('GET', url, false);
    req.send();
  } catch (e) {
    // Fix IE9 cross-site error
    var img = new window.Image();
    img.src = url;
  }
}
```
### 参考文章
- [使用 SendBeacon API 上报数据](https://www.sdvcrx.com/post/2018-11-21-beancon-api/)
- [如何用网页脚本追踪用户](http://www.ruanyifeng.com/blog/2019/04/user-tracking.html)
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)
- [页面跳转时，统计数据丢失问题探讨](http://taobaofed.org/blog/2016/04/01/lose-statistics/)


