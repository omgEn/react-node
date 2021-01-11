## 自述

Redux是JavaScript状态容器。WordPress框架

可以运行于不同的环境（客户端、服务器、原生应用），易于测试。

redux除了和Reacr一起用，还支持其他界面库。它只有2kb，包括依赖。

redux是由flux演变来的，但受elm的启发，避开了flux的复杂性。

npm install -S redux 

**redux是一些CommonJS模块的集合**。这些模式是在使用webpack、borwserify、或Node环境时引入的。最新用Rollup。

redux源文件由ES2015编写，但会预编译到CommonJS和YUMD规范的ES5，所以它支持任何现代浏览器。不必非得使用Babel或模块打包器来使用。**啥意思**

react绑定库和开发者工具：(很多redux生态下的包不提供UMD文件)

npm install react-redux -S

npm install redux-devtools -S

**要点**

**应用中的所有的state都一个对象树的形式存储在一个单一的store中**。唯一改变的方式是触发action。为了描述action如何改变state，需要编写reducers

````js
import {createStore} from 'redux';
/*
这是一个reducer:(state,action)=>state 的纯函数
描述了action怎么把state改成下一个state
当state变化时需要返回全新的对象，而不是修改传入的参数
*/
//不是直接修改state，而是通过对象action
//这是复制了一份吗？？
function counter(state=0,action){
    switch(action.type){
        case "INCREMENT":
            return state + 1;
        case "DECREMENT":
            return state - 1;
        default:
            return state;
    }
}
let store = createStore(counter);
//API 是 {subscribe,dispatch,getState}

//可以手动订阅，也可以把事件绑定到视图层
store.subscribe(()=>{
    console.log(store.getState())
})
//改变内部state唯一方法是dispatch一个action
//action可以被序列化，用日记记录和存储下来，后期还可以以回放的方式执行
//即可用action追溯应用的每一次修改
store.dispatch({type:"INCREMENT"});//1
store.dispatch({type:"INCREMENT"});//2
store.dispatch({type:"DECREMENT"});//1
````

Flux和Redux的区别：Redux没有dispatcher且不支持多个soter。只有单一的store和一个根级的reducer函数。可把根级reducer拆成多个小的reducers，分别独立操作state树的不同部分，而不是添加新的store。

## 介绍

* 动机

  state：服务器响应、缓存数据、本地尚未持久化到服务器的数据，UI状态：如激活的路由、被选中的标签、是否加载动效或分页器等。

  当view变化时，对应model变化，可能引起另一个view的变化，错综复杂。

  这里的复杂程度很大长度来自于：变化和异步。

  一些库如React视图在视图层禁止异步和直接操作DOM来解决这个问题。但处理state中的数据问题依旧留给了程序员，redux就是解决这个问题的。

  Flux、CQRS、Event Sourcing,**通过限制更新发生的时间和方式**，**redux视图让sate的变化变得可预测**

* 核心概念

  todo应用中的state长这样

  ````js
  {
    todos: [{
      text: 'Eat food',
      completed: true
    }, {
      text: 'Exercise',
      completed: false
    }],
    visibilityFilter: 'SHOW_COMPLETED'
  }
  ````

  这个对象没有setter，因此其他代码不能随意修改它。要修改这个对象要发起一个action。为了把action和state连接起来，通过函数reducer(state,action)=>return state。

  ````JS
  //action的示例
  { type: 'ADD_TODO', text: 'Go to swimming pool' }
  { type: 'TOGGLE_TODO', index: 1 }
  { type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
  ````

  ````js
  //两个小的reducer
  function visibilityFilter(state = 'SHOW_ALL', action) {
    if (action.type === 'SET_VISIBILITY_FILTER') {
      return action.filter;
    } else {
      return state;
    }
  }
  
  function todos(state = [], action) {
    switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }]);
    case 'TOGGLE_TODO':
      return state.map((todo, index) =>
        action.index === index ?
          { text: todo.text, completed: !todo.completed } :
          todo
     )
    default:
      return state;
    }
  }
  //一个大的reducer调用这两个reducer，进而管理整个应用的state
  function todoApp(state={},action){
      return{
          todos:todos(state.todos,action);
  		visibilityFilter:visibilityFilter(state.visibilityFilter, action)
      }
  }
  ````

