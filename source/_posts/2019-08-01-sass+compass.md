---
title: sass+compass使用
comments: true
date: 2019-08-01 17:00:00
tags:
- sass
- compass
categories:
- WEB前端
author: jun.zhou
---

![](https://img.fengjr.com/image/2019/07/31/14b7d54d9c92f52c211549ef418441de.png)

## 一、什么是sass
<font color=red>Sass</font> 是对<font color=red>CSS</font> 的扩展，让<font color=red> CSS </font>语言更强大、优雅。 它允许你使用变量、嵌套规则、 mixins</font>、导入等众多功能， 并且完全兼容 <font color=red>CSS</font> 语法。 <font color=red>Sass</font> 有助于保持大型样式表结构良好， 同时也让你能够快速开始小型项目， 特别是在搭配 <font color=red>Compass</font> 样式库一同使用时。
## 二、sass语法

<font color=red>Sass</font> 有两种语法。 第一种被称为<font color=red> SCSS (Sassy CSS)</font>，是一个 <font color=red>CSS3</font> 语法的扩充版本，这份参考资料使用的就是此语法。 也就是说，所有符合 <font color=red>CSS3</font> 语法的样式表也都是具有相同语法意义的 <font color=red>SCSS</font> 文件。 另外，<font color=red>SCSS</font> 理解大多数 <font color=red>CSS hacks</font> 以及浏览器专属语法，例如IE 古老的<font color=red> filter</font> 语法。 这种语种语法的样式表文件需要以 <font color=red>.scss</font> 扩展名。

<font color=red>SCSS </font>是<font color=red> Sass 3</font> 引入新的语法，其语法完全兼容<font color=red> CSS3</font>，并且继承了 <font color=red>Sass</font> 的强大功能。也就是说，任何标准的<font color=red> CSS3</font> 样式表都是具有相同语义的有效的<font color=red> SCSS</font> 文件。另外，<font color=red>SCSS </font>还能识别大部分 <font color=red>CSS hacks</font>（一些 <font color=red>CSS </font>小技巧）和特定于浏览器的语法

第二种比较老的语法成为缩排语法（或者就称为 "<font color=red>Sass</font>"）， 提供了一种更简洁的 <font color=red>CSS</font> 书写方式。 它不使用花括号，而是通过缩排的方式来表达选择符的嵌套层级，I 而且也不使用分号，而是用换行符来分隔属性。 很多人认为这种格式比 <font color=red>SCSS</font> 更容易阅读，书写也更快速。 缩排语法具有 <font color=red>Sass</font> 的所有特色功能， 虽然有些语法上稍有差异；使用此种语法的样式表文件需要以 <font color=red>.sass </font>作为扩展名。

任一语法都可以导入另一种语法撰写的文件中。 只要使用 <font color=red>sass-convert</font> 命令行工具，就可以将一种语法转换为另一种语法：
```css
# 将 Sass 转换为 SCSS
$ sass-convert style.sass style.scss

# 将 SCSS 转换为 Sass
$ sass-convert style.scss style.sass
```
## 三、sass安装及使用
<font color=red>SASS</font>是<font color=red>Ruby</font>语言写的，但是两者的语法没有关系。不懂<font color=red>Ruby</font>，照样使用。只是必须先安装<font color=red>Ruby</font>，然后再安装<font color=red>SASS</font>。
```css
gem install sass
```
如果你使用的是 <font color=red>Windows</font>， 就需要先安装 <font color=red>Ruby</font>,因为<font color=red>sass</font>编译是基于<font color=red>Ruby</font>环境的。

如果要在命令行中运行 <font color=red>Sass</font> ,只要输入
```css
sass input.scss output.css
```
SASS提供四个编译风格的选项：
```
nested：嵌套缩进的css代码，它是默认值。

expanded：没有缩进的、扩展的css代码。

compact：简洁格式的css代码。

compressed：压缩后的css代码。
```
```
sass --style compressed test.sass test.css
```
你也可以让SASS监听某个文件或目录，一旦源文件有变动，就自动生成编译后的版本。
```
// watch a file
sass --watch input.scss:output.css

// watch a directory
sass --watch app/sass:public/stylesheets
```
### 1、变量声明
```css
//编译前
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}

//编译后
nav {
  width: 100px;
  color: #F90;
}
```
在这段代码中，<font color=red>$nav-color</font>这个变量定义在了规则块外边，相当于全局变量，所以在这个样式表中都可以像<font color=red>nav</font>规则块那样引用它。<font color=red>$width</font>这个变量定义在了<font color=red>nav</font>的<font color=red>{ }</font>规则块内，相当于局部变量，所以它只能在<font color=red>nav</font>规则块内使用。这意味着是你可以在样式表的其他地方定义和使用<font color=red>$width</font>变量，不会对这里造成影响。其变量名用中线和下划线<font color=red>sass</font>不做强求且相互兼容。
### 2、嵌套CSS 规则
#### 2.1、 父选择器的标识符&

```css
//编译前
article a {
  color: blue;
  &:hover { color: red }
}
#content aside {
  color: red;
  body.ie & { color: green }
}
#content {
    &-item {
        font-size: #ff0;
    }
}
//编译后
article a { color: blue }
article a:hover { color: red }
#content aside {color: red};
body.ie #content aside { color: green }
#content-item { font-size: #ff0; }
```
一般情况下，<font color=red>sass</font>在解开一个嵌套规则时就会把父选择器通过一个空格连接到子选择器的前边，这种在<font color=red>CSS</font>里边被称为后代选择器。但在有些情况下你却不会希望<font color=red>sass</font>使用这种后代选择器的方式生成这种连接，这时候我们会用到'<font color=red>&</font>'符号，它起到连接作用

#### 2.2、群组选择器的嵌套

```css
//编译前
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
nav, aside {
  a {color: blue}
}

//编译后
.container h1, .container h2, .container h3 { margin-bottom: .8em }
nav a, aside a {color: blue}
```
处理这种群组选择器规则嵌套上的强大能力，正是<font color=red>sass</font>在减少重复敲写方面的贡献之一。尤其在当嵌套级别达到两层甚至三层以上时，与普通的<font color=red>css</font>编写方式相比，只写一遍群组选择器大大减少了工作量。

有利必有弊，你需要特别注意群组选择器的规则嵌套生成的<font color=red>css</font>。虽然<font color=red>sass</font>让你的样式表看上去很小，但实际生成的<font color=red>css</font>却可能非常大，这会降低网站的速度。
#### 2.3、子组合选择器和同层组合选择器：<font color=red> >、+</font>和<font color=red>~</font>
```css
//编译前
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}

//编译后
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```
你可以用子组合选择器<font color=red> > </font>选择一个元素的直接子元素。你可以用同层相邻组合选择器<font color=red> + </font>选择<font color=red>nav</font>元素后紧跟的<font color=red>article</font>元素。你还可以用同层全体组合选择器<font color=red> ~ </font>，选择所有跟在<font color=red>article</font>后的同层<font color=red>article</font>元素，不管它们之间隔了多少其他元素。

在<font color=red>sass</font>中，不仅仅<font color=red>css</font>规则可以嵌套，对属性进行嵌套也可以减少很多重复性的工作。

#### 2.4、嵌套属性
在<font color=red>sass</font>中，除了<font color=red>CSS</font>选择器，属性也可以进行嵌套。
```css
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```
嵌套属性的规则是这样的<font color=red>:</font>把属性名从中划线<font color=red> - </font>的地方断开，在根属性后边添加一个冒号<font color=red> : </font>，紧跟一个<font color=red>{ }</font>块，把子属性部分写在这个<font color=red>{ }</font>块中。就像<font color=red>css</font>选择器嵌套一样，<font color=red>sass</font>会把你的子属性一一解开，把根属性和子属性部分通过中划线<font color=red> - </font>连接起来，最后生成的效果与你手动一遍遍写的<font color=red>css</font>样式一样:
```css
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```
对于属性的缩写形式，你甚至可以像下边这样来嵌套，指明例外规则:
```css
//编译前
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}

//编译后
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```
属性和选择器嵌套是非常伟大的特性，因为它们不仅大大减少了你的编写量，而且通过视觉上的缩进使你编写的样式结构更加清晰，更易于阅读和开发。

即便如此，随着你的样式表变得越来越大，这种写法也很难保持结构清晰。有时，处理这种大量样式的唯一方法就是把它们分拆到多个文件中。<font color=red>sass</font>通过对<font color=red>css</font>原有<font color=red>@import</font>规则的改进直接支持了这一特性。
#### 2.5、计算功能
<font color=red>sass</font> 允许使用算式。
```css
//编译前
div {
    padding: 2px * 4;
    margin: (10px / 2);
    height: 112px - 12px;
    font-size: 12px + 4px;
}

//编译后
div{
    padding:8px;
    margin:5px;
    height:100px;
    font-size:16px;
}
```
###  三、导入SASS文件
<font color=red>css</font>有一个特别不常用的特性，即<font color=red>@import</font>规则，它允许在一个<font color=red>css</font>文件中导入其他<font color=red>css</font>文件。然而，后果是只有执行到<font color=red>@import</font>时，浏览器才会去下载其他<font color=red>css</font>文件，这导致页面加载起来特别慢。

<font color=red>sass</font>也有一个<font color=red>@import</font>规则，但不同的是，<font color=red>sass</font>的<font color=red>@import</font>规则在生成<font color=red>css</font>文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个<font color=red>css</font>文件中，而无需发起额外的下载请求。另外，所有在被导入文件中定义的变量和混合器均可在导入文件中使用。

使用<font color=red>sass</font>的<font color=red>@import</font>规则并不需要指明被导入文件的全名。你可以省略<font color=red>.sass</font>或<font color=red>.scss</font>文件后缀。这样，在不修改样式表的前提下，你完全可以随意修改你或别人写的被导入的<font color=red>sass</font>样式文件语法，在<font color=red>sass</font>和<font color=red>scss</font>语法之间随意切换。举例来说，<font color=red>@import"sidebar"</font>;这条命令将把<font color=red>sidebar.scss</font>文件中所有样式添加到当前样式表中。

#### 3.2、默认变量值
一般情况下，你反复声明一个变量，只有最后一处声明有效且它会覆盖前边的值。举例说明:
```css
//编译前
$link-color: blue;
$link-color: red;
a {
    color: $link-color;
}

//编译后
a {
    color: red;
}
```
在上边的例子中，超链接的<font color=red>color</font>会被设置为<font color=red>red</font>。这可能并不是你想要的结果，假如你写了一个可被他人通过<font color=red>@import</font>导入的<font color=red>sass</font>库文件，你可能希望导入者可以定制修改<font color=red>sass</font>库文件中的某些值。使用<font color=red>sass</font>的<font color=red>!default</font>标签可以实现这个目的。它很像<font color=red>css</font>属性中<font color=red>!important</font>标签的对立面，不同的是<font color=red>!default</font>用于变量，含义是:如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。
```css
//编译前
$link-color: red !default;
.a {
    color: $link-color;
}
//编译后
.a {
    color: blue;
}
```
在上例中，如果用户在导入你的<font color=red>sass</font>局部文件之前声明了一个<font color=red>$link-color</font>变量，那么你的局部文件中对<font color=red>color</font>赋<font color=red>red</font>的操作就无效。如果用户没有做这样的声明，则<font color=red>$link-color</font>将默认为<font color=red>red</font>。

#### 3.3、嵌套导入
跟原生的<font color=red>css</font>不同，<font color=red>sass</font>允许<font color=red>@import</font>命令写在<font color=red>css</font>规则内。这种导入方式下，生成对应的<font color=red>css</font>文件时，局部文件会被直接插入到<font color=red>css</font>规则内导入它的地方。举例说明，有一个名为<font color=red>_blue-theme.scss</font>的局部文件，内容如下:
```css
aside {
  background: blue;
  color: white;
}
```
然后把它导入到一个<font color=red>CSS</font>规则内，如下所示:
```css
.blue-theme {
    @import "blue-theme"
}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
```
被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。这些变量和混合器不会全局有效，这样我们就可以通过嵌套导入只对站点中某一特定区域运用某种颜色主题或其他通过变量配置的样式。

有时，可用<font color=red>css</font>原生的<font color=red>@import</font>机制，在浏览器中下载必需的<font color=red>css</font>文件。<font color=red>sass</font>也提供了几种方法来达成这种需求。
### 四、混合器
如果你的整个网站中有几处小小的样式类似(例如一致的颜色和字体)，那么使用变量来统一处理这种情况是非常不错的选择。但是当你的样式变得越来越复杂，你需要大段大段的重用样式的代码，独立的变量就没办法应付这种情况了。你可以通过<font color=red>sass的</font>混合器实现大段样式的重用。

下边的这段<font color=red>sass</font>代码，定义了一个非常简单的混合器，目的是添加跨浏览器的圆角边框。
```css
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```
然后就可以在你的样式表中通过<font color=red>@include</font>来使用这个混合器，放在你希望的任何地方。<font color=red>@include</font>调用会把混合器中的所有样式提取出来放在<font color=red>@include</font>被调用的地方。如果像下边这样写:
```css
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}
//编译后
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```
这一节将介绍使用混合器来避免重复。通过使用参数，你可以使用混合器把你样式中的通用样式抽离出来，然后轻松地在其他地方重用。实际上，混合器太好用了，一不小心你可能会过度使用。大量的重用可能会导致生成的样式表过大，导致加载缓慢。所以，首先我们将讨论混合器的使用场景，避免滥用。
#### 4.1、混合器中的CSS规则
混合器中不仅可以包含属性，也可以包含<font color=red>css</font>规则，包含选择器和选择器中的属性，如下代码:
```css
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}
```
当一个包含<font color=red>css</font>规则的混合器通过<font color=red>@include</font>包含在一个父规则中时，在混合器中的规则最终会生成父规则中的嵌套规则。举个例子，看看下边的<font color=red>sass</font>代码，这个例子中使用了<font color=red>no-bullets</font>这个混合器:
```css
ul.plain {
  color: #444;
  @include no-bullets;
}
```
<font color=red>sass</font>的<font color=red>@include</font>指令会将引入混合器的那行代码替换成混合器里边的内容。最终，上边的例子如下代码:
```css
ul.plain {
  color: #444;
  list-style: none;
}
ul.plain li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```
混合器中的规则甚至可以使用<font color=red> sass</font>的父选择器标识符<font color=red>&</font>。使用起来跟不用混合器时一样，<font color=red>sass</font>解开嵌套规则时，用父规则中的选择器替代<font color=red>&</font>。

如果一个混合器只包含<font color=red>css</font>规则，不包含属性，那么这个混合器就可以在文档的顶部调用，写在所有的<font color=red>css</font>规则之外。如果你只是为自己写一些混合器，这并没有什么大的用途，但是当你使用一个类似于<font color=red>Compass</font>的库时，你会发现，这是提供样式的好方法，原因在于你可以选择是否使用这些样式。
#### 4.2、给混合器传参
混合器并不一定总得生成相同的样式。可以通过在<font color=red>@include</font>混合器时给混合器传参，来定制混合器生成的精确样式。当<font color=red>@include</font>混合器时，参数其实就是可以赋值给<font color=red>css</font>属性值的变量。如果你写过<font color=red>JavaScript</font>，这种方式跟<font color=red>JavaScript</font>的<font color=red>function</font>很像:
```css
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```
当混合器被<font color=red>@include</font>时，你可以把它当作一个<font color=red>css</font>函数来传参。如果你像下边这样写:
```css
a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是:

a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```
当你<font color=red>@include</font>混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，<font color=red>sass</font>允许通过语法<font color=red>$name: value</font>的形式指定每个参数的值。这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可:
```css
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```
尽管给混合器加参数来实现定制很好，但是有时有些参数我们没有定制的需要，这时候也需要赋值一个变量就变成很痛苦的事情了。所以<font color=red>sass</font>允许混合器声明时给参数赋默认值。
#### 4.3、默认参数值
为了在<font color=red>@include</font>混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用<font color=red>$name: default-value</font>的声明形式，默认值可以是任何有效的<font color=red>css</font>属性值，甚至是其他参数的引用，如下代码:
```css
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```
如果像下边这样调用:<font color=red>@include link-colors(red) $hover</font>和<font color=red>$visited</font>也会被自动赋值为<font color=red>red</font>。

混合器只是<font color=red>sass</font>样式重用特性中的一个。我们已经了解到混合器主要用于样式展示层的重用，如果你想重用语义化的类呢？这就涉及<font color=red>sass</font>的另一个重要的重用特性:选择器继承。
### 五、使用选择器继承来精简CSS
使用<font color=red>sass</font>的时候，一个减少重复的主要特性就是选择器继承。选择器继承是说一个选择器可以继承为另一个选择器定义的所有样式。这个通过<font color=red>@extend</font>语法实现，如下代码:
```css
//通过选择器继承继承样式
.error {
  border: 1px red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```
任何<font color=red>css</font>规则都可以继承其他规则，几乎任何<font color=red>css</font>规则也都可以被继承。大多数情况你可能只想对类使用继承，但是有些场合你可能想做得更多。最常用的一种高级用法是继承一个<font color=red>html</font>元素的样式。尽管默认的浏览器样式不会被继承，因为它们不属于样式表中的样式，但是你对<font color=red>html</font>元素添加的所有样式都会被继承。

### 六、sass扩展及更新
#### 6.1、使用占位符选择器 % 
从<font color=red>sass3.2.0</font>后，就可以定义占位选择器<font color=red>%</font>，这个的优势在于，不调用不会有多余的<font color=red>css</font>文件
```css
// sass样式
%h1 {
    font-size:20px;
}
div {
    @extend %h1;
    color:red;
}
// css编译后样式
div {
    font-size:20px;
    color:red;
}
```
#### 6.2、@content
在<font color=red>sass3.2.0</font>中引入， 可以用来解决<font color=red>css3</font>中 <font color=red>@meidia</font> 或者<font color=red> @keyframes</font> 带来的问题。它可以使<font color=red>@mixin</font>接受一整块样式，接收的样式从<font color=red>@content</font>开始
```css
//sass 样式              
@mixin max-screen($res){
  @media only screen and ( max-width: $res )
  {
    @content;
  }
}
 
@include max-screen(480px) {
  body { color: red }
}
 
//css 编译后样式
@media only screen and (max-width: 480px) {
  body { color: red }
}
```
使用<font color=red>@content</font>解决<font color=red>@keyframes</font>关键帧的浏览器前缀问题
```css
// 初始化变量
$browser: null;
// 设置关键帧
@mixin keyframes($name) {
    @-webkit-keyframes #{$name} {
        $browser: '-webkit-'; @content;
    }
    @-moz-keyframes #{$name} {
        $browser: '-moz-'; @content;
    }
    @-o-keyframes #{$name} {
        $browser: '-o-'; @content;
    }
    @keyframes #{$name} {
        $browser: ''; @content;
    }
}
 
// 引入
@include keyframes(scale) {
    100% {
        #{$browser}transform: scale(0.8);
    }
}
 
// css编译后
@-webkit-keyframes scale {
    -webkit-transform: scale(0.8);
}
@-moz-keyframes scale  {
   -moz-transform: scale(0.8);
}
@-o-keyframes scale  {
    -o-transform: scale(0.8);
}
@keyframes scale  {
    transform: scale(0.8);
}
```
#### 6.3、颜色函数
<font color=red>sass</font>提供了一些内置的颜色函数
```css
lighten(#cc3, 10%)　　  // #d6d65c
darken(#cc3, 10%) 　　　// #a3a329
grayscale(#cc3) 　　　　// #808080
complement(#cc3) 　　　// #33c
```

### 七、sass高级用发
#### 7.1、函数 function
<font color=red>sass</font>允许用户编写自己的函数，以<font color=red>@function</font>开始
```css
// css编译前
$fontSize: 10px;
@function pxTorem($px) {
    @return $px / $fontSize * 1rem;
}
div {
    font-size: pxTorem(16px);
}

// css编译后
div {
    font-size: 1.6rem;
}
```

#### 7.2、if条件语句
<font color=red>@if</font>语句可以用来判断
```css
// sass样式
$type: monster;
div {
    @if $type == ocean {
        color: blue;
    } @else if $type == matador {
        color: red;
    } @else if $type == monster {
        color: green;
    } @else {
        color: black;
    }
}
// css编译后样式
div {
    color: green;
}
```
三目判断：语法为 <font color=red>if($condition, $if_true, $if_false)</font>。 三个参数分别表示： 条件，条件为真的值，条件为假的值
```css
if(true, 1px, 2px) => 1px
if(false, 1px, 2px) => 2px
```

#### 7.3、循环语句
<font color=red>for</font> 循环有两种形式，分别为：<font color=red>@for $var from <start> through <end></font> 和 <font color=red>@for $var from <start> to <end></font>。 <font color=red>$var </font>表示变量，<font color=red>start</font>表示开始值，end</font>表示结束值，两种形式的区别在于 <font color=red>through</font> 包括 <font color=red>end</font> 的值，<font color=red>to </font>不包括 <font color=red>end</font> 值。
```css
// sass样式
@for $i from 1 to 4 {
    .item-#{$i} {width: 2em * $i;}
}
// css编译后样式
.item-1 {
    width: 2em;
}
.item-2 {
    width: 4em;
}
.item-3 {
    width: 6em;
}
```
<font color=red>while</font>循环
```css
// sass样式
$i: 2;
@while $i > 0 {
    .item-#{$i} {width: 2em * $i;}
    $i: $i - 1;
}
// css编译后样式
.item-2 {
  width: 4em;
}
.item-1 {
  width: 2em;
}
```
<font color=red>@each</font>循环：语法为<font color=red>@each $var in <list or map></font>。 其中<font color=red>$var</font>表示变量，而<font color=red>list</font>和<font color=red>map</font>表示数据类型，<font color=red>sass3.3.0</font>新加入多字段循环和<font color=red>map</font>数据循环

单字段<font color=red> list </font>数据循环
```css
//sass 样式
$animal-list: puma, sea-slug, egret;
@each $animal in $animal-list {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}
//css 编译后样式
.puma-icon {
  background-image: url('/images/puma.png');
}
.sea-slug-icon {
  background-image: url('/images/sea-slug.png');
}
.egret-icon {
  background-image: url('/images/egret.png');
}
```
多字段<font color=red>list</font>数据循环
```css
//sass 样式
$animal-data: (puma, black, default),(sea-slug, blue, pointer);
@each $animal, $color, $cursor in $animal-data {
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
    border: 2px solid $color;
    cursor: $cursor;
  }
}
//css 编译后样式
.puma-icon {
  background-image: url('/images/puma.png');
  border: 2px solid black;
  cursor: default;
}
.sea-slug-icon {
  background-image: url('/images/sea-slug.png');
  border: 2px solid blue;
  cursor: pointer;
}
```
多字段 <font color=red>map</font> 数据循环
```css
//sass 样式
$headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
@each $header, $size in $headings {
  #{$header} {
    font-size: $size;
  }
}
//css 编译后样式
h1 {
  font-size: 2em;
}
h2 {
  font-size: 1.5em;
}
h3 {
  font-size: 1.2em;
}
```

### 八、compass用法
前面介绍了Sass的用法。

Sass是一种"CSS预处理器"，可以让CSS的开发变得简单和可维护。但是，只有搭配Compass，它才能显出真正的威力。学会了Compass，你的CSS开发效率会上一个台阶。
#### 1、compass是什么？
简单说，Compass是Sass的工具库（toolkit）。

Sass本身只是一个编译器，Compass在它的基础上，封装了一系列有用的模块和模板，补充Sass的功能。它们之间的关系，有点像Javascript和jQuery关系。

#### 2、安装
Compass是用Ruby语言开发的，所以安装它之前，必须安装Ruby。

假定你的机器（Linux或OS X）已经安装好Ruby，那么在命令行模式下键入：
```
sudo gem install compass
```
如果你用的是Windows系统，那么要省略前面的sudo。
#### 3、项目初始化
接下来，要创建一个你的Compass项目，假定它的名字叫做myproject，然后进入该目录，命令行键入：
```
compass create myproject
cd myproject
```
然后你会看到，里面有一个config.rb文件，这是你的项目的配置文件。还有两个子目录sass和stylesheets，前者存放Sass源文件，后者存放编译后的css文件。

#### 4、编译
```
compass compile
```
该命令在项目根目录下运行，会将sass子目录中的scss文件，编译成css文件，保存在stylesheets子目录中。

默认状态下，编译出来的css文件带有大量的注释。但是，生产环境需要压缩后的css文件，这时要使用--output-style参数。
```
compass compile --output-style compressed
```
Compass只编译发生变动的文件，如果你要重新编译未变动的文件，需要使用--force参数。
```
compass compile --force
```
除了使用命令行参数，还可以在配置文件config.rb中指定编译模式。

:expanded模式表示编译后保留原格式，其他值还包括:nested、:compact和:compressed。进入生产阶段后，就要改为:compressed模式。
```
output_style = :expanded
output_style = :compressed
```


也可以通过指定environment的值（:production或者:development），智能判断编译模式。
```
environment = :development
output_style = (environment == :production) ? :compressed : :expanded
```
compass还有自动编译命令
```
compass watch
```
运行该命令后，只要scss文件发生变化，就会被自动编译成css文件。
#### 5、Compass的模块
Compass采用模块结构，不同模块提供不同的功能。目前，它内置五个模块：
```
* reset
* css3
* layout
* typography
* utilities
```
##### 5.1、reset模块
通常，编写自己的样式之前，有必要重置浏览器的默认样式。
```
@import "compass/reset";
```

##### 5.2、css3模块
 目前，该模块提供19种CSS3命令
 
 ```
// 圆角（border-radius）的写法

 @import "compass/css3";
.rounded {
    @include border-radius(5px); //圆角
    //如果只需要左上角为圆角
    //@include border-corner-radius(top, left, 5px);
}

//编译后
.rounded {
    -moz-border-radius: 5px;
    -webkit-border-radius: 5px;
    -o-border-radius: 5px;
    -ms-border-radius: 5px;
    -khtml-border-radius: 5px;
    border-radius: 5px;
}
 ```
 ```
 // 透明（opacity）的写法为
 @import "compass/css3";
#opacity {
　@include opacity(0.5); 
}

//编译后
#opacity {
    filter: progid:DXImageTransform.Microsoft.Alpha(Opacity=0.5);
　　opacity: 0.5;
}
 ```
 ```
 // 行内区块（inline-block）的写法为
@import "compass/css3";
#inline-block {
    @include inline-block;
}

//编译后
#inline-block {
　　display: -moz-inline-stack;
　　display: inline-block;
　　vertical-align: middle;
　　*vertical-align: auto;
　　zoom: 1;
　　*display: inline;
}
 ```
##### 5.3、layout模块
 该模块提供布局功能。
 
比如，指定页面的footer部分总是出现在浏览器最底端：
 ```
 @import "compass/layout";
#footer {
    @include sticky-footer(54px);
}
```
又比如，指定子元素占满父元素的空间：
```
@import "compass/layout";
#stretch-full {
　　@include stretch; 
}
```
##### 5.4、typography模块
该模块提供版式功能。

比如，指定链接颜色的mixin为：
```
link-colors($normal, $hover, $active, $visited, $focus);

//使用时写成
 @import "compass/typography";
a {
    @include link-colors(#00c, #0cc, #c0c, #ccc, #cc0);
}
```
##### 5.5、utilities模块
该模块提供某些不属于其他模块的功能。

比如，清除浮动：
```
import "compass/utilities/";
.clearfix {
　　@include clearfix;
}
```
```
@import "compass/utilities";
table {
    @include table-scaffolding;
}

//编译后

table th {
    text-align: center;
    font-weight: bold;
}
table td,
table th {
    padding: 2px;
}

table td.numeric,
table th.numeric {
    text-align: right;
}
```
#### 6、Helper函数
除了模块，Compass还提供一系列函数。

有些函数非常有用，比如image-width()和image-height()返回图片的宽和高。

再比如，inline-image()可以将图片转为data协议的数据。
```
@import "compass";
.icon { background-image: inline-image("icon.png");}

//编译后
.icon { background-image: url('data:image/png;base64,iBROR...QmCC');}
```
函数与mixin的主要区别是，不需要使用@include命令，可以直接调用。

### 九、sass和less区别
#### 1.编译环境不一样

Sass的安装需要Ruby环境，是在服务端处理的，而Less是需要引入less.js来处理Less代码输出css到浏览器，也可以在开发环节使用Less，然后编译成css文件，直接放到项目中，也有 Less.app、SimpleLess、CodeKit.app这样的工具，也有在线编译地址。

#### 2.变量符不一样，Less是@，而Scss是$，而且变量的作用域也不一样。
```
//Less-作用域
@color: #00c; /* 蓝色 */
#header {
  @color: #c00; /* red */
  border: 1px solid @color; /* 红色边框 */
}

#footer {
  border: 1px solid @color; /* 蓝色边框 */
}

//Less-作用域编译后
#header{border:1px solid #cc0000;}
#footer{border:1px solid #0000cc;}

//scss-作用域
$color: #00c; /* 蓝色 */

#header {

  $color: #c00; /* red */
  border: 1px solid $color; /* 红色边框 */
}

#footer {
  border: 1px solid $color; /* 蓝色边框 */
}

//Sass-作用域编译后

#header{border:1px solid #c00}
#footer{border:1px solid #c00}
```
我们可以看出来，less和scss中的变量会随着作用域的变化而不一样。
#### 3.输出设置，Less没有输出设置，Sass提供4中输出选项：nested, compact, compressed 和 expanded。
输出样式的风格可以有四种选择，默认为nested
```
nested：//嵌套缩进的css代码
expanded：//展开的多行css代码
compact：//简洁格式的css代码
compressed：//压缩后的css代码
```
#### 4.Sass支持条件语句，可以使用if{}else{},for{}循环等等。而Less不支持。
```
/* Sample Sass “if” statement */

@if lightness($color) > 30% {

} @else {

}

/* Sample Sass “for” loop */

@for $i from 1 to 10 {
  .border-#{$i} {
    border: #{$i}px solid blue;
  }
}
```
#### 5. 引用外部CSS文件
css引用的外部文件命名必须以_开头, 如下例所示:其中_test1.scss、_test2.scss、_test3.scss文件分别设置的h1 h2 h3。文件名如果以下划线_开头的话，Sass会认为该文件是一个引用文件，不会将其编译为css文件.
```
// 源代码：
@import "_test1.scss";
@import "_test2.scss";
@import "_test3.scss";

// 编译后：
h1 {
  font-size: 17px;
}
 
h2 {
  font-size: 17px;
}
 
h3 {
  font-size: 17px;
}
```
Less引用外部文件和css中的@import没什么差异。

Less还有其他强大的功能，比如雪碧图等等...
#### 6.Sass和Less的工具库不同
Sass有工具库Compass, 简单说，Sass和Compass的关系有点像Javascript和jQuery的关系,Compass是Sass的工具库。在它的基础上，封装了一系列有用的模块和模板，补充强化了Sass的功能。

Less有UI组件库Bootstrap,Bootstrap是web前端开发中一个比较有名的前端UI组件库，Bootstrap的样式文件部分源码就是采用Less语法编写。

### 十、 总结
不管是Sass，还是Less，都可以视为一种基于CSS之上的高级语言，其目的是使得CSS开发更灵活和更强大，Sass的功能比Less强大,基本可以说是一种真正的编程语言了，Less则相对清晰明了,易于上手,对编译环境要求比较宽松。考虑到编译Sass要安装Ruby,而Ruby官网在国内访问不了,个人在实际开发中更倾向于选择Less。
最后还有个stylu，其接触的不多，它相对于前两种学习难度大点，其规范少如果想了解可以找些相关资料，这里就不多说了~