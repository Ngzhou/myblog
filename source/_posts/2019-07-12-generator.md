---
title: Generator函数的特征及应用
comments: true
date: 2019-07-12 16:20:11
tags:
- es6
categories:
- WEB前端
author: jun.zhou
---


![](https://img.fengjr.com/image/2019/07/12/65874791cbf960f60b71541ee3f5bff2.jpeg)

# 一、Generator 函数简介

1、Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

2、Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

3、执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

# 二、Generator 函数特征

#### 两个特征：
1、function关键字与函数名之间有一个星号
2、函数体内部使用yield表达式，定义不同的内部状态

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();

```

#### 调用方法：

1、调用 Generator 函数后，该函数并不执行
2、返回的也不是函数运行结果，而是一个指向内部状态的指针对象

```
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }

```

#### for...of 循环：

for...of循环可以自动遍历 Generator 函数运行时生成的Iterator对象，且此时不再需要调用next方法。

```
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5

```


```
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}

// 扩展运算符
[...numbers()] // [1, 2]

// Array.from 方法
Array.from(numbers()) // [1, 2]

// 解构赋值
let [x, y] = numbers();
x // 1
y // 2

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2

```

#### yield* 表达式：

如果在 Generator 函数内部，调用另一个 Generator 函数。需要在前者的函数体内部，自己手动完成遍历。

```
function* iterTree(tree) {
  if (Array.isArray(tree)) {
    for(let i=0; i < tree.length; i++) {
      yield* iterTree(tree[i]);
    }
  } else {
    yield tree;
  }
}

const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];

for(let x of iterTree(tree)) {
  console.log(x);
}
// a
// b
// c
// d
// e

```

# 三、Generator 函数应用

#### 异步操作的同步化表达：

Generator 函数的暂停执行的效果，意味着可以把异步操作写在yield表达式里面，等到调用next方法时再往后执行。这实际上等同于不需要写回调函数了，因为异步操作的后续操作可以放在yield表达式下面，反正要等到调用next方法时再执行。所以，Generator 函数的一个重要实际意义就是用来处理异步操作，改写回调函数。

```
function* loadUI() {
  showLoadingScreen();
  yield loadUIDataAsynchronously();
  hideLoadingScreen();
}
var loader = loadUI();
// 加载UI
loader.next()

// 卸载UI
loader.next()

```

#### 控制流管理：

如果有一个多步操作非常耗时，采用回调函数，可能会写成下面这样。

```
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value4
      });
    });
  });
});

```

采用 Promise 改写上面的代码。

```
Promise.resolve(step1)
  .then(step2)
  .then(step3)
  .then(step4)
  .then(function (value4) {
    // Do something with value4
  }, function (error) {
    // Handle any error from step1 through step4
  })
  .done();
```

上面代码已经把回调函数，改成了直线执行的形式，但是加入了大量 Promise 的语法。Generator 函数可以进一步改善代码运行流程。

```
function* longRunningTask(value1) {
  try {
    var value2 = yield step1(value1);
    var value3 = yield step2(value2);
    var value4 = yield step3(value3);
    var value5 = yield step4(value4);
    // Do something with value4
  } catch (e) {
    // Handle any error from step1 through step4
  }
}
```

然后，使用一个函数，按次序自动执行所有步骤。

```
scheduler(longRunningTask(initialValue));

function scheduler(task) {
  var taskObj = task.next(task.value);
  // 如果Generator函数未结束，就继续调用
  if (!taskObj.done) {
    task.value = taskObj.value
    scheduler(task);
  }
}

```

#### async 函数：

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。
async 函数是什么？一句话，它就是 Generator 函数的语法糖。

```
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}

asyncPrint('hello world', 50);

```

#### async 函数优点：

1、**内置执行器**
Generator 函数的执行必须靠执行器，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。这完全不像 Generator 函数，需要调用next方法，才能真正执行，得到最后结果。

2、**更好的语义**
async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。

3、**更广的适用性**
yield命令后面只能是 Thunk 函数或 Promise 对象，而async函数的await命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）

4、**返回 Promise**
async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用then方法指定下一步的操作。

# 项目实例

#### 抽奖

```
<button onclick="starts()">抽奖</button>
<script>
  //定义一个普通函数,输出剩余次数
  let draw = function (count) {
      alert(`剩余 ${count} 次`)
  }
  //定义一个Generator函数，接收一个count参数，每次调用都减少1
  let resize = function* (count) {
      while (count > 0){
          count -- ;
          yield draw(count) //调用draw函数，输出count
      }
  }
  //调用resize 函数，初始化count为5，此时resize 函数并不会执行。
  let start = resize(5);
  function starts() {
      start.next()//通过点击按钮，调用resize 的next方法执行函数体，执行一次便暂停一次
  }

```

#### 长轮训

```
let ajax = function* () {
    yield new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve({code:1})
        },500)
    })
}
let pull = function () {
    let genertaor = ajax();
    let step = genertaor.next();
    step.value.then(function (res) {
        if(res.code != 0){
            console.log(res)
            setTimeout(function () {
                pull();
            },1000)
        }else{
            console.log(res)
        }
    })
}
pull();

```

