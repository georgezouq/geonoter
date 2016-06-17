---
layout:     keynote
title:      "Transitioning from Server to Client Side Web Development with Angular 2"
subtitle:   "Keynote: JavaScript Modularization Journey"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - Java
    - 推送
---
# MQTT协议2 - 推送

## 主流的推送方案

APNS(Apple Push Notification Service)
GCM(Google Cloud Messaging)

APNS 和 GCM 是 IOS 和 Android 两大阵营退出的官方推送方案，两者技术架构较为相似，都是由系统来统一的维护一个长连接，所有 APP 统一发送心跳和接收推送。

APNS 使用的方便性毋庸置疑，但是GCM却在国内举步维艰，主要原因有三个：

1.GFW
2.由于国内2G和移动3G的NAT超时时间都小于GCM心跳时间（28min），TCP长连接必然无法保活，每次都要等28分钟心跳失败重连后才能接收到PUSH。
3.某些运营商可能限制了5228端口，移动3G/2G下，发现几乎无法连接上GCM服务器，也就无法获得GCM通知，WhatsApp放后台十分钟后，经常很长时间都收不到PUSH消息。
