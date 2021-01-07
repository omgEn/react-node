

React简介**

用户构建用户界面的JavaScript库 2013-5 facebook退出

vue是官方维护，react是社区维护

### 概念

#### 1 jsx

jsx是一个JavaScript的语法拓展。==xml+js。是一个语法糖。

jsx不是必需的，但jsx可以提高开发效率。

js与html的混写，是缺点也是优点

注：

React插值，对象不能直接渲染，数组以字符串的形式进行渲染。

class改为className，fonXxx,

style={{},{}}

-------

jsx的原理

* React.creteElement(tag,{attr},content)；虚拟DOM
* ReactDom.render(tag,document.querySelector());转成真实的dom，挂载

---------

jsx语法

* {有效的JavaScript表达式}

  > 一个表达式是代码的任何有效单位，**其解析为一个值**

  `2 + 2`，`user.firstName` 或 `formatName(user)` 都是有效的

  注：if，for是语句；**jsx也是一个表达式**

* 

#### 2 元素渲染

元素是构成React应用的最小块。组件是由元素构成的。

React DOM负责更新DOM与React元素保持一致。

React元素是**不可变对象**。一旦被创建，就无法更改它的子元素或属性。

React只更新它需要更新的部分。

#### 3 组件与Props

组件接收任意入参（props）

注：组件名必须以大写字母开头。**会将小写开头的组件视为原生DOM标签**。

* 无状态组件（函数组件）

  ````js
  const 组件名(首字母大写) = (props)=>{
  	return jsx表达式
  }
  //props:用于接收父组件传递的值，是个对象(json键值对)
  
  //函数组件
  function Welcome(props) {
    return <h1>Hello, {props.name}/h1>;
  }
  ````

* 类组件

  ````js
  class 组件名 extends React.Component {
      render(){
          //this 常用属性 state props refs
          //state 是组件内部的数据
          //props 类似于vue中的data，会引起组件重新渲染。来自组件外部的数据
          //refs 标识一个组件的节点
          return jsx表达式
      }
  }
  //render是必不可少的钩子函数
  ````

组件也在其输出中引用其他组件。这就可让我们用统一组件来抽象出任意层次的细节。

**Props的只读性**

**纯函数**：函数不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果。

所有React组件都必须像纯函数一样保护它们的props不被更改。

因此引入state，state允许组件随用户操作、网络响应或者其他变化而动态更新输出内容。

**组件传值**

父->子：通过属性传递  props

子->父：子组件调用父组件传递过来的方法。传递的数据放到参数里去。

兄弟之间不能传值，通过共有的的一个父亲，辗转传值

#### 4 state&生命周期

````js
//使用props的定时器
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);

//使用state的定时器
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
````

几个常见的生命周期

````js
class Clock extends React.Component{
    constructor(props){	//组件初始化 1
        super(props);
        this.state = {date:new Date()};
    }
    componentDidMount(){	//组件已经挂载 3
        this.timerID = setInterval(()=>this.tick(),1000);
    }
    componentWillUnmount(){	//组件卸载(当Clock组件从DOM中移除时调用)
        clearInterval(this.timerID);
    }
    tick(){ 4
        this.setState({ date:new Date() });
    }
    render(){	//组件渲染 2 5因为state改变了，
        return(
        	<h2>{this.satte.date.toLocaleTimeString()}</h2>
        )
    }
}
````

* state

  * 直接修改state无效，要通过setState()

  * 处于性能考虑，React会把setState()调用合并成一个

  * this.props和this.state会异步更新，所以不要依赖它们的值更新下一个状态

    ````js
    //wrong
    this.setState({
        counter:this.state.counter+this.props.increment
    },callback);
    //correct
    this.setState((prevState,props)=>{
        counter:prevState.counter+props.increment
    },callback)
    this.setState({key:新的value},()=>{回调函数})
    连续多个会合并，以最后一个为准，
    连续执行多次，放入队列中一次执行
    ````

  * setState

    * 是异步的，有回调函数，
    * state是组件内部的数据，改变了会触发render

  

* 数据是向下流动的

  state是局部的，除了拥有并设置了它的组件，其他组件都无法访问。

  组件可以选择把它的state作为props向下传递到它的子组件中。

  ````js
  <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
  <FormattedDate date={this.state.date} />
  ````

  自上而下或“单向”的数据流。state总是属于特定的组件，且从该state派生的任何数据或UI只能影响树中“低于”它的组件。

#### 5 事件处理

采用小驼峰式命名camelCase。

`<button onClick={activateLasers}></button>`

React中阻止默认行为，只能通过e.preventDefault()，return false不行。

阻止冒泡:e.stopPropagation()