* 三大原则

  * 单一数据源

    数据存在惟一的state中。

    state是保存在服务端。在开发过中，可有应用的state保存在本地，加快开发速度。

    “撤销/重做”这类功能能实现

    console.log(store.getState())

  * state是只读的

    唯一改变state的方法是触发action。

    确保了视图和网络请求都不能直接修改state，**只能表达想要修改的意图。**所有修改被集中化处理，按顺序执行，不用担心race condition（竞争关系）的出现

    action就是普通对象，因此可以被日志打印、序列化、存储、后期调试或测试时回放出来。

    ````
    store.dispatch({
      type: 'COMPLETE_TODO',
      index: 1
    })
    
    store.dispatch({
      type: 'SET_VISIBILITY_FILTER',
      filter: 'SHOW_COMPLETED'
    })
    ````

  * 用纯函数来执行修改

    reducer=(state,action)=>return state

    ````js
    import { combineReducers, createStore } from 'redux'
    let reducer = combineReducers({ visibilityFilter, todos })
    let store = createStore(reducer)
    ````

* 先前技术

  flux

* 生态系统

* 示例

  

## 基础

### Action

Action本质是JavaScript普通对象。约定type表示要执行的动作，

### Reducer

**设计state结构**

以todo应用为例，需要保存两种不同的数据：

* 当前选中的任务过滤条件
* 完整的任务列表

尽量把数据与UI相关的state分开

**Action处理**

reducer是一个纯函数

````js
(previousState,action)=>newState
````

