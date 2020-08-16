1.React.lazy

```js
//	ago
import OtherComponent from './OtherComponent';

//	new
const OtherComponent = React.lazy(() => import('./OtherComponent'));

//	React.lazy 接受一个函数，这个函数需要动态调用 import()。它必须返回一个 Promise，该 Promise 需要 resolve 一个 defalut export 的 React 组件
```

> React.lazy目前只支持 default exports出的组件,如果是 name exports出的组件,需要创建一个中间组件

```js
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;

// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";

// MyApp.js
import React, { lazy } from 'react';
const MyComponent = lazy(() => import("./MyComponent.js"));
```



2.Context(与redux相同)

```js
//	只有当所处组件没匹配到Provider时,defaultValue才生效
const MyContext = React.createContent(defaultValue)

<MyContext.Provider value={/* 某个值 */}>

```

3.深入JSX

```jsx
//	属性展开
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}

//	props.children就是Hello world!
<MyComponent>Hello world!</MyComponent>

//	React能返回数组
render() {
  // 不需要用额外的元素包裹列表元素！
  return [
    // 不要忘记设置 key :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

```jsx
//	函数作为子元素,将props.children作为回调传递
//	调用子元素回调 numTimes 次，来重复生成组件
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```

```jsx
//	布尔类型,Null,undefined将会被jsx忽略

//	showHeader为true时才渲染Header组件
<div>
  {showHeader && <Header />}
  <Content />
</div>

<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```



4.性能优化

```jsx
class CounterButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }

    //	当props.color或者state.count值改变时,才更新组件
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

//	PureComponent
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
```



5.Portals

将子节点渲染到父组件以外的DOM节点

典型应用:当父组件有overflow:hidden或z-index时,需要子组件在视觉上跳出父容器[对话框,悬浮卡,提示框]

```jsx
ReactDOM.createPortal(child, container)
//	child:任何可渲染的React子元素
//	container:DOM元素
```

```jsx
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
}
```

6.Refs

官方推荐场景:

- 管理焦点,文本选择或媒体播放
- 触发强制动画
- 集成第三方DOM库

```jsx
//	创建Refs
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}

//	访问Refs
const node = this.myRef.current
```

ref值根据节点类型而有所不同

```jsx
//	html元素
构造函数中使用React.createRef()创建的ref接收底层DOM元素作为其current属性
//	自定义class组件
ref对象接收组件的挂载实例作为current属性
//	不能再函数组件上使用ref,因为他们没有实例
```

```jsx
//	函数组件中使用ref
function CustomTextInput(props) {
  // 这里必须声明 textInput，这样 ref 才可以引用它
  let textInput = React.createRef();

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />

      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```

 

7.constructor

<!--React构造函数仅用于以下情况-->

- 通过给this.state赋值对象初始化state
- 为事件处理函数绑定实例

避免将props值赋值给state

```jsx
constructor(props) {
 super(props);
 // 不要这样做
 this.state = { color: props.color };
}
```