````js
//实现用户切换开关状态
class Toggle extends React.Component{
    constructor(props){
        super(props);
        this.state = {isToggle:true};
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick(){
        //自定义函数内部用了this要绑定，否则this=undefined
        //bind或用箭头函数
        this.setState(state=>({
            isToggle:!state:isToggle
        }));
    }
    render(){
        return(
        	<button onClick={this.handleClick}>
            	{this.state.isToggleOn?'ON':'OFF'}
            </button>
        );
    }
}
ReactDOM.render(
	<Toggle/>,
    document.getElementById('root');
)
````

* react把事件直接绑定在标签上（原生是不建议这么做的）。组件卸载的时候会把 组件内的事件和事件处理函数也卸载掉，所以不会造成内存泄漏。且所有的事件监听都用了事件委托挂在了顶层对象上，性能不错。
  * 在标签上挂事件，若没有清理掉会引起内存泄漏的
  * react用路由组织组件，切换页面时，上一个页面的组件就卸载了。

#### 6 列表&key

key给于数组中的每一个元素赋予一个确定的标识。不建议用index（default）作为可以值，会导致性能变差，还可能引起组件状态的问题。因为列表项目的顺序可能会变化。

key放在就近数组的上下文才有意义

key只是在兄弟节点之间必须唯一，不需要全局唯一。

key会传递信息给React，但不会传递给组件。props.key无效

#### 7 表单

表单具有默认的HTML表单行为，即用户提交表单后会浏览到新页面。

但使用JavaScript函数可方便处理表单提交，同时还可以访问用户填写的表单数据。实现这种效果的标准方式是使用“受控组件”。

**受控组件**

表单元素的值来自于state，普通组件，组件的所有数据来自于props

受控组件，搭配onChange可改变state，value

非受控组件 defaultValue

使React的state成为”唯一数据源“。渲染表单的React组件还控制着用户输入过程中表单发生的操作。被React以这种方式控制取值的表单输入元素就叫做”受控组件“。

````js
class NameForm extends React.Component{
    constructor(props){
        super(props);
        this.state = {value:""};
    }
    hdndleChange(event){
        this.setState({value:event.target.value});
    }
    handleSubmit(event){
        console.log('提交的名字'+this.state.value);
        event.preventDefault();
    }
    render(){
        return(
        	<form onSubmit={this.handleSubmit}>
            	<lable>名字<input type="text" value="this.state.value" onChange={this.handleChange}/>
                </label>
				<input type="submit" value="提交">
            </form>
        ) 
    }
}
````

**textarea**:React中`<textarea value={}></textarea>`用value代替文本内容。

**select** 没有selected，设置默认

````js
<select value={this.state.value} onChange={this.handleChange}>
    <option value=""></option>
</select>
//或传多选
<select multiple={true} value={['B', 'C']}>
````

------------



**处理多个输入**

````js
class Reservation extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            isGoing: true,
            numberOfGuests:2
        };
        this.handleInputChange = this.handleInputChange.bind(this);
    }
    handleInputChange(event){
        const target = event.target;
        const value = target.type === 'checkbox'?target.checked:target.value;
        const name = target.name;
        this.setState({
            [name]:value
        });
    }
    render(){
        return (
            <form>
                <label>参与:
                  <input
                    name="isGoing"
                    type="checkbox"
                    checked={this.state.isGoing}
                    onChange={this.handleInputChange} />
                </label>
                <br />
                <label>来宾人数:
                  <input
                    name="numberOfGuests"
                    type="number"
                    value={this.state.numberOfGuests}
                    onChange={this.handleInputChange} />
                </label>
          </form>
        )
    }
}
````

**受控输入空值**

在受控组件上指定value的props会阻止用户更改输入。如果指定了value，但输入仍可编辑，则可能是意外地将value设置为undefined或null。

**有时间测验**

**非受控组件**

当将之前的代码库转换为React或将React应用程序与非React库集成时，受控组件就不方便了。

非受控组件的表单数据将交由DOM节点来处理。

因为非受控组件将真实数据储存在DOM节点中，所以在使用非受控组件时，有时反而更容易同时继承React和非React代码。如果不介意代码美观性，并且希望快速编写代码，使用非受控组件可减少代码量。

````js
//非受控用refs
constructor(props){
    super(props);
    this.input = React.creatRef();
}
render(){
    return(
    	<input type="text" ref={this.input}/>
    )
}
````

* 默认值

  `<input type="checkbox/radio">` defaultChecked

  `<textarea><select>`defaultValue;type="text"

* 文件输入

  `<input type="file"/>` 这个是非受控组件

  ````js
  this.fileInput = React.createRef();
  //获取文件信息
  this.fileInput.current.file[0].name;
  <input type="file" ref={this.fileInput}/>
  ````




#### 8 状态提升

通常，多个组件需要反映相同的变化数据，建议**将共享状态提升到最近的共同父组件中去**。

在React应用中，任何可变数据应当只有一个相对应的唯一“数据源”。通常，state都是首先添加到需要渲染数据的组件中去。若其他组件也需要这个state，就将它提升至这些组件的最近共同父组件中。

虽然提升state比双向绑定要写更多的代码，好处是排除和隔离bug所需大的工作量将会变少。因为“存在”与组件中的任何state，仅有组件自己能够修改它，因此bug排除就方便很多。此外，也可用自定义逻辑来拒绝或转换用户的输入。

如果某些数据由props或state推到得出，则它就不应该存在于state中。

#### 9 组合VS继承

React有十分强大的组合模式。推荐使用组合而非继承来实现组件间的代码重用。

**包含关系**

有些组件无法提前知晓它们子组件的具体内容。

建议用props.children，然后任意组件调用。

````js
function FancyBorder(props){
    return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
    	{props.children}
	</div>
    )
}
function WelcomeDialog() {
  return (
    <FancyBorder color="blue"></FancyBorder>
    <FancyBorder color="red"></FancyBorder>
  );
}
````

少数情况下，需要留出一些属性，可以不用children，

````js
function SplitPane(props){
    return (
    <div className="SplitPane">
      	<div className="SplitPane-left">{props.left}</div>
      	<div className="SplitPane-right">{props.right}</div>
    </div>
    );
}
function App(){
    return (
        <SplitPane
			left={<Contacts/>}
			right={<Chats/>}
		/>
    )
}
````

注：组件可以接受任意props，包括基本数据类型，React元素以及函数。

--------

**特例关系**

有时，会把一些组件看做其他组件的特殊实例。

