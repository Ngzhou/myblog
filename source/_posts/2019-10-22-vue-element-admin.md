---
title: vue及vue-element-amdin框架介绍
comments: true
date: 2019-10-22 10:11:37
tags:
- vue
categories:
- WEB前端
author: jun.zhou
---



# 一、vue简介
Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

##### 渐进式框架的理解
提供足够的选择，并且没有很多强制性的要求。
渐进也可以理解为一步一步的意思，大概意思就是使用Vue的时候，并不需要把整个框架的所有东西都用上，可以根据实际情况选择你需要的部分。

##### 自底向上逐层应用
由基层开始做起，把基础的东西写好，再逐层往上添加效果和功能。

Vue 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件。
```
<div id="app">
  {{ message }}
</div>

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

先看一个简单实例代码结构：
```
<template>
  <div class="app-selection-banner">
    <el-row :gutter="16">
      <el-col :span="6">
        <banner-preview :list="list" :show-user-types="showUserTypes" />
      </el-col>
      <el-col :span="18">
        <banner-table :list="list" :user-type-options="userTypeOptions" @updateList="getList" />
      </el-col>
    </el-row>
  </div>
</template>

<script>
import BannerPreview from './components/BannerPreview'
import BannerTable from './components/BannerTable'
import { bannerList } from '@/api/selection'
import { userTypeInfo } from '@/api/common'
export default {
  name: 'BannerSelection',
  components: { BannerPreview, BannerTable },
  data() {
    return {
      list: null,
      userTypeOptions: [],
      showUserTypes: []
    }
  },
  created() {
    this.getList()
    userTypeInfo({ version: '1.0' }).then(response => {
      this.userTypeOptions = response.data
      this.showUserTypes = (response.data || []).filter((userType) => {
        return userType.type !== '0'
      })
    })
  },
  methods: {
    getList() {
      bannerList().then(response => {
        const { items } = response.data || []
        this.list = items.sort((a, b) => b.status - a.status)
      }).catch(err => console.log(err))
    }
  }
}
</script>

<style lang="scss" scoped>
.app-selection-banner{
  background-color:#eee;
  padding:16px;
  .el-col {
    padding: 0 6px;
  }
}
</style>

```

组件BannerPreview.vue : 
```
<template>
  <div class="md-wrap">
    <div class="md-title">预览区</div>
    <div class="md-text">
      <div v-for="item in showUserTypes" :key="item.type" class="block">
        <span class="demonstration">{{ item.name }}</span>
        <el-carousel v-if="previewList(item.type).length" :autoplay="false" arrow="never" trigger="click" height="100px">
          <el-carousel-item v-for="(child, key) in previewList(item.type)" :key="key">
            <a :href="child.link" target="_blank">
              <img class="list-img" :src="child.imgUrl">
            </a>
          </el-carousel-item>
        </el-carousel>
        <div v-else class="no-data">暂无数据</div>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'BannerPreview',
  props: {
    list: {
      type: Array,
      default: null
    },
    showUserTypes: {
      type: Array,
      default: null
    }
  },
  methods: {
    previewList(type) {
      const newArr = this.list || []
      return newArr.length > 0 && newArr.filter((item) => {
        const { userType } = item || ''
        return item.status === '2' && (userType.includes(type) || userType.includes('0'))
      }).slice(0, 5)
    }
  }
}
</script>

<style lang="scss" scoped>
.md-wrap{
  background-color:#fff;
  .md-title{
    line-height: 50px;
    border-bottom:1px #eee solid;
    padding-left: 16px;
  }
  .md-text{
    padding: 16px;
    .no-data{
      font-size: 14px;
      color: #999;
      padding: 10px 0;
    }
    .demonstration{
      display: inline-block;
      padding-bottom: 10px;
    }
    .block{
      margin-bottom: 20px;
    }
    .list-img{
      height: 100%;
      width: 100%;
    }
  }

  .el-carousel__item h3 {
    color: #475669;
    font-size: 14px;
    opacity: 0.75;
    line-height: 100px;
    margin: 0;
    text-align: center;
  }

  .el-carousel__item:nth-child(2n) {
     background-color: #99a9bf;
  }

  .el-carousel__item:nth-child(2n+1) {
     background-color: #d3dce6;
  }
}
</style>

