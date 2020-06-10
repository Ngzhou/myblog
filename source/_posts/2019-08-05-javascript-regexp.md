---
title: js正则表达式
comments: true
date: 2019-08-05 13:17:16
tags:
- js
categories:
- WEB前端
author: jun.zhou
---


![](https://img.fengjr.com/image/2019/08/05/3ca32cba0192d540ad69a8046670740d.jpg)

# 一、js正则简介

正则表达式是对字符串执行模式匹配的强大工具。

#### 创建一个正则

第一种：字面量创建，将正则表达式直接当做对象使用。

```
var cc = /[a-z]/;
```

第二种：实例化RegExp对象方式

```
var cc = new RegExp('[a-z]');
```
#### 前例

十八位身份证校验：

前六位：[1-9]\d{5}
年份（1900-2099）(19|20)\d{2}
月日（例子上的是闰年，如果平年的话把后面的02(0[1-9]|[1-2][0-9])改成02(0[1-9]|1[0-9]|2[0-8])即可）：( (01|03|05|07|08|10|12)(0[1-9]|[1,2][0-9]|3[0-1]) | (04|06|07|11)(0[1-9]|[1,2][0-9]|30) | 02(0[1-9]|[1-2][0-9]) )
三位顺序码：[0-9]{3}
校验码（0-9或者X或者x）:[0-9Xx]

```
var pattern = /^[1-9]\d{5}(19|20)\d{2}((01|03|05|07|08|10|12)(0[1-9]|[1-2][0-9]|3[0-1])|(04|06|07|11)(0[1-9]|[1-2][0-9]|30)|02(0[1-9]|[1-2][0-9]))[0-9]{3}[0-9Xx]$/;
var str = '23333319990223283X';
pattern.test(str); //true
```

# 二、js正则基础知识点

#### 修饰符

i : 执行对大小写不敏感的匹配。

```
new RegExp("regexp","i")
/regexp/i
```
```
var str = "Visit W3School";
var patt1 = /w3school/i;
str.match(patt1); //W3School
```

g : 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。

对 "is" 进行全局搜索：
```
var str="Is this all there is?";
var patt1=/is/g;
str.match(patt1); //is,is
```

对 "is" 进行全局且大小写不敏感的搜索：
```
var str="Is this all there is?";
var patt1=/is/gi;
str.match(patt1); //Is,is,is
```

#### 方括号

方括号用于查找某个范围内的字符：

[abc] : 查找方括号之间的任何字符。
```
var str="Is this all there is?";
var patt1=/[a-h]/g;
str.match(patt1); //h,a,h,e,e
```

[^abc] : 查找任何不在方括号之间的字符。
```
var str="Is this all there is?";
var patt1=/[^a-h]/g;
str.match(patt1); //I,s, ,t,i,s, ,l,l, ,t,r, ,i,s,?
```
[0-9] : 查找任何从 0 至 9 的数字。
[a-z] : 查找任何从小写 a 到小写 z 的字符。
[A-Z] : 查找任何从大写 A 到大写 Z 的字符。
[A-z] : 查找任何从大写 A 到小写 z 的字符。
(red|blue|green) : 查找任何指定的选项。

```
var str="Is this all there is?";
var patt1=/[ah]/g;
var patt2=/(a|h)/g;
str.match(patt1); //h,a,h
str.match(patt2); //h,a,h
```

#### 元字符

元字符（Metacharacter）是拥有特殊含义的字符：

. : 查找单个字符，除了换行和行结束符。
```
var str="That's hot!";
var patt1=/h.t/g;
str.match(patt1); //hat,hot
```

\w : 查找单词字符。
```
var str="Give 100%!"; 
var patt1=/\w/g;
str.match(patt1); //G,i,v,e,1,0,0
```
穿插小例子: 邮箱验证
```
var re=/^\w+@[a-z0-9]+\.[a-z]+$/i; //邮箱不区分大小写
re.test('aa@12.com'); //true
re.test('a.a@12.com'); //false
```

\W : 查找非单词字符。
```
var str="Give 100%!"; 
var patt1=/\W/g;
str.match(patt1); // ,%,!
```

\d : 查找数字。
```
var str="Give 100%!"; 
var patt1=/\d/g;
str.match(patt1); //1,0,0
```

\D : 查找非数字字符。
```
var str="Give 100%!"; 
var patt1=/\D/g;
str.match(patt1); //G,i,v,e, ,%,!
```
\s : 查找空白字符。
\S : 查找非空白字符。

穿插小例子: a标签的匹配
```
var pattern = /<a\b[^>]+\bhref="([^"]*)"[^>]*>([\s\S]*?)<\/a>/
var str = '<a title="title" href="//www.baidu.com" class="hh"></a>';
pattern.test(str); //true
```

解释一下，其中

<a\b 表示匹配a标签的开始，
[^>]+ 匹配非>字符的一到多个任意字符
\bhref="([^"]*)" 匹配href的值，并且将匹配到的值捕获到分组中
[^>]* 匹配href后除了>字符0到多个任意字符
([\s\S]*?) 匹配任意字符并捕获到组中（包含空格和非空格字符，非贪婪匹配，也可以用.?但是.不能包含空行）

