---
title: 设置七牛CDN加速
tags: cdn 域名
categories: cdn
date: 2020-05-15 13:35:05
---




​		我们选择了七牛的CDN，那我们简单配置一下就能起飞了，唯一的小坑就是设置域名和域名解析。

​       我们图(jian)文(dan)并(ming)茂(liao)的说一下。

​		![](http://qiniu.moluyun.com/WechatIMG9.png)

1.在cdn面板打开域名管理

2.在加速域名那里填入域名，这个地方填个二级域名。比如我的域名是www.moluyun.com 我可以填入 qiniu.moluyun.com。二级域名的前缀可以自定义，后面域名解析的时候填入就行。

![](http://qiniu.moluyun.com/WechatIMG10.png)

3.如果你已经开通了七牛云OSS那会自动出来你创建的buket

4.缓存可以根据自己的使用场景配置一下，比如我现在只上传点图片，更改需求不大，我就默认了30天的配置。

域名配置基本就完成了，下面去域名厂商去设置一下域名解析就完活了，走起！

![](http://qiniu.moluyun.com/WechatIMG11.png)

按步点击，来到终极信息的地方。

下面以我的万网域名为例

![](http://qiniu.moluyun.com/WechatIMG12.png)

记录类型选择 CNAME，主机记录填写上边在七牛自定义的二级域名，记录值填写七牛给你的CNAME值。完毕。可以开心的使用了！