WelcimeDialog是Dialog的特殊实例。

“特殊”组件可以通过props定制并渲染“一般组件”。（类似于继承）

基类（父类）+特殊组件（子类）

````js
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">{props.title}</h1>
      <p className="Dialog-message">{props.message}</p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />

  );
}
````



----------

**继承**

在Factbook，并没有发现需要继承来构建组件层次的情况。

Props和组合提供更清晰而安全地定制组件外观和行为的灵活方式。

若想在组件间复用非UI的功能，建议将其提取为一个单独的JavaScript模块，如函数、对象或者类。组件可以直接import而非extend继承。

#### 10 生命周期

* 挂载阶段

  * 1 constructor(props,context)  

    初始化：初始化state，将事件处理函数绑定到实例上。

  * 2 static getDerivedStateFromProps(props,state)  

    derived state派生状态

    组件实例化后，props更新了调用。若父组件的props更改所带来的的重新渲染，也会触发此方法。

    必须返回一个对象来更新状态或者返回null表示新的props不需要任何state的更新。

    **静态方法**不能用this，调用是lei.静态方法名()

  * 3 render()

    必需的，当被调用时，将计算this.props和this.state。并返回以下一种类型

       1.React元素。通过jsx创建，可是dom元素，也可是用户自定义的组件

       2.字符串或数字。他们将以文本节点形式渲染到dom中。

    3. null，什么也不渲染。
      4. 布尔值。也是什么都不渲染
    5. 数组：以字符串形式渲染 

    注：对象不能直接渲染

  * componentDidMount()

    组件被装配后立即调用。初始化使得dom节点应该进行到这里

    可操作DOM节点。
    
    通常在这里进行ajax请求。初始化第三方的dom库，

* 更新节点

  * static getDerivedStateFromProps(props,state)

  * shouldComponentUpdate(nextProps,nextState)

    默认ture,会render。可以加条件减少不必要的渲染，增加性能。

    若返回false，componentWillUpdate、render、componentDidUpdate不会调用

    * PureComponent  进行浅比对，进行性能的优化（纯组件） extends.PureComponent

      参数和返回值都是组件：高阶函数（map，filter，forEach.....）

      仅进行浅层对比，若对象中包含复杂的数据结构，则可能产生错误比对。

      仅在state和props较简单的时候用。

    * 对函数组件用React.memo(组件)  

      仅检查props有没改变

      ````js
      const MyComponent = React.memo(function MyComponent(props) {
        /* 使用 props 渲染 */
      });
      ````

      默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。

      ````js
      function MyComponent(props) {
        /* 使用 props 渲染 */
      }
      function areEqual(prevProps, nextProps) {
        /*
        如果把 nextProps 传入 render 方法的返回结果与
        将 prevProps 传入 render 方法的返回结果一致则返回 true，
        否则返回 false
        */
      }
      export default React.memo(MyComponent, areEqual);
      ````

      

  * **render()**

  * getSnapshotBeforeUpdate(prevProps,prevState)

    使组件能够在它们被潜在更改之前捕获当前位置（如滚动位置）

    必须与componentDidUpdate一起用

    必须返回一个值  返回的任何值会作为参数传递给componentDidUpdate()

    不能和旧版的钩子函数一起用

    目的是为了返回数据更新前的dom状态

  * **componnetDidUpdate(prevProps,prevState,snapshot)**

    将当前props和以前props比较。
    
    可单独使用

* 卸载阶段

  * componentWillUnmount()

    组件被卸载并销毁之前被调用。

    清理：定时器无效，取消网络请求、清理在componentDidMount中创建的任何监听
  
* 错误处理

  * static getDerivedStateFromError()
  * componentDidCatch()

constructor,render,componentDidMount,componentWillunmount,componentDidUpdate 监控组件里的数据的变化，**慎用setState？？**

**组件的异步传值问题**

* 非父子组件传值 public-js
  * Public.publish("事件名","数据")
  * Public.subscribe("事件名",(evt,data)=>{})



#### 11 Props默认值，限定类型

设置props默认值：调用this.props

defaultProps用于确保this.props.key在父组件没有指定其值的时，有一个默认值。

类组件

````js
//类组件
static defaultProps = {
    key:默认值
}
//无状态组件
组件名.defaultProps={
    key:默认值
}
````

props限定类型

propTypes类型检查发生在defaultProps赋值后，所以类型检查也使用于defaultProps

````js
//类组件
import PropTypes from 'prop-types'
static propTypes = {
    key:PropTypes.类型
    name: React.PropTypes.string.isRequired
}
//无状态组件
组件名.propTypes={
    key.propTypes.类型.isRequired(必须传)
    data: PropTypes.object.isRequired
}
````



#### 12 React哲学

React最棒的部分之一是引导我们思考如何构建一个应用。

案例：实现一个可搜索的产品数据表格

**从设计稿开始**

````json
//JSON API
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
````

**一：将设计好的UI划分为组件层级**

设计者的ps图层名称可能就是编写React组件的名称。

但如何确定将那些部分划分到一个组件中？

可将组件当做一种函数或是对象来考虑，根据**单一功能原则**来判定组件的范围。即一个组件原则上只能负责一个功能。

![image-20200219220154313](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200219220154313.png)

以上分成五个组件

![image-20200219220303728](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200219220303728.png)

**二：用React创建一个静态版本**

先写静态页面，在写交互

渲染UI：大量代码。props

添加交互：大量细节。state

小项目：自上而下

大项目：自下而上，同时为顶层组件编写测试。