\b : 匹配单词边界。
```
var str="moon"; 
var patt1=/\bm/;
var patt2=/oo\b/;
var patt3=/oon\b/;
var patt4=/\w\b\w/;

str.match(patt1); //m
str.match(patt2); //null
str.match(patt3); //oon
str.match(patt4); //null
```
说明：
/\bm/ 匹配 "moon" 中的 'm'；
/oo\b/ 不匹配 "moon" 中的 'oo'，因为 'oo' 后面的 'n' 是一个单词字符；
/oon\b/ 匹配 "moon" 中的 'oon'，因为 'oon' 位于字符串的末端，后面没有单词字符；
/\w\b\w/ 不匹配任何字符，因为单词字符之后绝不会同时紧跟着非单词字符和单词字符。

\B : 匹配非单词边界。
对字符串中不位于单词开头或结尾的 "School" 进行全局搜索：
```
var str="Visit W3School"; 
var patt1=/\BSchool/g;
str.match(patt1); //School
```

\n : 查找换行符。
```
var str="Visit W3School.\n Learn JavaScript."; 
var patt1=/\n/g;
str.search(patt1); //15
```

\uxxxx : 查找以十六进制数 xxxx 规定的 Unicode 字符。
对字符串中的十六进制 0057 (W) 进行全局搜索：
```
var str="Visit W3School. Hello World!"; 
var patt1=/\u0057/g;
str.match(patt1); //W,W
```

#### 量词

n+ : 匹配任何包含至少一个 n 的字符串。
```
var str="Hellooo World! Hello W3School!"; 
var patt1=/o+/g;
str.match(patt1); //ooo,o,o,oo
```

穿插小例子：Email地址
```
/^\w+([-+.]\w+)*@\w+([-.]\w+)*.\w+([-.]\w+)*$/
```

n* : 匹配任何包含零个或多个 n 的字符串。
```
var str="Hellooo World! Hello W3School!"; 
var patt1=/lo*/g;
str.match(patt1); //l,looo,l,l,lo,l
```

n? : 匹配任何包含零个或一个 n 的字符串。
```
var str="1, 100 or 1000?"; 
var patt1=/10?/g;
str.match(patt1); //1,10,10
```

n{X} : 匹配包含 X 个 n 的序列的字符串。
对包含四位数字序列的子串进行全局搜索：
```
var str="100, 1000 or 10000?";
var patt1=/\d{4}/g; 
str.match(patt1); //1000,1000
```

n{X,Y} : 匹配包含 X 至 Y 个 n 的序列的字符串。
```
var str="100, 1000 or 10000?";
var patt1=/\d{3,4}/g; 
str.match(patt1); //100,1000,1000
```

n{X,} : 匹配包含至少 X 个 n 的序列的字符串。
```
var str="100, 1000 or 10000?";
var patt1=/\d{3,}/g; 
str.match(patt1); //100,1000,10000
```

n$ : 匹配任何结尾为 n 的字符串。
```
var str="Is this his";
var patt1=/is$/g;
str.match(patt1); // is
patt1.test(str); //true
```

^n : 匹配任何开头为 n 的字符串。
```
var str="Is this his";
var patt1=/^Is/g;
str.match(patt1); //Is
patt1.test(str); //true
```

