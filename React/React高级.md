## React路由

```js
npm i react-router-dom -S

import {HashRouter,Link,Route,Switch,Redirect} from 'react-router-dom'
```

- HashRoter 定义哈希路由整体的容器标签
- Link 单个标签，定义路由的链接，通过“to”属性来定义链接地址
- Route 单个标签，定义组件的容器标签，通过“path”定义和Link的to属性对应的地址，component属性定义链接对应的组件
- Switch 多个Route标签外面的容器标签，如果需要定义404跳转和重定向跳转，需要用此标签包裹Route标签
- Redirect 定义路由重定向，通过“from”属性定义原始路由，通过“to”属性定义重定向路由
- exact是为了严格匹配path中的地址



#### 自定义路由

```js
function CustomLink({label,to,exact}){
    return (
    <Route
        path = { to }
        exact = { exact }
        // match参数是系统传入的，它是一个布尔值，匹配当前路由，就是true
        // 不匹配当前路由，就是false
        children = {({match})=>(
            <Link to={ to } className={ match?'active':''}>{ label }</Link>
        )}    
    />)
}

<CustomLink label="页面一" to="/" exact={ true } />
<CustomLink label="页面二" to="/page02"  />
<CustomLink label="页面三" to="/page03"  />
```



#### 路由传参

路由传参指的是路由携带参数，路由传参可以用params的方式，首先是Link标签的定义：

```javascript
<li><a href="#/page03/detail/1001">新闻标题一</a></li>
```

接着是Route标签的定义：

```javascript
<Route path="/page03/detail/:newsid" component={ Detail } />
```

组件中获取参数的方式：

```javascript
// 使用props属性获取路由传递过来的参数：
function Detail(props){
    // 通过 props.match.params.newsid 来接收传递的参数
    //console.log(props.match.params.newsid);
    return <p>这是新闻的详情页面,新闻的ID是{ props.match.params.newsid }</p>
}
```



#### 404页面及重定向

```js
<Switch>
    {/* 加上exact是为了让path精确匹配，否则“/”和“/china”的路径会同时匹配 */}
    {/* <Route exact path="/" component={page01} /> */}
    <Route path="/page01" component={ Page01 } />
    <Route path="/page02" component={ Page02 } />
    <Route exact path="/page03" component={ Page03 } />
    <Route path="/page03/detail/:newsid" component={ Detail } />

    {/* 重定向要写在跳转路由的下面 */}
    <Redirect exact from="/" to="/page01" />

    {/* 404的路由需要写在最下面 */}
    <Route component={NotFound} />
</Switch>
```



#### 子路由

<!-- react路由是直接写在组件中的，所以子路由就可以直接写在跳转的组件中就可以了，这个时候可以不写HashRouter容器标签，有时候，我们还需要在逻辑代码中跳转路由，这个叫做编程式导航，跳转的方式如下:-->

```js
this.props.history.push('/layout');
```

```js
import React, { Component } from 'react';
import { HashRouter, Link, Route, Switch, Redirect } from 'react-router-dom'


class Login extends Component {
    constructor(props) {
        super(props)
    }
    fnJump = () => {
        this.props.history.push('/layout');
    }
    render() {
        return (
            <form action="">
                <input type="text" /><br />
                <input type="password" /><br />
                <input type="submit" value="登录" onClick={this.fnJump} />
            </form>
        )
    }
}
function Menu01(props) {
    return (
        <div>管理页面内容一</div>
    )
}
function Menu02(props) {
    return (
        <div>管理页面内容二</div>
    )
}
function Layout(props) {
    return (
        <div>
            <div>
                <Link to="/layout/menu01">菜单一</Link>
                <Link to="/layout/menu02">菜单二</Link>
            </div>
            <hr />
            <Route exact path="/layout" component={Menu01} />
            <Route exact path="/layout/menu01" component={Menu01} />
            <Route exact path="/layout/menu02" component={Menu02} />
        </div>
    )
}


class Myrouter extends Component {
    render() {
        return (
            <HashRouter>
                <Route exact path="/" component={Login} />
                <Route path="/login" component={Login} />
                <Route path="/layout" component={Layout} />
            </HashRouter>
        )
    }
}

export default Myrouter;
```



## 数据传递

#### 父传子和子传父

父传子指的是父组件向子组件传递数据，这种传递方式可以使用子组件的props属性，将父级的值传递进去。

子传父指的是子组件向父组件传递数据，这种传递方式也是使用子组件的props属性，将父级的一个方法的引用传递到子组件中，子组件中调用这个方法，将子组件中的数据传递出来给父级。

```js
class Father extends Component{
    constructor(props){
        super(props);
        this.state = {
            name:'Rose',
            iNum:0

        }
    }
    // 定义传给子组件的函数
    fnGetData=(num)=>{
        this.setState({
            iNum:num
        })
    }
    render(){
        return (
            <div>
                <h1>父组件</h1>
                <p>接收子组件传过来的数据：{ this.state.iNum }</p>
                <Son 
                // 传递值给子组件
                name={ this.state.name } 
                // 传递函数给子组件
                fnSet={ this.fnGetData } 
                />
            </div>
        )
    }
}
class Son extends Component{
    render(){
        return (
            <div>
                <h2>子组件</h2>
                <p>{ this.props.name }</p>
                <input type="button" value="传数据给父组件" onClick={()=>{ this.props.fnSet(10) } } />
            </div>
        )
    }
}
export default Father;
```



#### 个同辈组件之间传递数据

使用系统提供的events模块，实例化模块中的EventEmitter类，通过实例的emit方法来发送数据，通过实例的on方法来接收数据。

