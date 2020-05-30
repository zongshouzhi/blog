---
title: 使用charles 抓起android http和https包
tags: Charles
categories: android
date: 2020-05-21 15:36:56
---


希望帮助到大家。[项目源码](https://github.com/zongshouzhi/DemoSource/tree/master/httpProxy)

#### 环境准备

* charles[官网下载]([https://www.**charles**proxy.com/](https://www.baidu.com/link?url=N9_YaSLU28VYtOaVy1LkUiETepKzA5RwWMmM0wODdca0WRcFw9ffbFxb9Qr3eaoC&wd=&eqid=d3357b0300042095000000035ec60e47))

#### 配置Charles

![](http://qiniu.moluyun.com/charles_proxy.png)

* 找到proxy 下的proxy setting

![](http://qiniu.moluyun.com/charles_8888.png)

* 将端口设置为8888

##### 二 配置手机代理

* 在电脑终端找到自己电脑的ip。window命令为ipconfig mac和linux 为ifconfig

* 打开手机设置，找到wlan。设置已连接的wifi代理为手动代理。代理主机名填写上一步获取的电脑ip，端口填写charles设置的8888

  注：手机和电脑要处于一个局域网下。

经过上面的简单配置就可以顺利的抓到Android http的包了。

#### 抓取https设置

![](http://qiniu.moluyun.com/charles_ssl_proxy1.png)

* 打开Charles ssl配置

![](http://qiniu.moluyun.com/charles_ssl_config1.png)

* 勾选 Enable SSL Proxying
* 添加 要抓去的域名。如果要抓取全部，可以填写* ：*

![](http://qiniu.moluyun.com/charles_ssl_cer.png)

* 点击help ->SSL Proxying -> Install Charles Root Certificate on a Mobile Device
* 跟据提示，在上一步手机已经设置好代理的基础上。用手机浏览器打开chls.pro/ssl这个网址。下载证书并安装。一定要安装！！！

现在就可以轻松的抓起https的包了。但是如果你的手机是android 7.0 以上的系统，请继续往下看。

android 官网说明 https://developer.android.google.cn/training/articles/security-config

不想看官网的随我来继续配置，后面也有demo 源码

* 下载charles证书，后面需要配置到app中

  ![](http://qiniu.moluyun.com/charles_ssl_root_cer.png)



* 在项目的res -> xml 文件夹中（没有则新建）新建一个 xxx.xml 内容为

  ```
  <?xml version="1.0" encoding="utf-8"?>
  <network-security-config>
      <domain-config>
          <domain includeSubdomains="true">moluyun.com</domain>
          <trust-anchors>
              <certificates src="@raw/charles"/>
          </trust-anchors>
      </domain-config>
  </network-security-config>
  ```

  注：其中 moluyun.com 替换为自己项目的域名，并把刚才保存到电脑的charles的证书，拷贝到res-> raw。名字要与 @raw/charles对应。

* 在Manifest 的<Application 的标签内 添加 android:networkSecurityConfig="@xml/network_security_config"

  ```
  <application
      android:allowBackup="true"
      android:icon="@mipmap/ic_launcher"
      android:label="@string/app_name"
      android:roundIcon="@mipmap/ic_launcher_round"   android:networkSecurityConfig="@xml/network_security_config"
      android:supportsRtl="true"
      android:theme="@style/AppTheme">
     
  ```

 到此，基本可以happy的抓包了。

在分享一下项目如何设置不让抓包：

已android常用okhttp为例，我们可以设置OkHttpClient的proxy

```
OkHttpClient.Builder BUILDER = new OkHttpClient.Builder();
BUILDER.proxy(java.net.Proxy.NO_PROXY);
```



