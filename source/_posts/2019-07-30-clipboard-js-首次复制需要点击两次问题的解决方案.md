---
title: clipboard.js 首次复制需要点击两次问题的解决方案
date: 2019-07-30 22:08:36
tags:
- clipboard.js
categories:
- 积累
---

> web项目中有对渲染后的列表进行复制文字的操作，采用了clipboard.js的方案，免去了解决兼容性的烦恼，但是实际效果不符合预期，第一次点击复制无效，从第二次之后才可以复制成功

项目使用的Vue技术栈，复制操作使用了动态复制的api
```javascript
new ClipboardJS('.btn', {
    text: function(trigger) {
        return trigger.getAttribute('aria-label');
    }
});
```
经过试验，是因为API调用放在了ajax的回调函数中，这样第一次创建ClipboardJS构造函数成功时，复制操作已经结束，没有复制到任何文本，由于要复制的目标文本必须要通过网络请求动态获取，可以采取hack方案，借助`mouseenter`实现，当鼠标移入的时候执行一次，在接下来的点击事件中就可以正常复制。


额外的处理就是区分事件调用者为鼠标移入事件还是点击事件，鼠标移入事件只简单的创建构造函数，屏蔽额外的业务逻辑，真正的逻辑放在点击事件中处理，**保证`mouseenter`只执行一次即可**

附基础代码：
```javascript
// html
<button class="btn" @mouseenter="copy" @click="copy"></button>

// script
function copy() {
    $.get(url, data).then(
        function (response) {
            if (response.code === 0) {
                var clipboard = new ClipboardJS(".btn", {
                    text: function () {
                        return response.text;
                    }
                });
                clipboard.on("success", e => {
                    clipboard.destroy();
                });
                clipboard.on("error", e => {
                    e.clearSelection();
                    clipboard.destroy();
                });
            }
        },
        function () {}
    );
}
```