---
layout: post
title: 一键脚本搭建SS/搭建SSR服务并开启BBR加速
subtitle:
date: 2019-04-10 10:00:00
author: Silence
header-img: ""
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
tags:
  - shadowsocks
---

### 前言

一键搭建shadowsocks/搭建shadowsocksR的shell脚本，一键脚本适用[Vultr](https://www.vultr.com/)上的和[搬瓦工](https://www.bwgyhw.cn)所有机型（CentOS、Ubuntu、Debian），搭建ss服务器支持所有客户端类型，本机你是iOS，Android，Windows，Mac，或者是Linux，搭建ss/ssr都是适用的科学上网方式。一键脚本搭建SS/SSR服务器，绝对没有任何问题，任何问题欢迎留言。一键脚本内容包括一键搭建shadowsocks/一键搭建shadowsocksR+一键开启bbr加速，适合新手小白。

### 什么是shadowsocks

shadowsocks可以指一种SOCKS5的加密传输协议，也可以指基于这种加密协议的各种数据传输包。  

__shadowsocks实现科学上网原理？__ shadowsocks正常工作需要服务器端和客户端两端合作实现，首先，客户端（本机）通过ss（shadowsocks）对正常的访问请求进行SOCK5加密，将加密后的访问请求传输给ss服务器端，服务器端接收到客户端的加密请求后，解密得到原始的访问请求，根据请求内容访问指定的网站（例如Google，YouTube，Facebook，instagram等），得到网站的返回结果后，再利用SOCKS5加密并返回给客户端，客户端通过ss解密后得到正常的访问结果，于是就可以实现你直接访问该网站的“假象”。  

__为什么选择shadowsocks？__ 不限终端（安卓，苹果，Windows，Mac都可用），流量便宜（服务器500G只要15元），方便（一键脚本，不需要专业知识）。
为什么要自己搭建ss/ssr？你也许会觉得买ss服务也很方便，但是你得要考虑以下几个问题。首先，买的ss服务，限制很多，终端可能只能同时在线2个，每个月就一点点流量可能价格却不便宜，有时候还被别人做手脚，流量跑的贼快；其次，别人收钱跑路怎么办？很多这种情况的；更重要的是，如第一个问题中描述的shadowsocks原理，如果有心人做了一点手脚，是可以得到你的访问记录的；而自己搭建ss/ssr服务，一键脚本也就10来分钟就可以搞定。

### 一键脚本搭建ss/ssr支持系统版本

脚本系统支持：CentOS 6+，Debian 7+，Ubuntu 12+

>注：这个脚本支持的系统版本是指ss服务器的版本（都没看过也没关系，不影响搭建），你本机是Windows、Mac、Linux，或者你想用手机端搭建ss/ssr服务器，安卓和苹果，都是可以的。

### 代理服务器购买

作为跳板的代理服务器推荐[Vultr](https://www.vultr.com/)和[搬瓦工](https://www.bwgyhw.cn)，一是因为本脚本在这两家的所有VPS都做了测试，二是因为都是老牌VPS服务商，不怕跑路。

Vultr和搬瓦工上的所有机型是绝对可以一键脚本搭建shadowsocks/搭建shadowsocksR+开启bbr加速成功的 

### 连接远程Linux服务器

购买完成后, 通过Terminal远程连接Linux即可。

你如果身边没有电脑，一定要搞什么手机搭建ss服务器 :symbols: 也是可以的，毕竟一键脚本只需要复制几行脚本命令就行了。iOS用户可以使用Termius这个工具，直接在App Store下载就行。

### 一键搭建SS/搭建SSR服务

在购买VPS并用Xshell连接上你刚购买的VPS后，你将看到如下图所示的界面：

![Shell](/img/shell.png)

1. 下载一键搭建ss脚本文件

	```
	  git clone https://github.com/suniceman/ss-fly
	```

	如果提示 `bash: git: command not found`，则先安装git：

	```
	Centos执行这个： yum -y install git
	Ubuntu/Debian执行这个： apt-get update && apt-get -y install git
	```

1. 运行搭建ss脚本代码
	```
	ss-fly/ss-fly.sh -i suniceman.com 1024
	```
	suniceman.com换成你要设置的shadowsocks的密码即可（这个suniceman.com就是你ss的密码了，是需要填在客户端的密码那一栏的），密码随便设置，最好只包含字母+数字，一些特殊字符可能会导致冲突。而第二个参数1024是端口号，也可以不加，不加默认是1024~（举个例子，脚本命令可以是ss-fly/ss-fly.sh -i suniceman，也可以是ss-fly/ss-fly.sh -i suniceman 8585，后者指定了服务器端口为8585，前者则是默认的端口号1024，两个命令设置的ss密码都是suniceman：  
 	界面如下就表示一键搭建ss成功了：  
	![Shell](/img/success_shell.png)
 	注：如果需要改密码或者改端口，只需要重新再执行一次搭建ss脚本代码就可以了，或者修改/etc/shadowsocks.json这个配置文件。  
1. 相关ss操作
	```
	启动：/etc/init.d/ss-fly start
	停止：/etc/init.d/ss-fly stop
	重启：/etc/init.d/ss-fly restart
	状态：/etc/init.d/ss-fly status
	查看ss链接：ss-fly/ss-fly.sh -sslink
	修改配置文件：vim /etc/shadowsocks.json
	```

1. 卸载ss服务
	```
	ss-fly/ss-fly.sh -uninstall
	```

### 一键搭建shadowsocksR

再次提醒，如果安装了SS，就不需要再安装SSR了，如果要改装SSR，请按照上一部分内容的教程先卸载SS！！！

1. 下载一键搭建ssr脚本
	```
	  git clone https://github.com/suniceman/ss-fly
	```

1. 运行搭建ssr脚本代码
	```
	ss-fly/ss-fly.sh -ssr
	```

1. 输入对应的参数
	执行完上述的脚本代码后，会进入到输入参数的界面，包括服务器端口，密码，加密方式，协议，混淆。可以直接输入回车选择默认值，也可以输入相应的值选择对应的选项.

全部选择结束后，会看到如下界面，就说明搭建ssr成功了：
```
Congratulations, ShadowsocksR server install completed!
Your Server IP        :你的服务器ip
Your Server Port      :你的端口
Your Password         :你的密码
Your Protocol         :你的协议
Your obfs             :你的混淆
Your Encryption Method:your_encryption_method
 
Welcome to visit:https://shadowsocks.be/9.html
Enjoy it!
```

1. 相关操作ssr命令
	```
	启动：/etc/init.d/shadowsocks start
	停止：/etc/init.d/shadowsocks stop
	重启：/etc/init.d/shadowsocks restart
	状态：/etc/init.d/shadowsocks status
	配置文件路径：/etc/shadowsocks.json
	日志文件路径：/var/log/shadowsocks.log
	代码安装目录：/usr/local/shadowsocks
	```

1. 卸载ssr服务
   ```
	./shadowsocksR.sh uninstall
    ```


### 一键开启BBR加速

BBR是Google开源的一套内核加速算法，可以让你搭建的shadowsocks/shadowsocksR速度上一个台阶，本一键搭建ss/ssr脚本支持一键升级最新版本的内核并开启BBR加速。  

BBR支持4.9以上的，如果低于这个版本则会自动下载最新内容版本的内核后开启BBR加速并重启，如果高于4.9以上则自动开启BBR加速，执行如下脚本命令即可自动开启BBR加速：

```
ss-fly/ss-fly.sh -bbr
```

装完后需要重启系统，输入y即可立即重启，或者之后输入`reboot`命令重启。

判断BBR加速有没有开启成功。输入以下命令：
```
sysctl net.ipv4.tcp_available_congestion_control
```
如果返回值为：
```
net.ipv4.tcp_available_congestion_control = bbr cubic reno
```
后面有bbr，则说明已经开启成功了。

### 声明

本文只作为技术分享，请遵守相关法律，严禁做违法乱纪的事情！