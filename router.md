https://reacttraining.com/react-router/

* 路由简介

  node(express)与react，vue，都是前端路由。

  后端路由：更根据用户的请求返回不同的内容

  前端路由：根据不同的url去切换页面（为了解决切换页面，感觉不到闪烁，不觉得切换了页面，想达到原生切换页面的体验）

  koa是express的第6个版本

  接口地址（后端，直接给数据）和页面地址（显示结构，在结构里面通过ajax在调用接口地址来获取数据）

* npm inistall react-router-dom

  import {BrowserRouter as Router }  from 'react-router-dom'//index.js

  <Router><App/></Router>

  <Route path="test1" component={Test1}/>历史记录模式

  import {Link,NavLink} from 'react-router-dom'

  NavLink有样式active，Link没有

* 路由两种模式

  * BrowserRouter   历史记录模式   二次刷新会丢失页面，需要后端重定向
  * HashRouter 模式  # 怎么刷新都不会丢失  不管怎么刷都不会变成404


### `<Redirect>`

类似于服务器端重定向（HTTP 3xx）

* to:string

  ````js
  <Redirect to="/somewhere/else" />
  ````

* to:object

  ````js
  <Redirect 
  	to={{
          pathname:"/login",
          search:"?utem=your+face",
          state:{ referrer:currentLocation}
  }}/>
  ````

  state 可通过this.props.location访问

  referrer 通过this.props.location.state.referrer访问 在pathname='/login'的Login中

* push:bool

  ````js
  <Redirect push to="/somewhere/else"/>
  ````

  true,会把这个推入历史堆栈中 但不是替换

* from:string

  重定向from的url到to

  ````js
  <Switch>
    <Redirect from='/old-path' to='/new-path' />
    <Route path='/new-path'><Place /></Route>
  </Switch>
  
  // Redirect with matched parameters
  <Switch>
    <Redirect from='/users/:id' to='/users/profile/:id'/>
    <Route path='/users/profile/:id'><Profile /></Route>
  </Switch>
  ````

* exact:bool

  match from exactly，类似于Route.exact

  ````js
  <Switch>
    <Redirect exact from="/" to="/home" />
    <Route path="/home"><Home /></Route>
    <Route path="/about"><About /></Route>
  </Switch>
  ````

* strict:bool

  match from stictly,类似于Route.strict?

  ````js
  <Switch>
    <Redirect strict from="/one/" to="/home" />
    <Route path="/home"><Home /></Route>
    <Route path="/about"><About /></Route>
  </Switch>
  ````

* sensitive:bool

  match from case sensitive;类似于Route.sensitive

### `<Route>`

* Route render methods

  路由有三种渲染方式

````js
//component
<Route path='/user/:username' component={User}/>
//render:func
<Route path='home' render={()=><div>Home</div> />
//children:func
<Route 
	path={to}
    children={({match})=>(
    	<li> className={match?"active":""}
        	<Link to={to} {...rest}></Link>
        </li>
    )}
></Route> 
//children:func 也可用于动画
<Route
	children={({match,...rest})=>(
        {/* Animate will always render, so you can use lifecycles
        to animate its child in and out */
        <Animate>{match&&<Something {...rest}/>}</Animate>
    )}
></Route>
````



#### Route props

route props 包含三个属性:match,location,history

#### history

三种历史记录

* browser history:	h5
* hash history:    针对老版本浏览器
* memory history：在测试和非dom环境中很有用（如React Native）

属性和方法

* length(number)：
* action(string)：当前动作，如push、replace、pop
* location(object)：
  * pathname(string)：the path of the URL
  * search(string)： URL查询字符串
  * hash(string)：
  * state(object)：此处的state是提供给例如push(path,[state])
* push(path,[state])：推个新历史去历史堆栈
* replace(path,[state])：替换历史堆栈的当前项
* go(n)：将历史堆栈中的指针移动n项
* goBack()：=go(-1)
* goForward()=go(1)
* block(prompt):阻止导航？

建议只用this.props.location而非this.props.history.location。因为history是可变的，这能确保生命周期函数的正常渲染。

#### location

````js
{
  key: 'ac3df4', // not with HashHistory!
  pathname: '/somewhere',
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
````

路由将在这几个地方提供location

* Route components as this.props.location
* Route render as ({location})=>()
* Route children as ({location})=>()
* withRouter as this.props.location

history.location不介意用。location是不变的，这在生命周期中用对数据获取和动画很有利



可在如下提供location而非strings

* Web Link to
* Native Link to
* Redirect to
* history.push
* history.replace

好处：UI切换是基于导航而非路径。且使用location，也可添加一些 location state

````js
const location = {
    pathname: '/somewhere',
    state:{ fromDashboard: true }
};
<Link to={location}/>
<Redirect to={location}/>
history.push(location)
history.replace(location)
````

location可传递给如下组件

* Route
* Switch

这样可以让组件渲染非真实位置，可防止他们在路由器下使用实际位置。

#### match

match对象：如何匹配Route path。属性：

* params(object)：键值对解析对应的URL
* isExact(boolean)：整个URL都匹配返回true
* **path**(string)：匹配path <Route>
* **url**(string)：匹配url <Link>

可在这几个地方获得match对象

* Route components as this.props.match
* Route render as ({match})=>()
* Route children as ({match})=>()
* withRouter as this.props.location
* matchPath as the return value

若一个路由没有path，则会匹配最近的父匹配，withRoute同。

**null matches**

<Route>使用children props将会调用children的方法。甚至the route'path没有匹配正确的location。这种情况，match=null，解决方式是会用相对路径

````js
let path = `${match.url}/relative-path`;
````

上面会报错TypeError。在使用the children prop 尝试在<Route>内部加入相对路径被认为是不安全的。

？？？not ending

### matchPath

### 杂

用nginx上下用 BrowserRouter(config/nginx.conf)

````js
location /{

try_files $uri $uri/ /index.html

}
````

* 监控路由的变化

  * vue中用路由守卫

  * app.js不是route切换过来的所以，this.props没有history，location

  * import {withRouter} from 'react-router-dom';

    export default withRouter(App)

    app.js

    作用：让不是路由切换的组件也具有路由切换组件的三个属性(location,match,history)

    console.log(this.props)

    history->listen用于监听路由的变化

    ````js
    this.props.history.listen((location)=>{})参数是一个回调
    this.props.history.listen((location)=>{
    	//会根据路由的变化而变化
    	//location.pathname
    })
    ````

    解决刷新 在constructor里又调用一次函数，把this.props.location.pathname传入

* 监控路由参数的变化（路由传参）

* 新增特性  hook

  useState  在无状态组件里也可用state

  let [数据，修改数据的函数] = useState(数据的初始化)

* 编程式导航
  this.props.history.push(path,state)

  this.props.location.state.变量

  (1)this.props.history.push  listen  go...适合做编程式导航和监听路由变化

  (2)match 主要用于路由的参数 this.props.match.params

  (3)location 用state传多个参数，向拿到查询字符串的时候用

  this.props.location.search(查询字符串)  ?a=111&b=222

  location.search.slice(1)去问号

  import qs from 'querystring';    qs.parse()把查询字符串变成对象

  qs.parse(a=xxx&b=yyy) {a:xxx,b:yyy}

  location.state 可以传递多个值 

* withRouter

  不是路由切换的组件没有那三个属性

  withRouter(App)

  withRouter是一个高阶组件，可给其他组件添加路由的三个对象

  * 高阶组件（HOC）

    作用：属性代理，反向继承
