---
title: JSDOC入门教程
comments: true
date: 2019-08-30 11:32:58
tags:
- jsdoc
- html
- js
categories:
- WEB前端
author: jun.zhou
---

# JSDOC入门教程

![JSDOC](https://img.fengjr.com/image/2019/08/30/b6abbf9f9aa6709426d7de301a069eca.png)

## jsDoc介绍

> JSDoc是一个根据javascript文件中注释信息，生成JavaScript应用程序或库、模块的API文档 的工具。你可以使用他记录如：命名空间，类，方法，方法参数等。类似JavaDoc和PHPDoc。现在很多编辑器或IDE中还可以通过JSDoc直接或使用插件生成智能提示。从而使开发者很容易了解整个类和其中的属性和方法，并且快速知道如何使用，从而提高开发效率，降低维护成本。

示例：

```JS
/**
 * Book类，代表一本书
 * @param title
 * @param anthor
 * @param pageNum
 * @constructor
 */
function Book (title, anthor, pageNum ) {
	// 实例属JS性
	this.title = title;
	this.anthor = anthor;
	this.pageNum = pageNum || 0;
}
Book.prototype = {
	/**
	 * 获取书本的标题
	 * @returns {*}
	 */
	getTitle : function () {
		return this.title;
	},
	/**
	 * 获取书本的作者
	 * @returns {*}
	 */
	getAnthor: function  () {
		return this.anthor;
	},
	/**
	 * 设置书本的页码
	 * @param pageNum
	 */
	setPageNum: function (pageNum) {
		this.pageNum = pageNum;
	}
};
```

## 入门

### 下载jsdoc

```shell
npm install jsdoc -g
```

### JSDoc 3中的名称路径

如果涉及到一个JavaScript变量，这个变量在文档中的其他地方，你必须提供一个唯一标识符映射到该变量。
名称路径提供了一种这样做的方法，并且消除实例成员，静态成员和内部变量之间的歧义。

jsDoc中名称路径的基本语法示例：


```JS
1、myFunction
2、MyConstructor
3、MyConstructor#instanceMember（实例成员）
4、MyConstructor.staticMember（静态成员）
5、MyConstructor~innerMember（内部成员）
```

示例：

```JS
/**
 * Person类，一个人
 * @param name
 * @constructor
 */
const  Person = function (name) {
	this.name = name;
	/**
	 * 这是一个实例成员
	 * @returns {string}
	 */
	this.say = () => {
		return 'I\'am an instance.';
	};
	/**
	 * 这是一个内部成员
	 * @returns {string}
	 */
	function say () {
		return 'I\' am an inner.';
	};
};
/**
 * 这是一个静态成员
 * @returns {string}
 */
Person.say = function () {
	return 'I\'am an static.';
};
/**
 * 实例化一个人
 * @type {Person}
 */
const person1 = new Person('fanerge');
// Person#say 使用实例方法
person1.say();
// Person.say 使用静态方法
Person.say();
// Person~say 使用内部方法，但这里不能访问到
```

使用名称路径也有一些特殊的情况：

- @module名称由”module:”前缀，
- @external 名称由”external:”前缀
- @event名称由”event:”前缀。

### JSDoc中的命令行参数

使用JSDoc最基本的，像这样使用：

```
/path/to/jsdoc yourSourceCodeFile.js anotherSourceCodeFile.js ...
```

其中…是生成文档文件的路径。

JSDoc支持大量的命令行选项，其中许多选项有长和短两种形式

选项 描述

```
-a , –access 只显示特定access方法属性的标识符： private, protected, public, or undefined, 或者 all（表示所有的访问级别）。默认情况下， 显示除private标识符以外的所有标识符。
-c , –configure JSDoc配置文件的路径。默认为安装JSDoc目录下的conf.json或conf.json.EXAMPLE。
-d , –destination 输出生成文档的文件夹路径。JSDoc内置的Haruki模板，使用console 将数据转储到控制台。默认为./out。
–debug 打印日志信息，可以帮助调试JSDoc本身的问题。
-e , –encoding 当JSDoc阅读源代码时假定使用这个编码，默认为 utf8。
-h, –help 显示JSDoc的命令行选项的信息，然后退出。
–match 只有运行测试，其名称中包含value。
–nocolor 当运行测试时，在控制台输出信息不要使用的颜色。在Windows中，这个选项是默认启用的。
-p, –private 将标记有[@private 标签][tags-private.md]的标识符也生成到文档中。默认情况下，不包括私有标识符。
-P, –package 包含项目名称，版本，和其他细节的package.json文件。默认为在源路径中找到的第一个package.json文件。
–pedantic 将错误视为致命错误，将警告视为错误。默认为false。
-q , –query 一个查询字符串用来解析和存储到全局变量env.opts.query中。示例：foo=bar&baz=true。
-r, –recurse 扫描源文件和导览时递归到子目录。
-R, –readme 用来包含到生成文档的README.md文件。默认为在源路径中找到的第一README.md 文件。
-t , –template 用于生成输出文档的模板的路径。默认为templates/default，JSDoc内置的默认模板。
-T, –test 运行JSDoc的测试套件，并把结果打印到控制台。
-u , –tutorials 导览路径，JSDoc要搜索的目录。如果省略，将不生成导览页。查看导览说明，以了解更多信息。
-v, –version 显示JSDoc的版本号，然后退出。
–verbose 日志的详细信息到控制台JSDoc运行。默认为false。
-X, –explain 以JSON格式转储所有的doclet到控制台，然后退出。
```

示例：

使用配置文件/path/to/my/conf.json，为./src目录的中文件生成文档，并保存输出到./docs目录中：

```
/path/to/jsdoc src -r -c /path/to/my/conf.json -d docs
```

运行所有JSDoc的测试，其名称包含 tag，并记录每个测试信息：

```
/path/to/jsdoc -T –match tag –verbose
```

### 用conf.json配置JSDoc

#### Configuration File(配置文件)

要自定义JSDoc的行为，可以使用JSON格式的配置文件格式化JSDoc，使用-c选项，例如： jsdoc -c /path/to/conf.json

默认的jsDoc配置文件conf.json.EXAMPLE


```
{
	"tags": {
		"allowUnknownTags": true,
		"dictionaries": ["jsdoc","closure"]
	},
	"source": {
		"includePattern": ".+\\.js(doc)?$",
		"excludePattern": "(^|\\/|\\\\)_"
	},
	"plugins": [],
	"templates": {
		"cleverLinks": false,
		"monospaceLinks": false
	}
}
```

这意味着：
- JSDoc允许您使用无法识别的标签（tags.allowUnknownTags）;
- 这两个标准JSDoc标签和closure标签被启用（tags.dictionaries）;
- 只有以.js和.jsdoc结尾的文件将会被处理（source.includePattern）;
- 任何文件以下划线开始或开始下划线的目录都将被忽略（source.excludePattern）;
- 无插件加载（plugins）;
- @link标签呈现在纯文本（templates.cleverLinks，templates.monospaceLinks）

#### Specifying input files(指定输入文件)

**source**选项组，结合给JSDoc命令行的路径，确定哪些文件要用JSDoc生成文档


```JS
"source": {
    "include": [ /* array of paths to files to generate documentation for */ ],
    "exclude": [ /* array of paths to exclude */ ],
    "includePattern": ".+\\.js(doc)?$",
    "excludePattern": "(^|\\/|\\\\)_"
} 
```

- source.include：可选的路径数组，JSDoc应该为它们生成文档。JSDoc将会结合命令行上的路径和这些文件名，以形成文件组，并且扫描。如果路径是一个目录，可以使用-r选项来递归。
- source.exclude：可选的路径数组，JSDoc应该忽略的路径。在JSDoc3.3.0或更高版本，该数组可包括source.include路径中的子目录。
- source.includePattern：一个可选的字符串，解释为一个正则表达式。如果存在，所有文件必须匹配这个正则表达式，以通过JSDoc进行扫描。默认情况下此选项设置为.+.js(doc)?$，这意味着只有以.js或者.jsdoc结尾的文件将被扫描。
- source.excludePattern：一个可选的字符串，解释为一个正则表达式。如果存在的话，任何匹配这个正则表达式的文件将被忽略。默认设置是以下划线开头的文件（或以下划线开头的目录下的所有文件）将被忽略。

#### Incorporating command-line options into the configuration file(合并命令行选项到配置文件)

它有可能把许多JSDoc的命令行选项放到配置文件，而不用在命令行中指定它们。要做到这一点，只要在conf.json的opts部分中使用的相关选项的longnames,值是该选项的值。


```JS
"opts": {
    "template": "templates/default",  // same as -t templates/default
    "encoding": "utf8",               // same as -e utf8
    "destination": "./out/",          // same as -d ./out/
    "recurse": true,                  // same as -r
    "tutorials": "path/to/tutorials", // same as -u path/to/tutorials
} 
```

这样可以通过source.include和opts，把所有的JSDoc的参数放在配置文件中，以便命令行简化为：

`jsdoc -c /path/to/conf.json`


```
注意：在命令行中所提供的选项 优先级高于在conf.json中提供的选项。
```

#### Plugins（插件）

要启用插件，只要添加它们的路径（相对于JSDoc文件夹）到plugins数组中就可以了。

示例：

例如，以下将包括 markdown 插件，它转换 markdown格式的文本为HTML，和“summarize”插件，该自动生成的每个的doclet的摘要


```JS
"plugins": [
    "plugins/markdown",
    "plugins/summarize"
] 
```

#### Tags and tag dictionaries(标签和标签字典)

`tags`选项控制哪些JSDoc标签允许被使用和解析。

```JS
"tags": {
    "allowUnknownTags": true,
    "dictionaries": ["jsdoc","closure"]
} 
```


`tags.allowUnknownTags`属性影响JSDoc如何处理无法识别的标签。如果将此选项设置为false，JSDoc发现它不能识别（例如,@foo），JSDoc将记录一个警告。默认情况下，此选项设置为true。

`tags.dictionaries`属性控制JSDoc识别哪些标签，以及JSDoc如何解析它识别标签。在JSDoc3.3.0或更高版本中，有两个内置的标签词典：

- jsdoc: 核心JSDoc标签
- closure: [Closure Compiler 标签](https://github.com/google/closure-compiler/wiki/Annotating-JavaScript-for-the-Closure-Compiler)

默认情况下，两个词典都是启用的。此外，在默认情况下，`jsdoc`字典首先被解析;作为一个结果，如果`jsdoc`词典处理一个标签不同于closure词典，`jsdoc`版本的标签优先被采用(即，以jsdoc版本的标签为准)。

如果您在Closure Compiler 项目中使用JSDoc，并且你想要避免使用 Closure Compiler无法识别的标签，更改tags.dictionaries设置为[“closure”]。如果你想允许核心JSDoc标签, 但又想要确保Closure Compiler特定的标记使用Closure Compiler对其进行解释，您也可以更改此设置为[“closure”,”jsdoc”]。

#### 配置JSDoc的默认模板


```
JSDoc的默认模板中提供了几个选项，您可以使用自定义外观和内容来生成文档。
要使用这些选项，您必须为JSDoc创建一个配置文件，并在配置文件中设置相应的选项。

Generating pretty-printed source files（生成适合打印的文档）
    默认情况下，JSDoc的默认模板为你的源文件生成适合打印的文档。在文档中，它还链接到那些适合的打印文件。
    要禁用适合打印的文件，设置选项templates.default.outputSourceFiles为false。
    使用该选项也将删除文档中链接到源文件的连接。此选项在JSDoc3.3.0及更高版本上是可用的。
Copying static files to the output directory(复制静态文件到输出目录)
    SDoc的默认模板会自动复制一些静态文件，如CSS样式表，到输出目录。
    在JSDoc3.3.0或更高版本，你可以告诉默认模板复制附加静态文件到输出目录。
    例如，您可能希望复制一个图像的目录到输出目录，所以你可以在你的文档中显示这些图像。
    如果您的静态文件目录中包含./myproject/static/img/screen.png文件，您可以通过HTML标签 <img src="img/screen.png">在您的文档中显示该图片
Showing the current date in the page footer（在页脚显示当前日期）
    默认情况下，JSDoc的默认模板总是在生成文档的页脚显示当前日期。
    在JSDoc3.3.0或更高版本，可以通过设置选项templates.default.includeDate为false来忽略当前日期。
Showing longnames in the navigation column（在导航栏中显示长文件名）
    默认情况下，JSDoc的默认模板在导航列中显示每个标识符缩写的名字。
    例如，标识符my.namespace.MyClass将简单地称为显示MyClass。相反,要显示完整的长名称，设置选项templates.default.useLongnameInNav为true。
    此选项在JSDoc3.4.0及更高版本中可用。
Overriding the default template's layout file（重写默认模板的布局文件）
    默认的模板使用名为 layout.tmpl 的文件 指定每个生成文档的页面中的页眉和页脚。
    特别是，每个生产的文档页面会加载该文件定义了CSS和JavaScript文件。在JSDoc3.3.0或更高版本，可以指定使用自己的layout.tmpl文件，它允许你加载自己的自定义CSS和JavaScript文件，去除或替代，标准的文件。
    要使用此功能，设置选项templates.default.layoutFile的路径到你的自定义布局文件。
    路径是相对于config.json文件，当前的工作目录，和JSDoc目录的相对路径，按照这个顺序。
```

#### 块标签和内联标签

##### 概述

JSDoc支持两种不同类型的标签：
- 块标签, 这是在一个JSDoc注释的最高级别。
- 内联标签, 块标签文本中的标签或说明。

块标签通常会提供有关您的代码的详细信息，如一个函数接受的参数。内联标签通常链接到文件的其他部分，类似于HTML中的锚标记（<a>）。

块标签总是以 at 符号（@）开头。除了JSDoc注释中最后一个块标记，每个块标签后面必须跟一个换行符。

内联标签也以 at 符号（@）开。然而，内联标签及其文本必须用花括号（{ and }）括起来。 { 表示行内联标签的开始，而}表示内联标签的结束。如果你的标签文本中包含右花括号（}），则必须用反斜线（ \ ）进行转义。在内联标签后,你并不需要使用一个换行符。

大多数JSDoc标签是块标签。一般来说，当这个网站上说"JSDoc 标签",我们真正的意思是"块标签"

##### 示例

在下面的例子中， @param 是一个块标签，而{@link}是一个内联标签。

```JS
/**
 * Shoe类，一双鞋子
 * @param color
 * @param size
 * @constructor
 */
const Shoe = function (color, size){
	this.color = color;
	this.size = size;
};
/**
 * Set shoe's color. Use {@link Shoe#setSize} to set the shoe size
 * @param color
 */
Shoe.prototype.setColor = function (color) {
	this.color = color;
};
/**
 * 设置鞋子的尺寸
 * @param size
 */
Shoe.prototype.setSize = function (size) {
	this.size = size;
};
/**
 * 设置花边的颜色和样式
 * @param color
 * @param type
 */
Shoe.prototype.setLaceType = function (color, type) {
	// ...
};
var shoe1 = new Shoe('red', 12);
```

#### 关于JSDoc插件

##### Creating and Enabling a Plugin（创建并启用插件）

创建并启用新JSDoc插件,需要两个步骤：

1. 创建一个包含你的插件代码的JavaScript模块.
2. 将该模块添加到JSDoc配置文件的plugins数组中。你可以指定一个绝对或相对路径。如果使用相对路径，JSDoc按照相对于配置文件所在的目录，当前的工作目录和JSDoc安装目录的顺序搜索插件。

例如，如果你的插件是在当前工作目录下，plugins/shout.js文件中定义的，你应该在JSDoc配置文件中的plugins数组中添加字符串plugins/shout。

例如，在JSDoc配置文件中添加一个插件：

```JSON
{
    "plugins": ["plugins/shout"]
}
```

##### Authoring JSDoc 3 Plugins（创建JSDoc3插件）

JSDoc 3的插件系统广泛的控制着解析过程。一个插件可以通过执行以下任何一项操作，影响解析结果：

- 定义事件处理程序
- 定义标签
- 定义一个抽象语法树节点的访问者


```
Event Handlers（事件处理程序）
事件: parseBegin – JSDoc开始加载和解析源文件之前，parseBegin事件被触发。
事件: fileBegin – 当解析器即将解析一个文件fileBegin事件触发。
事件: beforeParse – beforeParse事件在解析开始之前被触发。
事件: jsdocCommentFound – 每当JSDoc注释被发现,jsdocCommentFound事件就会被触发。
事件: symbolFound – 当解析器在代码中遇到一个可能需要被文档化的标识符时，symbolFound 事件就会被触发。
事件: newDoclet – newDoclet事件是最高级别的事件。新的doclet已被创建时，它就会被触发。
事件: fileComplete – 当解析器解析完一个文件时，fileComplete 事件就会被触发。
事件: parseComplete – JSDoc解析所有指定的源文件之后，parseComplete事件就会被触发。
事件: processingComplete – JSDoc更新反映继承和借来的标识符的解析结果后，processingComplete事件被触发。

Tag Definitions （标签定义）
添加标签到标签字典是影响文档生成的一个中级方式。在一个newDoclet事件被触发前，JSDoc注释块被解析以确定可能存在的说明和任何JSDoc标签。
当一个标签被发现，如果它已在标签字典被定义，它就会被赋予一个修改doclet的机会。

```

#### 使用Markdown插件

##### 概述

JSDoc包括markdown插件，自动把Markdown-formatted文本转换成HTML。你可以在任何JSDoc模板中使用这个插件

默认情况下，JSDoc会在以下JSDoc标签中查找markdown格式的文本：

- @author
- @classdesc
- @description (包括在JSDoc注释开始地方的未标记的)
- @param
- @property
- @returns
- @see
- @throws

##### 启用markdown插件

要启用markdown插件，只要将字符串plugins/markdown添加到JSDoc配置文件的plugins数组中即可。

示例，配置文件启用markdown插件：

```JSON
{
    "plugins": ["plugins/markdown"]
} 
```

##### 在额外的JSDoc标签中转换Markdown

默认情况下，markdown插件只处理特定JSDoc标签的markdown文本。您可以通过添加一个 markdown.tags属性到你的JSDoc配置文件中，来处理的其他标签中的markdown文本。

```JSON
{
    "plugins": ["plugins/markdown"],
    "markdown": {
        "tags": ["foo", "bar"]
    }
} 
```


##### 剔除markdown默认处理的标签

为了防止Markdown插件处理任何默认JSDoc标签，添加一个markdown.excludeTags属性到您的JSDoc配置文件。


```JSON

{
    "plugins": ["plugins/markdown"],
    "markdown": {
        "excludeTags": ["author"]
    }
} 
```
##### 用换行符换行文本

默认情况下，Markdown插件不处理换行符换行的文本。这是因为，这是正常的JSDoc注释可以多行。如果您更喜欢处理换行符换行的文本，设置JSDoc配置文件中的markdown.hardwrap属性为true。此属性是在JSDoc3.4.0及更高版本中可用。

##### 添加ID属性到标题标签

默认情况下，Markdown插件不会给每个HTML标题标签添加id 属性。想要标题标签文本自动添加ID属性，设置JSDoc配置文件markdown.idInHeadings属性为true。此属性是在JSDoc3.4.0及更高版本中可用

#### 包含Package（包）文件

包文件包含的信息对你的项目文档是很有用的，比如该项目的名称和版本号。

当JSDoc生成的文档的时候,可以自动使用项目中package.json文件中的信息。

示例，默认的模板在文档中显示项目的名称和版本号。

在源路径中包含一个包文件:


```
jsdoc path/to/js path/to/package/package.json
```

使用 -P/–package 选项：


```
jsdoc –package path/to/package/package-docs.json path/to/js
```

##### 包含 README 文件

有两种方法可以将 README 文件中的信息合并到您的文档：

1. 在你的JavaScript文件的源路径中，包含一个名为README.md的Markdown文件的路径。JSDoc将使用在源路径中发现的第一个 README.md 文件。
2. 使用-R/--readme 包命令行选项运行JSDoc，指定 README 文件的路径。此选项在JSDoc3.3.0及更高版本中可用。README文件可以使用任何名称和扩展名，但它必须是Markdown格式。

-R/--readme 命令行选项优先于你的源路径。如果使用-R/--readme命令行选项，JSDoc会忽略源路径中任何的README.md文件。

如果您正在使用JSDoc的默认模板，README文件的内容将渲染成HTML，生成在文档 index.html 文件中。

###### 例子

在源路径中包含一个 README 文件:

```
jsdoc path/to/js path/to/readme/README.md 
```
使用 -R/--readme 选项：

```
jsdoc --readme path/to/readme/README path/to/js 
```