[`Array.prototype.reduce(reducer, ?initialValue)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 里的回调函数属于相同的类型??

保持reducer纯净，不要在reducer里做下列操作：

* 修改传入参数
* 执行有副作用的操作，如API请求和路由跳转
* 调用非存函数，如Date.now()或Math.random()

纯函数：只要传入参数相同，返回计算得到的下一个state就一定相同。不理解？

注意：

* 不要修改state。

* 在default情况下返回旧的state。

  遇到未知的action，一定要返回旧的state

**处理多个action**

**拆分reducer**

### Store

**Store**

前进介绍了action描述“发生了什么”，使用reducers来根据action更新state的用法。

Store就是它们联系到一起的对象。Store有以下职责：

* 维持应用的state
* getState()
* dispatch(action)更新state
* subscribe(listener)注册监听器
* subscribe(listener) 通过返回的函数注销监听器

redux是单一的store，当需要拆分数据处理逻辑时，应该使用reducer组合而不是创建多个store

用combineReducers()将多个reducer合并成为一个。

````js
import {createStore} from 'redux';
import todoApp from './reducers';
let store = createStore(todoApp);
````

createStore()第二个参数是可选的，用于设置state初始状态。可让服务端的redux应用的state与客户端的保持一致，初始化

````js
let store = createStore(todoApp,window.SEATE_FROM_SERVER);
````

**发起初始化**

````js
import {
  addTodo,
  toggleTodo,
  setVisibilityFilter,
  VisibilityFilters
} from './actions'

// 打印初始状态
console.log(store.getState())

//每次state更新时，打印日志
//subscribe()返回一个函数用来注销监听器
const unsubscribe = store.subscribe(()=>{
    console.log(store.getState())
})

//发起一系列action
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(toggleTodo(0))
store.dispatch(toggleTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED)

//停止监听state 更新
unsubscribe();
````

### 数据流

严格的单向数据流

Redux应用中数据的生命周期遵循下列几个步骤

1. 调用store.dispatch(action)

   action就是一个描述“发生了什么”的普通对象

   ````js
    { type: 'LIKE_ARTICLE', articleId: 42 }
    { type: 'FETCH_USER_SUCCESS', response: { id: 3, name: 'Mary' } }
    { type: 'ADD_TODO', text: 'Read the Redux docs.' }
   ````

2. Redux store调用传入的reducer函数

   reducer(state,action)

   ````js
   // 当前应用的 state（todos 列表和选中的过滤器）
    let previousState = {
      visibleTodoFilter: 'SHOW_ALL',
      todos: [
        {
          text: 'Read the docs.',
          complete: false
        }
      ]
    }
   
    // 将要执行的 action（添加一个 todo）
    let action = {
      type: 'ADD_TODO',
      text: 'Understand the flow.'
    }
   
    // reducer 返回处理后的应用状态
    let nextState = todoApp(previousState, action)
   ````

   

3. 根reducer应该把多个reducer输出合并成单一的state树

   ````js
   function todos(state = [], action) {
       // 省略处理逻辑...
       return nextState
   }
   
   function visibleTodoFilter(state = 'SHOW_ALL', action) {
       // 省略处理逻辑...
       return nextState
   }
   
   let todoApp = combineReducers({
       todos,
       visibleTodoFilter
   })
   ````

   

4. Redux store保存了根reducer返回的完整state树

   所有订阅`store.subscribe(listener)`的监听器都被调用

   更新state:`component.setState(newState)`

### 搭配React

Redux支持React、Angular、Ember、jQuery、JavaScript

Redux默认不会包含react绑定库，需要单独安装

npm install --save react-redux

------

**设计组件层次关系**

|               | 展示组件            | 容器组件                           |
| ------------- | ------------------- | ---------------------------------- |
| 作用          | 骨架，样式          | 描述如何运行（数据获取、状态更新） |
| 直接使用Redux | 否                  | 是                                 |
| 数据来源      | props               | 监听Redux state                    |
| 数据修改      | 从props调用回调函数 | 向Redux派发actions                 |
| 调用方式      | 手动                | 通常由React Redux生成              |

**展示组件** component

* TodoList 用于显示todos列表
  * todos:Array 以{text,completed}形式显示的todo项数组
  * onTodoClick(index:number)  当todo项被点击时调用的回调函数
* Todo 一个todo项目
  * text:string	显示的文本内容
  * completed:boolean todo项是否显示删除线
  * onClick() 当todo项被点击时 调用的回调函数
* Link 带有callback回调功能的链接
  * onClick() 当点击链接时会触发
* Footer  一个允许用户改变可见todo过滤器的组件
* App  根组件，渲染余下的所有内容

这些组件之定义外观，并不关心来源和如何改变。传入什么就渲染什么。

**容器组件** container

用于把展示组件连接到Redux。

* visibleTodoList 根据当前显示的状态来对todo列表进行过滤，并渲染TodoList
* FilterLink  得到当前过滤器并渲染Link
  * filter:string 就是当前过滤的状态

**其他组件**

有时很难分清该使用容器组件还是展示组件。例如：

* addTodo   含有"Add"按钮的输入框

技术上是可以拆分成两个的。是小项目时候不用拆分。大项目拆分

------

**组件编码**

**实现容器组件**

connect()合成redux，能减少不必要的重复渲染。就不用使用shouldComponentUpdate

mapStateToProps 把当前Redux  store state映射到组件的props。

### 示例：Todo List



## 高级

本章介绍如何使用AJAX和路由

### 异步Action

上章创建了简单的todo应用。但只有同步操作。没当dispatch action时，state会立即被更新。

本章，将开发一个异步的应用。将使用Reddit API来获取并显示指定subreddit下的帖子列表。那么Redux要如何处理异步数据流呢？

**Action**

当异步调用API时，有两个很重要的时刻：发起请求和接收到响应（可能超时）。

这两个时刻都会更改应用的state。

一般情况下，每个API请求都需要dispatch至少三种action

* 一种通知reducer**请求开始**的action

  切换state的isFetching标记。以此告诉告诉UI显示加载页面

* 一种通知reducer**请求成功**的action

* **请求失败**的action

为了区分这三种action

````js
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }
//或者
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
````

使用多个type会较低错误的记录，但若用redux-actions这类的辅助库来生成action创建函数和reducer的话，就不是问题了。

无论用那种规定，一定要在整个应用中保持统一。

**同步Action创建函数(Action Creator)**

用户可选择要显示的subreddit——由用户控制的action

````js
export const SELECT_SUBREDDIT = 'SELECT_SUBREDDIT'

export function selectSubreddit(subreddit) {
  re turn {
    type: SELECT_SUBREDDIT,
    subreddit
  }
}
//或用刷新按钮
export const INVALIDATE_SUBREDDIT = 'INVALIDATE_SUBREDDIT'

export function invalidatesubreddit(subreddit) {
  return {
    type: INVALIDATE_SUBREDDIT,
    subreddit
  }
}
````

当需要获取指定的subreddit的帖子的时候，需要：——网络请求

````js
export const REQUEST_POSTS = 'REQUEST_POSTS'

export function requestPosts(subreddit) {
  return {
    type: REQUEST_POSTS,
    subreddit
  }
}
````

一开始就要把数据和特定的UI事件分开。

把 `REQUEST_POSTS` 和 `SELECT_SUBREDDIT` 或 `INVALIDATE_SUBREDDIT` 分开很重要。虽然他们又先后顺序，但有些用户操作（预加载：一段时间后自动刷新过期数据）后需要马上请求数据。路由变化时也可能需要请求数据。

最后，接收到响应式，用dispatch `RECEIVE_POSTS`：

````js
export const RECEIVE_POSTS = 'RECEIVE_POSTS'

export function receivePosts(subreddit, json) {
  return {
    type: RECEIVE_POSTS,
    subreddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  }
}
````

稍后介绍如何把disoatch action与网络请求结合起来。

错误处理须知：在实际应用中，网络请求失败也需要dispacth action。

**设计state结构**

注意对内容范式化

**处理Action**

**异步action创建函数**

如何把之前定义的同步action创建函数和网络请求结合起来。标准做法是用redux-thunk中间件。

通过制定的middleware，action创建函数除了返回aciton对象还可以返回函数。这时，action创建函数就成为了thunk。

当action创建函数返回函数时，这个函数会被redux thunk middleware执行。这个函数不需要保持纯净，可带有副作用，包括执行异步API请求。这个函数还可dispatch action，就像前面定义的同步action一样。

> 服务端渲染须知：
>
> 创建一个store，dispatch一个异步action创建函数，这个action创建函数有dispatch另一个异步action来为应用的一整块请求数据，同时在Promise完成和结束时才render界面。在render前，store就已经存在了需要用的state。

**连接到UI**

dispatch同步action与异步action间并没有区别。

参照搭配React获得React组件中使用Redux的介绍。

### 异步数据流

像redux-thunk或redux-promise这样支持异步的middleware都包装了store的dispatch方法。，以此让dispatch一些除了action以外的其他内容。

当middleware链中的最后一个middleware开始dispatch action，这个action必须是一个普通对象。

### Middleware

服务端框架Express，Koa。

Express 或者 Koa 的 middleware 可以完成添加 CORS headers、记录日志、内容压缩等工作

middleware 最优秀的特性就是可以被链式组合。你可以在一个项目中使用多个独立的第三方 middleware。

**理解Middleware**

middlw可以完成包括异步API调用在内的各种事情。

-----

**问题：记录日志**

redux好处：每当action发起完成后，新的state就会被计算并保存下来。state不能被自身修改，只能由特定的action引起变化

**1.手动记录**

**2.封装Dispatch**

**3.Monkeypatching Diapatch**

----

**问题：奔溃报告**



### 搭配React Router

若想在Redux中应用路由，react-router

Redux 和 React Router 将分别成为你数据和 URL 的事实来源

````js
import { Provider } from 'react-redux';
import { Router, Route, browserHistory } from 'react-router';
const Root = ({ store }) => (
  <Provider store={store}>
    <Router>
      <Route path="/" component={App} />
    </Router>
  </Provider>
);
<Route path="/(:filter)" component={App} />
````



### 示例：Reddit API



## 技巧
