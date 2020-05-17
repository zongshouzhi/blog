---
title: Drawerlayout基础使用
tags: android drawerlayout
categories: android
date: 2020-05-16 13:06:48
---


​       最近想写个海外app，赚点美刀。好久没写androidUI了，居然连DrawerLayout都不会用了。赶紧学习一波，记录一下以供日后学习。

​       基本使用很简单，直接上。

![](http://qiniu.moluyun.com/WechatIMG14.png)

drawerLayout 的第一个child（图中的ConstraintLayout）布局就是抽屉关闭时显示的主界面。

第二个和第三个则是抽屉布局。图中的Framelayout就是左边的抽屉。同理我们还可以添加右边的抽屉。只需要把箭头的left改为right就行了。

使用起来还是相当的简单。

使用过程中也遇到了一个问题：

![](http://qiniu.moluyun.com/WechatIMG13.png)

这个问题说Drawerlayout must be measured with MeasureSpec.Exactly.也是就说DrawerLayout的宽高我们必须指定具体的大小，数值或match_parent.

[源码下载](https://github.com/zongshouzhi/DemoSource/tree/master/DrawLayout)

