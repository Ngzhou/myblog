---
title: 基于asterisk的语音通话方案在凤金语音平台的实践
comments: true
date: 2019-06-20 17:52:24
tags:
- asterrisk
- 语音
- 通信
- C语言
categories:
- C语言
author: jun.zhou
---

![](https://img.fengjr.com/image/2019/06/20/a0f32ca1a5cf0db218a0634ed40ac35f.png)

# 基于Asterisk的语音通话方案在凤金语音平台的实践

## 什么是 Asterisk

### 1、定义

**[Asterisk](https://baike.baidu.com/item/%E8%B7%9D%E7%A6%BB%E5%90%91%E9%87%8F/100343?fromtitle=asterisk&fromid=1181693&fr=aladdin)** 是一个应用于 VOIP 的开放源代码的 PBX 系统；可以运行在 Linux，BSD，Windows(仿真的)以及 OS X 上；提供了语音邮箱服务(Voicemail)、电话会议、交互式语音应答(IVR)、呼叫队列，它还可以支持三方呼叫，主叫标识；支持 4 类 VOIP 的协议；通过使用相对便宜的硬件，它可以与几乎全部基于标准的电话设备进行互联操作。

与硬件 VOIP 相比，Asterisk 不仅具有其常用功能，还具有能够支持多媒体，具有可编程功能；Asterisk 需要的带宽，一般为 32KB/线路，也就是说每支持一条线路，只需要增32KB 的带宽，但是需要网络质量良好；Asterisk 可支持成千的客户端（需要板卡与带宽支持）。

**Asterisk 能够支持传统的线路，包括：**

- TDM （Time Division Multiplexing） 
- T1/E1 PRI/ PRA & RBS （Robbed Bit Signal）modes 
- Analog phone lines/ phones （POTS） 
- ISDN （Integrated Services Digital Network） 
- Both BRI （Basic Rate）and PRI （Primary Rate）

Asterisk 支持的协议包括：

- Session Initiation Protocol （SIP） 
- H. 323 （ITU standard, contributed support） 
- Inter- Asterisk eXchange （IAX） 
- Media Gateway Control Protocol （MGCP）

### 2、结构

![](https://img.fengjr.com/image/2019/06/20/685dc844b97b7277c0c9a66b0f3331a6.png)

### 3、主要配置文件
#### asterisk.confAsterisk
各模块目录配置文件
 
#### sip.conf
配置 sip 帐号信息文件

#### extensions.conf
拨号计划配置文件

#### voicemail.conf
语音邮箱留言配置文件

#### cdr_mysql.conf
通话记录详情文件

### manager.conf
AMI 配置文件

### 4、SIP 协议

#### SIP 协议介绍

会话初始协议（Session Initiation Protocol）是一个在 IP 网络上进行多媒体通信的应用层控制信令协议，它被用来创建、修改、和终结一个或多个参加者参加的会话进程，与 SDP、RTP/RTCP、DNS 等协议配合，共同完成会话建立及媒体协商。

#### SIP 相关协议

会话描述协议 SDP（Session Description Protocol）为应用层的控制协议，用于会话建立过程中的媒体协商。

RTP/RTSP：都为应用层的承载面协议，会话建立后，RTP 协议保证媒体流的实时传输。RTSP 协议对实时传输的媒体流进行监控。

#### SIP 在协议栈中的位置

![](https://img.fengjr.com/image/2019/06/20/190783db5ec80efe86ea90b71cf120a4.png)

#### SIP 的网络模型

![](https://img.fengjr.com/image/2019/06/20/36c462619b1a0c4888ae27fea6900eda.png)

#### SIP 协议报文格式

```
REGISTER sip:10.10.255.161:5060 SIP/2.0
Via: SIP/2.0/UDP 10.254.21.11:44982;branch=z9hG4bK-d87543-4c778a261b56f612-1--d87543-;rport
Max-Forwards: 70
Contact: <sip:111@10.254.21.11:44982;rinstance=40db0908120d6299>
To: "111"<sip:111@10.10.255.161:5060>
From: "111"<sip:111@10.10.255.161:5060>;tag=3418703c
Call-ID: ZmRiYmQyNzA1OGI4YTc0Y2Q2NmYwMTUyOGMwYmJjOTc.
CSeq: 1 REGISTER
Expires: 3600
Allow: INVITE, ACK, CANCEL, OPTIONS, BYE, REFER, NOTIFY, MESSAGE, SUBSCRIBE, INFO
User-Agent: eyeBeam release 1011d stamp 40820
Content-Length: 0
```

#### 基本呼叫过程

![](https://img.fengjr.com/image/2019/06/20/906a0515a50f9ed85ac7a285f5f92d79.png)

### 5、Dialplan 介绍

Dialplan 是 Asterisk 系统的真正核心，它定义了 Asterisk 怎样处理呼入和呼出。有四个部分构成： contexts， extensions， priorities 和 applications。

```
[TestMenu]
exten=> 1234,1,Answer()
    same=> n, Background(main-menu)
    same=> n, WaitExten(5)
exten=> 1,1,Dial(SIP/111)
    same => n,Hangup()
exten=> 2,1,Dial(SIP/222) 
    same => n,Hangup()
exten=> i,1,Playback(pbx-invalid) ; i  invalid
   same=>n, Goto(TestMenu,1234,1)
 exten=> t,1,Playback(vm-goodbye) ; t  timeout
   same=>n, Hangup()

```

### 6、AGI 原理

AGI 即 Asterisk  Gateway  Interface 它为外部程序提供了标准的接口来控制 Asterisk 的dialplan。 

拨号计划中，可以采用各种语言很方便的通过 AGI 接口写脚本。脚本和 Asterisk 之间通过标准的输入输出进行交互，标准的输入输出分别为：
- STDIN : 标准输入，外部脚本程序通过标准的输入，从  Asterisk 接收信息。
- STDOUT: 标准输出, 外部脚本程序通过标准的输出，发送命令到 Asterisk。
- STDERR : 标准错误输出，外部脚本程序通过标准错误输出调式信息到 Asterisk 控制台。　

用例:

```
exten => 1234,1,Answer()
same=> n,agi(demo.php); demo.php 位于/var/lib/asterisk/agi-bin/，并且是可执行权限
same=> n,NoOp(${CALL_ID}); 在 Asterisk 控制台打印 CALL_ID 变量
same=> n,hangup()
```

```
array (
  'agi_request' => 'demo/record.php',
  'agi_channel' => 'SIP/111-00000051',
  'agi_language' => 'en',
  'agi_type' => 'SIP',
  'agi_uniqueid' => '1524539689.81',
  'agi_version' => '11.25.1',
  'agi_callerid' => '111',
  'agi_calleridname' => '111',
  'agi_callingpres' => '0',
  'agi_callingani2' => '0',
  'agi_callington' => '0',
  'agi_callingtns' => '0',
  'agi_dnid' => '444',
  'agi_rdnis' => 'unknown',
  'agi_context' => 'internal',
  'agi_extension' => '444',
  'agi_priority' => '2',
  'agi_enhanced' => '0.0',
  'agi_accountcode' => '',
  'agi_threadid' => '140102795544320',
  'agi_arg_1' => '111',
  'agi_arg_2' => 'SIP/111-00000051',
)

```

![](https://img.fengjr.com/image/2019/06/20/8f52d5d355486b664bd8996a0af18b82.png)

### 7、AMI 远程控制 Asterisk

Asterisk Manager API (Asterisk 控制接口)允许管理客户端程序连接到一个 Asterisk 实例，并且可以通过 TCP/IP (AMI 通过 TCP/IP 协议连接到 Asterisk 服务器的端口默认为 5038)流发送命令或读取事件。通过此接口，可以实现对 Asterisk 系统的监控和控制。

/etc/asterisk/manager.conf 用于对这个管理接口进行配置，可以在 manager.conf 中设定新的用户名、口令和允许连接的网络地址，读写的权限等。

```
[general]
enabled = yes
port = 5038
bindaddr = 10.254.21.11
[admin]
secret = admin
deny = 0.0.0.0/0.0.0.0
permit = 127.0.0.1/ 10.254.21.11
read = all,system,call,log,verbose,command,agent,user,config
write = all,system,call,log,verbose,command,agent,user,config
```

TCP 的 AMI 可以简单使用 telnet 命令 连接，例如


```
telnet 10.254.21.11 5038
```


登录


```
Action: Login
Username: admin
Secret: admin
```

拨打电话

```
Action: Originate
Channel: SIP/111 ;主叫通道，只有主叫通道接通后，才会调用被叫号码/被叫所属 context
Exten: 222 ; 被叫号码，与 Context、 Priority 一起，是必须的参数
Context: internal ; Exten 关联的上下文
Priority: 1 ; Exten 关联的优先级
Timeout: 3000  ;发起连接发生的超时（以毫秒为单位）（默认为30000毫秒）
CallerId: 111 ;用于呼叫的来电显示
Async: true ;对于发起来说是异步的（允许在不等待响应的情况下生成多个调用）
```


退出

```
Action: Logoff
```

### 8、SIP Proxy

1 . 配置 Asterisk 作为 SIP Proxy, 并让 eyeBeam 注册上去, 确保能打通电话就要用到  sip.conf  , Extension.conf 文件。

配制分别如下：

在 sip.conf文件中分别添加如下内容，其中 111 和 222 是预设的两个 sip 号码

```
[111]
secret=aaa                         ;密码，随意设置
port=5060                         ;SIP端口地址
type=friend                       ;类型为 friend，可以相互拨打对方
host=dynamic                   ;要求号码注册,以便 Asterisk 可以知道如何找到电话
qualify = yes                      ;确认远端设备是否可达
nat = no                            ;如果一个号码在NAT设备后面,例如防火墙，配置为yes
context = internal            ;定义了指令的地点,用于控制电话的权限,及如何处理此号码的呼入叫。
```

```
[222]
secret=aaa
port=5060
qualify = yes
nat = no
context = internal
canreinvite = no
type=friend
host=dynamic
```

## Asterisk 语音通话方案

### 1、网络拓扑

![](https://img.fengjr.com/image/2019/06/20/287a9c0702e7d3b4e8c7ec7c94b4c39f.png)

### 2、系统明细

|项次|产品|
|---|---|
|1|系统服务器（32G 内存，8 核 CPU，1.8T 硬盘）|
|1.1|语音服务器 |
|1.2|录音服务器|
|1.3|WEB 服务器|
|2|数字中继网关|
|3|CTI 坐席授权|
|4|SIP 话机|
|5|耳麦|

- 语音服务器。部署基于开放源代码的 Asterisk 的 PBX 系统，提供了语音邮箱服务、电话会议、交互式语音应答(IVR)、呼叫队列，它还可以支持三方呼叫，主叫标识。
- 录音服务器。存放、管理呼叫中心所有通话录音，同时防止单机问题，采取双台冗余。
- WEB 服务器。业务管理、分机授权、线路接入、API 调用等功能模块。
- 数字中继网关。接运营商中继线；与语音服务器对接。

### 3、呼叫中心

![](https://img.fengjr.com/image/2019/06/20/d8c6b0531e961c6b1c64cbcf96662e6b.png)
### 4、中继线路

- 独立组建。在 IDC 接入运行商中继线，配置语音网关。
- 三方线路。三方给定语音服务器地址和端口，呼叫中心开发对接流程，在呼叫时与三方交互，同时由于通话经过 PBX 系统，因此录音也会在录音服务器有存留，方便后续检索。

### 5、话机与中继网关

话机。支持 SIP 协议，同时配有耳麦。

![](https://img.fengjr.com/image/2019/06/20/8383a003a66405aea52ca8a5e2c233f8.png)

中继网关。

![](https://img.fengjr.com/image/2019/06/20/bb5c08e0a05a415f864ba6ba39bce8a2.png)

![](https://img.fengjr.com/image/2019/06/20/594d180a3c02777646fb91e34bbbb8e8.png)

## 凤金语音平台

### 1、系统架构
![系统架构图](https://img.fengjr.com/image/2019/06/20/0c4492813f1a2e28b3bdc40dee6681ec.png)
### 2、线路对接

|项次|厂商|
|---|---|
|1|中通|
|2|鼎信通达 GSM 无线网关|
|3|云之讯|
|4|其他厂商（支持扩展）|

### 3、数据统计

**业务分机使用情况，总计 95**


|业务|数量|备注|
|---|---|---|
|信审|	26	||
||12|	外包|
|催收|	45|	包含提醒|
||12|	回访外包|

**2018年7月到2019年5月外呼量**

![](https://img.fengjr.com/image/2019/06/20/e9b231aef189017fd015dfe6bc2e337b.png)

### 4、安全规划

#### 分机、线路、业务日期设定

![](https://img.fengjr.com/image/2019/06/20/96d3ec785bff1a62152568c222e0a2b4.png)

![](https://img.fengjr.com/image/2019/06/20/b57fd122947e194f34483f497d59b60c.png)


![](https://img.fengjr.com/image/2019/06/20/6fc2e68b65eb69e66d85519af4025f3c.png)

#### API 鉴权

![](https://img.fengjr.com/image/2019/06/20/a324aac1e30b64fdfcf1dcce6b9489d8.png)

#### CTI 服务器防火墙白名单
#### 服务器多机部署，数据冗余


![](https://img.fengjr.com/image/2019/06/20/041eae51d040aed6aff301ccfdc7f4f0.png)