---
title: js继承介绍及应用实例
comments: true
date: 2019-12-26 13:40:50
tags:
- js
categories:
- WEB前端
author: jun.zhou
---

![](https://img.fengjr.com/image/2019/12/26/8a99a91c4e358ca58a39f074681a2a7f.jpg)

# 一、js继承介绍

说起JavaScript 的继承，相信许多初学者对它感到一头雾水，特别是当你了解或学过基于类的语言（例如Java或C++）。javascript作为动态和弱类型的语言，它没有提供类Class来实现继承。直到ES6才有了class的语法糖，但JavaScript保留了prototype这个基本属性。

关于继承，JavaScript是通过对象来实现的。 每个对象都有一个私有属性，该属性包含指向另一个称为其原型的对象的链接。该原型对象具有自己的原型，依此类推，直到到达以null为原型的对象。根据定义，null没有原型，并充当该原型链中的最终链接。

接下来的继承相关案例，就是围绕prototype,prototype chain展开的。

#### 1、原型链继承
子类的原型指向父类的实例，这就是原型链的指向方式。
```
function Parent () {
    this.name = 'haha';
}

Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child () {

}
Child.prototype = new Parent();

var child1 = new Child();

console.log(child1.getName()) // haha
```
#### 2、借用构造函数(经典继承)
```
function Parent() {
    this.x = 100;
    this.y = 199;
}
Parent.prototype.fn = function() {}

function Child() {
    this.d = 100;
    Parent.call(this); //构造函数中的this就是当前实例
}
var p = new Parent();
var c = new Child();
console.log(p)  //Parent {x: 100, y: 199}
console.log(c)  //Child {d: 100, x: 100, y: 199}
```
优点：通过Funtion的call方法改变this指向，子类继承了父类的私有属性和方法，

避免了引用类型的属性被所有实例共享。还可以在 Child 中向 Parent 传参，覆盖父类的私有属性，如下：
```
function Parent(z) {
  this.x = 100;
  this.y = 199;
  this.z=z;
}
Parent.prototype.fn = function() {}

function Child(z) {
  Parent.call(this,z); //构造函数中的this就是当前实例
}
var p = new Parent(666);
var c = new Child(777);
console.log(p)  //Parent {x: 100, y: 199, z: 666}
console.log(c)  //Child {x: 100, y: 199, z: 777}
```

#### 3、组合继承
结合了原型链继承和构造函数经典继承，拥有公有和私有的方法属性。
```
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);//new Child()的时候，调用一次构造函数
    this.age = age;
}
//子类的原型指向父类的实例对象
Child.prototype = new Parent(); //这里也调用一次构造函数
//改变子类的原型的constructor属性为Child,而不是Parent
Child.prototype.constructor = Child;

var child1 = new Child('Mary', '18');

child1.colors.push('black');

console.log(child1.name); // Mary
console.log(child1.age); // 18
console.log(child1.colors); // ["red", "blue", "green", "black"]
```
优点：融合原型链继承和构造函数的优点，是 JavaScript 中常用的继承模式。

缺点：每次都会调用两次构造函数：一次是在创建子类的原型的时候，另一次是在子类型构造函数的内部。子类最终会包含父类型对象的全部实例属性，但我们不得不在调用子类构造函数时重写这些属性。

通过几个实例我们发现prototype有很多属性，下面我们了解一下：
#### Object.prototype的成员
1.constructor：原型对象内的一个属性，指向该原型对象相关联的构造函数
2.hasOwnProperty：一个方法，用来判断对象本身（不包含原型）是否有某个属性
3.propertyIsEnumerable
（1）判断属性是否属于对象本身
（2）判断属性是否可以被遍历
4.defineProperty()：添加属性，可以加一些附加信息（比如可写、可读、可遍历等）
5.toString和toLocaleString
6.valueOf：获取当前对象的值
    在对象参与运算的时候：
    （1）默认会先去调用对象的valueOf()方法
    （2）如果valueOf获取到的值无法进行运算，就去调用toString方法，最终做的就是拼接字符串的工作
7.__proto__：原型对象中的属性，可以使用 对象.__proto__ 去访问原型对象


# 二、Javascript继承机制的设计思想

我们一直很难理解Javascript语言的继承机制。

它没有"子类"和"父类"的概念，也没有"类"（class）和"实例"（instance）的区分，全靠一种很奇特的"原型链"（prototype chain）模式，来实现继承。

我们常常花了很多时间，学习这个部分。但是都属于强行记忆，无法从根本上理解。

下面，我尝试用自己的语言，来解释它的设计思想。彻底说明白prototype对象到底是怎么回事。其实根本就没那么复杂，真相非常简单。

#### 1、从古代说起

要理解Javascript的设计思想，必须从它的诞生说起。

1994年，网景公司（Netscape）发布了Navigator浏览器0.9版。这是历史上第一个比较成熟的网络浏览器，轰动一时。但是，这个版本的浏览器只能用来浏览，不具备与访问者互动的能力。比如，如果网页上有一栏"用户名"要求填写，浏览器就无法判断访问者是否真的填写了，只有让服务器端判断。如果没有填写，服务器端就返回错误，要求用户重新填写，这太浪费时间和服务器资源了。

因此，网景公司急需一种网页脚本语言，使得浏览器可以与网页互动。工程师Brendan Eich负责开发这种新语言。他觉得，没必要设计得很复杂，这种语言只要能够完成一些简单操作就够了，比如判断用户有没有填写表单。

1994年正是面向对象编程（object-oriented programming）最兴盛的时期，C++是当时最流行的语言，而Java语言的1.0版即将于第二年推出，Sun公司正在大肆造势。

Brendan Eich无疑受到了影响，Javascript里面所有的数据类型都是对象（object），这一点与Java非常相似。但是，他随即就遇到了一个难题，到底要不要设计"继承"机制呢？

#### 2、Brendan Eich的选择
如果真的是一种简易的脚本语言，其实不需要有"继承"机制。但是，Javascript里面都是对象，必须有一种机制，将所有对象联系起来。所以，Brendan Eich最后还是设计了"继承"。

但是，他不打算引入"类"（class）的概念，因为一旦有了"类"，Javascript就是一种完整的面向对象编程语言了，这好像有点太正式了，而且增加了初学者的入门难度。

他考虑到，C++和Java语言都使用new命令，生成实例。

C++的写法是：
```
ClassName *object = new ClassName(param);
```
Java的写法是：
```
　　Foo foo = new Foo();
```
因此，他就把new命令引入了Javascript，用来从原型对象生成一个实例对象。但是，Javascript没有"类"，怎么来表示原型对象呢？

这时，他想到C++和Java使用new命令时，都会调用"类"的构造函数（constructor）。他就做了一个简化的设计，在Javascript语言中，new命令后面跟的不是类，而是构造函数。

举例来说，现在有一个叫做DOG的构造函数，表示狗对象的原型。

```
function DOG(name){
　　this.name = name;
}
```
对这个构造函数使用new，就会生成一个狗对象的实例。
```
　　var dogA = new DOG('大毛');

　　alert(dogA.name); // 大毛
```
注意构造函数中的this关键字，它就代表了新创建的实例对象。

#### 3、new运算符的缺点

用构造函数生成实例对象，有一个缺点，那就是无法共享属性和方法。

比如，在DOG对象的构造函数中，设置一个实例对象的共有属性species。
```
　　function DOG(name){

　　　　this.name = name;

　　　　this.species = '犬科';

　　}
```
然后，生成两个实例对象：
```
　　var dogA = new DOG('大毛');

　　var dogB = new DOG('二毛');
```
这两个对象的species属性是独立的，修改其中一个，不会影响到另一个。
```
  dogA.species = '猫科';

　alert(dogB.species); // 显示"犬科"，不受dogA的影响
```
每一个实例对象，都有自己的属性和方法的副本。这不仅无法做到数据共享，也是极大的资源浪费。

#### 4、prototype属性的引入

考虑到这一点，Brendan Eich决定为构造函数设置一个prototype属性。

这个属性包含一个对象（以下简称"prototype对象"），所有实例对象需要共享的属性和方法，都放在这个对象里面；那些不需要共享的属性和方法，就放在构造函数里面。

实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的。

还是以DOG构造函数为例，现在用prototype属性进行改写：

```
function DOG(name){

　　　　this.name = name;

　　}

　　DOG.prototype = { species : '犬科' };


　　var dogA = new DOG('大毛');

　　var dogB = new DOG('二毛');


　　alert(dogA.species); // 犬科

　　alert(dogB.species); // 犬科
```

现在，species属性放在prototype对象里，是两个实例对象共享的。只要修改了prototype对象，就会同时影响到两个实例对象。
```
　　DOG.prototype.species = '猫科';


　　alert(dogA.species); // 猫科

　　alert(dogB.species); // 猫科
```

由于所有的实例对象共享同一个prototype对象，那么从外界看起来，prototype对象就好像是实例对象的原型，而实例对象则好像"继承"了prototype对象一样。

这就是Javascript继承机制的设计思想。

# 三、构造函数的继承实例

通过上面对继承的了解，我们继续看实例。

比如，现在有一个"动物"对象的构造函数。
```
function Animal(){

　　　　this.species = "动物";

　　}
```
还有一个"猫"对象的构造函数。

```
　　function Cat(name,color){

　　　　this.name = name;

　　　　this.color = color;

　　}
```
怎样才能使"猫"继承"动物"呢？

#### 1、 构造函数绑定
第一种方法也是最简单的方法，使用call或apply方法，将父对象的构造函数绑定在子对象上，即在子对象构造函数中加一行：
```
　　function Cat(name,color){

　　　　Animal.apply(this, arguments);

　　　　this.name = name;

　　　　this.color = color;

　　}

　　var cat1 = new Cat("大毛","黄色");

　　alert(cat1.species); // 动物
```

#### 2、 prototype模式

第二种方法更常见，使用prototype属性。

如果"猫"的prototype对象，指向一个Animal的实例，那么所有"猫"的实例，就能继承Animal了。
```
　　Cat.prototype = new Animal();

　　Cat.prototype.constructor = Cat;

　　var cat1 = new Cat("大毛","黄色");

　　alert(cat1.species); // 动物
```
代码的第一行，我们将Cat的prototype对象指向一个Animal的实例。
```
　　Cat.prototype = new Animal();
```
它相当于完全删除了prototype 对象原先的值，然后赋予一个新值。但是，第二行又是什么意思呢？
```
　　Cat.prototype.constructor = Cat;
```
原来，任何一个prototype对象都有一个constructor属性，指向它的构造函数。如果没有"Cat.prototype = new Animal();"这一行，Cat.prototype.constructor是指向Cat的；加了这一行以后，Cat.prototype.constructor指向Animal。
```
　　alert(Cat.prototype.constructor == Animal); //true
```
更重要的是，每一个实例也有一个constructor属性，默认调用prototype对象的constructor属性。
```
　　alert(cat1.constructor == Cat.prototype.constructor); // true
```
因此，在运行"Cat.prototype = new Animal();"这一行之后，cat1.constructor也指向Animal！
```
　　alert(cat1.constructor == Animal); // true
```
这显然会导致继承链的紊乱（cat1明明是用构造函数Cat生成的），因此我们必须手动纠正，将Cat.prototype对象的constructor值改为Cat。这就是第二行的意思。

这是很重要的一点，编程时务必要遵守。下文都遵循这一点，即如果替换了prototype对象，
```
　　o.prototype = {};
```
那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。
```
o.prototype.constructor = o;
```

#### 3、 直接继承prototype
第三种方法是对第二种方法的改进。由于Animal对象中，不变的属性都可以直接写入Animal.prototype。所以，我们也可以让Cat()跳过 Animal()，直接继承Animal.prototype。

现在，我们先将Animal对象改写：
```
　　function Animal(){ }

　　Animal.prototype.species = "动物";
```
然后，将Cat的prototype对象，然后指向Animal的prototype对象，这样就完成了继承。
```
　　Cat.prototype = Animal.prototype;

　　Cat.prototype.constructor = Cat;

　　var cat1 = new Cat("大毛","黄色");

　　alert(cat1.species); // 动物
```
与前一种方法相比，这样做的优点是效率比较高（不用执行和建立Animal的实例了），比较省内存。缺点是 Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到Animal.prototype。

所以，上面这一段代码其实是有问题的。请看第二行
```
　　Cat.prototype.constructor = Cat;
```
这一句实际上把Animal.prototype对象的constructor属性也改掉了！
```
　　alert(Animal.prototype.constructor); // Cat
```
#### 4、 利用空对象作为中介

由于"直接继承prototype"存在上述的缺点，所以就有第四种方法，利用一个空对象作为中介。
```
　　var F = function(){};

　　F.prototype = Animal.prototype;

　　Cat.prototype = new F();

　　Cat.prototype.constructor = Cat;
```
F是空对象，所以几乎不占内存。这时，修改Cat的prototype对象，就不会影响到Animal的prototype对象。
```
　　alert(Animal.prototype.constructor); // Animal
```
我们将上面的方法，封装成一个函数，便于使用。
```
　function extend(Child, Parent) {

　　　　var F = function(){};

　　　　F.prototype = Parent.prototype;

　　　　Child.prototype = new F();

　　　　Child.prototype.constructor = Child;

　　　　Child.uber = Parent.prototype;

　　}
```
使用的时候，方法如下
```
　　extend(Cat,Animal);

　　var cat1 = new Cat("大毛","黄色");

　　alert(cat1.species); // 动物
```
这个extend函数，就是YUI库如何实现继承的方法。

另外，说明一点，函数体最后一行
```
　　Child.uber = Parent.prototype;
```
意思是为子对象设一个uber属性，这个属性直接指向父对象的prototype属性。（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。

#### 5、 拷贝继承

上面是采用prototype对象，实现继承。我们也可以换一种思路，纯粹采用"拷贝"方法实现继承。简单说，如果把父对象的所有属性和方法，拷贝进子对象，不也能够实现继承吗？这样我们就有了第五种方法。

首先，还是把Animal的所有不变属性，都放到它的prototype对象上。
```
　　function Animal(){}

　　Animal.prototype.species = "动物";
```
然后，再写一个函数，实现属性拷贝的目的。
```
function extend2(Child, Parent) {

　　　　var p = Parent.prototype;

　　　　var c = Child.prototype;

　　　　for (var i in p) {

　　　　　　c[i] = p[i];

　　　　　　}

　　　　c.uber = p;

　　}
```
这个函数的作用，就是将父对象的prototype对象中的属性，一一拷贝给Child对象的prototype对象。

使用的时候，这样写：
```
　　extend2(Cat, Animal);

　　var cat1 = new Cat("大毛","黄色");

　　alert(cat1.species); // 动物
```

# 四、完整的项目实例

下面是一个拖拽的实例。
html
```
<!DOCTYPE html>
<html>
<head>
    <title>drag Div</title>
    <style type="text/css">
        #div1{width: 100px;height: 100px;background: red;position: absolute;}
        #div2{width: 100px;height: 100px;background: yellow;position: absolute;}
    </style>
    <script src="drag.js"></script>
    <script src="Limitdrag.js"></script>
    <script type="text/javascript">
    window.onload=function(){
        new Drag('div1');
        new LimitDrag('div2');
    }
    </script>
</head>
<body>
    <div id="div1"></div>
    <div id="div2"></div>
</body>
</html>
```
首先创建一个基础的Drag构造函数，实现基础拖拽功能。
drag.js
```
function Drag(id){
    var _this=this;
    this.disX=0;
    this.dixY=0;
    this.oDiv=document.getElementById(id);
    this.oDiv.onmousedown=function(ev)
    {
        _this.fnDown(ev);
        return false;
    };

}
Drag.prototype.fnDown=function(ev){
    var _this=this;
    var oEvent=ev||event;
    this.disX=oEvent.clientX-this.oDiv.offsetLeft;
    this.disY=oEvent.clientY-this.oDiv.offsetTop;
    document.onmousemove=function(ev){
        _this.fnMove(ev);
    };
    document.onmouseup=function(){
        _this.fnUp();
    };
};
Drag.prototype.fnMove=function(ev){
    var oEvent=ev||event;
    this.oDiv.style.left=oEvent.clientX-this.disX+'px';
    this.oDiv.style.top=oEvent.clientY-this.disY+'px';
    // this.oDiv.style.left=l+'px';
    // this.oDiv.style.top=t+'px';
};
Drag.prototype.fnUp=function(){
    document.onmousemove=null;
    document.onmouseup=null;
};
```

在上面基础实例上通过继承来完成进一步功能优化。
Limitdrag.js
```
function LimitDrag(id){
    Drag.call(this,id);
}
console.log(Drag.prototype,'111')
console.log(Drag.LimitDrag,'222')
for(var i in Drag.prototype){
    LimitDrag.prototype[i]=Drag.prototype[i];
}
console.log(LimitDrag.prototype,'333')
LimitDrag.prototype.fnMove=function(ev){
    var oEvent=ev||event;
    var l=oEvent.clientX-this.disX;
    var t=oEvent.clientY-this.disY;

        if (l<0)
        {l=0;}
    else if(l>document.documentElement.clientWidth-this.oDiv.offsetWidth){
        l=document.documentElement.clientWidth-this.oDiv.offsetWidth;
    }
        if (t<0)
        {t=0;}
    else if(t>document.documentElement.clientHeight-this.oDiv.offsetHeight){
        t=document.documentElement.clientHeight-this.oDiv.offsetHeight;
    }
    this.oDiv.style.left=l+'px';
    this.oDiv.style.top=t+'px';
};
```

通过继承实现封装的代码看起来是不是很优雅，也非常方便后续修改和更新。

# 五、class继承
在上面的章节中我们看到了JavaScript的对象模型是基于原型实现的，特点是简单，缺点是理解起来比传统的类－实例模型要困难，最大的缺点是继承的实现需要编写大量代码，并且需要正确实现原型链。

有没有更简单的写法？有！

新的关键字class从ES6开始正式被引入到JavaScript中。class的目的就是让定义类更简单。

我们先用函数实现Student的方法：
```
function Student(name) {
    this.name = name;
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}
```
如果用新的class关键字来编写Student，可以这样写：
```
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}
```

比较一下就可以发现，class的定义包含了构造函数constructor和定义在原型对象上的函数hello()（注意没有function关键字），这样就避免了Student.prototype.hello = function () {...}这样分散的代码。
最后实现的效果是一样的。

用class定义对象的另一个巨大的好处是继承更方便了。想一想我们从Student派生一个PrimaryStudent需要编写的代码量。现在，原型继承的中间对象，原型对象的构造函数等等都不需要考虑了，直接通过extends来实现：
```
class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

注意PrimaryStudent的定义也是class关键字实现的，而extends则表示原型链对象来自Student。子类的构造函数可能会与父类不太相同，例如，PrimaryStudent需要name和grade两个参数，并且需要通过super(name)来调用父类的构造函数，否则父类的name属性无法正常初始化。

PrimaryStudent已经自动获得了父类Student的hello方法，我们又在子类中定义了新的myGrade方法。

ES6引入的class和原有的JavaScript原型继承有什么区别呢？实际上它们没有任何区别，class的作用就是让JavaScript引擎去实现原来需要我们自己编写的原型链代码。简而言之，用class的好处就是极大地简化了原型链代码。

那么，class这么好用，兼容性怎么样呢？

不是所有的主流浏览器都支持ES6的class。如果一定要现在就用上，就需要一个工具把class代码转换为传统的prototype代码，可以用Babel这个工具。