**三：确定UI state的最小（且完整）表示**

即state要小而完整

* 总数据
  * 包含所有产品的原始列表——props
  * 用户输入的搜索词——state
  * 复选框是否选中的值——state
  * 经过搜索筛选的产品列表——不是，因为它的值可用产品原始列表根据搜索词和复选框的选择计算。
* 逐个检查相应数据是否属于state
  * 数据是否通过父组件的props传递而来，若是，则不是state
  * 是否保持不变，若是，则不是state
  * **能否根据其他state或props计算得出**，若是，则不是state

**四：确定state放置的位置**

即这些state放在那些组件较合适。

* 对于应用中的每一个state
  * 找到根据这个state进行渲染的所有组件
  * 找出这些组件的共同所有者。
  * 该共同所有者组件或者比他更高级的组件应该拥有该state
  * 若找不到一个合适的位置放该state，看创建一个新组件存放该state，并将该组件置于高于共同所有者组件层级的位置。
* 

**五：添加反向数据流**

低级的state想更新更高级的state。

React通过传统欧双向绑定实现反向数据传递。

通过回调函数触发。

****

总结：代码模块化和清晰度的重要性。且随着代码重用程度的增加，代码行数会显著减少。



### 高级指引

#### 代码分割

打包工具webpack，rollup，browserify。打包：把一个文件合并到一个单独文件的过程，最终形成一个“bundle。接着在页面上引入该bundle，这个应用即可一次性加载。

react脚手架create react app,next.js,gatsby



#### Context

#### 错误边界

#### Refs转发

Refs转发是一项将ref**自动**地通过组件传递到其一子组件的技巧。

应用：

* 转发refs到DOM组件

React组件隐藏其实现细节，包括其渲染结果。这能防止过度依赖其他组件的DOM结构。

* 在高阶组件中转发refs
* 在DevTols中显示自定义名称

#### Fragments

* import {Fragment} from 'react'

* <React.Fragment></React.Fragment>

* <></> 不支持key或属性

* 带key的Fragments

  key是唯一可以传递给Fragment的属性。未来可能会添加对其他属性的支持，例如事件。

#### 高阶组件

HOC是React中用于复用组件逻辑的一种高级技巧。HOC不是React API的一部分，是居于React组合特性而形成的**设计模式**。

HOC=以参数为组件，返回值为新组件 的函数。

组件是将 props 转换为 UI，而高阶组件是将组件转换为另一个组件。

HOC 在 React 的第三方库中很常见，例如 Redux 的 [`connect`](https://github.com/reduxjs/react-redux/blob/master/docs/api/connect.md#connect) 和 Relay 的 [`createFragmentContainer`](http://facebook.github.io/relay/docs/en/fragment-container.html)。

组件是React代码

#### 与第三方库协同

React可被用于任何web应用中。它可被嵌入到其他应用，同理其他应用也可嵌入到React中。

React不会理会React自身之外的DOM操作。它根据内部虚拟DOM来决定是否需要更新，且若同一个DOM节点被另一个库操作，React会觉得困惑且没有办法恢复。

#### 深入JSX

实际上JSX仅仅是`React.createElement(component,props,...children)`函数的语法糖。

````js
<MyButton color="blue" shadowSize={2}>Click Me</MyButton>
//会编译成
React.createElement(
  MyButton,
  {color: 'blue', shadowSize: 2},
  'Click Me'
)

<div className="sidebar" />
React.createElement(
  'div',
  {className: 'sidebar'},
  null
)
````

* 指定

* 用户自定义的组件必须以大写字母开头

  <span>编译成字符串'span'=>React.createElement(作为参数)

  <Foo/>编译成React.createElement(Foo)

* 只允许时选择类型

----------

**props**

* props默认值为true

  ````js
  <MyTextBox autocomplete />
  //==
  <MyTextBox autocomplete={true} />
  ````

  不建议这么写，这会与ES6对象简写混淆 {foo}是{foo:foo}的简写

* props展开写法

  ````js
  const Button = props => {
    const { kind, ...other } = props;
    const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
    return <button className={className} {...other} />;
  };
  
  const App = () => {
    return (
      <div>
        <Button kind="primary" onClick={() => console.log("clicked!")}>
     		Hello World!
        </Button>
      </div>
    );
  };
  ````

  kind的props会别保留。其他的props会通过...other传递。

  谨慎使用。容易将不必要的pros传递给不相关的组件，或将无效的HTML属性传递给DOM。

----------

**JSX中的子元素**

包含在开始和结束标签之间的JSX表达式内容将作为特定属性`props.children`传递给外层组件。

有如下几种方法来传递子元素

字符字面量；JSX子元素；JavaScript表达式作为子元素；函数作为子元素；

布尔类型、Null以及Undefined将会忽略。以下渲染结果相同

````js
<div />
<div></div>
<div>{false}</div>
<div>{null}</div>
<div>{undefined}</div>	
<div>{true}</div>
````

注意0会被渲染

```js
{props.messages.length &&<MessageList messages={props.messages} />}
//length=0时候还是会渲染，应当这么写
{props.messages.length > 0 &&<MessageList messages={props.messages} />}
```

若想渲染false、true、null、undefined等值。应当先将它们转为字符串

`String(myVariable)`

#### 性能优化

UI更新需要昂贵的DOM操作，而React内部使用几种巧妙的技术以便最小化DOM操作次数。

**使用生产版本**

开发者工具，黑色是生产版本。若是基于React开发模式的网站，背景是红色



-------

**使用Chrome Performance标签分析组件**

-----

**使用开发者工具中的分析器对组件进行分析**

--------------

**虚拟化长列表**

-----

**避免调停**

即使React只更更新改变了的DOM节点，重新渲染仍然花费了一些时间。

可通过覆盖生命周期方法 `shouldComponentUpdate`来进行提速。该方法会在重新渲染前被触发，默认返回true，让React执行更新

````js
shouldComponentUpdate(nextProps, nextState) {
  return true;
}
````

**React.PureComponent**   **!!!**

例子

````js
//当组件的props.color或者state.count改变才更新
class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
````

用React.PureComponent改造

通过“浅比较”的模式检查props和state中所有的字段，以此决定是否组件需要更新。

````js
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

  render() {
    return (
      <button
        color={this.props.color}
        onClick={() => this.setState(state => ({count: state.count + 1}))}>
        Count: {this.state.count}
      </button>
    );
  }
}
````