?=n : 匹配任何其后紧接指定字符串 n 的字符串。
对其后紧跟 "all" 的 "is" 进行全局搜索：
```
var str="Is this all there is";
var patt1=/is(?= all)/;
str.match(patt1); //is
```

穿插小例子：密码强度正则
```
// 必须是包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间。
/^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$/.test("weeeeeeeW2");
//密码强度正则，最少6位，包括至少1个大写字母，1个小写字母，1个数字，1个特殊字符
/^.*(?=.{6,})(?=.*\d)(?=.*[A-Z])(?=.*[a-z])(?=.*[!@#$%^&*? ]).*$/.test("diaoD123#");
//输出 true
```

?!n : 匹配任何其后没有紧接指定字符串 n 的字符串。
```
var str="Is this all there is";
var patt1=/is(?! all)/gi;
str.match(patt1); //Is,is
/^(?!0)\d{4}$/.test('2019'); //true
/^(?!0)\d{4}$/.test('0019'); //false
```

#### 对象方法

compile : 编译正则表达式。

在字符串中全局搜索 "man"，并用 "person" 替换。然后通过 compile() 方法，改变正则表达式，用 "person" 替换 "man" 或 "woman" :
```
var str="Every man in the world! Every woman on earth!";

patt=/man/g;
str2=str.replace(patt,"person");
document.write(str2+"<br />");

patt=/(wo)?man/g;
patt.compile(patt); //改变正则表达式
str2=str.replace(patt,"person");
document.write(str2);
//输出
Every person in the world! Every woperson on earth!
Every person in the world! Every person on earth!
```

exec : 检索字符串中指定的值。返回找到的值，并确定其位置。
```
var str = "Visit W3School, W3School is a place to study web technology."; 
var patt = new RegExp("W3School","g");
var result;

while ((result = patt.exec(str)) != null)  {
  console.log(result);
  console.log(patt.lastIndex);
 }
//输出
W3School
14
W3School
24
```

test : 检索字符串中指定的值。返回 true 或 false。
```
var pattern = /[hf]ello/g,
    str = 'hello';
pattern.test(str); //true
pattern = /[ef]ello/g;
pattern.test(str); //false
```

#### String 对象的方法

search : 检索与正则表达式相匹配的值。
```
var str="Hello world!"
str.search("world"); //6
str.search("World"); //-1
str.search("woorld"); //-1
```
说明:search() 方法不执行全局匹配，它将忽略标志 g。

match : 找到一个或多个正则表达式的匹配。
```
var str="Hello world!"
str.match("world"); //world
str.match("World"); //null
```

replace : 替换与正则表达式匹配的子串。
```
var str="Visit Microsoft!"
str.replace(/Microsoft/,"W3School"); //Visit W3School!
```
$1、$2、...、$99 : 与 regexp 中的第 1 到第 99 个子表达式相匹配的文本。
```
name = "Doe, John";
name.replace(/(\w+)\s*, \s*(\w+)/, "$2 $1"); //John Doe
```
穿插小例子: 找重复项最多的字符和个数
```
var str = 'sassdfdfffdasdffffffsdsdddsss';
var arr = str.split('');//先把字符串分割为字符串数组
str = arr.sort().join('');
var value = '';
var index = 0;
var re = /(\w)\1+/g;
str.replace(re,function($0,$1){
    if(index<$0.length){
      index = $0.length;
      value = $1;
    }
});
console.log('最多的字符：'+ value +' ,重复的次数：'+index);
//最多的字符：f ,重复的次数：10
```
说明：\1 是捕获组，就是第一个小括号内的值（从左向→）


split : 把字符串分割为字符串数组。
```
var str = "How are you doing today?";
str.split(' '); //["How", "are", "you", "doing", "today?"]
str.split(' ', 3); //["How", "are", "you"]
```

