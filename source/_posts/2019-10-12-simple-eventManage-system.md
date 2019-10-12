---
title: simple eventManage system
date: 2019-10-12 15:57:05
tags:
- JavaScript
categories:
- 积累
---

### 一个精简的事件系统

```javascript
let eventManage = (function() {
  let event = {}
  // 触发事件
  function dispatchEvent(type, infoData) {
    if (event.handlers) {
      if (Object.prototype.hasOwnProperty.call(event.handlers, type)) {
        for (let i = 0; i < event.handlers[type].length; i++) {
          event.handlers[type][i](infoData)
        }
      }
    }
  }
  // 添加监听
  function addEventListener(type, callBack) {
    if (!event.handlers) {
      Object.defineProperty(event, 'handlers', {
        value: {},
        enumerable: false,
        configurable: true,
        writable: true
      })
    }
    if (!event.handlers[type]) {
      event.handlers[type] = []
    }
    event.handlers[type].push(callBack)
  }
  //移除监听
  function removeEventListener(type) {
    if (event.handlers) {
      if (Object.prototype.hasOwnProperty.call(event.handlers, type)) {
        event.handlers[type] = []
      }
    }
  }
  return {
    event,
    eventType,
    dispatchEvent,
    addEventListener,
    removeEventListener
  }
})()

export { eventManage }
```