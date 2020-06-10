---
title: async/await原理
comments: false
date: 2019-09-09 10:58:28
tags:
- js
updated:
categories:
- WEB前端
author: jun.zhou
---

### 主要内容：
  ##### 1 Iterator
  ##### 2 Generator
  ##### 3 Async/await

# 1 迭代器 iterator
Iterator 函数返回一个对象，它实现了迭代协议，并且迭代了一个对象的可枚举属性。
### (1) 可迭代协议（对象）
一个对象要变成可迭代的，需要实现 @@iterator 方法
即必须有一个名字是 Symbol.iterator 的无参函数，返回一个符合迭代器协议的迭代器对象
### (2) 迭代器协议（对象）
迭代器协议定义了一种标准的方式来产生一个有限或无限序列的值，并且当所有的值都已经被迭代后，就会有一个默认的返回值
必须实现next()方法，该方法必须要返回一个对象，该对象有两个必要的属性： done和value

下面我们实现一个可迭代的对象
```javascript
    let iterableObj = {
        obj:{
            a: 'v_a',
            b: 'v_b',
            c: 'v_c',
        },
        [Symbol.iterator]: function () {
            let fields =this.getFields();
            let index = 0;
            return {
                next: ()=> {
                    if (index < fields.length) {
                        return {value: fields[index++]}
                    } else {
                        return {done: true}
                    }
                }
            }
        },
        getFields(){
            let fields = [];
            for (let f in this.obj) {
                fields.push({key: f, value: this.obj[f]});
            }
            return fields;
        }
    }

    for (let {key,value} of iterableObj) {
        console.log(key, ':', value);
    }
```

# 2 Generator
Generator 是ES6 提供的一种异步编程解决方案， 函数是一个状态机，封装了多个内部状态
yield 表达式本身没有返回值
yield 表达式左边的值等于next方法所带的参数。
执行 Generator 函数会返回一个实现了可迭代协议的迭代器对象
下面通过例子熟悉一下generator的用法
```javascript
    function* gen(x) {
        var y = yield x + 2;
        return y + 2;
    }

    let g = gen(1);
    let iter = g[Symbol.iterator]();//包含Symbol.iterator属性
    console.log(g === iter);//true
    let re = g.next()
    console.log('re:', re);// { value: 3, done: false }
    re = g.next(2)
    console.log('re:', re);// { value: 4, done: true }
```

利用Iterator我们模拟一个自己的Generator
```javascript
    function myGenerator(yields) {//返回一个迭代器
        let l = yields.length;
        let index = 0;
        return {
            next(value){
                let newValue = yields[index++](value)//表达式左边的值是next函数传入的
                return {
                    value: newValue,
                    done: index >= l,
                }
            }
        }
    }

    let x = 1;
    let g = myGenerator([
          value=> {//模拟yield表达式
            return x + 2;
          },
          value=> {//模拟yield表达式
            return value + 2;
          }
      ]);
    let re = g.next()
    console.log('re:', re);// { value: 3, done: false }
    re = g.next(2)
    console.log('re:', re);// { value: 4, done: true }
```

# 3 async/await
ES2017 标准引入了 async 函数，使得异步操作变得更加方便。
async 函数是什么？一句话，它就是 Generator 函数的语法糖

下面从读取文件的异步操作开始，我们先来看一下读取文件的函数
```javascript
function readFilePromise(path) {
    return new Promise(resolve=> {
        setTimeout(()=> {
            resolve(`path:${path}`)
        }, 1000)
    })
}
```

如果我们要读取两个文件，要怎么写的呢？
### 一 通常写法
```javascript
let f1 = readFilePromise('file1');
f1.then(function (file) {
    console.log('f1:', file);
    let f2 = readFilePromise('file2')
    f2.then(function (file) {
        console.log('f2:', file);
    })
})
/*
   f1: path:file1
   f2: path:file2
*/
```

### 二 用Generator的写法

```javascript
let genFun = function*() {
    let f1 = yield readFilePromise('/file1');
    console.log('f1:', f1);
    let f2 = yield readFilePromise('/file2');
    console.log('f2:', f2);
    return f2;
}
```