对象属性lastIndex : 一个整数，标示开始下一次匹配的字符位置。
```
var str = "The rain in Spain stays mainly in the plain";
var patt1 = new RegExp("ain", "g");

for(i = 0; i < 4; i++) 
{
  patt1.test(str)
  console.log(patt1.lastIndex);
}
//输出
8
17
28
43
```
例子1：
```
var str="I love antzone ,this is animate";
var reg=/an/g;
console.log(reg.exec(str)); //["an", index: 7, input: "I love antzone ,this is animate", groups: undefined]
console.log(reg.exec(str)); // ["an", index: 24, input: "I love antzone ,this is animate", groups: undefined]
```
例子2：
```
var str="I love antzone ,this is animate";
var reg=/an/g;
reg.lastIndex = 20
console.log(reg.exec(str)); // ["an", index: 24, input: "I love antzone ,this is animate", groups: undefined]
```


#### 等价的正则表达式

常规等价：
/\d/ = /[0-9]/ = /[0123456789]//\D/ = /[^\d]/ = /^[0-9]/
/\s/ 匹配一个空白字符，等价于/[\n\r\f\t\v] 
/\S/ 匹配一个非空白字符，等价于/[^\n\f\r\t\v]
/\w/ 任何单字字符, 等价于/[a-zA-Z0-9_]//\W/ 任何非单字字符,等价于/[^a-zA-Z0-9_]/
// ? 匹配前一项0次或1次,也就是说前一项是可选的. 等价于 {0, 1}
// + 匹配前一项1次或多次,等价于{1,}
// * 匹配前一项0次或多次.等价于{0,}

其它等价：
/^[ab]cd$/.test("abc"); //true 
等价于/^(a|b)cd$/.test("abc")

转义：'/', '\', '.' ,'?'等需要被转义字符都符合[char] = \char
/^[.]abc/.test("a.abc")//true 
等价于/^\.abc/.test(".abc")　


# 三、js正则常用实例

#### 用户名正则
```
//用户名正则，4到16位（字母，数字，下划线，减号）
/^[a-zA-Z0-9_-]{4,16}$/.test("diaodiao");
//输出 true
```

#### 整数正则
```
/^\d+$/.test("42");    //正整数正则  -> 输出 true
/^-\d+$/.test("-42");  //负整数正则  -> 输出 true
/^-?\d+$/.test("-42"); //整数正则  -> 输出 true
/^[0-9]+$/.test(25.5455) //正整数正则  -> 输出 false
```

#### 数字正则
可以是整数也可以是浮点数
```
/^\d*\.?\d+$/.test("42.2");     //正数正则  -> 输出 true
/^-\d*\.?\d+$/.test("-42.2");   //负数正则 -> 输出 true
/^-?\d*\.?\d+$/.test("-42.2");  //数字正则 -> 输出 true
```

#### Email正则
```
//Email正则
/^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/.test("wowohoo@qq.com");
//输出 true
```

#### 传真号码
```
// 国家代码(2到3位)-区号(2到3位)-电话号码(7到8位)-分机号(3位)
/^(([0\+]\d{2,3}-)?(0\d{2,3})-)(\d{7,8})(-(\d{3,}))?$/.test('021-5055455')
```

#### 手机号码正则
```
//手机号正则
/^1[34578]\d{9}$/.test("13611778887");
//输出 true

//* 国际码 如：中国(+86)
/^((\+?[0-9]{1,4})|(\(\+86\)))?(13[0-9]|14[57]|15[012356789]|17[03678]|18[0-9])\d{8}$/.test("13611778887");
```

#### 颜色值校验
```
/^#?([0-9a-fA-F]{3}|[0-9a-fA-F]{6})$/.test("#ccb2b2")
```

#### 清除字符串前后空格的正则表达式
```
String.prototype.trim = function(){return this.replace(/(^\s*)|(\s*$)/g, "");}
var str2 = " hi space "//这里前后共有两个空格
console.log(str2.length);//14
console.log(str2.trim().length);//8
console.log(str2.trim());//hi space
```

#### 获取网址url参数转化为键值对
```
(function parseURL(url=window.location.href){/*es6语法直接设置默认值*/
　　const search = url.substr(url.indexOf('?')+1);
　　const obj={};
　　search.replace(/([^&=]+)=([^&=]*)/g,function(rs,$1,$2){
		console.log(rs)
　　　　obj[decodeURIComponent($1)]=decodeURIComponent($2)

　　})
　　return obj
})()
```