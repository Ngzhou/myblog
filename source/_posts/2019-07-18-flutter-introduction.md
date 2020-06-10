---
title: Flutter技术研究与应用
comments: true
date: 2019-07-18 14:45:26
tags:
- flutter
- app
- 跨平台
categories:
- iOS/Android开发
author: jun.zhou
---

# Flutter技术研究与应用

![Flutter](https://img.fengjr.com/image/2019/07/18/181fc116474931e5a98b71d7af165eb0.jpeg)

## 什么是Flutter

### 1、Flutter 的概念

Flutter是Google推出并开源的移动应用开发框架 , 主打跨平台、高保真、高性能 . 开发者可以通过Dart语言开发APP , 一套代码同时运行在 ios 和 Android平台 . Flutter提供了丰富的组件、接口 , 开发者可以很快地为Flutter添加native扩展 . 同时Flutter还是用Native引擎渲染试图, 可以为用户提供良好的体验。
 
### 2、跨平台自绘引擎

Flutter与用于构建移动应用程序的其他大多数框架不同 , Flutter既不使用WebView , 也不使用操作系统的原生控件 .Flutter使用自己的高性能渲染引擎来绘制widget . 这样不仅可以保证在Android和ios上UI的一致性 , 而且也可以避免对原生控件依赖而带来的限制及高昂的维护成本.
Flutter使用Skia作为其2D渲染引擎 , Skia是Google的一个2D图形处理函数库 , 包含字型、坐标转换,以及点阵图都有高效能且简洁的表现 , Skia是跨平台的,并提供非常友好的API , 目前Google Chrome浏览器和Android均采用Skia作为其绘制引擎 .

### 3、高性能

Flutter高性能主要由两点来保证 , 首先 , Flutter APP采用Dart语言开发 . Dart在 JIT(即时编译)模式下 , 速度与javaScript基本持平 . 但是 Dart支持 AOT , 当以AOT模式运行时 , javascript便远远追不上 . 其次 , Flutter使用自己的渲染引擎来绘制UI , 布局数据等由Dart语言直接控制,所以在布局过程中不需要像RN那样要在javascript和Native之间通信 , 这在一些滑动和拖动的场景下具有明显优势 , 因为滑动和拖动过程往往都会引起布局变化 , 所以javascript需要和Native之间不停的同步布局信息 , 这和在浏览器中需要javascript频繁操作DOM所带来的问题是一样,都会带来比较可观的性能开销.

### 4、采用Dart语言开发

Dart运行时和编译器支持Flutter两个关键特性的组合:

1. 基于JIT的快速开发周期: Flutter在开发阶段采用JIT模式 , 这样就避免了每次改动都需要进行				编译 , 极大的节省了开发时间;
2. 基于AOT的发布包: Flutter在发布时可以通过AOT生成高效的ARM代码来确保应用的性能, 而			  	 javaScript就不具备该能力.
			
### 5、Flutter底层架构示意图
![image](https://img.fengjr.com/image/2019/07/18/3382931996bd356421f0b85b29946c9a.png)

### 6、Flutter Framework

该库是由纯Dart语言实现的, 它实现了一套基础库 , 自底向上 .

底下两层(Foundation 和 Animation 、Painting 、Gestures)在Google的一些视频中被合并为一个Dart UI层 , 对应的是Flutter中的 dart:ui 包 , 它是Flutter引擎暴漏的底层UI库, 提供动画、手势及绘制能留.

Rendering层 , 是一个抽象的布局层 , 它依赖dart UI 层 , Rendering层会构建一个UI树 , 当UI树有变化时 , 会计算出有变化的部分, 然后更新UI树 , 最终将UI树绘制到屏幕上 , 这个过程就类似于React中的虚拟DOM . Rendering层是Flutter UI 层框架最核心的部分 , 它除了确定每个Ui元素的位置、大小之外还要进行坐标转换、绘制(调用底层dart:ui).

Widgets层是Flutter提供的一套基础组件库 , 在基础组件库之上 , Flutter还提供了Material和Cupertino两种视觉风格的组件库.

### 7、Flutter Engine

该层是有纯C++实现的SDK , 其中包括了Skia引擎、Dart运行时、文字排版引擎等, 在代码调用 dart:ui 库时, 调用最终会走到Engine层, 然后实现真正的绘制逻辑 . 

## 跨平台框架发展历史

目前移动端主流平台主要就Android和iOS两大平台 .  iOS应用程序开发语言:  Object-c 、 Swift  , Android应用程序开发语言:  Java 、Kotlin . 所以开发一个兼顾两个平台的应用程序就得使用两套代码. 为了尽可能复用代码 、节约成本 , 跨平台技术就成了各大公司关注的焦点 . 

目前市面上主流的跨平台方案主要有: h5/js + webkit/webview 、 React Native (RN) 、Weex 、Flutter , 目前就凤金app来说已经使用了前两种跨平台技术. 未来我们计划会在财管app中使用Flutter跨平台技术.

### 1、H5 + 原生混合开发

这类框架主要原理是将APP中部分需要变动的内容通过H5来实现,通过原生网页控件加载webview(Android)或WKWebView (ios)来加载 . 这样,H5部分可以随时改变而不用发版,动态化需求能满足 ; 同时, 由于H5代码只需要一次开发,就能同时在Android 和 ios两个平台运行, 也可以减小开发成本 .

由于H5代码主要运行在WebView中, 而WebView实质上就是一个浏览器内核 , 其javascript运行在一个权限受限的沙箱中, 所以对于大多数系统能力都没有访问权限 .对于H5不能实现的功能,就需要原生去做. 所以一般都会在原生代码中预先实现一些访问系统的API, 然后暴漏给WebView以供javaScript来调用.这样WebView就成了javascript与原生API之间通信的桥梁, 主要负责javascript与原生之间传递调用信息 .

### 2、React Native (RN) 跨平台开发

React Native (RN) 是Facebook于2015年4月开源的跨品台移动应用框架 , 是Facebook 早先开源
的Ui框架React在原生移动应用平台中的衍生产物 , 支持 iOS 和 Android 两大平台 . 它使用javaScript语言、以及类似于HTML的 JSX 和 CSS 来开发移动应用 , 因此熟悉Web前端开发的技术人员只需要很少的学习即可快速上手.

### 3、Weex

Weex是阿里巴巴于2016年发布的跨平台移动端开发框架，思想及原理和React Native类似，最大的不同是语法层面，Weex支持Vue语法和Rax语法，Rax 的 DSL(Domain Specific Language) 语法是基于 React JSX 语法而创造。与 React 不同，在 Rax 中 JSX 是必选的，它不支持通过其它方式创建组件，所以学习 JSX 是使用 Rax 的必要基础。而React Native只支持JSX语法。

## Flutter基础widget及小案例

Flutter中几乎所有的对象都是Widget , 与原生开发中控件不同的是, Flutter中的Widget概念更加广泛 , 它不仅可以表示Ui元素 , 也可以表示一些功能性组件如:用于手势检测的GestureDetector widget、用于应用主题数据传递的Theme等.

### 基础widgets

`StatelessWidget`:  继承自Widget ,  用于不需要维护状态的场景 , 它通常在build方法中通过嵌套其他widget来构建UI, 在构建过程中会递归的构建其嵌套的Widget . 控件自身的状态不会改变,创建了就直接显示,不会有色值、大小或者其他属性的变化 .

`StatefulWidget`: 同样继承自Widget , 并重写了createElement方法 , 不同的是返回的Element对象并不相同 ; 

#### 文本及样式:

- Text: 用于显示简单样式文本,它包含一些控制文本显示样式的一些属性;
- TextStyle: 用于指定文本显示的样式如颜色、字体、粗细、背景等;
- TextSpan: Text中所有文本内容只能按同一个样式,如果需要对Text内容中不同部分按照不同样式显示,就需要使用TextSpan。

#### 按钮: 

- RaisedButton： 漂浮按钮,默认带有阴影和灰色背景.按下后,阴影会变大。
- FlatButton：扁平按钮,默认背景透明不带阴影 , 按下后会有背景色。
- OutlineButton：默认有一个边框,不带阴影且背景透明.按下后 ,边框颜色会变亮、同时出现背景和阴影(较弱)
- IconButton：是一个可点击的Icon,不包括文字、默认没有背景、点击会出现背景.

#### 图片:       

- ImageProvider：定义了图片数据获取接口load(). 从不同数据源获取图片需要实现不同的ImageProvider 
- AssetImage：加载本地工程目录下的图片
- NetworkImage：用于加载、显示网络图片


#### 单选框

Switch

#### 复选框

Checkbox

#### 输入框

TextField

#### 表单

Form

### 布局类Widget

布局类Widget都会包含一个或多个widget,不同布局类Widget对子Widget排版方式不同, 布局类Widget就是一个容器。

#### 流式布局(Wrap)

在介绍Row和Column时,如果子Widget超出屏幕范围, 会包溢出错误 . 这是因为超出屏幕不会折行. 我们把超出屏幕显示范围会自动折行的布局称为流式布局 . Flutter中通过Wrap和Flow来支持流式布局.

#### 层叠布局

层叠布局和Web中的绝对定位、Android中的Frame布局是相似的，子widget可以根据到父容器四个角的位置来确定本身的位置。绝对定位允许子widget堆叠（按照代码中声明的顺序）。Flutter中使用Stack和Positioned来实现绝对定位，Stack允许子widget堆叠，而Positioned可以给子widget定位（根据Stack的四个角）。

### 可滚动Widget

当内容超过显示视口(ViewPort)时，如果没有特殊处理，Flutter则会提示Overflow错误。为此，Flutter提供了多种可滚动widget（Scrollable Widget）用于显示列表和长布局。

#### SingleChildScrollView

类似于Android中的ScrollView, 只能接收一个子Widget。

#### ListView

沿一个方向线性排布所有子Widget 

#### GridView

可以构建一个二维网格列表

#### CustomerScrollView

可以包含多种滚动模型