```

#### 生命周期

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。

比如 created 钩子可以用来在一个实例被创建之后执行代码：
```
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```
也有一些其它的钩子，在实例生命周期的不同阶段被调用，如 mounted、updated 和 destroyed。生命周期钩子的 this 上下文指向调用它的 Vue 实例。

*<font color="red">注意：</font>不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数并没有 this，this 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。*

#### 生命周期图示
下图展示了实例的生命周期。你不需要立马弄明白所有的东西，不过随着你的不断学习和使用，它的参考价值会越来越高。
![](https://img.fengjr.com/image/2019/10/22/b3251a15e5779fcfec925b78a149f5c8.png)

#### 计算属性
模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。例如：
```
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```
在这个地方，模板不再是简单的声明式逻辑。你必须看一段时间才能意识到，这里是想要显示变量 message 的翻转字符串。当你想要在模板中多次引用此处的翻转字符串时，就会更加难以处理。

所以，对于任何复杂逻辑，你都应当使用计算属性。

基础例子:
```
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})

```
Original message: "Hello"
Computed reversed message: "olleH"

#### 计算属性缓存 vs 方法
你可能已经注意到我们可以通过在表达式中调用方法来达到同样的效果：
```
<p>Reversed message: "{{ reversedMessage() }}"</p>

// 在组件中
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。

这也同样意味着下面的计算属性将不再更新，因为 Date.now() 不是响应式依赖
```
computed: {
  now: function () {
    return Date.now()
  }
}
```
相比之下，每当触发重新渲染时，调用方法将总会再次执行函数。

我们为什么需要缓存？假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用方法来替代。

#### 计算属性 vs 侦听属性
Vue 提供了一种更通用的方式来观察和响应 Vue 实例上的数据变动：侦听属性。当你有一些数据需要随着其它数据变动而变动时，你很容易滥用 watch——特别是如果你之前使用过 AngularJS。然而，通常更好的做法是使用计算属性而不是命令式的 watch 回调。细想一下这个例子：

```
<div id="demo">{{ fullName }}</div>
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})

```
上面代码是命令式且重复的。将它与计算属性的版本进行比较：
```
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```
好得多了，不是吗？

#### 侦听器
虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 watch 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

例子：
```
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>

<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```
在这个示例中，使用 watch 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。


# 二、Vue与React两个框架的区别和优势对比

Vue和React两个JavaScript框架都是当下比较受欢迎的，他们两者之间的区别有哪些，各自的优缺点是什么？

#### 简单介绍

除非你一直不关注前端的发展，不然你肯定听说过由Facebook创建的JavaScript UI框架——React。它支撑着包括Instagram在内的大多数Facebook网站。React与当时流行的jQuery,Backbone.js和Angular 1等框架不同，它的诞生改变了JavaScript的世界。其中最大的变化是React推广了Virtual DOM并创造了新的语法——JSX，JSX允许开发者在JavaScript中书写HTML（译者注：即HTML in JavaScript）。

Vue致力解决的问题与React一致，但却提供了另外一套解决方案。Vue使用模板系统而不是JSX，使其对现有应用的升级更加容易。这是因为模板用的就是普通的HTML，通过Vue来整合现有的系统是比较容易的，不需要整体重构。同时Vue声称它更容易学习，我们最近接触Vue，就能证明所言非虚。关于Vue还需要说的是，Vue主要是由一位开发者进行维护的，而不像React一样由如Facebook这类大公司维护。

#### 相似之处
React与Vue存在很多相似之处：
- 使用 Virtual DOM
- 提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
- 将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。

#### Virtual DOM
啊哈，人们经常说Virtual DOM是什么呢？

Vue.js(2.0版本)与React的其中最大一个相似之处，就是他们都使用了一种叫'Virtual DOM'的东西。所谓的Virtual DOM基本上说就是它名字的意思：虚拟DOM，DOM树的虚拟表现。它的诞生是基于这么一个概念：改变真实的DOM状态远比改变一个JavaScript对象的花销要大得多。