**notending**

--------

不可变数据的力量

#### Portals

Portal——将子节点渲染到存在于父组件以外的DOM节点。

````jsx
ReactMOM.createRortal(child,container);
````

child:任何**可渲染的React子元素**，container:DOM元素

---------

**用法**

通常，当从组件的render方法返回一个元素时，该元素将被挂载到DOM节点中离其最近的父节点。

应用：当父组件有overflow:hidden,或z-index样式时，需要子组件能够在视觉上“跳出”其容器、例如对话框、悬浮卡以及提示框。

注：当使用portal时，键盘焦点很重要。

````js
render(){
    //React挂载了一个新的div，且把子元素渲染其中
    return <div>{this.props.children}</div>
}
render(){
    //只是将子元素渲染到domNode中，
    //domNode是一个可以在任何位置的有效DOM节点
    return ReactDOM.createProtal(
    	this.props.children,
        domNode
    )
}
````

-------

**通过Protal进行事件冒泡**

 尽管protal可以被放在DOM树中的任何地方，其行为和普通的React子节点行为一致。

在父组件里捕获一个来自portal冒泡上来的事件，使之能够在开发时具有不完全依赖于portal的更为灵活的抽象。

Protal是干嘛的？？？

#### Profiler

**Profiler测量渲染一个React应用多久渲染一次以及渲染一次的“代价”**。它的目的是识别出应用中渲染较慢的部分，或是可以使用类似**memoizatiob**优化的部分。

注：Profiling增加了额外的开支，所以它在生成构建中会被禁用。但有解决之法。

------

**用法**

Profiler能添加在React树中的任何地方。

需要两个props：id(string),onRender={callback}

````js
render(
  <App>
    <Profiler id="Panel" onRender={callback}>
      <Panel {...props}>
        <Profiler id="Content" onRender={callback}>
          <Content {...props} />
        </Profiler>
        <Profiler id="PreviewPane" onRender={callback}>
          <PreviewPane {...props} />
        </Profiler>
      </Panel>
    </Profiler>
  </App>
);
````

注：Profiler虽是一个轻量级组件，但也要只在需要的时候才使用它。对于一个应用来说，没添加一些都会给CPU和内存带来一些负担。

-------

**onRender回调**

它的参数描述了渲染了什么和花费了多久。

````js
function onRenderdCallBack((prop),{
    //合计或记录渲染时间.....
})
````

各prop：

* id:string

* phase:"mount"|"update"；组件刚加载|若重渲染了。

  判断组件树的第一次装载引起的重渲染，还是由props、state或是hooks改变引起的重渲染

* actualDuration:number

  本次更新在渲染Profiler和它的子代上花费的时间。

* baseDuration:number

  最近一次的持续时间。这个值估计了最差的渲染时间

* startTime:number

  本次更新中React开始渲染的时间

* commitTime:number

  本次更新中React commit阶段结束的时间戳

* interations:Set="interactions"

  属于本次更新的interactions的集合。（例如 当render或者setState被调用）

  Interations能用来识别更新是由什么引起的，尽管这个追踪更新的API依然是实验性质的。

#### 不使用ES6

定义组件，不用ES6，用create-react-class模块

````js
var createrReactClass = require('create-react-class');
var Greeting = createReactClass({
    //声明默认属性
    getDefaultProps:function(){
        return {
            name:'Mary'
        };
    };
    //返回初始state 
    getInitialState:function(){
    	return {count:this.props.initalCount};
	};
	//方法自动绑定了实例，不用bind
	handleClick:function(){
        console.log(this.state.count);
    }
    render:function(){
        return <h1>{this.props.name}</h1>
    }
})

//ES6 声明默认属性
Greeting.defaultProps={
    name:'Mary'
}
//ES6中函数需要
this.handleClick=this.handleClick.bind(this);
//箭头函数把方法绑定给实例，但还在试验阶段
//用了Babel插件的Class Proerties
handleClick=()=>{}
````

注：箭头函数目前还在实验性阶段，这意味着语法随时都可能改变，也存在最终不被列入框架标准的可能。

为了保险起见，以下三种做法是可以的：

* 在construcor中绑定方法
* 使用箭头函数，比如onClick={(e)=>this.handleClick(e)}
* 继续使用createReactClass

------

**Minis**

notending

#### 不使用JSX

每个JSX元素只是调用React.createElement(component,props,...children)的语法糖。因此使用JSX完成的任何事情都可通过存JavaScrip完成

````js
const e = React.createElement;
ReactDom.render(
	e.('div',null,'hello world');
    document.getElementById("root");
);
````

Babel编译器。

#### 协调

本文描述了在实现React的“diffing”算法，设计决策以保证组件满足更新具有可预测性，以及在繁杂业务下依然保持应用的高性能性。