但是Generator不会自动执行，我们知道Generator函数返回的就是一个迭代器，因此我们可以手动执行
那么直接执行可不可以呢？
```javascript
let g = genFun();
let t1 = g.next();
//t1: {value: Promise {<resolved>: "path:/file1"}, done: false}
let t2 = g.next();
//t2: {value: Promise {<resolved>: "path:/file2"}, done: false}
let t3 = g.next();
//t3: {value: undefined, done: true}
```
直接执行显然不行，f1和f2没有输出

正确执行
```javascript
let g2=genFun();
let t1=g2.next();
t1.value.then(value=>{
    let t2=g2.next(value);
    t2.value.then(value=>{
        let t3=g2.next(value);
    })
})
/*
  f1: path:/file1
  f2: path:/file2
*/
```

很明显，上面的运行是一个递归调用，因此我们可以写成下面这种形式
```javascript
function step(value) {
    let t = g2.next(value);
    if (t.done)return t.value;
    t.value.then(value=> {
        step(value);
    })
}
step();
```
这就是一个执行器，有了这个，我们可以方便的执行Generator

### 三 async/await的写法
```javascript
async function gen2(){
    let f1=await readFilePromise('/file1', 'open');
    console.log('f1:', f1);
    let f2 = await readFilePromise('/file2', 'read');
    console.log('f2:', f2);
}
```
这种写法跟Generator的写法非常类似，只是把*换成了async，把yield换成了await，这样语义更明确。

而且async/await就是自带执行器的，可以自动执行。

## 下面我们利用Generator来实现一下async/await

```javascript
function myAsync(genF) {
    return new Promise(function (resolve, reject) {
        const gen = genF();
        function step(data) {
            let next = gen.next(data);
            if (next.done) {
                return resolve(next.value);
            }
            Promise.resolve(next.value)//这样写可以把不是promise的对象转为proimsie,若是Promise则等待其执行结束
                .then(v=> {//用新的promise保留的中间结果
                    step(v);
                }, e=> {
                    step(e)
                })
        }
        step();
    });
}

let genF = function*() {
    let f1 = yield readFilePromise('/file1');
    console.log('f1:', f1);
    let f2 = yield readFilePromise('/file2');
    console.log('f2:', f2);
    return f2;
}
let f1 = myAsync(genF);

f1.then(data=> {
    console.log('data:', data);
})

/*
 f1: path:/file1
 f2: path:/file2
 data: path:/file2
 */
```
其实就是封装了一个执行器。

## 结合之前模拟的Generator，我们用Iterator来模拟一下async/await
```javascript

function myAsync(genF) {
    return new Promise(function (resolve, reject) {
        const gen = genF();
        //执行器
        function step(data) {
            let next = gen.next(data);
            if (next.done) {
                return resolve(next.value);
            }
            Promise.resolve(next.value)//这样写可以把不是promse的对象转为promsie
                .then(v=> {//用新的promise保留的中间结果
                    step(v);
                }, e=> {
                    step(e)
                })
        }
        step();
    });
}

function myGenerator(yields) {//返回一个迭代器
    let l = yields.length;
    let index = 0;
    return {
        next(value){
            let newValue = yields[index++](value)//表达式左边的值是next函数传入的
            return {
                value: newValue,
                done: index >= l,
            }
        }
    }
}

function getGen() {//generator函数
    return myGenerator(
        [
            (value)=> {
                return readFilePromise('/file1');
            },
            (value)=> {
                console.log('f1:', value);
                return readFilePromise('/file2')
            },
            (value)=> {
                console.log('f2:', value);
                return value;
            }
        ]
    );
}

let f2 = myAsync(getGen);
f2.then(data=> {
    console.log('data:', data);
})
```
**总结**
<font color=#c7254e >async/await本质是就是一个迭代器和一个自动执行器，结合promise组成的一个异步编程中的同步解决方案，如果我们不需要等待执行结果，那就没必要用async/await</font>





