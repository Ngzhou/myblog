---
title: 使用verdaccio搭建私有npm仓库
comments: true
date: 2019-07-10 14:31:54
tags:
- npm
- web
- node
categories:
- WEB前端
author: jun.zhou
---
# 使用verdaccio搭建私有npm仓库

![verdaccio](https://img.fengjr.com/image/2019/07/10/c9bedb7017ab78b1bd2b76c6333ed947.png)

## 项目背景

熟悉前端开发的朋友肯定对npm都有了解，npm是node的包管理工具，我们开发项目的时候都会通过npm来下载开源的公共库，相当方便。我们组内部也有一些公用的代码块，各项目都需要引用这些代码，所以有两个项目就放在了npm源上，因为代码都是封装的一些公用的函数，不涉及任何公司的业务代码，所有就开源了。近期信息科技部在整理的时候发现这些代码，要求我们迁移到公司的私有源上，不能公开，所以有了接下来我们要做的事情 —— 搭建私有npm仓库。

## 为什么选择verdaccio

前期调研了一段时间，发现可选的方案有以下几种，下面介绍一下它们的主要区别：

- [使用cnpm搭建npm私有仓库](https://www.jianshu.com/p/80b88104ec1f)，阿里的解决方案，缺点是需要搭建MySQL数据库，配置较麻烦，安装配置需要都切换到cnpm上，现有改动略大
- 使用github作为私有仓库，原理是在项目里面引用另一个github仓库，有更新时，只需要要pull一下即可(pc目前有些是这么做的，不对的欢迎补充)，缺点就是两个项目有独立的分支，独立的github仓库，管理麻烦，仓库有更新，不仅需要`npm install`，还需要进入仓库目录`pull`另一个项目。
- [使用verdaccio搭建私有npm仓库](https://juejin.im/entry/5c64db9851882562851b328f)，配置简单，安装非常容易，项目为sinopia的分支，用法和sinopia差不多

最后决定用一个相对简单的verdaccio试试手，发现使用起来相当方便，还有web页面进行预览。接下来我教大家搭建的流程。

## 服务器搭建

### 1、安装node

因为verdaccio 是基于node的，所有我们的服务器需要安装node。[下载node](https://nodejs.org/zh-cn/download/)

因为选取的服务器是Centos7，我详细介绍一下在这个上面安装的教程。

#### 使用EPEL安装node

EPEL（Extra Packages for Enterprise Linux）企业版Linux的额外软件包，是Fedora小组维护的一个软件仓库项目，为RHEL/CentOS提供他们默认不提供的软件包

```shell
 sudo yum install epel-release
```

安装完成之后，就可以使用yum安装node，node和npm一起安装。

```shell
sudo yum install nodejs
```

安装完成之后，验证node是否正确安装 `node -v`，如果输出以下版本，说明正常安装。

```shell
v6.1.7
```

发现安装的node版本过低，对于verdaccio 最新4.0版本的，node版本最低要求为8.0，所以我们需要更新node版本。更新和切换node版本还需要一个我们常用的库，nvm。

#### 安装nvm

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

安装完成后需要在对nvm配置环境变量，配置方法如下：

进入bash_profile 文件


```shell
vi ~/.bash_profile
```
在末尾增加添加以下环境变量

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
添加完之后用以下命令使其生效

```shell
source ~/.bash_profile
```

执行`nvm`命令查看是否安装成功

#### nvm更新node版本

因为node版本较低，接下来要使用nvm更新node版本

```shell
nvm install 8.11.1
nvm use 8.11.1
```

### 2、安装verdaccio

```shell
npm install -g verdaccio --unsafe-perm
```

加上--unsafe-perm选项是为了防止gyp ERR! permission denied权限问题报错，如下：

```shell
gyp ERR! configure error
gyp ERR! stack Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules/verdaccio/node_modules/dtrace-provider/build'
gyp ERR! System Linux 3.10.0-862.14.4.el7.x86_64
gyp ERR! command "/usr/local/bin/node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /usr/local/lib/node_modules/verdaccio/node_modules/dtrace-provider
gyp ERR! node -v v8.12.0
gyp ERR! node-gyp -v v3.8.0
gyp ERR! not ok
```

如果出现需要更新npm，执行 `npm install -g npm`即可

### 3、启动verdaccio

```shell
verdaccio
```

执行结果如下

```shell
Verdaccio doesn't need superuser privileges. Don't run it under root.
 warn --- config file  - /root/.config/verdaccio/config.yaml
 warn --- Plugin successfully loaded: htpasswd
 warn --- Plugin successfully loaded: audit
 warn --- http address - http://localhost:4873/ - verdaccio/3.10.2
```

从控制台我们可以看到verdaccio的配置文件`config.yaml`和默认的访问地址`http://localhost:4873/`，我们需要在配置文件中添加 `listen: 0.0.0.0:4873`才能使4873的端口可以访问，看到web端的管理页面


```bash
# 进入verdaccio 目录
cd /root/.config/verdaccio/

# 查看该目录下的文件，该目录默认有两个文件，config.yaml和storage，config.yaml 是配置文件，storage是代码仓库，上传和下载的库都存放在这个文件夹下
ls
> config.yaml  storage

# 编辑配置文件

vim config.yaml

# 在配置文件末尾添加代码

listen: 0.0.0.0:4873

```

因为国内网络环境的原因，我们可能还需要将npm的源改为淘宝源，配置也在`config.yaml`中修改

```yaml
# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: https://registry.npm.taobao.org/
```

### 4、使用pm2启动verdaccio

为了使程序一直处于打开状态，我们用pm2来启动我们的程序。

安装pm2

```shell
npm install -g pm2 --unsafe-perm
```

使用pm2启动verdaccio

```shell
pm2 start verdaccio
```

### 5、访问搭建好的私有仓库

在浏览器中打开 http://localhost:4873， 如果能正常访问则说明已经搭建成功，如图：

![](https://img.fengjr.com/image/2019/07/10/0bf4d65010fb8b329f91edee91f606e5.png)

## 客户端上传npm到私有仓库

搭建好服务器之后，接下来就是怎么使用客户端上传包到私有的仓库。

首先需要这册一个npm账号，参考文章[如何开发一个npm包并发布到npm中央仓库](http://liaolongdong.com/2019/01/24/publish-public-npm.html)

### 1、登陆

```shell
npm adduser --registry  http://localhost:4873
```

输入npm账户名、密码和邮箱：

```shell
Username: aaa
Password: 
Email: (this IS public) aaa@qq.com
Logged in as aaa on http:/localhost:4873/.
```
如果输出`Logged in as better1025 on http://localhost:4873/.`则代表aaa账号已经成功登陆到 `http://localhost:4873/` 的私有仓库

### 2、发布npm包到私有仓库

```shell
# 生成npm包，npm version命令用于生成npm包的版本号，verison后可跟三个参数 patch 补丁版本；minor：这个是小修小改；major：是大版本
npm version patch
npm publish --registry http://localhost:4873
```
发布成功之后查看 http://localhost:4873 里已经有了该npm包的信息。

## 如何使用npm私有仓库源

将npm包放在了私有仓库之后，接下来我们还需要使用的时候将该仓库拉下来，

### 1、更换npm源

首先，我们需要切换npm源，切换到我们的私有源上

```shell
npm set registry http://localhost:4873
```

### 2、下载npm包

然后我们执行npm install命令下载上传的包

```shell
npm install demo
```
verdaccio会优先找我们私有仓库下载的包，如果没有找到，则会从npm中央仓库下载。

## 总结

通过以上的步骤，我们已经成功的切换到了私有的npm上，我们可以发现使用verdaccio非常的简单，而且对于发布一些公司内部的公用包也很方便，跟npm体验一样，并且作用域只在公司内部，也避免了安全隐患。