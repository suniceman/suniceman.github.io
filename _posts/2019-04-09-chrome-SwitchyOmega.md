---
layout: post
title: Chrome 配置 SwitchyOmega
subtitle: 使用shadowsocks科学上网
date: 2019-04-09 18:00:00
author: Silence
header-img: ""
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
tags:
  - shadowsocks
  - SwitchyOmega
---

### 前言

此文章是以 Shadowsocks 代理为例，若想使用 Shadowsocks 请搭建SS/搭建SSR服务并安装对应系统的客户端并启动。详情请参考：

* [一键脚本搭建SS/搭建SSR服务并开启BBR加速](http://suniceman.com/2019/04/10/install-shadowsocks-in-one-command/)

* 客户端下载
	* Windows [下载地址](https://github.com/shadowsocks/shadowsocks-windows/releases)
	* Linux [下载地址](https://github.com/shadowsocks/shadowsocks-qt5/releases)
	* Ios [下载地址](http://shadowsocks.org/en/index.html)

* 配置使用Shadowsocks
	以linux为例：
	1. 打开Shadowsocks客户端软件。
	1. 在客户端对应位置输入服务器shdowsocks.json配置信息。
		![shadowsocks_config](/img/shadowsocks_config.png)
	ios: 
		![shadowsocks_ios](/img/shadowsocks_ios.png)

### SwitchyOmega

Google Chrome 浏览器上的一个代理扩展程序，可以轻松快捷地管理和切换多个代理设置。比如我们接下来要介绍的 自动切换模式。

### 下载安装

点击 [Github-SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega/releases)，下载页面有详细的安装教程，仔细看一下就好。


### 配置 Shadowsocks 情景模式

1. 打开 Chrome， 点击右上角的图标，再点击 `options`。
	![shadowsocks_options](/img/shadowsocks_options.png)

1. 点击左侧的 `New profile`，输入Profile name proxy【自己任意设置名称】，类型选择第一个代理服务器。创建完成后做如下配置：
	![proxy_config](/img/proxy_config.png)

	你也可以自己设置不代理的地址列表。如上图。

1. 保存后你就可以通过这个情景模式科学上网了

### 配置自动切换模式

配置好 Shadowsocks 情景模式后虽然可以使用 Chrome 浏览器科学上网了，但是这样的话无论你访问什么网站都会走代理，有时候访问国内的一些网站反而会很慢，这时候自动切换模式就解决了这个问题。下面介绍一下如何配置自动切换模式。 

1. 点击左侧的 `auto switch`，或者自己新建情景模式，类型选择第二个 自动切换模式。然后做如下配置：
	![auto_switch](/img/auto_switch.png)
	* 切换规则 是在访问 条件设置 的域名时候使用后面设置的 情景模式。比如图中我设置 *.google.com 和 *.github.com 使用 proxy 情景模式【刚刚创建的那个情景模式】。我们可以点击 添加条件 来添加自己的规则。
		* 规则列表格式： `AutoProxy`；
		* 规则列表网址：  https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
	* 这样设置完成 规则列表规则 后就不需要在切换规则中一个一个添加条件了。
	* 切换规则 最后一行的 默认情景模式 代表不在规则列表中网址我们使用 直接连接 情景模式，也就是说不走代理。   

### 参考资料

> [Github-SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega)  
> [Github-gfwlist](https://github.com/gfwlist/gfwlist)