--------

**设计动力**

在某一个时间节点调用React的render()方法，会创建一颗由React元素组成的树。**在下一次state或props更新时**，相同的render()方法会返回一颗不同的树。React需要基于这两颗树之间的差别来判断如何有效率的更新UI以保证当前UI与最新的树保持同步。

最前沿的算法中，复杂程度是O(n3)。n是树中元素的数量。

React在以下两个假设上提出了一套O(n)的启发式算法

* 另个不同类型的元素会产生不同的数
* 开发者可以通过key prop来暗示哪些子元素在不同的渲染下能保持稳定

在实践中，发现以上假设在几乎所有实用的场景下都成立。

---------

**Diffing算法**

先对比两棵树的根节点。不同类型的根节点元素会有不同的形态。

* 对比不同类型的元素

  当根节点为不同类型的元素时，React会拆卸原有的树并且建立起新的树。

  当拆卸一颗树时，对应的DOM节点也会销毁。

----

**权衡**

目前，可以理解为一颗子树能在其兄弟之间移动，但不能移动到其他位置。在这种情况下，算法会重新渲染整颗子树。

优化：

1. 该算法不会尝试匹配不同组件类型的子树。

   若发现在两种不同类型的组件中切换，但输出非常相似的内容，建议改成同一类型。（在实践中，还没遇到这类问题）

2. key应该具有稳定、可预测、以及列表内唯一的特质。

   不稳定的key会导致许多组件实例和DOM节点被不必要地重新创建，这可能导致性能下降和子组件中的状态丢失。

#### Refs&DOM

Refs，允许访问DOM节点或在render方法中创建的React元素。

在典型的React数据流中，props是父组件与子组件交互的唯一方式。要修改一个子组件，需要使用新的props来重新渲染它。但在某些情况下，需要在典型数据流之外强制修改子组件。

**何时使用Refs**

* 管理焦点，文本选择或媒体播放
* 触发强制动画
* 集成第三方DOM库

避免使用refs来做任何可以通过声明式实现来完成的事情。

**忽过度使用Refs**

注意state的状态提升

**访问Refs**

当ref被传递给render中的元素时，对该节点的引用可在ref的current属性中被访问。

`const node = this.myRef.current;`

ref的值根据节点的类型而有不同

* 当ref属性用于HTML元素，构造函数中使用React.createRef()创建的ref接收底层DOM元素作为其current属性。

* 当ref属性用于自定义class组件时，ref对象接收组件的挂载实例作为其current属性。

* 不能在函数组件上使用ref属性，因为他们没有实例。

  ````js
  //函数组件
  function MyFunctionComponent(){
      return <input/>
  }
  //会报错
  <MyFunctionComponent ref={this.textInput}/>
  ````


ref的三种使用方式：

* 字符串：ref="a"  this.refs.a

* 回调函数:ref={(node)=>this.变量=node}  this.变量  就能拿到当前dom节点

* createRef     

  this.state = {变量:createRef()}  this.state.变量.current.value 

  <node ref={this.state.变量}>

  引用这个节点：this.steate.变量.current

* 用this.refs.用ref标识的节点？

#### Render Props

render props 是指一种在react组件之间使用一个**值为函数的prop共享代码**的简单技术。

具有render

#### 静态类型检查

#### 严格模式

StrictMode是一个用来突出显示应用程序中潜在问题的工具。与Fragment一样，StricMode不会渲染任何可见的UI。它为其后代元素触发额外的检查和警告。

`<React.StrictMode><ComponentOne/></React.StricMode>`

StrctMode目前有助于

* 识别不安全的生命周期

  若使用了第三方库，很难确保它们不使用这些生命周期方法。

  报错：unsafe LifeCycle methods were found within a strict-mode tree:

  componentWillMount:please update the following components to use componentDidMount instead:ThridPartyComponent

* 关于使用过时字符串ref API警告

  * 已过时的字符串ref	 ref=‘txt'

  * 回调函数API形式       ref={(node)=>this.node=node} 官方推荐使用

  * this.inputRef = React.createRef()   ref={this.inputRef} this.inputRef.current.focus()

    最好的方法——React16.3

* 关于使用废弃的findDOMNode方法的警告

  可将ref直接绑定到DOM节点。

* 检测意外的副作用

* 检测过时的context API

* 

#### 使用PropsTypes类型

自React.v15.5起，React.PropTypes已移入另一个包中。用prop-types代替。

对于某些应用程序来说，可使用Flow或TypeScript等JavaScript拓展做类型检查。React也内置了一些类型检查的工具。要在组件的props上进行类型检查，只需配置特定的propsType属性。

````js
import PropTypes from 'prop-types';
class Greeting extends React.Component{
    render(){
        return(
        	<h2>{this.props.name}</h2>
        )
    }
}
Greeting.propTypes = {
    name:PropTypes.string
}
````

处于性能方面的考虑，propType仅在开发模式下进行检查。

PropTypes的例子

