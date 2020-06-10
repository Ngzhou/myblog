---
title: pdm系统框架介绍及开发总结
comments: true
date: 2019-06-26 10:05:01
tags:
- react
categories:
- WEB前端
author: jun.zhou
---

# 一、React管理系统框架

#### React-dva:
React后端管理系统应用比较多的是dva框架
通过 reducers, effects 和 subscriptions 组织 model,简化 redux.
官网：[https://dvajs.com](https://dvajs.com)

#### Dva-cli:
Dva脚手架安装简易便捷、使用方便
ui库：antd
地址：[https://github.com/dvajs/dva-cli](https://github.com/dvajs/dva-cli)

#### React-dva工作原理介绍:

![](https://img.fengjr.com/image/2019/06/26/175b3d3b97ce0c843944d9e63cb96379.png)

我简单的分析一下这个图：

首先我们根据 url 访问相关的 Route-Component，在组件中我们通过 dispatch 发送 action 到 model 里面的 effect 或者直接 Reducer

当我们将action发送给Effect，基本上是取服务器上面请求数据的，服务器返回数据之后，effect 会发送相应的 action 给 reducer，由唯一能改变 state 的 reducer 改变 state ，然后通过connect重新渲染组件。

当我们将action发送给reducer，那直接由 reducer 改变 state，然后通过 connect 重新渲染组件。
这样我们就能走完一个流程了。

#### React-admin:
响应式支持pc+移动端,redux-alita极简的redux2react工具
脚手架：creact-react-app
组件：antd、echarts

<font color="red">*之前移动端用的比较多的react脚手架是creact-react-app，而React-admin框架也是基于这开发的；ui组件库与pdm系统需求也比较吻合，所以最后我们选用这套框架。*</font>
[GitHub地址](https://github.com/yezihaohao/react-admin) [预览地址](https://admiring-dijkstra-34cb29.netlify.com/#/login)

# 二、React-admin介绍

#### 依赖模块

项目是用create-react-app创建的，主要还是列出新加的功能依赖包

- react-router(react路由，4.x的版本，如果还使用3.x的版本，请切换分支（ps:分支不再维护）)
- redux(基础用法，但是封装了通用action和reducer，demo中主要用于权限控制（ps：目前可以用16.x的context api代替），可以简单了解下)
- antd(蚂蚁金服开源的react ui组件框架)
- axios(http请求模块，可用于前端任何场景，很强大👍)
- echarts-for-react(可视化图表，别人基于react对echarts的封装，足够用了)
- recharts(另一个基于react封装的图表，个人觉得是没有echarts好用)
- nprogress(顶部加载条，蛮好用👍)
- react-draft-wysiwyg(别人基于react的富文本封装，如果找到其他更好的可以替换)
- react-draggable(拖拽模块，找了个简单版的)
- screenfull(全屏插件)
- photoswipe(图片弹层查看插件，不依赖jQuery，还是蛮好用👍)
- animate.css(css动画库)
- react-loadable(代码拆分，按需加载，预加载，样样都行，具体见其文档，推荐使用)
- redux-alita 极简的redux2react工具

#### 功能模块
备注：项目只引入了ant-design的部分组件，其他的组件antd官网有源码，可以直接复制到项目中使用，后续有时间补上全部组件。

项目使用了antd的自定义主题功能-->黑色，若想替换其他颜色，具体操作请查看antd官网

- **首页**
  完整布局
  换肤(全局功能，暂时只实现了顶部导航的换肤，后续加上其他模块)
- **导航菜单**
  顶部导航(菜单伸缩，全屏功能)
  左边菜单(增加滚动条以及适配路由的active操作)
- **UI模块**
  按钮(antd组件)
  图标(antd组件并增加彩色表情符)
  加载中(antd组件并增加顶部加载条)
  通知提醒框(antd组件)
  标签页(antd组件)
  轮播图(ant动效组件)
  富文本
  拖拽
  画廊
- **动画**
  基础动画(animate.css所有动画)
  动画案例
- **表格**
  基础表格(antd组件)
  高级表格(antd组件)
  异步表格(数据来自掘金酱的接口)
- **表单**
  基础表单(antd组件)
- **图表**
  echarts图表
  recharts图表
- **页面**
  登录页面(包括GitHub第三方登录)
  404页面

#### redux-alita api

React-admin框架自己封装了一套redux模块，主要有四个api

- **AlitaProvider**
  provider component for root node
- **connectAlita**
  connect function (just prepared mapStateToProps and mapDispatchToProps)
- **setAlitaState**
  set redux data fucntion (after connect, you can use it in props)
  funcParams -> { funcName, params, stateName = funcName, data }
  only stateName and data for synchronous datas
  funcName, params for asynchronous fetch apis
- **setConfig**
  register fetch functions before fetch usage

# 三、Pdm开发遇到问题

尝试应该一个新的框架，不可避免的会遇到一些坑。

#### redux-alita 只能在 render(){} 才能获取到

![](https://img.fengjr.com/image/2019/06/26/7534292b0e555a5c94fd49502f1ac09e.png)

框架封装的redux模块在 componentDidMount() 里面取不到值，不知道为啥。
解决办法就是在 render 里取然后 回调传值方式调用。

#### 浏览器开多个窗口登录不同用户数据不统一

解决办法: 路由变化时候加新旧缓存id对比

![](https://img.fengjr.com/image/2019/06/26/b5f1d9368f7f7d87984aa55e1575495d.png)


#### 首页echarts图标点击进入详情页方法
![](https://img.fengjr.com/image/2019/06/26/c42209b8960d56ee694142732a89cb73.png)

#### 角色权限设置级别问题

权限树组件有级别区分，但接口返回没有级别
![](https://img.fengjr.com/image/2019/06/26/a6a6ef09e7bd4325ab17368c1373752a.png)
解决办法：提交时候存全部，接口返回时候多返一个子集。

#### 角色权限设置级别问题
权限树组件根据子集获取组装全部级别方法

![](https://img.fengjr.com/image/2019/06/26/0e36624b2a677235f79565ca40c43500.png)

#### Table组件编辑可获取到全部静态数据
点编辑时候读缓存列表里的数据，不必多调用一次接口

![](https://img.fengjr.com/image/2019/06/26/06f371ab175c974f4a2bb3a22ce48ce2.png)

#### 插入变量获取文本框焦点位置
先获取焦点对象，再获取焦点位置，然后重置值

![](https://img.fengjr.com/image/2019/06/26/c19a786faeade91068b557c040e37d1d.png)

# 四、技术调研

[umijs](https://umijs.org/zh/guide/) 一个比较新的框架，dva脚手架也将向 umi 靠拢。

![](https://img.fengjr.com/image/2019/06/26/3b68313f58e61a2077795dbd3d471cdc.png)

看了一遍官方文档，搭建了一个小项目，个人感觉umi确实好用，更方便快捷的脚手架，完全工程化。







