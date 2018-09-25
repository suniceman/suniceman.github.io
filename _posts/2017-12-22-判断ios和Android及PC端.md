---
layout: post
title: 判断ios和Android及PC端
date:   2017-12-22 13:32:20 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: post-7.jpg # Add image post (optional)
tags: [Javascript, useragent, navigator]
author: # Add name author (optional)
comments: true
---
### navigator的一些常用属性

 > navigator为window对象的一个属性，指向了一个包含浏览器相关信息的对象

```
navigator.appVersion 浏览器的版本号
navigator.language 浏览器使用的语言
navigator.userAgent 浏览器的userAgent信息
```
> 其中userAgent 属性是一个只读的字符串，声明了浏览器用于 HTTP 请求的用户代理头的值。

### 较常见的ios端、Android端及PC端的判断
#### 1. 简单点的
```javascript
    /* 判断浏览器类型 */
    let userAgent = navigator.userAgent;
    /* 判断手机型号 */
    let app = navigator.appVersion;
    /* Android 终端 */
    let isAndroid = userAgent.indexOf('Android');
    /* ios终端 */
    let isMac = !!userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
```

#### 2. 封装性的
```javascript
/* 判断各类型方法 */
const browser = {
   version: function() {
       const userAgent = navigator.userAgent;
       return {
           /* 判断是否是ios */
           ios: !!userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/),
           /* 判断是否是Android */
           android: userAgent.indexOf('Android') > -1 || userAgent.indexOf('Adr') > -1,

           /* 判断是否是移动端 */
           mobilePhone: !!userAgent.match(/AppleWebKit.*Mobile.*/),

           /* IE内核 */
           trident: userAgent.indexOf('Trident') > -1,
           /* opera内核 */
           presto: userAgent.indexOf('Presto') > -1,
           /* 苹果、谷歌内核 */
           webkit: userAgent.indexOf('AppleWebKit') > -1,
           /* 火狐内核 */
           gecko: userAgent.indexOf('Gecko') > -1 && userAgent.indexOf('KHTML') == -1,


           /* 判断是否是IPone手机或者QQHD浏览器 */
           iphone: userAgent.indexOf('iPhone') > -1,
           /* 判断是否是iPad */
           iPad: userAgent.indexOf('iPad') > -1,

           /* 判断是否是web应用程序(能够让用户完成某些特定任务的网站)，没有头部和底部 */
           webApp: userAgent.indexOf('Safari'),
           /* 是否是微信 */
           weixin: userAgent.indexOf('MicroMessenger'),
           /* QQ */
           QQ: userAgent.match(/\sQQ/i) == ' qq',
      }
   }(),
   /* 判断浏览器使用的语言:navigator.language除IE浏览器外的浏览器使用的语言，
    * navigator.browserLanguageIE浏览器使用的语言
    */
   browserLanguage: (navigator.language || navigator.browserLanguage).toLowerCase()
};
if(browser.version.ios || browser.version.android || browser.version.mobilePhone) {
  console.log('是移动端');
}
```

#### 3.meta标签设置

如对浏览器进行强制全屏的设置（UC全屏），webapp模式等
```html
<meta charset="UTF-8">
<!-- 视图窗口，移动端特属的标签 -->
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,minimum-scale=1,user-scalable=no">
<!-- 避免IE使用兼容模式 -->
<meta http-equiv="x-ua-compatible" content="IE=edge">
<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">
<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">
<!-- UC强制全屏 -->
<meta name="full-screen" content="yes">
<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">
<!-- UC应用模式 -->
<meta name="browsermode" content="application">
<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">
<!-- 是否启动webapp功能，会删除默认的苹果工具栏和菜单栏 -->
<meta name="apple-mobile-web-app-capable" content="yes">
<!-- 这个主要是根据实际的页面设计的主体色为搭配来进行设置 -->
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<!-- 忽略页面中的数字识别为电话号码,email识别 -->
<meta name="format-decoration" content="telephone=no,email=no">
<!-- 启用360浏览器的极速模式(webkit) -->
<meta name="renderer" content="webkit">
<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true">
<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320">
<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no">
```