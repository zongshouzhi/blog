---
title: android使用svg
tags: android svg
categories: android
date: 2020-05-17 17:48:15
---


android 对svg的支持很友好，Androidstudio支持的也很方便。

先说一下使用姿势。

![](http://qiniu.moluyun.com/WechatIMG1.png)

* 找到drawable资源文件夹
* 选择new 创建新文件
* 选择 Vector Asset
* 选择本地下载的xx.svg 文件
* 完成后就可以像使用一般的drawable资源一样使用了。

android使用svg也是存在一些问题。

* 5.0 以下 的兼容问题

  Android是从5.0开始支持svg的，5.0以前的textView这类不能使用drawable。ImageView是可以的

* 7.0 以下的渐变语法

  svg的语法支持也是随着Android版本的升级支持的更多。

这是我使用的一些习惯：

图标的svg 我们一般选择 size是24的。命名一般采取 ic_rule_color_size。

如果你们的app还兼容5.0以下，并不建议使用svg。每一处都要考虑兼容，也为线上带来风险，投入产出比堪忧。