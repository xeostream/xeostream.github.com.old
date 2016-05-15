---
layout: post
title: "Windows端react native 开发android的一些问题"
date: 2015-11-25 23:05:37 +0800
comments: true
categories: 
---
最近react-native的技术十分火热，正好最近在看Android的开发。因为了解到react-native号称一套解决方案打通Web、Android、iOS之间的壁垒，如果可以做到的话，绝对是鼓舞人心的大好事。
<!--more-->

事先说明，我的系统是win10，node的版本是4.1.2，npm的版本是2.14.4，react-native的版本是0.12.0，这些组合目前遇到了特殊的问题。另外在安装react-native可能会遇到一些C++模块缺失的问题，这类问题可通过安装最新的Vistual Studio解决。

遇到的问题主要是在运行`react-native run-android`命令后，APP会显示`unable to download bundle js`的错误，即使设置了服务端的ip地址reload js之后依然存在这个问题。我在搜索中查找了各种答案都是在说需要设置服务端的ip地址，但是没有答案说明为什么需要这样做。通过查阅官方文档得知，手机APP需要连接service:8081/index.android.bundle?platform=android这个url下载一些脚本，如果服务端没有启动监听此端口的服务器，APP如何设置都是没用的。

那么为什么其他人没有出现这种问题呢，主要原因是react-native对windows的支持还不够完善，无法在执行`react-native run-android`时，先启动服务器。所以需要我们手动启动服务器。命令是`node ~\工程名\node_modules\react-native\packager\packager.js`，启动服务端之后，重新reload js，即OK。

这件事情告诉我们一个道理：搞开源的技术，首先要有一台*nix电脑；然后就是一定要看官方文档，其他翻译的都不要当真。