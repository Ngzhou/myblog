---
title: 搭建你的第一个博客
comments: true
date: 2019-06-11 18:39:03
tags:
- hexo
- js
- html
- css
- node
updated:
author: jun.zhou
categories: 
- WEB前端
---

![](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1970559802,386658033&fm=26&gp=0.jpg)

# 项目背景
web前端组每周会进行技术分享，分享的介质一般都是用ppt，但是ppt是属于纲领性的文件，对于技术的一些细节还是表现的不是很到位，所以前端组想搭建一个博客的平台，来承载前端技术成果，也方便大家能够对积累的成果实时查看和学习。
# 技术选型
因为之前用 jekyll 写过博客，但是感觉 jekyll 太重，环境配置太麻烦，当初也踩了很多坑，所以研究了一下目前主流的博客三类框架：
#### 1) jekyll
> jekyll是一个简单的免费的Blog生成工具，类似WordPress。但是和WordPress又有很大的不同，原因是jekyll只是一个生成静态网页的工具，不需要数据库支持。但是可以配合第三方服务,例如Disqus。最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。

优点：
- jekyll是一个生成工具，不需要数据库，将生成的目录放入网站中即可
- 能部署到github上，不需要vps，因为是静态的，迁移方便
- 支持markdown，并且可以免费的放在github上，github会帮你生成自己独有的域名
- 可以直接在git仓库里面修改代码和编辑博客

缺点：
- 安装比较繁琐，环境搭建比较复杂，前期学习成本较高
- jekyll用的liquid语法并不是太友好，官方文档不太友好，学习起来比较困难
#### 2) wordpress
> WordPress是使用PHP语言开发的博客平台，用户可以在支持PHP和MySQL数据库的服务器上架设属于自己的网站。也可以把 WordPress当作一个内容管理系统（CMS）来使用。

WordPress我并没有用过，因为我想要做的是静态的博客站，所以WordPress我并不中意，以下是百度关于WordPress的优缺点介绍，供大家参考。

优点：
- WordPress 功能强大、扩展性强，这主要得益于其插件众多，易于扩充功能，基本上一个完整网站该有的功能，通过其第三方插件都能实现所有功能
- wordpress搭建的博客对seo搜索引擎友好，收录也快，排名靠前；
- wordpress有强大的社区支持，有上千万的开发者贡献和审查wordpress，所以wordpress是安全并且活跃的。
- 主题很多，网站上一大片都是wordpress的主题，各色各样，应有尽有！
- wordpress备份和网站转移比较方便，原站点使用站内工具导出后，使用WordPress Importer插件就能方便地将内容导入新网站。
- wordpress有强大的社区支持，有上千万的开发者贡献和审查wordpress，所以wordpress是安全并且活跃的。

缺点：
- wordpress源码系统初始内容基本只是一个框架，需要时间自己搭建；
- 插件虽多，但是不能安装太多插件，否则会拖累网站速度和降低用户体验；
- 服务器空间选择自由较小！
- 静态化较差，确切地说是真正静态化做得不好，如果要想对整个网站生成真正静态化页面，还做不好，最多只能生成首页和文章页静态页面，所以只能对整站实现伪静态化!
- wordpress的博客程序定位，简单的数据库层等都注定了他不能适应大数据。

#### 3) hexo
> Hexo是一个基于node.js的静态博客生成系统，它使用markdown语法来写作，同时支持丰富的自定义标签系统。   
用户在本地安装Hexo系统并进行写作，通过一条命令，Hexo可以自动生成静态页面，并发布到多个平台上。
与传统的博客相比，Hexo可以说是一个本地运行远程发布的博客程序。

优点：
- 快速、简介，而且可以搭建在github上，公司的可以搭建在gitlab上
- 操作简单，官方文档清晰，也比较全，容易上手
- 安装方便，node对于前端来说比较熟悉
- 支持markdown，而且生成的是静态博客
缺点：
- 没有后台和插件的支持，需要一些网页知识，学习成本略高
- 每次换电脑需要重新安装和配置编译环境，比较繁琐

最后我选择了 hexo，一来是我中了它属于静态博客，安装也比较简单，学习成本对我来说比较地，还有是因为也看到很多博主安利，所以试了试感觉还可以，主题也比较多，可以满足自己的需求。

# 搭建流程
## 环境搭建和安装
hexo的环境搭建非常简单，可参考官方案例

```
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```
四则命令就可以启动一个hexo项目，其中blog就是你所要建立的博客仓库名，打开 http://localhost:4000 即可看到默认的博客主页

## 项目结构介绍
新建完成后，大家可以在文件夹中看到如下的目录：

```
.
├── _config.yml
├── package.json
├── scaffolds
|   ├──post.md
|   ├──draft.md
|   ├──page.md
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
### _config.yml
这个文件是存放网站的配置信息，网站的标题、副标题、语言、分页、部署等都在这个配置文件中，相关配置文档可查看[配置](https://hexo.io/zh-cn/docs/configuration)
### package.json
这是应用程序的信息，程序中需要的npm包和程序的版本都会在这个中体现

```
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.8.0"
  },
  "dependencies": {
    "hexo": "^3.8.0",
    "hexo-codepen": "^0.1.1",
    "hexo-deployer-git": "^1.0.0",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-json-content": "^4.1.3",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-jade": "^0.3.0",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-server": "^0.3.3"
  }
}

```
其中可以看到，程序的名称、版本，所使用的hexo的版本，和依赖的资源

### scaffolds
[模版 ](https://hexo.io/zh-cn/docs/writing)文件夹，当你新建文章时，hexo会按照scaffold来建立文件，比如之前模版文件夹中有_drafts 和 _post, _drafts 代表的是草稿文件夹， _post代表的是发布的文件夹，当执行
> hexo new post first

程序会创建一个已 _post 模版为基础的名字为 first 的md文件，该文件会在 source/_post 文件夹中

### source
soruce 文件夹中存放用户资源的地方，除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

### themes
[主题](https://hexo.io/zh-cn/docs/themes) 文件夹，hexo会根据主题来渲染静态页面，用户可以自由切换主题，而不需要更改资源文件，做到了资源和样式分离互不影响的功能

## 编写和发布博文

之前介绍过，创建博客可以通过命令创建，发布博文同样也非常简单，只需要三步即可：
### 第一步：创建博文

```
hexo new first
```

创建名为first的md文件，并且编写文件
### 第二步：渲染静态文件

```
hexo g
```
该命令是将你编写的文件生成为html文件，存放到public文件下面，完整的命令为 hexo generate

### 第三步：上传文件


```
hexo d
```
该命令是按照_config.yml 配置中的发布方式，将public里的静态资源上传到你的线上代码仓库（gitlab或者其它）

仅需以上三步即可将你的项目托管到你的代码仓库中，deploy中还可以支持 heroku、netlify、rsync、openShift、ftpsync、
sftp 等众多方式，还可以自己将生成的文件放在任何你想放的地方，[相关配置](https://hexo.io/zh-cn/docs/deployment)

# 总结
总的来说，hexo是一个非常方便，也很适合学习的一个博客工具，其中配置的东西还很多，这期先给大家讲一个大概，之后的篇幅会给大家具体的讲解其中主题更换、hexo中博客的语法等的内容。