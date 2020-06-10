---
title: Web Components 介绍
comments: false
date: 2019-08-09 10:58:28
tags:
- js
updated:
categories:
- WEB前端
author: jun.zhou
---

### 主要内容：
  ##### 1 Web组件简介
  ##### 2 Web组件的组成
  ##### 3 Web组件总结

# 1 Web组件简介
&emsp;&emsp;**Web组件**（英语：Web Components）是W3C正在向HTML和DOM规范添加的一套标准，它允许在Web文档和Web应用程序中创建可重用的小部件或组件。这样做的目的是将基于组件的软件工程引入万维网。组件模型将允许单个HTML元素的**封装和互操作性**。
&emsp;&emsp;作为开发者，我们都知道尽可能多的重用代码是一个好主意。这对于自定义标记结构来说通常不是那么容易 — 想想复杂的HTML（以及相关的样式和脚本），有时您不得不写代码来呈现自定义UI控件，并且如果您不小心的话，多次使用它们会使您的页面变得一团糟。
&emsp;&emsp;Web Components旨在解决这些问题——它由三项主要技术组成，它们可以单独或一起使用来创建封装功能的定制元素，可以在你喜欢的任何地方重用，不必担心代码冲突。
+ **Custom elements（自定义元素）**：一组JavaScript API，允许您定义custom elements及其行为，然后可以在您的用户界面中按照需要使用它们。
+ **Shadow DOM（影子DOM）**：一组JavaScript API，用于将封装的“影子”DOM树附加到元素（与主文档DOM分开呈现）并控制其关联的功能。通过这种方式，您可以保持元素的功能私有，这样它们就可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突。
+ **HTML templates（HTML模板）**：&lt;template&gt; 和 &lt;slot&gt;元素使您可以编写不在呈现页面中显示的标记模板。然后它们可以作为自定义元素结构的基础被多次重用。
+ **HTML Imports**:该标准已被弃用，将用ES6的module代替。

**实现web components的基本方法通常如下所示：**
 1. 创建一个类或函数来指定web组件的功能。
 2. 使用 CustomElementRegistry.define() 方法注册您的自定义元素 ，并向其传递要定义的元素名称、指定元素功能的类、以及可选的其所继承自的元素。
 3. 如果需要的话，使用Element.attachShadow() 方法将一个shadow DOM附加到自定义元素上。使用通常的DOM方法向shadow DOM中添加子元素、事件监听器等等。
 4. 如果需要的话，使用&lt;template&gt; 和&lt;slot&gt; 定义一个HTML模板。再次使用常规DOM方法克隆模板并将其附加到您的shadow DOM中。
 5. 在页面任何您喜欢的位置使用自定义元素，就像使用常规HTML元素那样。

# 2 Web组件的组成
## 2.1 custom elements（自定义标签）
Web Components 标准非常重要的一个特性是，它使开发者能够将HTML页面的功能封装为 custom elements（自定义标签），而往常，开发者不得不写一大堆冗长、深层嵌套的标签来实现同样的页面功能。
**下面我们实现一个简单的组件：**
``` javascript
//定义组件
class HelloWorld extends HTMLElement {
    constructor() {
        super();
        let span=document.createElement('span');
        span.innerText='hello world';
        this.append(span);
    }
}
//注册组件
window.customElements.define('hello-world', HelloWorld);
```