Virtual DOM是一个映射真实DOM的JavaScript对象，如果需要改变任何元素的状态，那么是先在Virtual DOM上进行改变，而不是直接改变真实的DOM。当有变化产生时，一个新的Virtual DOM对象会被创建并计算新旧Virtual DOM之间的差别。之后这些差别会应用在真实的DOM上。

例子如下，我们可以看看下面这个列表在HTML中的代码是如何写的：
```
<ul class="list">
  <li>item 1</li>
  <li>item 2</li>
</ul>
```
而在JavaScript中，我们可以用对象简单地创造一个针对上面例子的映射：
```
{
    type: 'ul', 
    props: {'class': 'list'}, 
    children: [
        { type: 'li', props: {}, children: ['item 1'] },
        { type: 'li', props: {}, children: ['item 2'] }
    ]
}
```

真实的Virtual DOM会比上面的例子更复杂，但它本质上是一个嵌套着数组的原生对象。

当新一项被加进去这个JavaScript对象时，一个函数会计算新旧Virtual DOM之间的差异并反应在真实的DOM上。计算差异的算法是高性能框架的秘密所在，React和Vue在实现上有点不同。

Vue宣称可以更快地计算出Virtual DOM的差异，这是由于它在渲染过程中，会跟踪每一个组件的依赖关系，不需要重新渲染整个组件树。

而对于React而言，每当应用的状态被改变时，全部子组件都会重新渲染。当然，这可以通过shouldComponentUpdate这个生命周期方法来进行控制，但Vue将此视为默认的优化。


在react开发中，经常会遇到组件重复渲染的问题，父组件一个state的变化，就会导致以该组件的所有子组件都重写render，尽管绝大多数子组件的props没有变化

