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
# MQTT 协议 理论篇

MQTT(Message Queuing Telemetry Transport,消息队列遥测传输) 是IBM开发的一个及时通讯协议，有可能成为物联网的重要组成部分。该协议支持所有平台，几乎可以把所有联网物品和外部链接起来，被用来当做传感器和制动器

## MQTT 特点

MQTT 协议是为了大量计算能力有限，且工作在低带宽、不可靠订单网络的远程传感器和控制设备通讯而设计的协议，它具有以下主要特征：

1.使用发布/订阅消息模式，提供一对多的消息发布，解除应用程序耦合（这一点类似XMPP，但是MQTT的信息冗余远小于XMPP，因为XMPP使用的是XML这种格式来传输数据）

2.对负载内容屏蔽的消息传输

3.使用 TCP/IP 提供网络连接，主流的MQTT是基于TCP连接进行数据推送的，但是同样有基于UDP的版本，叫做MQTT-SN。这两种版本由于基于不同的链接方式，优缺点自然也就各有不同了。

4.有三种消息发布服务质量：

#### “至多一次”

消息发布完全依赖底层 TCP/IP 网络，会发生消息流失或重复。这一级别可用于如下情况，环境传感器数据，丢失一次读记录无所谓，因为不久后还会有二次发送

#### 至少一次

确保消息到达，但是消息重复可能会发生

#### 只有一次

确保消息到达一次。这一级别可用于如下情况，在计费系统中，消息重复或丢失会导致不正确的结果。这种高质量的消息发布服务还可以用于即时通讯类的APP的推送，确保用户收到且只收到一次。

5.小型传输，开销很小（固定长度的头部是2字节）协议交换最小化，以降低网络流量。

6.使用 Last Will 和 Testament 特性通知有关各方客户端异常中断的机制

Last Will：即遗言机制，用于通知同一主题下的其他的设备发送遗言的设备以及断开了链接

Testament：遗嘱机制，功能类似 Last Will

# MQTT 连接 心跳 确认 断开

## CONNECT

正如前面说，MQTT有关字符串部分采用的修改版UTF-8编码，CONNECT可变头部中协议名称、消息体都是采用修改版的UTF-8编码。前面基本上可变头部内容不多。
