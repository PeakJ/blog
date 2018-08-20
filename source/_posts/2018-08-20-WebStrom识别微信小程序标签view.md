---
title: WebStrom识别微信小程序标签view
date: 2018-08-20 23:46:26
tags:
- webstorm
- 小程序
categories:
- 工具
---

微信小程序开发工具，很多功能没webstrom方便，但webstrom默认识别不了微信小程序开发的标签，

![（如图1所示，webstrom不能识别微信小程序标签）](http://upload-images.jianshu.io/upload_images/1231991-b65ce8a0295a6318.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "（如图1所示，webstrom不能识别微信小程序标签）")

（如图1所示，webstrom不能识别微信小程序标签）

所以，我们来配置一下，以便我们开发微信小程序.

步骤如下：
<!-- more -->

**1、FileType下Cascading Style Sheet 添加*.wxss**

![（如图2，webstrom添加.wxss）](http://upload-images.jianshu.io/upload_images/1231991-b50d9eece3f82897.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "（如图2，webstrom添加.wxss）")

（如图2，webstrom添加.wxss）

**2、FileType下HTML 添加*.wxml**

![（如图3，webstrom添加.wxml）](http://upload-images.jianshu.io/upload_images/1231991-3b3e5a8858c3515f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "（如图3，webstrom添加.wxml）")

（如图3，webstrom添加.wxml）

**3、file -> settings -> editor -> inspections -> html -> unkown html tag, unkown html tag attribute**

![（如图4，webstrom，unkown html tag：添加“view,image,text....”等微信小程序标签。）](http://upload-images.jianshu.io/upload_images/1231991-7d11df6b188438b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "（如图4，webstrom，unkown html tag：添加“view,image,text....”等微信小程序标签。）")

（如图4，webstrom，unkown html tag：添加“view,image,text....”等微信小程序标签。）

![unkown html tag attribute：添加“view,image,text....”等微信小程序标签。](http://upload-images.jianshu.io/upload_images/1231991-d98f48496d25d533.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "unkown html tag attribute：添加“view,image,text....”等微信小程序标签。")

（如图5，unkown html tag attribute：添加“view,image,text....”等微信小程序标签。）

**4、到github将其中的wecharCode.jar下载下来，**

**网址是：**[https://github.com/miaozhang9/wecharCodejar](https://github.com/miaozhang9/wecharCodejar) 

**然后在webStorm 的 File -> import settings 中导入即可，如图所示：**

![（如图7，webStorm引入wecharCode.jar)](http://upload-images.jianshu.io/upload_images/1231991-e75ab2bb45c6b732.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "（如图7，webStorm引入wecharCode.jar)")

（如图7，webStorm引入wecharCode.jar）

**5、做到以上4点，大概就可以在webstrom工具中自由编辑微信小程序了。效果如下图所示：**

![webStorm识别view标签](http://upload-images.jianshu.io/upload_images/1231991-16047a2364457fa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "webStorm识别view标签")

（图8，webStorm识别view标签）

![webStorm识别wx.开js提示](http://upload-images.jianshu.io/upload_images/1231991-29c188ca407432aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "webStorm识别wx.开js提示")

（图9，webStorm识别wx.开js提示）


[原文链接](https://www.jsben.com/webTool/WebStrom/d!90.html)