render什么时候会触发
首先，先上一张react生命周期图：
![](https://img.fengjr.com/file/2019/10/30/c7e8067e7fd11a07a1ecc0f643a4b90f.jpeg)
这张图将react的生命周期分为了三个阶段：生成期、存在期、销毁期，这样在create、props、state、unMount状态变化时我们可以清楚的看到reacte触发了哪些生命周期钩子以及什么时候会render。

如何避免这些不必要的render:
- shouldComponentUpdate()

```
shouldComponentUpdate(nextProps, nextState)
```
使用shouldComponentUpdate()以让React知道当前状态或属性的改变是否不影响组件的输出，默认返回ture，返回false时不会重写render，而且该方法并不会在初始化渲染或当使用forceUpdate()时被调用，我们要做的只是这样：
```
shouldComponentUpdate(nextProps, nextState) {
  return nextState.someData !== this.state.someData
}
```
但是，state里的数据这么多，还有对象，还有复杂类型数据，react的理念就是拆分拆分再拆分，这么多子组件，我要每个组件都去自己一个一个对比吗？？不存在的，这么麻烦，要知道我们的终极目标是不劳而获-_-

- React.PureComponent

React.PureComponent 与 React.Component 几乎完全相同，但 React.PureComponent 通过props和state的浅对比来实现 shouldComponentUpate()。如果对象包含复杂的数据结构，它可能会因深层的数据不一致而产生错误的否定判断(表现为对象深层的数据已改变视图却没有更新）

关注点：无论组件是否是 PureComponent，如果定义了 shouldComponentUpdate()，那么会调用它并以它的执行结果来判断是否 update。在组件未定义 shouldComponentUpdate() 的情况下，会判断该组件是否是 PureComponent，如果是的话，会对新旧 props、state 进行 shallowEqual 比较，一旦新旧不一致，会触发 update。

小结：如果你的应用中，交互复杂，需要处理大量的UI变化，那么使用Virtual DOM是一个好主意。如果你更新元素并不频繁，那么Virtual DOM并不一定适用，性能很可能还不如直接操控DOM。

#### 组件化

React与Vue都鼓励组件化应用。这本质上说，是建议你将你的应用分拆成一个个功能明确的模块，每个模块之间可以通过合适的方式互相联系。关于组件化的例子可以在这篇文章的中间部分被找到:

你可以认为组件就是用户界面中的一小块。如果让我来设计Facebook的UI界面，那么聊天窗口会是一个组件，评论会是另一个组件，不断更新的好友列表也会作为一个组件。

在Vue中，如果你遵守一定的规则，你可以使用单文件组件.
```
<template>
<li class="pasta-dish list-unstyled">
    <div class="row">
        <div class="col-md-3">
            <img :src="this.item.image" :alt="this.item.name" />
        </div>
        <div class="col-md-9 text-left">
            <h3>{{this.item.name}}</h3>
            <p>
                {{this.item.desc}}
            </p>
            <button v-on:click="addToOrderNew" class="btn btn-primary">Add to order</button> <mark>{{this.orders}}</mark>
        </div>
    </div>
</li>
</template>

<script>

export default {
    name: 'pasta-item',
    props: ['item'],
    data:  function(){
        return{
            orders: 0
        }
    },
    methods: {
        addToOrderNew: function(y){
            this.orders += 1;
            this.$emit('order');
        }
    }
}

</script>

<style src="./Pasta.css"></style>
```
正如上面你看到的例子中，HTML, JavaScript和CSS都写在一个文件之中。你不再需要在.vue组件文件中引入CSS，虽然这也是可以的。

React也是非常相似的，JavaScript与JSX被写入同一个组件文件中。
```
import React from "react";

class PastaItem extends React.Component {

    render() {
        const { details, index } = this.props;

        return (
            <li className="pasta-dish list-unstyled">
                <div className="row">
                    <div className="col-md-3">
                        <img src={details.image} alt={details.name} />
                    </div>
                    <div className="col-md-9 text-left">
                        <h3>{details.name}</h3>
                        <p>
                            {details.desc}
                        </p>
                        <button onClick={() => this.props.addToOrder(index)} className="btn btn-primary">Add to order</button> <mark>{this.props.orders || 0}</mark>
                    </div>
                </div>
            </li>
        );
    }
}

export default PastaItem;
```
Props
在上面两个例子中，我们可以看到React和Vue都有'props'的概念，这是properties的简写。props在组件中是一个特殊的属性，允许父组件往子组件传送数据。
```
Object.keys(this.state.pastadishes).map(key =>
    <PastaItem index={key} key={key} details={this.state.pastadishes[key]} addToOrder={this.addToOrder} orders={this.state.orders[key]} />
)
```
上面的JSX库组中，index, key, details, orders 与 addToOrder都是props，数据会被下传到子组件PastaItem中去。

在React中，这是必须的，它依赖一个“单一数据源”作为它的“状态”。

而在Vue中，props略有不同。它们一样是在组件中被定义，但Vue依赖于模板语法，你可以通过模板的循环函数更高效地展示传入的数据。
```
<pasta-item v-for="(item, key) in samplePasta" :item="item" :key="key" @order="handleOrder(key)"></pasta-item>
```
这是模板的实现，但这代码完全能工作，然而在React中展现相同数据会更麻烦一点。

#### 构建工具
React和Vue都有自己的构建工具，你可以使用它快速搭建开发环境。React可以使用Create React App (CRA)，而Vue对应的则是vue-cli。两个工具都能让你得到一个根据最佳实践设置的项目模板。

由于CRA有很多选项，使用起来会稍微麻烦一点。这个工具会逼迫你使用Webpack和Babel。而vue-cli则有模板列表可选，能按需创造不同模板，使用起来更灵活一点。

事实上说，两个工具都非常好用，都能为你建立一个好环境。而且如果可以不配置Webpack的话，我认为这是天大的好事。

#### Chrome 开发工具
React和Vue都有很好的Chrome扩展工具去帮助你找出bug。它们会检查你的应用，让你看到Vue或者React中的变化。你也可以看到应用中的状态，并实时看到更新。

React的开发工具: https://cdn.deliciousbrains.com/content/uploads/2017/06/15151112/react-devtools.mp4

Vue的开发工具: https://cdn.deliciousbrains.com/content/uploads/2017/06/15151111/vue-devtools.mp4

#### 配套框架
Vue与React最后一个相似但略有不同之处是它们配套框架的处理方法。相同之处在于，两个框架都专注于UI层，其他的功能如路由、状态管理等都交由同伴框架进行处理。

而不同之处是在于它们如何关联它们各自的配套框架。Vue的核心团队维护着vue-router和vuex，它们都是作为官方推荐的存在。而React的react-router和react-redux则是由社区成员维护，它们都不是官方维护的。

#### 主要区别
Vue与react有很多的相似之处，但他们也有完全不一致的地方。

模板 vs JSX：

在 React 中，一切都是 JavaScript。
在 React 中，所有的组件的渲染功能都依靠 JSX。
JSX 是使用 XML 语法编写 JavaScript 的一种语法糖。
```
import React, { Component } from 'react'

import { 
  SearchContainer,
  SearchContent
} from './styledComponent.js'

import search from 'images/search.png'

class Search extends Component {
  render () {
    return (
      <SearchContainer>
        <SearchContent { ...this.props }>
          <img src={search} alt=""/>
          <span>想吃什么搜这里，川菜</span>
        </SearchContent>
      </SearchContainer>
    )
  }
}

export default Search
```

vue是把html，css，js组合到一起，用各自的处理方式
Vue 设置样式的默认方法是单文件组件里类似 style 的标签。
```
<template>
  <div class="m-movie">
    <div class="white-bg topbar-bg">
      <div class="city-entry">
        <router-link tag="span" to="/cities" class="city-name">北京</router-link>
      </div>
      
      <div class="switch-hot">
        <router-link tag="div" to="/home/movies/intheater" active-class="active" class="hot-item">正在热映</router-link>
        <router-link tag="div" to="/home/movies/coming" active-class="active" class="hot-item">即将上映</router-link>
      </div>
    </div>
    <transition :name="transitionName">
      <router-view class="movies-outlet"></router-view>
    </transition>
  </div>
</template>

<script>
export default {
  data () {
    return {
      transitionName: ''
    }
  },
  watch: {
    $route (to, from) {
      if ( to.meta > from.meta ) {
        this.transitionName = 'slide-left'
      } else {
        this.transitionName = 'slide-right'
      }
    }
  }
}
</script>


<style lang="stylus" scoped>
@import '~styles/border.styl'
@import '~styles/variables.styl'

.slide-right-enter-active,
.slide-right-leave-active,
.slide-left-enter-active,
.slide-left-leave-active {
  transition: all 1s;
}

.slide-right-enter {
  opacity: 0;
  transform: translate3d(-100%, 0, 0);
}
.slide-right-leave-to {
  opacity: 0;
  transform: translate3d(100%, 0, 0);
}
.slide-left-enter {
  opacity: 0;
  transform: translate3d(100%, 0, 0);
}
.slide-left-leave-to {
  opacity: 0;
  transform: translate3d(-100%, 0, 0);
}
</style>
```

总结一下，我们发现

Vue的优势包括：
- 模板和渲染函数的弹性选择
- 简单的语法及项目创建
- 更快的渲染速度和更小的体积

React的优势包括：
- 更适用于大型应用和更好的可测试性
- 同时适用于Web端和原生App
- 更大的生态圈带来的更多支持和工具

而实际上，React和Vue都是非常优秀的框架，它们之间的相似之处多过不同之处，并且它们大部分最棒的功能是相通的：

- 利用虚拟DOM实现快速渲染
- 轻量级
- 响应式和组件化
- 服务器端渲染
- 易于集成路由工具，打包工具以及状态管理工具
- 优秀的支持和社区

# 三、vue-element-amdin介绍

vue-element-admin 是一个后台前端解决方案，它基于 vue 和 element-ui实现。它使用了最新的前端技术栈，内置了 i18 国际化解决方案，动态路由，权限验证，提炼了典型的业务模型，提供了丰富的功能组件，它可以帮助你快速搭建企业级中后台产品原型。

#### 功能
```
- 登录 / 注销

- 权限验证
  - 页面权限
  - 指令权限
  - 权限配置
  - 二步登录

- 多环境发布
  - dev sit stage prod

- 全局功能
  - 国际化多语言
  - 多种动态换肤
  - 动态侧边栏（支持多级路由嵌套）
  - 动态面包屑
  - 快捷导航(标签页)
  - Svg Sprite 图标
  - 本地/后端 mock 数据
  - Screenfull全屏
  - 自适应收缩侧边栏

- 编辑器
  - 富文本
  - Markdown
  - JSON 等多格式

- Excel
  - 导出excel
  - 导入excel
  - 前端可视化excel
  - 导出zip

- 表格
  - 动态表格
  - 拖拽表格
  - 内联编辑

- 错误页面
  - 401
  - 404

- 組件
  - 头像上传
  - 返回顶部
  - 拖拽Dialog
  - 拖拽Select
  - 拖拽看板
  - 列表拖拽
  - SplitPane
  - Dropzone
  - Sticky
  - CountTo

- 综合实例
- 错误日志
- Dashboard
- 引导页
- ECharts 图表
- Clipboard(剪贴复制)
- Markdown2html
```
本项目不支持低版本浏览器(如 ie)，有需求请自行添加 polyfill [详情](https://github.com/PanJiaChen/vue-element-admin/wiki#babel-polyfill)


#### 目录结构
```
├── build                      # 构建相关
├── mock                       # 项目mock 模拟数据
├── plop-templates             # 基本模板
├── public                     # 静态资源
│   │── favicon.ico            # favicon图标
│   └── index.html             # html模板
├── src                        # 源代码
│   ├── api                    # 所有请求
│   ├── assets                 # 主题 字体等静态资源
│   ├── components             # 全局公用组件
│   ├── directive              # 全局指令
│   ├── filters                # 全局 filter
│   ├── icons                  # 项目所有 svg icons
│   ├── lang                   # 国际化 language
│   ├── layout                 # 全局 layout
│   ├── router                 # 路由
│   ├── store                  # 全局 store管理
│   ├── styles                 # 全局样式
│   ├── utils                  # 全局公用方法
│   ├── vendor                 # 公用vendor
│   ├── views                  # views 所有页面
│   ├── App.vue                # 入口页面
│   ├── main.js                # 入口文件 加载组件 初始化等
│   └── permission.js          # 权限管理
├── tests                      # 测试
├── .env.xxx                   # 环境变量配置
├── .eslintrc.js               # eslint 配置项
├── .babelrc                   # babel-loader 配置
├── .travis.yml                # 自动化CI配置
├── vue.config.js              # vue-cli 配置
├── postcss.config.js          # postcss 配置
└── package.json               # package.json
```
#### 安装
```
# 克隆项目
git clone https://github.com/PanJiaChen/vue-element-admin.git

# 进入项目目录
cd vue-element-admin

# 安装依赖
npm install

# 建议不要用 cnpm 安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 本地开发 启动项目
npm run dev

```

[预览地址](https://panjiachen.github.io/vue-element-admin/)


# 四、fund-cms前端项目实例

基金cms管理系统是基于vue-element-amdin框架并通过upm单点登录方式实现的后台管理系统。数据库用的是mongodb实现并输出api接口。

#### 单点登录
进入页面判断cookie是否存在，不存在则跳到upm系统进行登录，登录成功自动跳回并带cookie参数。然后取到cookie值并手动种上，此时可以访问api接口。访问接口时如果cookie过期返回错误标识，前端判断自动调到upm登录系统重新登录。
```
// 获取upm单点登录 cookie
const { ticket } = param2Obj(window.location.search) || ''
if (ticket) {
  setToken(ticket)
  const newUrl = location.href.replace(location.search, '')
  window.location.href = newUrl
}

// 判断单点登录 cookie
if (!getToken()) {
  logoutRedirect()
}
```
```
/**
 * 退出并跳转
 */
export function logoutRedirect() {
  const toUrl = process.env.VUE_APP_BASE_TOLOGIN + '/auth?appkey=fund-cms&return=' + window.location.href
  if (process.env.NODE_ENV !== 'development') {
    window.location.href = toUrl
  }
}
```

#### 路由
静态路由：登录进入系统就可以看到的菜单
```
/**
 * constantRoutes
 * a base page that does not have permission requirements
 * all roles can be accessed
 */
export const constantRoutes = [
  {
    path: '/404',
    component: () => import('@/views/error-page/404'),
    hidden: true
  },
  {
    path: '',
    component: Layout,
    redirect: 'dashboard',
    children: [
      {
        path: 'dashboard',
        component: () => import('@/views/dashboard/index'),
        name: 'Dashboard',
        meta: { title: 'dashboard', icon: 'dashboard', affix: true }
      }
    ]
  },
  {
    path: '/selection',
    component: Layout,
    redirect: '/selection/module',
    alwaysShow: true, // will always show the root menu
    name: 'Selection',
    meta: {
      title: '精选',
      icon: 'shopping'
    },
    children: [
      {
        path: 'module',
        component: () => import('@/views/selection/module'),
        name: 'ModuleSelection',
        hidden: true,
        meta: {
          title: '模块管理'
        }
      }, {
        path: 'search',
        component: () => import('@/views/selection/search'),
        name: 'SearchSelection',
        meta: {
          title: '搜索'
        }
      }, {
        path: 'fengj',
        component: () => import('@/views/selection/fengj'),
        name: 'FengjSelection',
        meta: {
          title: '凤金精选'
        }
      }
    }
  }  
]
```

动态路由：需要用户权限匹配才能显示
```
export const asyncRoutes = [
  {
    path: '/permission',
    component: Layout,
    redirect: '/permission/page',
    alwaysShow: true, // will always show the root menu
    name: 'Permission',
    meta: {
      title: 'permission',
      icon: 'lock',
      roles: ['admin', 'editor'] // you can set roles in root nav
    },
    children: [
      {
        path: 'page',
        component: () => import('@/views/permission/page'),
        name: 'PagePermission',
        meta: {
          title: 'pagePermission',
          roles: ['admin'] // or you can only set roles in sub nav
        }
      },
      {
        path: 'directive',
        component: () => import('@/views/permission/directive'),
        name: 'DirectivePermission',
        meta: {
          title: 'directivePermission'
          // if do not set roles, means: this page does not require permission
        }
      },
      {
        path: 'role',
        component: () => import('@/views/permission/role'),
        name: 'RolePermission',
        meta: {
          title: 'rolePermission',
          roles: ['admin']
        }
      }
    ]
  }
]
```

#### api
接口api通过axios封装，支持环境变量配置。
```
import axios from 'axios'
import { Message } from 'element-ui'
// import store from '@/store'
import { removeToken } from '@/utils/auth'
import { logoutRedirect } from '@/utils'
// create an axios instance
const service = axios.create({
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url
  // withCredentials: true, // send cookies when cross-domain requests
  timeout: 5000 // request timeout
})
```
配置：selection.js
```
import request from '@/utils/request'
//get api
export function fengjList(params) {
  return request({
    url: '/fengj-management/fengjlist',
    method: 'get',
    params
  })
}
//post api
export function updateOpenStatus(data) {
  return request({
    url: '/module-management/openmodule/update',
    method: 'post',
    data
  })
}
```
调用：fengj.vue
```
import { fengjList } from '@/api/selection'
export default {
  name: 'FengjSelection',
  components: { FengjPreview, FengjTable },
  data() {
    return {
      list: null,
      userTypeOptions: [],
      showUserTypes: []
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      fengjList().then(response => {
        const { items } = response.data || []
        this.list = items.sort((a, b) => b.status - a.status)
      })
    }
  }
}
```

#### 开发遇到问题

1.状态排序
数据库里没有status字段，前端拿到数据后再重新排一下序。
```
methods: {
  getList() {
    fengjList().then(response => {
      const { items } = response.data || []
      this.list = items.sort((a, b) => b.status - a.status)
    })
  }
}
```

2.表单添加动态校验
基金产品输入基金code读api异步获取联动。
缺点是调两次api。
```
import { searchFundInfo } from '@/api/common'
data() {
  const checkCode = async(rule, value, callback) => {
    try {
      await searchFundInfo({ key: value })
      callback()
    } catch (err) {
      return callback(new Error(err.message))
    }
  }
  return {
    rules: {
      code: [{ required: true, message: '基金代码不能为空', trigger: 'blur' }, { validator: checkCode, trigger: 'blur' }]
    }
  }
}
```

3.列表组件格式化
```
<el-table-column label="用户类型" prop="userType" align="center" :formatter="formatter" />
methods: {
  formatter(row, column) {
    const { userType } = row
    const nameArr = []
    this.userTypeOptions.forEach((item) => {
      if (userType.includes(item.type)) {
        nameArr.push(item.name)
      }
    }
    return nameArr.join()
  }
}
```

4.子组件调用父组件方法
表单更新后，预览区实现自动更新。
```
this.$emit('updateList')
//父组件
<fengj-table :list="list" :user-type-options="userTypeOptions" @updateList="getList" />
```

5.阻止冒泡方法@click.stop
咨询列表点击显示预览区和字典联动，操作按钮需要做阻止冒泡处理。（阻止默认行为：@click.prevent）
```
<el-button type="success" size="mini" @click.stop="handleUpdate(row)">
  编辑
</el-button>
```

6.文本编辑器缓存问题
列表点编辑时候，文本编辑器缓存清不掉，显示上一次的内容,因为用的第三方插件。
解决办法添加更新方法，传时间戳，组件内监听更新。
```
<Tinymce ref="editor" v-model="temp.content" :update-con="updateCon" :height="200" />
//点编辑弹层显示时候
handleUpdate(row) {
  this.temp = Object.assign({}, row) // copy obj
  this.temp.startTime = new Date(this.temp.startTime)
  this.dialogStatus = 'update'
  this.dialogFormVisible = true
  this.$nextTick(() => {
    this.$refs['dataForm'].clearValidate()
    this.updateCon = new Date().getTime()
  })
}
```
Tinymce.vue
```
watch: {
    value(val) {
      if (!this.hasChange && this.hasInit) {
        this.$nextTick(() =>
          window.tinymce.get(this.tinymceId).setContent(this.val || ''))
      }
    },
    language() {
      this.destroyTinymce()
      this.$nextTick(() => this.initTinymce())
    },
    updateCon(val) {
      this.$nextTick(() =>
        window.tinymce && window.tinymce.get(this.tinymceId).setContent(this.newVal || ''))
    }
  }
}
```

7.文本编辑器扩展上传图片功能
Tinymce.vue
```
<template>
  <div :class="{fullscreen:fullscreen}" class="tinymce-container" :style="{width:containerWidth}">
    <textarea :id="tinymceId" class="tinymce-textarea" />
    <div class="editor-custom-btn-container">
      <editorImage color="#1890ff" class="editor-upload-btn" @successCBK="imageSuccessCBK" />
    </div>
  </div>
</template>
//上传成功图片插入文本编辑器中
imageSuccessCBK(arr) {
  const _this = this
  arr.forEach(v => {
    window.tinymce.get(_this.tinymceId).insertContent(`<img class="wscnph" src="${v.url}" >`)
  })
}
```
编辑上传图片组件：EditorImage.vue
```
<el-upload
  :multiple="true"
  :file-list="fileList"
  :show-file-list="true"
  :on-remove="handleRemove"
  :on-success="handleSuccess"
  :before-upload="beforeUpload"
  class="editor-slide-upload"
  :action="uploadUrl"
  list-type="picture-card"
>
  <el-button size="small" type="primary">
    点击上传
  </el-button>
</el-upload>

computed: {
  uploadUrl() {
    const url = process.env.VUE_APP_BASE_API + '/upload/uploadfile'
    return url
  }
}
```

开发总结：vue-element-admin框架搭建后台管理系统还是比较不错，架构清晰，功能全面，上手快。结合mongodb就可以前端一站式完成项目开发。