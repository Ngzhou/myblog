---
title: 基于Nginx-rtmp-module直播实战
comments: true
date: 2019-11-21 14:00:11
tags:
categories: WEB前端
author: jun.zhou
---

# 基于Nginx-rtmp-module直播实战

![Nginx-rtmp-module](https://img.fengjr.com/image/2019/11/20/336537d7e80764f5a6a297f9a0546822.png)

随着流量资费的降低和网路带宽的增加，直播已经深入到我们的方方面面，从最开始的游戏主播，到后来的生活主播、没事主播，从PC到移动端，出现了很多很多知名平台和主播。技术媒体的进步，让人们可以进一步与明星和主播进行沟通和互动。下面我给大家介绍一种基于Nginx 的流媒体服务器，它的特点是安装简单，并且可以基于Nginx进行分布式部署，能够实现高并发、易扩展、占用内存小的特点。

# 1、协议介绍

我们大家都知道WEB网站内容的获取用的是HTTP协议，它是基于TCP层上的应用层协议，那么直播领域也用到一些常用的协议，如RTMP、HTTP-FLV、HLS，还有一些其它的协议。

## RMTP协议
RTMP（Real Time Messaging Protocol）是基于TCP协议的应用层协议，它主要是给Adobe公司为flash播放器和服务器进行音视频传输而开发的协议，默认使用1935端口，它是一种流式协议，流式协议就是将一整帧完成数据切割成大小基本相等的块（chunk）进行传输，支持推流和拉流操作，目前adobe已经停止了该协议进行升级，所以RTMP不支持一些新的音视频编码（例如H265、VP8、VP9等），国内CDN厂商会对RTMP进行扩展，使其支持H265协议

## HTTP-FLV协议

这是一种更简单的协议，它的实现是基于HTTP下载的，如果http头中没有content-length字段，那么http客户端就会一直接受服务端传过来的数据，知道服务器主动将TCP链接断开。所以，试想一下，如果服务器下发给客户端一个flv头，然后返回直播内容，那么客户端将一直接受到该直播内容，知道直播结束，服务端断开连接。

## HLS协议

HLS（HTTP Live Streaming）它也是一种基于HTTP下载的媒体协议，它是由Apple公司开发，应用在apple产品上的流媒体网络传输协议。它的工作原理是把整个流分成一个个小的基于HTTP的文件来下载，每次只下载一些。当媒体流正在播放时，客户端可以选择从许多不同的备用源中以不同的速率下载同样的资源，允许流媒体会话适应不同的数据速率。在开始一个流媒体会话时，客户端会下载一个包含元数据的extended M3U (m3u8) playlist文件，用于寻找可用的媒体流。

## 其它协议

其它的直播协议还有DASH、HTTP-TS、HTTP-FMP4等

## 总结


|协议 |	场景|	延时|
|---|---|---|
|rtmp |	flash、ffplay、vlc等终端播放器 | 2s+（（如果取消缓存也可以做到1s左右延时））|
|http-flv |	flash、ffplay、vlc等终端播放器，当然也有h5播放方案（利用mes方案，如flv.js，以后会介绍具体实现方式）|	2s+（如果取消缓存也可以做到1s左右延时）|
|hls  |	几乎支持所有终端播放器（包括html5）|	5s+|
|http-ts  |	与http-flv非常类似，只不过是利用http协议分发ts媒体流 | 同http-flv|

# 2、Nginx-rtmp-module安装

简单的直播系统的架构图也很简单，基本长这样

![image](https://img.fengjr.com/image/2019/11/20/c795ffb45b720b47e0031d1994761f6b.png)


Nginx-rtmp-module的安装也比较简单，我目前用的是基于Nginx-rtmp-module的做的扩展库，这个扩展库支持以下功能

## 服务器功能

1. 接收RTMP流
2. 支持rtmp、http-flv、http-ts、hls、hls+（内存切片） 直播服务
3. 支持实时录制功能
4. 支持流状态监控

## 搭建环境

### 环境

以Centos、MacOS、Linux 为例

### 安装依赖

```
yum install -y gcc gcc-c++ openssl openssl-devel pcre-devel
```

### 安装资源包

```
git clone https://github.com/im-pingo/nginx-client-module.git
git clone https://github.com/im-pingo/nginx-multiport-module.git
git clone https://github.com/im-pingo/nginx-toolkit-module.git
git clone https://github.com/im-pingo/nginx-rtmp-module.git

// 如果已经安装过nginx的可以，可以找到nginx的安装目录，执行以下操作
git clone https://github.com/nginx/nginx.git
cd nginx
./auto/configure --add-module=../nginx-client-module   \
    --add-module=../nginx-multiport-module             \
    --add-module=../nginx-toolkit-module               \
    --add-module=../nginx-rtmp-module

make && sudo make install
```

# 3、Nginx-rtmp-module配置介绍


## 配置文件介绍
```
user  root;
daemon on;
master_process on;
worker_processes  1;
#worker_rlimit 4g;
#working_directory /usr/local/openresty/nginx/logs;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
error_log  logs/error.log  info;

worker_rlimit_nofile 102400;
worker_rlimit_core   2G;
working_directory    /tmp;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}
stream_zone buckets=1024 streams=4096;

rtmp {
    server {
        listen 1935;
        application live {
            send_all off;
            zero_start off;
            live on;
            hls on;
            hls_path /tmp/hls;
  
            hls2memory on;
            mpegts_cache_time 20s;
            hls2_fragment 1300ms;
            hls2_max_fragment 1800ms;
            hls2_playlist_length 3900ms;
            wait_key on;
            wait_video on;
            cache_time 2s;
            low_latency on;
            fix_timestamp 2000ms;
            # h265 codecid, default 12
            hevc_codecid  12;
        }
    }
}

http {
    server {
        listen     80;
	    location / {
           chunked_transfer_encoding on;
            root html/;
        }
        location /flv {
            flv_live 1935 app=live;
        }
        location /ts {
            ts_live 1935 app=live;
        }
        location /rtmp_stat {
            rtmp_stat all;
            rtmp_stat_stylesheet /stat.xsl;
        }
        location /xstat {
            rtmp_stat all;
        }
        location /sys_stat {
            sys_stat;
        }
        location /hls2 {
            hls2_live 1935 app=live;
        }
        location /hls {
              # Serve HLS fragments
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }
            root /tmp;
            add_header Cache-Control no-cache;
        }
        location /dash {
            # Serve DASH fragments
            root /tmp;
            add_header Cache-Control no-cache;
        }
    }
}

```

## 开启流量监控页面

```
cd nginx-rtmp-module
cp stat.xsl /usr/local/nginx/html/stat.xsl
```

## 启动nginx

```
cd /usr/local/nginx
./sbin/nginx
```

### 推流地址

rtmp://your-server-ip/live/stream-name

### 播放地址

- rtmp播放地址： rtmp://your-server-ip/live/stream-name
- http-flv播放地址：http://your-server-ip/flv/stream-name
- http-ts播放地址：http://your-server-ip/ts/stream-name
- hls播放地址：http://your-server-ip/hls/stream-name.m3u8
- “hls+”播放地址：http://your-server-ip/hls2/stream-name.m3u8


### 后台监控

在浏览器中打开：http://your-server-ip/rtmp_stat
可以通过后台查到当前实时情况
![image](https://img.fengjr.com/image/2019/11/20/fe2615b6ad55d47242265900865486e6.png)


# 4、推流工具介绍

## FFmpeg

FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序

### 基本命令

```
ffmpeg -re -i your-input-file -vcodec libx264 -acodec aac -f flv rtmp://your-server-ip/live/stream-name
```

## OBS

OBS是一个用于录制和进行网络直播的自由开源软件套件。OBS使用C和C++语音编写，提供实时源和设备捕获、场景组成、编码、录制和广播。数据传输主要通过实时消息协议（RTMP）完成，可以发送到任何支持RTMP的目的地，包括YouTube、Twitch.tv、Instagram和Facebook等流媒体网站。

页面概览
![image](https://img.fengjr.com/image/2019/11/20/08505f893cd3fa25f8198e3637e0012a.png)

配置说明

![image](https://img.fengjr.com/image/2019/11/20/267c91f529621908bbe025a4bebd627b.png)

# 5、web端拉流介绍

因为flash在移动端不支持，所以播放涉及到很多兼容性，而且PC浏览器目前也逐渐在废弃掉flash，各浏览器兼容性差异也很大，原声的video标签样式在各个平台也不统一，所以很多开源优秀的播放器插件被广泛的应用，诸如video.js、flv.js等等。

移动端目前建议使用hls进行，flv、flash普遍不支持，以react为例，移动端合PC端都支持

代码如下

## HLS 

```JSX
import React, { useEffect, useRef } from 'react';
import videojs from 'video.js';
import 'video.js/dist/video-js.css'

export default function Live () {
  const videoOption = {
    autoplay: true,
    controls: true,
    sources: [{
      src: 'http://10.255.72.234/hls/0.m3u8',
      type: 'application/x-mpegURL'
    }]
  }
  return (
    <div>
      <VideoPlaer  {...videoOption} />
    </div>
  )
}

function VideoPlaer (props) {
  const videoRef = useRef(null);
  const isSupportHls = videojs.Hls.supportsNativeHls 
  useEffect(() => {
    if (!isSupportHls) {
      const player = videojs(videoRef.current, props, function onPlayerReady() {
        console.log('onPlayerReady', this)
      });
      return () => {
        player.dispose()
      }
    }
  })

  return (
    <div>
      {!isSupportHls && <video-js ref={videoRef} className="video-js"></video-js> }
      {isSupportHls && 
        <video className="vjs-tech" width="100%" height="100%"
        controls="controls" autoplay="autoplay"
        x-webkit-airplay="true" x5-video-player-fullscreen="true"
        preload="auto" playsinline="true" webkit-playsinline
        x5-video-player-typ="h5">
        <source type="application/x-mpegURL" src="http://10.255.72.234/hls/.m3u8" />
      </video>}
    </div>
  )
}
```

## RTMP

RTMP只支持PC端播放，代码如下

```
import React, { useEffect, useRef } from 'react';
import videojs from 'video.js';
import 'video.js/dist/video-js.css' //video样式文件
import 'videojs-flash' //必须

export default function Live () {
  const videoOption = {
    autoplay: true,
    controls: true,
    sources: [{
      src: 'rtmp://10.255.72.234/live/0',
      type: 'rtmp/flv'
    }]
  }
  return (
    <div>
      <VideoPlaer  {...videoOption} />
    </div>
  )
}

function VideoPlaer (props) {
  const videoRef = useRef(null);
  useEffect(() => {
    const player = videojs(videoRef.current, props, function onPlayerReady() {
      console.log('onPlayerReady', this)
    })
    return () => {
      player.dispose()
    }
  })

  return (
    <div>
      <video-js ref={videoRef} className="video-js" />
    </div>
  )
}
```

## HTTP-FLV

HTTP-FLV格式的播放需要用到flv.js，这个框架是bilibili的开源框架，是可以通过纯js播放flv直播文件，而不需要通过flash。


```
import React, { useEffect, useRef } from 'react';
import flvjs from  'flv.js'

export default function Live () {
  return (
    <div>
      <VideoPlaer />
    </div>
  )
}

function VideoPlaer () {
  const flvRef = useRef(null);
  useEffect(() => {
    const flvPlayer = flvjs.createPlayer({
      type: 'flv',
      isLive: true,
      hasAudio: true,
      hasVideo: true,
      url: 'http://10.255.72.234/flv/0'
    });
    flvPlayer.attachMediaElement(flvRef.current);
    flvPlayer.load();
    flvPlayer.play();
  }, [])
  return (
    <div>
       <video controls autoplay ref={flvRef} />
    </div>
  )
}

```