````js
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  	//可将属性声明为JS原生类型，
    optionalArray: PropTypes.array,
    optionalBool: PropTypes.bool,
    optionalFunc: PropTypes.func,
    optionalNumber: PropTypes.number,
    optionalObject: PropTypes.object,
    optionalString: PropTypes.string,
    optionalSymbol: PropTypes.symbol,
    
    //任何可被渲染的元素（包括数字、字符串、元素或数组）
    //（或Fragment）也包含这些类型
    optionalNode:PropTypes.element,
    
    // 一个 React 元素。
  	optionalElement: PropTypes.element,
    
    // 一个 React 元素类型（即，MyComponent）。
  	optionalElementType: PropTypes.elementType,
    
    // 你也可以声明 prop 为类的实例，这里使用
    // JS 的 instanceof 操作符。
    optionalMessage: PropTypes.instanceOf(Message),
    
    // 你可以让你的 prop 只能是特定的值，指定它为枚举类型。
  	optionalEnum: PropTypes.oneOf(['News', 'Photos']),
    
    // 一个对象可以是几种类型中的任意一个类型
    optionalUnion: PropTypes.oneOfType([
        PropTypes.string,
        PropTypes.number,
        PropTypes.instanceOf(Message)
    ]),
    
    // 可以指定一个数组由某一类型的元素组成
    optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

    // 可以指定一个对象由某一类型的值组成
    optionalObjectOf: PropTypes.objectOf(PropTypes.number),

    // 可以指定一个对象由特定的类型值组成
    optionalObjectWithShape: PropTypes.shape({
        color: PropTypes.string,
        fontSize: PropTypes.number
    })
    
     // An object with warnings on extra properties
    optionalObjectWithStrictShape: PropTypes.exact({
        name: PropTypes.string,
        quantity: PropTypes.number
    }),   

    // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
    // 这个 prop 没有被提供时，会打印警告信息。
    requiredFunc: PropTypes.func.isRequired,
    
    // 任意类型的数据
  	requiredAny: PropTypes.any.isRequired,

    // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
    // 请不要使用 `console.warn` 或抛出异常，因为这在 `onOfType` 中不会起作用。
    customProp: function(props, propName, componentName) {
		if (!/matchme/.test(props[propName])) {
            return new Error(
                'Invalid prop `' + propName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
        }
    },

    // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
    // 它应该在验证失败时返回一个 Error 对象。
    // 验证器将验证数组或对象中的每个值。验证器的前两个参数
    // 第一个是数组或对象本身
    // 第二个是他们当前的键。
    customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
        if (!/matchme/.test(propValue[key])) {
            return new Error(
                'Invalid prop `' + propFullName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
        }
    })
}
````

**限制单个元素**

通过PropTypes.element来确保传递给组件的children中包含一个元素

？？？

````js
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这必须只有一个元素，否则控制台会打印警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
````

```js
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这必须只有一个元素，否则控制台会打印警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
```

**默认Prop值**

````js
class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};
````

若用了transform-class-properties的Babel转换工具也可在React组件类中声明defaultProps作为静态属性。此提案还没确定。

defaultProps用于确保this.props.name在父组件没有指定其值时，有一个默认值。

propTypes类型检查发生在defaultProps赋值后，所以类型检查也适用于defaultProps

````js
class Greeting extends React.Component {
  static defaultProps = {
    name: 'stranger'
  }

  render() {
    return (
      <div>Hello, {this.props.name}</div>
    )
  }
}
````



#### Web Components

Web Components为可复用组件提供了强大的封装；

而React则提供了声明式的解决方案，使DOM与数据保持同步。

两者可以互相嵌套或共存使用。

### API REFERENCE

#### React 顶层API



#### ReactDOM

#### ReactDOMServer

#### DOM元素

所有的DOM特性和属性（包括事件处理）都是小驼峰命名的。

------

**属性差异**

React与HTML间很多属性存在差异

* checked

  <input type="checkbox/radio">判断组件是否被选中

  defaultChecked是非受控组件的属性，用于设置组件首次挂载时是否被选中

* value

  <input>和<texttarea>组件支持 `value` 属性。这对受控组件有帮助。

  defaultValue属性对应的是非受控组件的实现，用于设置组件第一次挂载时候的value

* className

* dangerouslySetInnerHTML

  ````
  <div dangerouslySetInnerHTML={{__html:html字符串}}></div>
  ````

  替换innerHTML的方案。

  直接用代码设置HTML存在风险，容易保留跨站脚本XSS的攻击。

  ````js
  function createMarkup() {
    return {__html: 'First &middot; Second'};
  }
  //key为__html的对象
  
  function MyComponent() {
    return <div dangerouslySetInnerHTML={createMarkup()} />;
  }
  ````

* htmlFor

  由于for在JavaScript在保留字，所以用htmlFor代替

* onChange

  每当表单字段变化时，该事件会被触发。React依靠了该事件实时处理用户输入。

* selected

  <option>支持selected属性，可用该属性设置组件是否被选中。这对受控组件很有帮助。

* style

  style小驼峰命名的JavaScript对象，而不是css字符串。

  style的JavaScript属性是一致的，且能预防跨站脚本XSS的安全漏洞。

  React会自动添加px，所以可不设置。

  ````js
  const divStyle = {
    color: 'blue',
    backgroundImage: 'url(' + imgUrl + ')',
  };
  
  function HelloWorldComponent() {
    return <div style={divStyle}>Hello World!</div>;
  }
  ````


### 术语表

* 单页面应用 single-page application 

* ES6、ES2015、ES2016等

  ES6=ES2015s是对ES5的补充
  
* Compiler（编译器）
  

JavaScript compiler接收JavaScript代码，然后对齐进行转换，最终返回不同格式的JavaScript代码。比如，接收ES6代码，然后将其转换为旧版本浏览器能够解释执行的语法。

  Babel是React最常用的compiler。

* Bundler(打包工具)

  bundler会接收模块然后将他们组合在一起，最终生成出一些为浏览器优化的文件。常用的打包React应用的工具有webpack和Brouserify

* Package管理工具

  管理项目依赖的工具。npm和yarn是常用的管理React应用依赖的package管理工具。

