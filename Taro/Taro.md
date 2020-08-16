1.Taro自带路由跳转方式

```js
// 跳转到目的页面，打开新页面
Taro.navigateTo({
  url: '/pages/page/path/name'
})

// 跳转到目的页面，在当前页面打开
Taro.redirectTo({
  url: '/pages/page/path/name'
})

this.$router.params		//	类组件获取路由参数方式
useRouter()				//	函数组件获取路由参数方式
```

2.classnames

```js
import classNames from 'classnames`;

classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'
```

2.Taro-ui配置

```js
npm install taro-ui
//	如需在h5使用taro-ui需要在config/index.js添加配置
h5:{
    exnextMModules:['taro-ui']
}
//	taro-ui	组件名都是At开头	AtButton/AtMessage
```

3.Taro主题颜色定制

```scss
/* src/custom-theme.scss */
$color-brand: #02b875;
$color-brand-light: #41ca98;
$color-brand-dark: #02935e;

/*	app.scss */
@import './custom-theme.scss';

@import '~taro-ui/dist/style/components/button.scss';
@import '~taro-ui/dist/style/components/fab.scss';
@import '~taro-ui/dist/style/components/icon.scss';
@import '~taro-ui/dist/style/components/float-layout.scss';
@import '~taro-ui/dist/style/components/textarea.scss';
@import '~taro-ui/dist/style/components/message.scss';
@import '~taro-ui/dist/style/components/avatar.scss';
```

4.Taro判断当前运行环境

```js
 const isWeapp = Taro.getEnv()===Taro.ENV_TYPE.WEAPP
 const isAlipay = Taro.getEnv()===Taro.ENV_TYPE.ALIPAY
```

5.redux-Logger

<!--用于在发起action时,在控制台打印action以及前后store中保存的状态信息-->

```js
import { createLogger } from 'redux-logger'
```

6.combineReducers

```js
import { combineReducers } from 'redux'

import user from './user'
import post from './post'

export default combineReducers({
  user,
  post,
})
//	组合 user Reducer 和 post Reducer 中的状态，并将其合并成一颗形如 { user, post } 的状态树，其中 user 属性保存这 user Reducer 的状态，post 属性保存着 post Reducer 的状态。
//	分发 Action，当组件中 dispatch 一个 Action， combineReducers 会遍历 user Reducer 和 post Reducer，当匹配到任一 Reducer 的 switch 语句时，就会响应这个 Action。
```

7.useDispatch

```js
//	这个 Hooks 返回 Redux store 的 dispatch 引用。你可以使用它来 dispatch actions。
import { useDispatch } from '@tarojs/redux'
 const dispatch = useDispatch()
 dispatch({
     type:xxx,
     payload:xxx
 })
```

8.useSelector

```js
//	useSelector 允许你使用 selector 函数从一个 Redux Store 中获取数据。
//	相当于connect函数的mapStateToProps 函数
//	Selector会在组件每次渲染时调用,useSelector同样会订阅Redux store,在dispatch时调用
```