```js
import { EventEmitter } from 'events';

// 实例化一个对象bus，兄弟组件之间通过它进行通信
const bus = new EventEmitter();
class Father extends Component{
    render(){
        return(
            <div>
                <h1>这是父组件</h1>
                <Son01 />
                <Son02 />
            </div>
        )
    }
}
class Son01 extends Component{
    constructor(props){
        super(props);
        this.state = {
            msg:''
        }
    }
    // 绑定监听，接收数据
    componentDidMount(){
        bus.on('go',dat=>{
            this.setState({
                msg:dat.msg
            })
        })           
    }
    render(){
        return(
            <div>
                <h2>这是子组件一</h2>
                <p>接收子组件传递过来的值：{ this.state.msg }</p>
            </div>
        )
    }
}
class Son02 extends Component{
    // 定义传递数据的函数
    fnSend=()=>{
        bus.emit('go',{
            msg:'我来也'
        })
    }

    render(){
        return(
            <div>
                <h2>这是子组件二</h2>
                <input type="button" value="传值给子组件一" onClick={ this.fnSend } />
            </div>
        )
    }
}

export default Father;
```



## Redux

- Store 数据调控中心，相当于是数据仓库的管理员，组件可以通过它得到数据

- Action Creators 组件发起的数据变更需求，它相当于是仓库的变更工作工单，管理员会拿到工单，然后去仓库变更数据

- Reducers 数据仓库，数据的存储和变更在这里完成，完成之后，仓库会给Store返回最新的数据。

  

```
npm i redux -S
```

<!--在src文件夹中创建一个store文件夹，里面单独放置redux相关代码，首先在这个文件夹中创建一个index.js文件，在这个文件中编写上面图片中的Store模块对应的代码：-->

```js
// 导入创建store的的工厂函数
import { createStore } from 'redux';
// 导入等会要创建的reducer文件
import reducer from './reducer'

// 创建一个store
let store = createStore(reducer);

export default store;
```

<!--接着在这个文件夹中创建一个reducer.js文件，文件里面编写上面图片中的Reducers对应的代码：-->

```js
const defaultState = {
    "aList":[],
    "sTodo":""
};
// state参数起始存放的是原始数据
// action参数是由store传递进来的数据变更，它其实是一个对象
let reducer = (state=defaultState,action)=>{
    return state;
}

export default reducer;
```

接着就能在组件中使用这个数据了

```javascript
// 导入store文件夹下面的index.js文件，下面是简写方式
import store from './store';

class Todolist extends Component{
    constructor(props){
        super(props);
        // 执行store.getState方法获取store中的数据
        this.state = store.getState();
    }
    ......
```



#### 变更数据

要变更数据，就在组件的方法中编写Action Creators对应的代码：

```javascript
// 数据变更的action其实就是一个对象
// 通过store.dispatch方法将数据变更传给Store
fnChange=(e)=>{
    const action = {
        type:'change_val',
        value:e.target.value
    }
    store.dispatch(action);
}
```

Store会自动将变更传给Reducers，接着在reducer.js中编写对应变更代码：

```javascript
let reducer = (state=defaultState,action)=>{
    if(action.type=='change_val'){
        const oNewState = JSON.parse(JSON.stringify(state));
        oNewState.sTodo = action.value;
        return oNewState;
    }
    return state;
}
export default reducer;
```

数据变更后，Store拿到最新数据，需要通知组件更新state里面的数据，组件需要订阅Store的通知，一旦收到通知，就让组件重新获取Store中的数据：

```javascript
class Todolist extends Component{
    constructor(props){
        super(props);
        this.state = store.getState();
        // 订阅store中的通知，收到通知，就触发fnStoreChange方法重新或者数据
        // 订阅通知会返回一个函数，这个函数可以解除订阅
        this.unsubscribe = store.subscribe(this.fnStoreChange);
    }
    // 向store获取数据
    fnStoreChange=()=>{
        this.setState(store.getState());
    }
```

数据如果存放在数据库，组件初始化时可以通过ajax将远程数据读取回来存入redux中：

```javascript
// 在生命周期函数中自动去获取远程数据
componentDidMount(){
        axios.get('./data.json').then(res=>{
            store.dispatch({
                type:'init_data',
                value:res.data
            })
        })
    }
```

在reducer.js中添加对应代码：

```javascript
if(action.type=='init_data'){       
    return action.value;
}
```





## React-redux

react-redux模块是对redux的使用进行优化，redux使用中，每个使用redux的组件都需要导入store，而且如果想跟踪store的变化，还需要订阅store，在组件销毁时还需要撤销订阅，这样使用非常麻烦，使用react-redux可以优化这些操作。



首先需要安装react-redux:

```js
npm install react-redux
```

在react应用的index.js文件中导入store，然后在最外层的组件中通过provider注入store，那么，在组件里面引入的组件都不需要导入store了。

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import store from './store'

import App from './App'

const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
)
```

接下来，要应用redux的组件需要用connect来连接stroe，并且通过mapStateToProps和mapDispatchToProps， 将组件中的属性和方法全部映射到组件的props属性中。

```js
import React from 'react'
import { connect } from 'react-redux';

const List =props=>{
    return (
        <div>
               {
                props.aList.map...
               }
               <button onClick={ ()=>{ props.fnAddCart(item) } }>按钮</button>    
        </div>
    )
}

// 映射一般的属性和方法到props属性上
const mapStateToProps = (state) => {
    return {
        aList:state.aList
    }
}

// 映射操作redux的方法到props属性上
const mapDispatchToProps = dispatch =>{
    return {
        fnAddCart(goods){
            dispatch({
                type:'ADD_GOODS',
                goods:goods
            })
        }
    }
}

// 导出connet方法的返回值，方法将组件名作为参数传入
export default connect(mapStateToProps,mapDispatchToProps)(List);
```