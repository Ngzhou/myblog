---
title: websocket介绍及应用
comments: true
date: 2019-11-27 15:16:10
tags:
- websocket
categories:
- WEB前端
author: jun.zhou
---

# 一、websocket介绍

![](https://img.fengjr.com/image/2019/11/27/379278605a69626b3915c6829b1a2cb5.png)

#### WebSocket协议出现的背景
我们在上网过程中经常用到的是HTTP和HTTPS协议，HTTP协议和HTTPS协议通信过程通常是客户端通过浏览器发出一个请求，服务器接受请求后进行处理并返回结果给客户端，客户端处理结果。

这种机制对于信息变化不是特别频繁的应用可以良好支撑，但对于实时要求高、海量并发的应用来说显得捉襟见肘，尤其在移动互联网蓬勃发展的趋势下，高并发与用户实时响应是Web应用经常面临的问题，比如金融证券的实时信息、社交网络的实时消息推送等。

WebSocket出现前我们实现推送技术，用的都是轮询，在特定的时间间隔，浏览器自动发出请求，将服务器的消息主动的拉回来，这种情况下，我们需要不断的向服务器发送请求，并且HTTP 请求 的header非常长，里面包含的数据可能只是一个很小的值，这样会占用很多的带宽和服务器资源，并且服务器不能主动向客户端推送数据。在这种情况下需要一种高效节能的双向通信机制来保证数据的实时传输，于是基于HTML5规范的WebSocket应运而生。


#### 什么是websocket
websocket是HTML5的一种新的通信协议，它实现了浏览器与服务器的双向通讯。在 WebSocket API 中，浏览器和服务器只需要要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。

在客户端使用websocket需要创建WebSocket对象，通过提供的open、send、message、close等方法实现创建、发送、监听信息、关闭连接。现在chrome、firefox等浏览器都已经支持了websocket，而IE却没有。

#### WebSocket与TCP，HTTP的关系
WebSocket与http协议一样都是基于TCP的，所以他们都是可靠的协议，调用的WebSocket的send函数在实现中最终都是通过TCP的系统接口进行传输的。WebSocket和Http协议一样都属于应用层的协议，WebSocket在建立握手连接时，数据是通过http协议传输的，但是在建立连接之后，真正的数据传输阶段是不需要http协议参与的。

#### WebSocket与HTTP轮询
- ajax轮询 
ajax轮询 的原理非常简单，让浏览器隔个几秒就发送一次请求，询问服务器是否有新信息。场景再现：
客户端：啦啦啦，有没有新信息(Request)
服务端：没有（Response）
客户端：啦啦啦，有没有新信息(Request)
服务端：没有。。（Response）
客户端：啦啦啦，有没有新信息(Request)
服务端：你好烦啊，没有啊。。（Response）
客户端：啦啦啦，有没有新消息（Request）
服务端：好啦好啦，有啦给你。（Response）
客户端：啦啦啦，有没有新消息（Request）
服务端：。。。。。没。。。。没。。。没有（Response） ---- loop

![](https://img.fengjr.com/image/2019/11/27/2a0bf85b8ef69023aa962f94b2fde30a.png)

- long poll 
long poll 其实原理跟 ajax轮询 差不多，都是采用轮询的方式，不过采取的是阻塞模型（一直打电话，没收到就不挂电话），也就是说，客户端发起连接后，如果没消息，就一直不返回Response给客户端。直到有消息才返回，返回完之后，客户端再次建立连接，周而复始。
场景再现：
客户端：啦啦啦，有没有新信息，没有的话就等有了才返回给我吧（Request）
服务端：额。。   等待到有消息的时候。。来 给你（Response）
客户端：啦啦啦，有没有新信息，没有的话就等有了才返回给我吧（Request） -loop

![](https://img.fengjr.com/image/2019/11/27/4c1058fe7a183d623c25ab72f946a868.png)

从上面可以看出其实这两种方式，都是在不断地建立HTTP连接，然后等待服务端处理，可以体现HTTP协议的另外一个特点，被动性。何为被动性呢，其实就是，服务端不能主动联系客户端，只能有客户端发起。简单地说就是，服务器是一个很懒的冰箱（不会、不能主动发起连接），但是上司有命令，如果有客户来，不管多么累都要好好接待。

说完这个，我们再来说一说上面的缺陷
从上面很容易看出来，不管怎么样，上面这两种都是非常消耗资源的。
ajax轮询 需要服务器有很快的处理速度和资源。（速度）
long poll 需要有很高的并发，也就是说同时接待客户的能力。（场地大小）

- WebSocket
WebSocket的出现解决了轮询实时交互性和全双工的问题。
在JavaScript中创建了WebSocket后，会有一个HTTP请求发送到服务器以发起连接。取得服务器响应后，建立的连接使用HTTP升级，从HTTP协议交换为WebSocket协议。即，使用标准的HTTP服务器无法实现WebSocket，只有支持这种协议的专门服务器才能正常工作。
WebSocket使用了自定义的协议，未加密的连接不再是http://，而是ws://，默认端口为80，加密的连接也不是https://，而是wss://，默认端口为443。

<img src="https://img.fengjr.com/image/2019/11/27/34927c1cd3258dab7aa4156463715e6b.png" width="50%" height="50%">

WebSocket是类似Socket的TCP长连接通讯模式。一旦WebSocket连接建立后，后续数据都以帧序列的形式传输。在客户端断开WebSocket连接或Server端中断连接前，不需要客户端和服务端重新发起连接请求。在海量并发及客户端与服务器交互负载流量大的情况下，极大的节省了网络带宽资源的消耗，有明显的性能优势，且客户端发送和接受消息是在同一个持久连接上发起，实时性优势明显。

WebSocket与HTTP轮询对比得出的结论：
WebSocket是真正的全双工方式，建立连接后客户端与服务器端是完全平等的，可以互相主动请求。而HTTP长连接基于HTTP，是传统的客户端对服务器发起请求的模式。


# 二、WebSocket API

上面讲述了WebSocket比HTTP轮询好，下面介绍一下WebSocket API。

#### 创建WebSocket实例
要创建WebSocket，先实例一个WebSocket对象并传入要连接的URL：
```
var socket = new WebSocket('http://localhost:8000');
```
执行上面语句后，浏览器会马上尝试创建连接，与XHR类似，WebSocket也有一个表示当前状态的readyState属性。不过，这个属性的值与XHR不相同， socket.readyState值如下：

0：正在建立连接， WebSocket.OPENING
1：已经建立连接， WebSocket.OPEN
2：正在关闭连接， WebSocket.CLOSING
3：已经关闭连接， WebSocket.CLOSE
WebSocket没有readystatechange事件，不过，有其他事件对应着不同的状态，readyState的值永远从0开始。
示例如下：
```
var socket = new WebSocket('ws://localhost:8000');

  //正在建立连接
  console.log("[readyState]-" + socket.readyState); //0

  //连接建立成功回调
  socket.onopen = function() {
    console.log('Connection established.')
    console.log("[readyState]-" + socket.readyState); //1
    //发送消息
    // socket.send('hello world');
  };

  //连接失败回调
  socket.onerror = function() {
    console.log("[readyState]-" + socket.readyState);//3
    console.log('Connection error.')
  };

  //连接关闭回调
  socket.onclose = function(event) {
    var code = event.code;
    var reason = event.reason;
    var wasClean = event.wasClean;
    console.log("[readyState]-" + socket.readyState);//3
    console.log('Connection closed.')
    console.log(code, reason, wasClean)
  };
```
要关闭WebSocket连接，可以在任何时候调用close方法。
```
socket.close();
```
调用了close()之后，readyState的值立即变为2（正在关闭），关闭连接后就会变成3。

#### 发送和接收数据
WebSocket连接建立之后，可以通过连接发送和接收数据。
使用send()方法像服务器发送数据，如下：
```
var socket = new WebSocket('ws://localhost:8000');
socket.send('hello world');
```
当服务器向客户端发来消息时，WebSocket对象会触发message事件。这个message事件与其他传递消息的协议类似，也是把返回的数据保存在event.data属性中。
```
 socket.onmessage = function(event) {
  var data = event.data;
  //处理数据
};
```

#### 其他事件
WebSocket对象还有其他三个事件，在连接生命周期的不同阶段触发。

open：成功建立连接时触发。
error：发生错误时触发，连接断开。
close: 连接关闭时触发。
```
var socket = new WebSocket('ws://localhost:8000');
socket.onopen = function() {
  console.log('Connection established.')
};
socket.onerror = function() {
  console.log('Connection error.')
};
socket.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  console.log('Connection closed.')
};
```
这三个事件中，只有close事件的event对象有额外信息，这个事件的事件对象有三个额外的属性：wasClean、code和reason。
其中wasClean是一个布尔值，表示连接是否已经明确的关闭；
code是服务器返回的数值状态码；
reason是一个字符串，包含服务器发回的信息。

# 三、WebSocket心跳及重连机制
在使用websocket的过程中，有时候会遇到网络断开的情况，但是在网络断开的时候服务器端并没有触发onclose的事件。这样会有：服务器会继续向客户端发送多余的链接，并且这些数据还会丢失。所以就需要一种机制来检测客户端和服务端是否处于正常的链接状态。因此就有了websocket的心跳了。还有心跳，说明还活着，没有心跳说明已经挂掉了。

1. 为什么叫心跳包呢？
它就像心跳一样每隔固定的时间发一次，来告诉服务器，我还活着。

2. 心跳机制是？
心跳机制是每隔一段时间会向服务器发送一个数据包，告诉服务器自己还活着，同时客户端会确认服务器端是否还活着，如果还活着的话，就会回传一个数据包给客户端来确定服务器端也还活着，否则的话，有可能是网络断开连接了。需要重连~

那么需要怎么去实现它呢？如下所有代码：
```
<html>
<head>
  <meta charset="utf-8">
  <title>WebSocket Demo</title>
</head>
<body>
  <script type="text/javascript">
    // var ws = new WebSocket("wss://echo.websocket.org");
    /*
    ws.onerror = function(e) {
      console.log('已关闭');
    };
    ws.onopen = function(e) {
      console.log('握手成功');
      ws.send('123456789');
    }
    ws.onclose = function() {
      console.log('已关闭');
    }
    ws.onmessage = function(e) {
      console.log('收到消息');
      console.log(e);
    }
    */
    
    var lockReconnect = false;//避免重复连接
    var wsUrl = "wss://echo.websocket.org";
    var ws;
    var tt;
    function createWebSocket() {
      try {
        ws = new WebSocket(wsUrl);
        init();
      } catch(e) {
        console.log('catch');
        reconnect(wsUrl);
      }
    }
    function init() {
      ws.onclose = function () {
        console.log('链接关闭');
        reconnect(wsUrl);
      };
      ws.onerror = function() {
        console.log('发生异常了');
        reconnect(wsUrl);
      };
      ws.onopen = function () {
        //心跳检测重置
        heartCheck.start();
      };
      ws.onmessage = function (event) {
        //拿到任何消息都说明当前连接是正常的
        console.log('接收到消息');
        heartCheck.start();
      }
    }
    function reconnect(url) {
      if(lockReconnect) {
        return;
      };
      lockReconnect = true;
      //没连接上会一直重连，设置延迟避免请求过多
      tt && clearTimeout(tt);
      tt = setTimeout(function () {
        createWebSocket(url);
        lockReconnect = false;
      }, 4000);
    }
    //心跳检测
    var heartCheck = {
      timeout: 3000,
      timeoutObj: null,
      serverTimeoutObj: null,
      start: function(){
        console.log('start');
        var self = this;
        this.timeoutObj && clearTimeout(this.timeoutObj);
        this.serverTimeoutObj && clearTimeout(this.serverTimeoutObj);
        this.timeoutObj = setTimeout(function(){
          //这里发送一个心跳，后端收到后，返回一个心跳消息，
          console.log('55555');
          ws.send("123456789");
          self.serverTimeoutObj = setTimeout(function() {
            console.log(111);
            console.log(ws);
            ws.close();
            // createWebSocket();
          }, self.timeout);

        }, this.timeout)
      }
    }
    createWebSocket(wsUrl);
  </script>
</body>
</html>
```

具体的思路如下：
1. 第一步页面初始化，先调用createWebSocket函数，目的是创建一个websocket的方法：new WebSocket(wsUrl);因此封装成函数内如下代码：
```
function createWebSocket() {
  try {
    ws = new WebSocket(wsUrl);
    init();
  } catch(e) {
    console.log('catch');
    reconnect(wsUrl);
  }
}
```
2. 第二步调用init方法，该方法内把一些监听事件封装如下：
```
function init() {
  ws.onclose = function () {
    console.log('链接关闭');
    reconnect(wsUrl);
  };
  ws.onerror = function() {
    console.log('发生异常了');
    reconnect(wsUrl);
  };
  ws.onopen = function () {
    //心跳检测重置
    heartCheck.start();
  };
  ws.onmessage = function (event) {
    //拿到任何消息都说明当前连接是正常的
    console.log('接收到消息');
    heartCheck.start();
  }
}
```
3. 如上第二步，当网络断开的时候，会先调用onerror，onclose事件可以监听到，会调用reconnect方法进行重连操作。正常的情况下，是先调用
onopen方法的，当接收到数据时，会被onmessage事件监听到。

4. 重连操作 reconnect代码如下：
```
var lockReconnect = false;//避免重复连接
function reconnect(url) {
  if(lockReconnect) {
    return;
  };
  lockReconnect = true;
  //没连接上会一直重连，设置延迟避免请求过多
  tt && clearTimeout(tt);
  tt = setTimeout(function () {
    createWebSocket(url);
    lockReconnect = false;
  }, 4000);
}
```
如上代码，如果网络断开的话，会执行reconnect方法，使用了一个定时器，4秒后会重新创建一个新的websocket链接，重新调用createWebSocket函数，
重新会执行及发送数据给服务器端。

5. 最后一步就是实现心跳检测的代码：如下：
```
//心跳检测
var heartCheck = {
  timeout: 3000,
  timeoutObj: null,
  serverTimeoutObj: null,
  start: function(){
    console.log('start');
    var self = this;
    this.timeoutObj && clearTimeout(this.timeoutObj);
    this.serverTimeoutObj && clearTimeout(this.serverTimeoutObj);
    this.timeoutObj = setTimeout(function(){
      //这里发送一个心跳，后端收到后，返回一个心跳消息，
      //onmessage拿到返回的心跳就说明连接正常
      console.log('55555');
      ws.send("123456789");
      self.serverTimeoutObj = setTimeout(function() {
        console.log(111);
        console.log(ws);
        ws.close();
        // createWebSocket();
      }, self.timeout);

    }, this.timeout)
  }
}
```
实现心跳检测的思路是：每隔一段固定的时间，向服务器端发送一个ping数据，如果在正常的情况下，服务器会返回一个pong给客户端，如果客户端通过onmessage事件能监听到的话，说明请求正常，这里我们使用了一个定时器，每隔3秒的情况下，如果是网络断开的情况下，在指定的时间内服务器端并没有返回心跳响应消息，因此服务器端断开了，因此这个时候我们使用ws.close关闭连接，在一段时间后(在不同的浏览器下，时间是不一样的，firefox响应更快)，可以通过 onclose事件监听到。因此在onclose事件内，我们可以调用 reconnect事件进行重连操作。



# 四、WebSocket 实例
前面已经学习了WebSocket API，包括事件、方法和属性。WebSocket是基于事件驱动，支持全双工通信。下面通过二个简单例子体验一下。

#### 简单在线聊天
1、通过nodejs在项目里面新建一个server.js，创建服务，指定8181端口
```
var express = require('express');
var http = require('http');
var WebSocket = require('ws');
var app = express();
app.use(express.static(__dirname));
app.get('/', function(req, res){
	res.send('<h1>Welcome Realtime Server</h1>');
});
var server = http.createServer(app);
var wss = new WebSocket.Server({server});
wss.on('connection', function connection(ws) {
    console.log('链接成功！');
    ws.on('message', function incoming(data) {
        /**
         * 把消息发送到所有的客户端
         * wss.clients获取所有链接的客户端
         */
        wss.clients.forEach(function each(client) {
            client.send(data);
        });
    });
});

server.listen(8181, function listening() {
    console.log('服务器启动成功！');
});
```
在浏览器里输入http://localhost:8181/测试服务是否启动成功。

2、创建客户端。为了能在多台设备上测试，可以本地在启一个服务来跑客户端代码。
通node启一个8282端口的本机服务 
app.js
```
var express = require('express');
var http = require('http');

var app = express();
app.use(express.static(__dirname));
var server = http.createServer(app);

app.get('/', function(req, res){
	res.send('<h1>Welcome client</h1>');
});

server.listen(8282, function listening() {
    console.log('ok！');
});
```
3、创建index.html
```
<div>
    <h1>websocket chat</h1>
    <div>
        <input id="name" size="15" type="text" placeholder="姓名" value="">
        <input id="message" size="50" type="text" placeholder="内容" value="">
        <input id="btn_post" type="button" value="post">
    </div>
    <ul id="chat"></ul>
</div>
<script src="http://cdn.bootcss.com/jquery/1.9.1/jquery.js"></script>
```
可以输入姓名和内容，然后有一个提交按钮来发送数据给服务器。

4、创建websocket链接
```
var ws = new WebSocket("ws://localhost:8181");

```
要在多台服务测试需要把localhost改成IP
```
var ws = new WebSocket("ws://10.254.124.8:8181");
```
5、点击提交按钮或回车向服务器发送信息，使用 ws.send 方法
```
$(function(){
  $("#btn_post").click(post);
  $("#message").keydown(function(e){
    if(e.keyCode == 13) post();
  });
});
var post = function(){
  var name = $("#name").val();
  var mes = $("#message").val();
  ws.send(name+" : "+mes);
  $("input#message").val("");
};
```
6、服务器接收到信息后返回给客户端，使用 ws.onmessage 方法
```
ws.onmessage = function(e){
  print(e.data);
};
var print = function(msg){
  $("#chat").prepend($("<li>").text(msg));
};
```
浏览器里输入 http://10.254.124.8:8282/index.html 可以看到预览效果
![](https://img.fengjr.com/image/2019/11/27/a36c14d25b7ef14a1787320e29a09266.png)

#### 模拟股票实例
上面的例子很简单，只是为了演示如何运用nodejs的ws创建一个WebSocket服务器。且可以接受客户端的消息。那么下面这个例子演示股票的实时更新。客服端只需要连接一次，服务器端会不断地发送新数据，客户端收数据后更新UI.页面如下，有五只股票，开始和停止按钮测试连接和关闭。

![](https://img.fengjr.com/image/2019/11/27/83b293b5e353e2d9bdf080e7cf7cbb82.png)

服务端：
1.模拟五只股票的涨跌。
```
var stocks = {
    "AAPL": 95.0,
    "MSFT": 50.0,
    "AMZN": 300.0,
    "GOOG": 550.0,
    "YHOO": 35.0
}
function randomInterval(min, max) {
    return Math.floor(Math.random() * (max - min + 1) + min);
}
var stockUpdater;
var randomStockUpdater = function() {
    for (var symbol in stocks) {
        if(stocks.hasOwnProperty(symbol)) {
            var randomizedChange = randomInterval(-150, 150);
            var floatChange = randomizedChange / 100;
            stocks[symbol] += floatChange;
        }
    }
    var randomMSTime = randomInterval(500, 2500);
    stockUpdater = setTimeout(function() {
        randomStockUpdater();
    }, randomMSTime);
}
randomStockUpdater();
```
2.连接建立之后就开始更新数据
```
wss.on('connection', function (ws) {
  var sendStockUpdates = function (ws) {
      if (ws.readyState == 1) {
          var stocksObj = {};
          for (var i = 0; i < clientStocks.length; i++) {
            var symbol = clientStocks[i];
              stocksObj[symbol] = stocks[symbol];
          }
          if (stocksObj.length !== 0) {
              ws.send(JSON.stringify(stocksObj));//需要将对象转成字符串。WebSocket只支持文本和二进制数据
              console.log("更新", JSON.stringify(stocksObj));
          }
          
      }
  }
  var clientStockUpdater = setInterval(function () {
      sendStockUpdates(ws);
  }, 1000);
  ws.on('message', function (message) {
      var stockRequest = JSON.parse(message);//根据请求过来的数据来更新。
      console.log("收到消息", stockRequest);
      clientStocks = stockRequest['stocks'];
      sendStockUpdates(ws);
  });
```

客户端：
建立连接：
```
 var ws = new WebSocket("ws://localhost:8181");
```
onopen直接只有在连接成功后才会触发，在这个时候将客户端需要请求的股票发送给服务端。
```
var isClose = false;
  var stocks = {
      "AAPL": 0, "MSFT": 0, "AMZN": 0, "GOOG": 0, "YHOO": 0
  };
  function updataUI() {
      ws.onopen = function (e) {
          console.log('Connection to server opened');
          isClose = false;
          ws.send(JSON.stringify(stock_request));
          console.log("sened a mesg");
      }
      //更新UI
      var changeStockEntry = function (symbol, originalValue, newValue) {
          var valElem = $('#' + symbol + ' span');
          valElem.html(newValue.toFixed(2));
          if (newValue < originalValue) {
              valElem.addClass('label-danger');
              valElem.removeClass('label-success');
          } else if (newValue > originalValue) {
              valElem.addClass('label-success');
              valElem.removeClass('label-danger');
          }
      }
      // 处理受到的消息
      ws.onmessage = function (e) {
          var stocksData = JSON.parse(e.data);
          console.log(stocksData);
          for (var symbol in stocksData) {
              if (stocksData.hasOwnProperty(symbol)) {
                  changeStockEntry(symbol, stocks[symbol], stocksData[symbol]);
                  stocks[symbol] = stocksData[symbol];
              }
          }
      };
  }

  updataUI();
```
运行可以看到效果，只需要请求一次，数据就会不断的更新，效果是不是很赞，不用轮询，也不用长连接那么麻烦了。




# 五、socket.io
socket.IO是一个websocket库，包括了客户端的js和服务器端的nodejs。官方地址：[http://socket.io](http://socket.io)

#### 客户端使用socket.io
去github clone socket.io的最新版本，或者直接饮用使用socket.io的CDN服务：
```
<script src="http://cdn.socket.io/stable/socket.io.js"></script>
```
下面可以创建使用socket.io库来创建客户端js代码了：
```
var socket = io.connect('http://localhost');
socket.on('news', function (data) {
	console.log(data);
	socket.emit('my other event', { my: 'data' });
});
```
socket.on是监听，收到服务器端发来的news的内容，则运行function，其中data就是请求回来的数据，socket.emit是发送消息给服务器端的方法。

#### 使用socket.io和nodejs搭建websocket服务器端
socket.io不仅可以搭建客户端的websocket服务，而且支持nodejs服务器端的websocket。

nodejs安装socket.io
使用node插件管理包，运行下面的命令就可以安装成功socket.io
```
npm install socket.io
```
没有npm的或者windows用户可以使用github下载socket.io并且放入到node_modules文件夹中。

#### nodejs建立socket.io服务
通过nodejs的http模块就可以方便的搭建websocket服务器环境，例如下面的代码：
```
// 引入需要的模块：http和socket.io
var http = require('http'), io = require('socket.io');
//创建server
var server = http.createServer(function(req, res){ 
  // Send HTML headers and message
  res.writeHead(200,{ 'Content-Type': 'text/html' }); 
  res.end('<h1>Hello Socket Lover!</h1>');
});

//端口8000
server.listen(8080);
//创建socket
var socket = io.listen(server);
//添加连接监听
socket.on('connection', function(client){   
	//连接成功则执行下面的监听
	client.on('message',function(event){ 
		console.log('Received message from client!',event);
	});
	//断开连接callback
	client.on('disconnect',function(){
		console.log('Server has disconnected');
	});
});
```
保存为socket.js然后在命令行执行：node socket.js 即可启动服务器，现在访问localhost:8000就可以了。

目标：Socket.IO旨在让各种浏览器与移动设备上实现实时app功能，模糊化各种传输机制。
 

缺点：socket.io 在高并发的情况下，稳定性与可靠性其实是不高的。特别是其websocket的实现，websocket本身就存在很多局限，对服务端，客户端的要求都比较高，要使其稳定的运行，还是有蛮多的工作要做的。 另外，socket.io 的作者又开发了另一个类似的实现：[engine.io](https://github.com/socketio/engine.io) 应该是就socket.io的一些不足作了改进。

#### socket.io搭建多人聊天室实例

需求分析
1、兼容不支持WebSocket的低版本浏览器。
2、允许客户端有相同的用户名。
3、进入聊天室后可以看到当前在线的用户和在线人数。
4、用户上线或退出，所有在线的客户端应该实时更新。
5、用户发送消息，所有客户端实时收取。

有了前面的搭建基础，就不多啰嗦了，直接上代码：

index.html
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="format-detection" content="telephone=no"/>
        <meta name="format-detection" content="email=no"/>
<meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=0" name="viewport">
        <title>多人聊天室</title>
        <link rel="stylesheet" type="text/css" href="./style.css" />
        <!--[if lt IE 8]><script src="./json3.min.js"></script><![endif]-->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.3.0/socket.io.dev.js"></script>
    </head>
    <body>
        <div id="loginbox">
            <div style="width:260px;margin:200px auto;">
                请先输入你在聊天室的昵称
                <br/>
                <br/>
                <input type="text" style="width:180px;" placeholder="请输入用户名" id="username" name="username" />
				<input type="button" style="width:50px;" value="提交" onclick="CHAT.usernameSubmit();"/>
            </div>
        </div>
        <div id="chatbox" style="display:none;">
            <div style="background:#3d3d3d;height: 28px; width: 100%;font-size:12px;">
                <div style="line-height: 28px;color:#fff;">
                    <span style="text-align:left;margin-left:10px;">Websocket多人聊天室</span>
                    <span style="float:right; margin-right:10px;"><span id="showusername"></span> | 
					<a href="javascript:;" onclick="CHAT.logout()" style="color:#fff;">退出</a></span>
                </div>
            </div>
            <div id="doc">
                <div id="chat">
                    <div id="message" class="message">
<div id="onlinecount" style="background:#EFEFF4; font-size:12px; margin-top:10px; margin-left:10px; color:#666;">
</div>
                    </div>
                    <div class="input-box">
                        <div class="input">
<input type="text" maxlength="140" placeholder="请输入聊天内容，按Enter提交" id="content" name="content">
                        </div>
                        <div class="action">
                            <button type="button" id="mjr_send" onclick="CHAT.submit();">提交</button>
                        </div>
                        
                    </div>
                </div>
            </div>
        </div>
        <script type="text/javascript" src="./client.js"></script>
    </body>
</html>
```

client.js 客户端代码，里面有详细注释，很清晰。
```
(function () {
	var d = document,
	w = window,
	p = parseInt,
	dd = d.documentElement,
	db = d.body,
	dc = d.compatMode == 'CSS1Compat',
	dx = dc ? dd: db,
	ec = encodeURIComponent;
	
	
	w.CHAT = {
		msgObj:d.getElementById("message"),
		screenheight:w.innerHeight ? w.innerHeight : dx.clientHeight,
		username:null,
		userid:null,
		socket:null,
		//让浏览器滚动条保持在最低部
		scrollToBottom:function(){
			w.scrollTo(0, this.msgObj.clientHeight);
		},
		//退出，本例只是一个简单的刷新
		logout:function(){
			//this.socket.disconnect();
			location.reload();
		},
		//提交聊天消息内容
		submit:function(){
			var content = d.getElementById("content").value;
			if(content != ''){
				var obj = {
					userid: this.userid,
					username: this.username,
					content: content
				};
				this.socket.emit('message', obj);
				d.getElementById("content").value = '';
			}
			return false;
		},
		genUid:function(){
			return new Date().getTime()+""+Math.floor(Math.random()*899+100);
		},
		//更新系统消息，本例中在用户加入、退出的时候调用
		updateSysMsg:function(o, action){
			//当前在线用户列表
			var onlineUsers = o.onlineUsers;
			//当前在线人数
			var onlineCount = o.onlineCount;
			//新加入用户的信息
			var user = o.user;
				
			//更新在线人数
			var userhtml = '';
			var separator = '';
			for(key in onlineUsers) {
		        if(onlineUsers.hasOwnProperty(key)){
					userhtml += separator+onlineUsers[key];
					separator = '、';
				}
		    }
			d.getElementById("onlinecount").innerHTML = '当前共有 '+onlineCount+' 人在线，在线列表：'+userhtml;
			
			//添加系统消息
			var html = '';
			html += '<div class="msg-system">';
			html += user.username;
			html += (action == 'login') ? ' 加入了聊天室' : ' 退出了聊天室';
			html += '</div>';
			var section = d.createElement('section');
			section.className = 'system J-mjrlinkWrap J-cutMsg';
			section.innerHTML = html;
			this.msgObj.appendChild(section);	
			this.scrollToBottom();
		},
		//第一个界面用户提交用户名
		usernameSubmit:function(){
			var username = d.getElementById("username").value;
			if(username != ""){
				d.getElementById("username").value = '';
				d.getElementById("loginbox").style.display = 'none';
				d.getElementById("chatbox").style.display = 'block';
				this.init(username);
			}
			return false;
		},
		init:function(username){
			/*
			客户端根据时间和随机数生成uid,这样使得聊天室用户名称可以重复。
			实际项目中，如果是需要用户登录，那么直接采用用户的uid来做标识就可以
			*/
			this.userid = this.genUid();
			this.username = username;
			
			d.getElementById("showusername").innerHTML = this.username;
			//this.msgObj.style.minHeight = (this.screenheight - db.clientHeight + this.msgObj.clientHeight) + "px";
			this.scrollToBottom();
			
			//连接websocket后端服务器
			this.socket = io.connect('ws://10.254.124.8:8383');
			
			//告诉服务器端有用户登录
			this.socket.emit('login', {userid:this.userid, username:this.username});
			
			//监听新用户登录
			this.socket.on('login', function(o){
				CHAT.updateSysMsg(o, 'login');	
			});
			
			//监听用户退出
			this.socket.on('logout', function(o){
				CHAT.updateSysMsg(o, 'logout');
			});
			
			//监听消息发送
			this.socket.on('message', function(obj){
				var isme = (obj.userid == CHAT.userid) ? true : false;
				var contentDiv = '<div>'+obj.content+'</div>';
				var usernameDiv = '<span>'+obj.username+'</span>';
				
				var section = d.createElement('section');
				if(isme){
					section.className = 'user';
					section.innerHTML = contentDiv + usernameDiv;
				} else {
					section.className = 'service';
					section.innerHTML = usernameDiv + contentDiv;
				}
				CHAT.msgObj.appendChild(section);
				CHAT.scrollToBottom();	
			});

		}
	};
	//通过“回车”提交用户名
	d.getElementById("username").onkeydown = function(e) {
		e = e || event;
		if (e.keyCode === 13) {
			CHAT.usernameSubmit();
		}
	};
	//通过“回车”提交信息
	d.getElementById("content").onkeydown = function(e) {
		e = e || event;
		if (e.keyCode === 13) {
			CHAT.submit();
		}
	};
})();
```

server.js 服务端代码，同样代码里有详细注释。
```
var app = require('express')();
var http = require('http').Server(app);
var io = require('socket.io')(http);

app.get('/', function(req, res){
	res.send('<h1>Welcome Realtime Server</h1>');
});

//在线用户
var onlineUsers = {};
//当前在线人数
var onlineCount = 0;

io.on('connection', function(socket){
	console.log('a user connected');
	
	//监听新用户加入
	socket.on('login', function(obj){
		//将新加入用户的唯一标识当作socket的名称，后面退出的时候会用到
		socket.name = obj.userid;
		
		//检查在线列表，如果不在里面就加入
		if(!onlineUsers.hasOwnProperty(obj.userid)) {
			onlineUsers[obj.userid] = obj.username;
			//在线人数+1
			onlineCount++;
		}
		
		//向所有客户端广播用户加入
		io.emit('login', {onlineUsers:onlineUsers, onlineCount:onlineCount, user:obj});
		console.log(obj.username+'加入了聊天室');
	});
	
	//监听用户退出
	socket.on('disconnect', function(){
		//将退出的用户从在线列表中删除
		if(onlineUsers.hasOwnProperty(socket.name)) {
			//退出用户的信息
			var obj = {userid:socket.name, username:onlineUsers[socket.name]};
			
			//删除
			delete onlineUsers[socket.name];
			//在线人数-1
			onlineCount--;
			
			//向所有客户端广播用户退出
			io.emit('logout', {onlineUsers:onlineUsers, onlineCount:onlineCount, user:obj});
			console.log(obj.username+'退出了聊天室');
		}
	});
	
	//监听用户发布聊天内容
	socket.on('message', function(obj){
		//向所有客户端广播发布的消息
		io.emit('message', obj);
		console.log(obj.username+'说：'+obj.content);
	});
  
});

http.listen(8383, function(){
	console.log('listening on *:8383');
});
```

实现效果：
<img src="https://img.fengjr.com/image/2019/11/28/21a61b1204f4722431232f133b129b00.png" width="50%" />

#### 总结
websocket学起来并不难，api也不多，但可扩张和延伸的东西很多，想实现一个完整的项目，需要先有一个完整的设计思路才行。比如是一个在线WebIM系统，实现类似微信，qq的功能，客户端可以看到好友在线状态，在线列表，添加好友，删除好友，新建群组等，消息的发送除了支持基本的文字外，还能支持表情、图片和文件。有兴趣的同学可以继续一起深入研究。