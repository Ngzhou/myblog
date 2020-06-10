---
title: react-hook介绍及应用
comments: true
date: 2019-11-15
tags:
- react hooks
categories:
- WEB前端
author: jun.zhou
---

![](https://img.fengjr.com/image/2019/12/13/cbdd0825ad6d689bfc8b572387788dec.jpg)

# 一、React Hooks 简介
Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性
React Hooks就是用函数的形式代替原来的继承类的形式，并且使用预函数的形式管理state，有Hooks可以不再使用类的形式定义组件了。这时候你的认知也要发生变化了，原来把组件分为有状态组件和无状态组件，有状态组件用类的形式声明，无状态组件用函数的形式声明。那现在所有的组件都可以用函数来声明了。
### React Hooks 编写形式对比
先来写一个最简单的有状体组件，点我们点击按钮时，点击数量不断增加。

```js
//原始写法
import React, { Component } from 'react';

class Example extends Component {
    constructor(props) {
        super(props);
        this.state = { count:0 }
    }
    render() { 
        return (
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={this.addCount.bind(this)}>Chlick me</button>
            </div>
        );
    }
    addCount(){
        this.setState({count:this.state.count+1})
    }
}
 
export default Example;
```
```js
//hook写法
import React, { useState } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```
从这两个程序的对比上可以看出Hooks本质上就是一类特殊的函数，他们可以为你的函数型组件（function component）注入一些特殊的功能。hooks的目的就是让你不再写class，让function一统江湖。

# 二、useState 的介绍和多状态声明
### useState的介绍
useState是react自带的一个hook函数，它的作用是用来声明状态变量。
我们从三个方面来看useState的用法，分别是声明、读取、使用（修改）。这三个方面掌握了，你基本也就会使用useState了.

先来看一下声明的方式，代码如下
```js
const [ count , setCount ] = useState(0);
```
这种方法是ES6语法中的数组解构，这样看起来代码变的简单易懂。 如果不写成数组解构，上边的语法要写成下面的三行:
```js
let _useState = userState(0)
let count = _useState[0]
let setCount = _useState[1]
```

useState这个函数，需要传入一个参数作为状态的初始值(Initial state)，当函数执行后会返回两个值，一个是当前状态的属性，一个是修改状态的方法。且返回的是一个数组，这个数组的第0位是当前的状态值，第1位是可以改变状态值的方法函数。 所以上面的代码的意思就是声明了一个状态变量为count，并把它的初始值设为0，同时提供了一个可以改变count的状态值的方法函数。

这时候你已经会声明一个状态了，接下来我们看看如何读取状态中的值。
```html
<p>You clicked {count} times</p>
```
你可以发现，我们读取是很简单的。只要使用{count}就可以，因为这时候的count就是JS里的一个变量，想在JSX中使用，值用加上{}就可以。

最后看看如果改变State中的值,看下面的代码:
```html
<button onClick={()=>{setCount(count+1)}}>click me</button>
```
直接调用setCount函数，这个函数接收的参数是修改过的新状态值。接下来的事情就交给React,他会重新渲染组件。React自动帮助我们记忆了组件的上一次状态值。
### 多状态声明的注意事项
比如现在我们要声明多个状态，有年龄（age）、性别(sex)和工作(work)。代码可以这么写.
```js
import React, { useState } from 'react';
function Example(){
    const [ age , setAge ] = useState(18)
    const [ sex , setSex ] = useState('男')
    const [ work , setWork ] = useState('前端程序员')
    return (
        <div>
            <p>JSPang 今年:{age}岁</p>
            <p>性别:{sex}</p>
            <p>工作是:{work}</p>
            
        </div>
    )
}
export default Example;
```
从上面的代码可以发现，在使用useState的时候只赋了初始值，并没有绑定任何的key,那React是怎么保证这三个useState找到它自己对应的state呢？

**答案是：React是根据useState出现的顺序来确定的**

比如我们把代码改成下面的样子：
```js
import React, { useState } from 'react';

let showSex = true
function Example(){
    const [ age , setAge ] = useState(18)
    if(showSex){
        const [ sex , setSex ] = useState('男')
        showSex=false
    }
   
    const [ work , setWork ] = useState('前端程序员')
    return (
        <div>
            <p>JSPang 今年:{age}岁</p>
            <p>性别:{sex}</p>
            <p>工作是:{work}</p>
            
        </div>
    )
}
export default Example;
```
这时候控制台就会直接给我们报错，错误如下：
```
React Hook "useState" is called conditionally. React Hooks must be called in the exact same order in every component render 
```
意思就是useState不能在if...else...这样的条件语句中进行调用，必须要按照相同的顺序进行渲染。因为这是hook的规则：
- **只能在顶层调用hooks,不能在循环、条件或嵌套方法中调用**
- **仅在React功能组件中使用hooks。不能在常规的JavaScript方法中调用**


# 三、useEffect代替常用生命周期函数
在用Class制作组件时，经常会用生命周期函数，来处理一些额外的事情（副作用：和函数业务主逻辑关联不大，特定时间或事件中执行的动作，比如Ajax请求后端数据，添加登录监听和取消登录，手动修改DOM等等）。在React Hooks中也需要这样类似的生命周期函数，比如在每次状态（State）更新时执行，它为我们准备了useEffect。
### 用Class的方式为计数器增加生命周期函数
为了让你更好的理解useEffect的使用，先用原始的方式把计数器的Demo增加两个生命周期函数componentDidMount和componentDidUpdate。分别在组件第一次渲染后在浏览器控制台打印出计数器结果和在每次计数器状态发生变化后打印出结果。代码如下：
```js
import React, { Component } from 'react';
class Example extends Component {
    constructor(props) {
        super(props);
        this.state = { count:0 }
    }
    componentDidMount(){
        console.log(`ComponentDidMount=>You clicked ${this.state.count} times`)
    }
    componentDidUpdate(){
        console.log(`componentDidUpdate=>You clicked ${this.state.count} times`)
    }
    render() {
        return (
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={this.addCount.bind(this)}>Chlick me</button>
            </div>
        );
    }
    addCount(){
        this.setState({count:this.state.count+1})
    }
}
export default Example;
```
这就是在不使用Hooks情况下的写法，那如何用Hooks来代替这段代码，并产生一样的效果那。
### 用useEffect函数来代替生命周期函数
在使用React Hooks的情况下，我们可以使用下面的代码来完成上边代码的生命周期效果，代码如下（修改了以前的diamond）： 记得要先引入useEffect后，才可以正常使用。
```js
import React, { useState , useEffect } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    //---关键代码---------start-------
    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)
    })
    //---关键代码---------end-------
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```
在浏览器中进行预览一下，可以看出跟class形式的生命周期函数是完全一样的，这代表第一次组件渲染和每次组件更新都会执行这个函数。那这段代码逻辑是什么？我们梳理一下:首先，我们声明了一个状态变量count,将它的初始值设为0，然后我们告诉react，我们的这个组件有一个副作用。给useEffecthook传了一个匿名函数，这个匿名函数就是我们的副作用。在这里我们打印了一句话，当然你也可以手动的去修改一个DOM元素。当React要渲染组件时，它会记住用到的副作用，然后执行一次。等Reat更新了State状态时，它再一词执行定义的副作用函数。

### useEffect两个注意点
- React首次渲染和之后的每次渲染都会调用一遍useEffect函数，而之前我们要用两个生命周期函数分别表示首次渲染(componentDidMonut)和更新导致的重新渲染(componentDidUpdate)。

- useEffect中定义的函数的执行不会阻碍浏览器更新视图，也就是说这些函数时异步执行的，而componentDidMonut和componentDidUpdate中的代码都是同步执行的。个人认为这个有好处也有坏处吧，比如我们要根据页面的大小，然后绘制当前弹出窗口的大小，如果时异步的就不好操作了。

# 四、useEffect 实现 componentWillUnmount生命周期函数
在写React应用的时候，在组件中经常用到componentWillUnmount生命周期函数（组件将要被卸载时执行）。比如我们的定时器要清空，避免发生内存泄漏;比如登录状态要取消掉，避免下次进入信息出错。所以这个生命周期函数也是必不可少的，这节就来用useEffect来实现这个生命周期函数,并讲解一下useEffect容易踩的坑。

### useEffect解绑副作用
学习React Hooks 时，我们要改掉生命周期函数的概念，因为Hooks叫它副作用，所以componentWillUnmount也可以理解成解绑副作用。这里为了演示用useEffect来实现类似componentWillUnmount效果，在此之前我们先得安装React-Router路由。

然后编写两个新组件(由于这两个组件都非常的简单，所以就不单独建立一个新的文件来写了),然后两个新组件中分别加入useEffect()函数:。

```js
import React, { useState , useEffect } from 'react';
import { BrowserRouter as Router, Route, Link } from "react-router-dom"
function Index() {
    return <h2>www.justyouxx.top</h2>;
}
function List() {
    return <h2>List-Page</h2>;
}
function Example(){
    const [ count , setCount ] = useState(0);
    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)
    })
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>

            <Router>
                <ul>
                    <li> <Link to="/">首页</Link> </li>
                    <li><Link to="/list/">列表</Link> </li>
                </ul>
                <Route path="/" exact component={Index} />
                <Route path="/list/" component={List} />
            </Router>
        </div>
    )
}
export default Example;
```
这时候我们点击Link进入任何一个组件，在浏览器中都会打印出对应的一段话。这时候可以用**返回一个函数的形式进行解绑，代码如下：**
```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟你来了！Index页面')
        return ()=>{
            console.log('老弟，你走了!Index页面')
        }
    })
    return <h2>www.justyouxx.top</h2>;
  }
```
这时候你在浏览器中预览，我们仿佛实现了componentWillUnmount方法。但这只是好像实现了，当点击计数器按钮时，你会发现老弟，你走了!Index页面，也出现了。这到底是怎么回事那？其实每次状态发生变化，useEffect都进行了解绑。

### useEffect的第二个参数

那到底要如何实现类似componentWillUnmount的效果呢?这就需要useEffect的第二个参数，它是一个数组，数组中可以写入很多状态对应的变量，意思是当状态值发生变化时，我们才进行解绑。但是当传空数组[]时，就是当组件将被销毁时才进行解绑，这也就实现了componentWillUnmount的生命周期函数。

```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟你来了！Index页面')
        return ()=>{
            console.log('老弟，你走了!Index页面')
        }
    },[])
    return <h2>www.justyouxx.top</h2>;
}
```
为了更加深入了解第二个参数的作用，把计数器的代码也加上useEffect和解绑方法，并加入第二个参数为空数组。代码如下：
```js
function Example(){
    const [ count , setCount ] = useState(0);
    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)

        return ()=>{
            console.log('====================')
        }
    },[])
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>

            <Router>
                <ul>
                    <li> <Link to="/">首页</Link> </li>
                    <li><Link to="/list/">列表</Link> </li>
                </ul>
                <Route path="/" exact component={Index} />
                <Route path="/list/" component={List} />
            </Router>
        </div>
    )
}
```
这时候的代码是不能执行解绑副作用函数的。但是如果我们想每次count发生变化，我们都进行解绑，只需要在第二个参数的数组里加入count变量就可以了。代码如下：
```js
function Example(){
    const [ count , setCount ] = useState(0);

    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)

        return ()=>{
            console.log('====================')
        }
    },[count])

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>

            <Router>
                <ul>
                    <li> <Link to="/">首页</Link> </li>
                    <li><Link to="/list/">列表</Link> </li>
                </ul>
                <Route path="/" exact component={Index} />
                <Route path="/list/" component={List} />
            </Router>
        </div>
    )
}
```
这时候只要count状态发生变化，都会执行解绑副作用函数，浏览器的控制台也就打印出了一串=================。

从这里开始我们对useEffect函数有了一个比较深入的了解，并且可以通过useEffect实现生命周期函数了，现在用React Hooks这种函数的方法编写组件，对比以前用Class编写组件几乎一样了。但这并不是Hooks的所有东西，它还有一些让我们惊喜的新特性。

# 五、useContext 让父子组件传值更简单
有了useState和useEffect已经可以实现大部分的业务逻辑了，但是React Hooks中还是有很多好用的Hooks函数的，比如useContext和useReducer。

在用类声明组件时，父子组件的传值是通过组件属性和props进行的，那现在使用方法(Function)来声明组件，已经没有了constructor构造函数也就没有了props的接收，那父子组件的传值就成了一个问题。React Hooks 为我们准备了useContext。这节就学习一下useContext，它可以帮助我们跨越组件层级直接传递变量，实现共享。需要注意的是useContext和redux的作用是不同的，一个解决的是组件之间值传递的问题，一个是应用中统一管理状态的问题，但通过和useReducer的配合使用，可以实现类似Redux的作用。

Context的作用就是对它所包含的组件树提供全局共享数据的一种技术。

### createContext 函数创建context
在src目录下新建一个文件contextDemo.js,然后拷贝example.js里的代码，并进行修改，删除路由部分和副作用的代码，只留计数器的核心代码就可以了。
```js
import React, { useState , useEffect } from 'react';

function ContextDemo(){
    const [ count , setCount ] = useState(0);
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default ContextDemo;
```
然后修改一下index.js让它渲染这个contextDemo.js组件，修改的代码如下
```js
import React from 'react';
import ReactDOM from 'react-dom';
import Example from './ContextDemo'
ReactDOM.render(<Example />, document.getElementById('root'));
```
之后在contextDemo.js中引入createContext函数，并使用得到一个组件，然后在return方法中进行使用。
```js
import React, { useState , createContext } from 'react';
//===关键代码
const CountContext = createContext()

function ContextDemo(){
    const [ count , setCount ] = useState(0);

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
            {/*======关键代码 */}
            <CountContext.Provider value={count}>
            </CountContext.Provider>

        </div>
    )
}
export default ContextDemo;
```
这段代码就相当于把count变量允许跨层级实现传递和使用了（也就是实现了上下文），当父组件的count变量发生变化时，子组件也会发生变化。接下来我们就看看一个React Hooks的组件如何接收到这个变量。

### useContext 接收上下文变量
已经有了上下文变量，剩下的就时如何接收了，接收这个直接使用useContext就可以，但是在使用前需要新进行引入useContext（不引入是没办法使用的）。
```js
import React, { useState , createContext , useContext } from 'react';
```
引入后写一个Counter组件，只是显示上下文中的count变量代码如下：
```js
function Counter(){
    const count = useContext(CountContext)  //一句话就可以得到count
    return (<h2>{count}</h2>)
}
```
得到后就可以显示出来了，但是要记得在<CountContext.Provider>的闭合标签中,代码如下。
```js
<CountContext.Provider value={count}>
    <Counter />
</CountContext.Provider>
```

# 六、useReducer介绍和简单使用
上节学习了useContext函数，那这节开始学习一下useReducer，因为他们两个很像，并且合作可以完成类似的Redux库的操作。在开发中使用useReducer可以让代码具有更好的可读性和可维护性，并且会给测试提供方便。那现在来学习一下useReducer。这节我们只是简单的学习一下useReducer语法和使用方法，尽量避免Redux的一些操作。这样讲更容易让不了解Redux的小伙伴接受。

### reducer到底是什么？
为了更好的理解useReducer，所以先要了解JavaScript里的Reducer是什么。它的兴起是从Redux广泛使用开始的，但不仅仅存在Redux中，可以使用JavaScript来完成Reducer操作。那reducer其实就是一个函数，这个函数接收两个参数，一个是状态，一个用来控制业务逻辑的判断参数。我们举一个最简单的例子。
```js
function countReducer(state, action) {
    switch(action.type) {
        case 'add':
            return state + 1;
        case 'sub':
            return state - 1;
        default: 
            return state;
    }
}
```
上面的代码就是Reducer，你主要理解的就是这种形式和两个参数的作用，一个参数是状态，一个参数是如何控制状态。

### useReducer的使用
了解reducer的含义后，就可以讲useReducer了，它也是React hooks提供的函数，可以增强我们的Reducer，实现类似Redux的功能。我们用useReducer实现计数器的加减双向操作，如下代码：
```js
import React, { useReducer } from 'react';
function ReducerDemo(){
    const [ count , dispatch ] =useReducer((state,action)=>{
        switch(action){
            case 'add':
                return state+1
            case 'sub':
                return state-1
            default:
                return state
        }
    },0)
    return (
       <div>
           <h2>现在的分数是{count}</h2>
           <button onClick={()=>dispatch('add')}>Increment</button>
           <button onClick={()=>dispatch('sub')}>Decrement</button>
       </div>
    )

}

export default ReducerDemo
```
这段代码是useReducer的最简单实现了，这时候可以在浏览器中实现了计数器的增加减少。

# 七、useMemo优化React Hooks程序性能
useMemo主要用来解决使用React hooks产生的无用渲染的性能问题。使用function的形式来声明组件，失去了shouldCompnentUpdate（在组件更新之前）这个生命周期，也就是说我们没有办法通过组件更新前条件来决定组件是否更新。而且在函数组件中，也不再区分mount和update两个状态，这意味着函数组件的每一次调用都会执行内部的所有逻辑，就带来了非常大的性能损耗。useMemo和useCallback都是解决上述性能问题的，这节先学习useMemo.

### 性能问题展示案例
先编写一下刚才所说的性能问题，建立两个组件,一个父组件一个子组件，组件上由两个按钮，一个是A，一个是B，点击哪个，哪个就向我们走来了。然后先写第一个父组件。
```js
import React , {useState,useMemo} from 'react';

function Example(){
    const [a , setA] = useState('A状态')
    const [b , setB] = useState('B状态')
    return (
        <div>
            <button onClick={()=>{setA(new Date().getTime())}}>A</button>
            <button onClick={()=>{setB(new Date().getTime()+',B向我们走来了')}}>B</button>
            <ChildComponent name={a}>{b}</ChildComponent>
        <div/>
    )
}
```
父组件调用了子组件，子组件我们输出组件的状态，显示在界面上。代码如下：
```js
function ChildComponent({name,children}){
    function changeA(name){
        console.log('她来了，她来了。A向我们走来了')
        return name+',A向我们走来了'
    }

    const actionA = changeA(name)
    return (
        <div>
            <div>{actionA}</div>
            <div>{children}</div>
        </div>
    )
}
```
这时候你会发现在浏览器中点击B按钮，A对应的方法都会执行，结果虽然没变，但是每次都执行，这就是性能的损耗。目前只有子组件，业务逻辑也非常简单，如果是一个后台查询，这将产生严重的后果。所以这个问题必须解决。当我们点击B按钮时，对应的changeA方法不能执行，只有在点击A按钮时才能执行。

### useMemo 优化性能
其实只要使用useMemo，然后给A组件传递第二个参数，参数匹配成功，才会执行。代码如下：
```js
function ChildComponent({name,children}){
    function changeA(name){
        console.log('来了，来了。A向我们走来了')
        return name+',A向我们走来了'
    }

    const actionA = useMemo(()=>changeA(name),[name]) 
    return (
        <div>
            <div>{actionA}</div>
            <div>{children}</div>
        </div>
    )
}
```
这时在浏览器中点击一下B按钮，changeA就不再执行了。也节省了性能的消耗。案例只是让你更好理解，你还要从程序本身看到优化的作用。好的程序员对自己写的程序都是会进行不断优化的，这种没必要的性能浪费也是绝对不允许的，所以useMemo的使用在工作中还是比较多的。

### useMemo和useCallback对比

useCallback和useMemo的参数跟useEffect一致，他们之间最大的区别有是useEffect会用于处理副作用，而前两个hooks不能。

useMemo和useCallback都会在组件第一次渲染的时候执行，之后会在其依赖的变量发生改变时再次执行；并且这两个hooks都返回缓存的值，useMemo返回缓存的变量，useCallback返回缓存的函数。

useCallback跟useMemo比较类似，但它返回的是缓存的函数。我们看一下最简单的用法：
```js
useMemo( ()=>{fn} ) 等价于 useCallback(fn)
```
这里就不多介绍了，如有想了解更多关于两者区别，可查找相关资料~

# 八、useRef获取DOM元素和保存变量
useRef在工作中虽然用的不多，但是也不能缺少。它有两个主要的作用:
- 用useRef获取React JSX中的DOM元素，获取后你就可以控制DOM的任何东西了。但是一般不建议这样来做，React界面的变化可以通过状态来控制。

- 用useRef来保存变量，这个在工作中也很少能用到，我们有了useContext这样的保存其实意义不大，简单的提一下。

### useRef获取DOM元素
界面上有一个文本框，在文本框的旁边有一个按钮，当我们点击按钮时，在控制台打印出input的DOM元素，并进行复制到DOM中的value上。这一切都是通过useRef来实现。
```js
import React, { useRef} from 'react';
function Example(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello World"
        console.log(inputEl) //输出获取到的DOM节点
    }
    return (
        <div>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
        </div>
    )
}
export default Example
```
当点击按钮时，你可以看到在浏览器中的控制台完整的打印出了DOM的所有东西，并且界面上的<input/>框的value值也输出了我们写好的Hello World。这一切说明我们可以使用useRef获取DOM元素，并且可以通过useRefu控制DOM的属性和值。

### useRef保存普通变量
这个操作在实际开发中用的并不多，但我们还是要讲解一下。就是useRef可以保存React中的变量。我们这里就写一个文本框，文本框用来改变text状态。又用useRef把text状态进行保存，最后打印在控制台上。写这段代码你会觉的很绕，其实显示开发中没必要这样写，用一个state状态就可以搞定，这里只是为了展示知识点。

接着上面的代码来写，就没必要重新写一个文件了。先用useState声明了一个text状态和setText函数。然后编写界面，界面就是一个文本框。然后输入的时候不断变化。
```js
import React, { useRef ,useState,useEffect } from 'react';



function Example(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    const [text, setText] = useState('jspang')
    return (
        <div>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />
         
        </div>
    )
}

export default Example
```
这时想每次text发生状态改变，保存到一个变量中或者说是useRef中，这时候就可以使用useRef了。先声明一个textRef变量，他其实就是useRef函数。然后使用useEffect函数实现每次状态变化都进行变量修改，并打印。最后的全部代码如下.
```js
import React, { useRef ,useState,useEffect } from 'react';
function Example(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    //-----------关键代码--------start
    const [text, setText] = useState('jspang')
    const textRef = useRef()

    useEffect(()=>{
        textRef.current = text;
        console.log('textRef.current:', textRef.current)
    })
    //----------关键代码--------------end
    return (
        <div>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />
        </div>
    )
}

export default Example
```
这时候就可以实现每次状态修改，同时保存到useRef中了。也就是我们说的保存变量的功能。那useRef的主要功能就是获得DOM和变量保存，我们都已经讲过了。

# 九、自定义Hooks函数获取窗口大小
其实自定义Hooks函数和用Hooks创建组件很相似，跟我们平时用JavaScript写函数几乎一模一样，可能就是多了些React Hooks的特性，自定义Hooks函数偏向于功能，而组件偏向于界面和业务逻辑。由于差别不大，所以使用起来也是很随意的。如果是小型项目是可以的，但是如果项目足够复杂，这会让项目结构不够清晰。所以学习自定义Hooks函数还是很有必要的。

### 编写自定义函数
在实际开发中，为了界面更加美观。获取浏览器窗口的尺寸是一个经常使用的功能，这样经常使用的功能，就可以封装成一个自定义Hooks函数，记住一定要用use开头，这样才能区分出什么是组件，什么是自定义函数。

编写一个useWinSize,编写时我们会用到useState、useEffect和useCallback所以先用import进行引入。
```js
import React, { useState ,useEffect ,useCallback } from 'react';
```
然后编写函数，函数中先用useState设置size状态，然后编写一个每次修改状态的方法onResize，这个方法使用useCallback，目的是为了缓存方法(useMemo是为了缓存变量)。 然后在第一次进入方法时用useEffect来注册resize监听时间。为了防止一直监听所以在方法移除时，使用return的方式移除监听。最后返回size变量就可以了。
```js
function useWinSize(){
    const [ size , setSize] = useState({
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
    })

    const onResize = useCallback(()=>{
        setSize({
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        })
    },[]) 
    useEffect(()=>{
        window.addEventListener('resize',onResize)
        return ()=>{
            window.removeEventListener('resize',onResize)
        }
    },[])

    return size;

}
```
这就是一个自定义函数，其实和我们以前写的JS函数没什么区别，所以这里也不做太多的介绍。

### 编写组件并使用自定义函数
自定义Hooks函数已经写好了，可以直接进行使用，用法和JavaScript的普通函数用起来是一样的。直接在Example组件使用useWinSize并把结果实时展示在页面上。
```js
function Example(){

    const size = useWinSize()
    return (
        <div>页面Size:{size.width}x{size.height}</div>
    )
}

export default Example
```
之后就可以在浏览器中预览一下结果，可以看到当我们放大缩小浏览器窗口时，页面上的结果都会跟着进行变化。说明自定义的函数起到了作用。