* CDN

  内容分发网格 content delivery network

  CDN会通过一个遍布全球的服务器网络来分发缓存的静态内容

* JSX

  jsx是JavaScript语法拓展

* 元素

  React元素是不可变元素

  `const element = <h1>Hello, world</h1>;`

* 组件

  返回React元素

* props.children

  每个组件都可获得props.children

  ````js
  function Welcome(props) {
    return <p>{props.children}</p>;
  }
  class Welcome extends React.Component {
    render() {
      return <p>{this.props.children}</p>;
    }
  }
  ````

  ````js
  <组价1>
  	<组件2></组件2>
  </组件1>
  组件1 this.props.children  可以拿到组件2
  ````

  

  

  

### HOOK

#### 1 Hook简介

hook是React16.8新增特性。可让你在不先写class的情况下使用state以及其他的React特性。

````react
function ExampleWithManyStates() {
   // 声明一个叫 “count” 的 state 变量。
  const [count, setCount] = useState(0);
  // 声明多个 state 变量！
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
    
   // 相当于 componentDidMount 和 componentDidUpdate:
  useEffect(() => {
    // 使用浏览器的 API 更新页面标题
    document.title = `You clicked ${count} times`;
  });
    
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
````



注意，升级时，要更新所有的package，yarn undate?

------

**没有破坏性改动**

在继续之前，记住Hook是

* 完全可选的。

  不用重写任何代码就可在组件中尝试Hook

* 100%向后兼容

* 现在可用。

--------------

**动机**

Hook解决了了五年来编写和维护成千上万的组件时遇到的各种各样看起来不想关的问题。



#### 2 Hook概览

什么是Hook，Hook是让你可在函数组件里“钩入”React state及生命周期等特性的函数。Hook不能在class组件中使用。

#### 3 使用State Hook

### 其他

#### 1mock

npm i json-server -g 

json-server --version

````js
{
    "list":[
        {
            "id":1,
            "name":"zhangsan",
            "age":20
        },
        {
            "id":2,
            "name":"lisi",
            "age":25
        }
    ]
} 
````

json-server xxx.json --port 端口号 -w 数据修改json后不用重启

json-server的使用说明   https://gitee.com/rh_hg/json-server?_from=gitee_search

json-server 模糊查找有两种形式:
	url?q=查找的关键字
    以某一个字段模糊查找:url？字段_like=查找的关键字

**2代理**

正向代理    开发环境

反向代理   上下环境配置文件的路径目录下：

````js
/node_modules/react-scripts/config/webpackDevServer.config.js
proxy:{
        "/weather":{
             target:"http://www.weather.com.cn",
             changeOrigin:true,
             "pathRewrite":{
               "^/weather":"/"
             }
        }
    },
````

3 弹射 eject
  yarn eject 不能撤销  
   如果弹射后出错，重装依赖

4fiber算法 (菜鸟勿入)
https://segmentfault.com/a/1190000018250127?utm_source=tag-newest

5yarn和npm命令表对比  https://yarn.bootcss.com/docs/migrating-from-npm/

6脚手架自带的服务器是webpack-dev-server

  项目的目录/node_modules/react-scripts/scripts/start.js

  改端口 const DEFAULT_PORT = parseInt(process.env.PORT, 10) || 5000;

7 eslint   no-restricted-globals // eslint-disable-next-line

下面这句js 就不用eslint进行语法检查了。

react cli  eslint 配置  https://www.codercto.com/a/13256.html

8 Fragment 空表记 相当不 <></>

9 props传给子组件的值不能改

10 forceUpdate 强制刷新

* 脚手架环境

  * npm install -g create-react-app 

    create-react-app --version 说明安装好了

    不想全局安装，就用npx create-react-app your-app

  * 导入导出

    ````js
    export xxx
    export yyy
    import {xxx,yyy} from path
    import * as name from path
    
    export default {a}
    import Test from path
    Test.a
    ````

  * 引入图片

    ````js
    import 变量 fom path
    <img src={变量}/>
    或者
    <img src={require("path")}/>
    ````

  * 脚手架实际是在安装

    * react:react的顶级库

    * react-dom:

      因为react有很多的运行环境，比如app端的react-native，我们要在web上运行就用react-dom

    * react-script:（下载最耗时），包含运行和打包react应用程序的所有脚本及配置
  
  * npm安装失败
    * npm cache clean --force  在执行npm install
    * rm -rf $(npm root)删除node_modules文件
  
* 代理

  modules->react-script->webpackDevConfig.js

  * 正向代理  开发环境
  * 反向代理  上线环境

* 弹射

  yarn eject==npm run eject

  若弹射失败，需要删除node_modules文件删除，在重新下载。

* let {a:testa} = this.props; console.log(testa)

  <One a={obj.a} b={obj.b} c={obj.c}>

  等价于<Once {...obj}>

* 无状态组件没有this

  ````js
  //父组件
  componentDidMount(){
      //这里才能找到节点
      //父组件调用子组件方法
      this.refs.child.test();
  }
  
  return(
      var fun = (x)=>{return x}
      <One fun={fun} ref="child"/>
  )
  
      
  //子组件
  export default class One extends {
  //父组件调用子组件方法
  test(){
      console.log("test");
  }
  render() {
      var fun = this.props.fun;
  	fun(111)//向父组件传值
      
  }
  }
  ````

  refs要用类组件才行

  无状态组件只能渲染不能干别的

  this.add = this.add.bind(this)  实例=this

* fetch和axios区别

  * fetch是原生就支持
  * axios是xml的封装

