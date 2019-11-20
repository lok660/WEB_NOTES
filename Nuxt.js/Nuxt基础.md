## 一、Nuxt环境搭建

#### 1.nuxt.js搭建项目

```js
npx create-nuxt-app	your-project			//	npx创建项目

Project name                                //  项目名称
Project description                         //  项目描述
Use a custom server framework               //  选择服务器框架
Choose features to install                  //  选择安装的特性
Use a custom UI framework                   //  选择UI框架
Use a custom test framework                 //  测试框架
Choose rendering mode                       //  渲染模式
Universal                                   //  渲染所有连接页面
Single Page App                             //  只渲染当前页面
```



#### 2.nuxt.config.js

```js
const pkg = require('./package')
module.exports = {
  mode: 'universal',    //  当前渲染使用模式
  head: {       //  页面head配置信息
    title: pkg.name,        //  title
    meta: [         //  meat
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: pkg.description }
    ],
    link: [     //  favicon，若引用css不会进行打包处理
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
  loading: { color: '#fff' },   //  页面进度条
  css: [    //  全局css（会进行webpack打包处理）
    'element-ui/lib/theme-chalk/index.css'  
  ],
  plugins: [        //  插件
    '@/plugins/element-ui'
  ],
  modules: [        //  模块
    '@nuxtjs/axios',
  ],
  axios: {},
  build: {      //  打包
    transpile: [/^element-ui/],
    extend(config, ctx) {       //  webpack自定义配置
    }
  }
}
```



## 二、常用配置项

#### 1.plugins

<!--nuxtjs不会自动提取插件中的文件,因此需要手动添加component.js文件到plugins-->

```js
module.exports = { 
	plugins: 
		[ '~/plugins/components' ]
}
```

#### 2.端口与IP

开发中遇到端口被占用或指定IP的情况,在package.json中添加以下

```js
"config":{
    "nuxt":{
      "host":"127.0.0.1",
      "port":"1000"
    }
}
```

#### 3.修改SEO

```js
 head: {
    title: 'wfaceboss',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 'Nuxt.js project' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
```

#### 4.全局CSS

```css
//	common.css
html{
	color:red
}
```



```js
//	nuxt.config.js
css:['~/assets/css/normailze.css']
```

## 三、Nuxt路由

#### 1.基本路由

<!--nuxt是根据pages目录结构自动生成vue-router模块的路由配置-->

```js
└─pages
    ├─index.vue
    └─user
      ├─index.vue
      ├─one.vue
```

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

#### 2.页面跳转

- 不能写成a标签,因为是重新获取一个新的页面,并不是SPA
- <nuxt-link to="/users"></nuxt-link>
- this.$router.push('/users')

#### 3.动态路由

nuxt中的动态路由,需要创建**对应的以下划线作为前缀**的Vue文件或目录

获取动态参数{{$route.params.id}}

4.跳转路由传递参数并且取值

(1)使用nuxt传递参数

```vue
<template>
  <div>
    <ul>
      <li><nuxt-link :to="`informa/${item.newsCode}-${item.newsType}`"></li>
    </ul>
  </div>
</template>
```

name其实只想的是路由,而路由区分大小写,所以to后面区分大小写

5.nuxt-link与router-link组件作用一样,但是跳转不能直接写a标签,因为a标签相当于重新刷新页面

this.$router.push({path:'/student'})

6.动态路由,在nuxtjs里面定义带参数的动态路由,需要创建对应的 **以下划线作为前缀**的 vue文件或目录, 获取动态参数 {{$route.params.id}}

7使用ElementUI

1)npm i element-ui -S

2)在plugins文件夹下创建ElementUI.js文件

```js
import Vue from 'vue'

import ElementUI from 'element-ui'

Vue.use(ElementUI)
```



3)在nuxt.config.js添加配置

```js
css:[

'element-ui/lib/theme-chalk/index.css

],

plugins:[

src:'~/plugins/ElmentUI',ssr:true		//ssr,表示这个插件只在服务端起作用

],

build:{

vendor:['element-ui']			//防止element-ui被多次打包

}
```



9.asyncData是Nuxj.js中扩展的方法,使我们可以在设置组件之前获取数据(每次在组件加载之前调用)

!!!:created里面发,是在客户端发,我们要求是服务端渲染,且aysncData不能用this(因为是在组件初始化前被调用的)

使用方法:asyncData(context,callback){callback(null,data)}

context.route.params.xxx获取参数

callback(new Error(),data)

9.vendor防止被多次打包

10.layout是公共布局组件,里面的nuxt是放路由内的组件

11.SEO优化,在nuxt.config.js文件中的headJSON中修改,主要优化的是meta标签,里面可定义自己的SEO

12.Nuxt开发环境中不支持IP访问,在package.json中添加一下配置

```js
"config": {
    "nuxt": {
        "host": "0.0.0.0",          
        "port": "3000"      
    } 
}
```



13.配置文件

```js
const pkg = require('./package')
module.exports = {
  mode: 'universal',    //  当前渲染使用模式
  head: {       //  页面head配置信息
    title: pkg.name,        //  title
    meta: [         //  meat
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: pkg.description }
    ],
    link: [     //  favicon，若引用css不会进行打包处理
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  },
  loading: { color: '#fff' },   //  页面进度条
  css: [    //  全局css（会进行webpack打包处理）
    'element-ui/lib/theme-chalk/index.css'  
  ],
  plugins: [        //  插件
    '@/plugins/element-ui'
  ],
  modules: [        //  模块
    '@nuxtjs/axios',
  ],
  axios: {},
  build: {      //  打包
    transpile: [/^element-ui/],
    extend(config, ctx) {       //  webpack自定义配置
    }
  }
}

```

14.Nuxt的路由动画效果

> ​	1.添加样式文件	/assets/css/normailze.css(没有请自行建立)

```css
.page-enter-active, .page-leave-active {
    transition: opacity 2s;
}

.page-enter, .page-leave-active {
    opacity: 0;
}
```

> 2.文件配置

```css
css:['assets/css/main.css']
```

> ​	3.需要使用<nuxt-link>组件来制作跳转链接,才有效果

```vue
<li><nuxt-link :to="{name:'news-id',params:{id:123}}">News-1</nuxt-link></li>
```

Nuxt生命周期

```js
export default {
  middleware () {}, //服务端
  validate () {}, // 服务端
  asyncData () {}, //服务端
  fetch () {}, // store数据加载
  beforeCreate () {  // 服务端和客户端都会执行},
  created () { // 服务端和客户端都会执行 },
  beforeMount () {}, 
  mounted () {} // 客户端
}

```