``` html
<!--使用组件-->
<hello-world></hello-world>
或
let hello = document.createElement("hello-world");
```
**结果：**
![](https://img.fengjr.com/image/2019/10/25/2d8140250c99c357a3d0e7629e063cdc.png)

**CustomElementRegistry.define()方法用来注册一个custom element，该方法接受以下参数：**

+ 表示所创建的元素名称的符合 DOMString 标准的字符串。注意，custom element 的名称不能是单个单词，且其中必须要有短横线，用与区别原生的 HTML 元素。
+ 用于定义元素行为的 类 。
+ 可选参数，一个包含 extends 属性的配置对象，是可选参数。它指定了所创建的元素继承自哪个内置元素，可以继承任何内置元素。

```javascript
class HelloP extends HTMLParagraphElement {
    constructor() {
        super();
        this.innerText='hello paragraph'
    }
}
window.customElements.define('hello-p', HelloP,{ extends: 'p' });
```
```html
<p is="hello-p"></p>
或
let hello = document.createElement("p", { is: "hello-p" });
```
**共有两种 custom elements:**
+ Autonomous custom elements 是**独立元素**，它不继承其他内建的HTML元素。你可以直接把它们写成HTML标签的形式，来在页面上使用。
+ Customized built-in elements **继承自基本的HTML元素**。在创建时，你必须指定所需扩展的元素（正如上面例子所示），使用时，需要先写出基本的元素标签，并通过 is 属性指定custom element的名称。

### 使用生命周期回调函数

在custom element的构造函数中，可以指定多个不同的回调函数，它们将会在元素的不同生命时期被调用：

+ connectedCallback：当 custom element首次被插入文档DOM时，被调用。
+ disconnectedCallback：当 custom element从文档DOM中删除时，被调用。
+ adoptedCallback：当 custom element被移动到新的文档时，被调用。
+ attributeChangedCallback: 当 custom element增加、删除、修改自身属性时，被调用。

```javascript

<hello-world attr="hello"></hello-world>

class HelloWorld extends HTMLElement {
    static get observedAttributes() {
        return ['attr'];
    }

    constructor() {
        super();
        let attr = this.getAttribute('attr');
        this.innerText = attr + ' world'
    }

    connectedCallback() {//加载
        console.log('第一次被连接到文档dom')
    }

    disconnectedCallback() {//卸载
        console.log('自定义元素与文档DOM断开连接')
    }

    adoptedCallback() {
        console.log('当自定义元素被移动到新文档时被调用')
    }

    attributeChangedCallback(name, oldValue, newValue) {//修改
        console.log('被修改。', name, oldValue, newValue)
        let attr = this.getAttribute('attr');
        this.innerText = attr + ' world'
    }
}
window.customElements.define('hello-world', HelloWorld);
```
需要注意的是，如果需要在元素属性变化后，触发attributeChangedCallback()回调函数，你必须监听这个属性。这可以通过定义observedAttributes()get函数来实现，observedAttributes()函数体内包含一个return语句，返回一个数组，包含了需要监听的属性名称。

# 2.2 shadow DOM（影子DOM）

Shadow DOM允许将隐藏的DOM树添加到常规的DOM树中——它以shadow root为起始根节点，在这个根节点的下方，可以是任意元素，和普通的DOM元素一样。
![](https://img.fengjr.com/image/2019/10/23/cdf0ebf83bc2fc85c748178b7024ebc7.png)
+ Shadow host： 一个常规 DOM节点，Shadow DOM会被添加到这个节点上。
+ Shadow tree：Shadow DOM内部的DOM树。
+ Shadow boundary：Shadow DOM结束的地方，也是常规 DOM开始的地方。
+ Shadow root: Shadow tree的根节点。

&emsp;&emsp;你可以使用同样的方式来操作Shadow DOM，就和操作常规DOM一样，不同的是，Shadow DOM内部的元素始终不会影响到它外部的元素，这为封装提供了便利。
&emsp;&emsp;Shadow DOM 都不是一个新事物——在过去的很长一段时间里，浏览器用它来封装一个元素的内部结构，我们可以看一下video标签的Shadow DOM。
&emsp;&emsp;浏览器默认隐藏Shadow DOM实现，若要查看，首先打开chrome的开发者工具，然后打开setting，将Show user agent shadow Dom打钩，如下图。
![](https://img.fengjr.com/image/2019/10/23/20bd6d5c36f60dbffdc623f4972da530.png)
&emsp;&emsp;然后，我们就可以看到 video 标签的真面目了：
![](https://img.fengjr.com/image/2019/10/23/d9e68eb2060e6ca3e60cbcb0b6979ea2.png)
&emsp;&emsp;我们可以看到上面这些 shadow DOM 中的节点大多都有 pseudo 属性，根据这个属性，你就可以在外面编写 CSS 样式来控制对应的节点样式了。比如，将上面这个 pseudo="-webkit-media-controls-play-button" 的 input 按钮的背景色改为橙色：
```css
video::-webkit-media-controls-play-button {
            background-color: orange;
}
```
&emsp;&emsp;浏览器中还有很多 Element 都使用了 Shadow DOM 的形式进行封装，比如 &lt;input&gt;、&lt;select&gt;、&lt;audio&gt; 等。

### 基本用法
可以使用**Element.attachShadow()**方法来将一个 shadow root 附加到任何一个元素上。它接受一个配置对象作为参数，该对象有一个mode属性，值可以是open或者closed。
open 表示你可以通过**Element.shadowRoot**属性获取 Shadow DOM，closed的话返回是null
```javascript
//<div id="container" ></div>

let container=document.getElementById('container');
let shadow=container.attachShadow({mode:'open'});
console.log('shadow:',container.shadowRoot)//shadow: #shadow-root (open)
```
如果你想将一个Shadow DOM添加到 custom element上，可以在 custom element的构造函数添加如下实现
```javascript
let shadow = this.attachShadow({mode: 'open'});
```
下面我们看一个实例
```javascript
class HelloWorld extends HTMLElement {
        constructor() {
            super();
            let shadow = this.attachShadow({mode: 'open'});
            //创建任意标签
            let span=document.createElement('span');
            span.innerText='hello world';
            //创建样式
            let style=document.createElement('style');
            style.textContent='span{color:red}';
            //
            shadow.append(style);
            shadow.append(span);
        }
    }
    window.customElements.define('hello-world', HelloWorld);
```
运行结果：
![](https://img.fengjr.com/image/2019/10/25/103b301fcc820410e3a46b0fc242ab30.png)

# 2.3 HTML templates（HTML模板）

### 关于模板 (Templates)
当在网页上重复使用相同的标记结构时，肯定是某种模板而不是一遍又一遍地重复复制。但使用HTML &lt;template&gt; 元素更容易实现(这在现代浏览器中得到了很好的支持)。 此元素及其内容不会在DOM中呈现，但仍可使用JavaScript去引用它。
下面是一个使用template模板的例子：
```html
<!--定义模板-->
<template id="my-paragraph">
  <p>My paragraph</p>
</template>
```
```javascript
//使用模板
let template = document.getElementById('my-paragraph');
let templateContent = template.content;
document.body.appendChild(templateContent);
```

### 在Web Components中使用模板
使用模板，我们创建web组件就更加简单了，实例：
```html
<template id="template">
    <style>
        span{
            color: red;
        }
    </style>
    <span></span>
</template>

<hello-world></hello-world>
```
```javascript
class HelloWorld extends HTMLElement {
        constructor() {
            super();
            let shadow = this.attachShadow({mode: 'open'});
            let template = document.getElementById('template'); //获取模板
            let content = template.content.cloneNode(true);//复制模板
            let span=content.querySelector('span');//获取span
            span.innerText='hello world';
            shadow.append(content);
        }
    }
    window.customElements.define('hello-world', HelloWorld);
```
运行结果：
![](https://img.fengjr.com/image/2019/10/25/fa7a633ca1acc134bb685030175d4432.png)

# 3 Web组件总结
### 好处
1. 原生，无需框架；
2. 易于集成，无需转换；
3. 真正的作用域 CSS；
4. 标准化，只有 HTML、CSS 和 JavaScript。
5. 互操作性
react和vue仅限于他们的生态内，不能在vue中调用react组件，也不能在react中用vue组件。但是webComponents可以超越框架而存在，可以在不同的技术栈中使用。可以在react，vue中使用，也可以在原生js使用。且不用考虑react或者vue升级，改变公共组件。
6. 寿命
因为组件的互操作性，它们将有更长的寿命，基本不需要为了适应新的技术而重写。
7. 移植性
组件可以在任何地方使用，因为很少甚至没有依赖，组件的使用障碍要明显低于依赖库或者框架的组件
原生代码可以为你带来与框架相同的功能，但性能更强、需要的代码更少，更加简洁。

### 坏处
1. 数据绑定
2. 状态管理
3. 兼容性，需要引入polyfill，Shady CSS polyfill，webcomponents-loader
5. 全局注入

### 发展
&emsp;&emsp; 对于大型项目而言，直接用web Components开发是不显示的，必然需要一定的库来简化开发和管理状态，例如Polymer。Web Component编码工具会更像编译器而非框架。目前前端框架仍然由三巨头（React、Vue、Angular）占领，Web Components能否有所突破还要靠时间来检验。

