---
title: mac chrome跨域快捷方式
date: 2018-04-05 17:03:22
tags: 
- mac
- chrome
- 跨域
categories: 
- 效率
---
前端er开发通常避不开跨域问题，开发阶段，本地代码访问服务器接口会提示跨域，无法获取到想要的数据。之前用Windows开发可以很好的解决，安装Chrome，然后在快捷方式右键属性，在目标一栏最后添加 `--args --disable-web-security` 即可，有两种版本设置方式，这里不做过多说明。但是Mac上的跨域就没那么方便了，因为应用启动不再是像Windows的快捷方式了，也没有属性了，只能在终端用命令行打开跨域模式，命令如下（***需替换路径中的yourname***）：
> `open -n /Applications/Google\ Chrome.app/ --args --disable-web-security  --user-data-dir=/Users/yourname/MyChromeDevUserData/`

每次开启跨域都需要重新敲命令，比较麻烦，下面介绍一种简单的方式，利用Mac自带的automator创建一个应用程序。
***
<!-- more -->
1. command+ 空格打开，输入automator回车打开automator

2. 选择应用程序，点击选取
![](https://upload-images.jianshu.io/upload_images/1231991-15ccfdbdd7d8671e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 选择实用工具中的shell脚本，双击之后输入
>    ```open -n /Applications/Google\ Chrome.app/ --args --disable-web-security --user-data-dir=/Users/yourname/MyChromeDevUserData/```

![](https://upload-images.jianshu.io/upload_images/1231991-63b137aa69983e3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4. 关闭并重命名，保存到应用程序，点击存储
![](https://upload-images.jianshu.io/upload_images/1231991-2be3260cf3b095d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这时候就可以Launchpad种看到创建的应用了
![](https://upload-images.jianshu.io/upload_images/1231991-d102665723f211d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5. 为了以后启动方便可以拖动到程序坞中保留

![](https://upload-images.jianshu.io/upload_images/1231991-4c74f8a704f56d36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
至此，跨域Chrome的快捷启动方式就完成了！