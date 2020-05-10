Entry

> 入口文件(可以多个)

```js
//	单入口:entry是一个字符串
module.exports = {
    entry:'./path/to/my/entry/file.js'
}
//	多入口:entry是一个对象
module.exports = {
    entry:{
        app:'./src/app.js',
        adminApp:'./src/adminApp.js'
    }
}
```

Output

> 输出文件(只有一个,但是可以使用占位符)

```js
 output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js'	//	占位符
  }
```

Loaders

> 通过Loaders支持其他JS,JSON之外的文件类型打包

- babel-loader				转换ES6,ES7等JS新特性语法
- css-loader					支持.css文件的加载和解析
- less-loader					将less文件转换成css
- ts-loader						将TS转换成JS
- file-loader						进行图片,字体等的打包
- url-loader						与file-loader相同作用外,还支持较小资源自动base64
- raw-loader						将文件以字符串的形式导入
- thread-loader					多进程打包JS和CSS

```js
module:{
    rules:[
        {
            test:/\.txt$/,		//	指定匹配规则
            use:'raw-loader'	//	指定使用的loader名称
        }
    ]
}
```

Plugin

> 用于bundle文件的优化,资源管理和环境变量注入,作用域整个构建过程

- CommonsChunkPluign						将chunks相同的模块代码提取成公共JS
- CleanWebpackPlugin							清除构建目录
- ExtractTextWebpackPlugin					将CSS从bunlde文件里提取成一个独立的CSS文件
- CopyWebpackPlugin							将文件或者文件夹拷贝到构建的输出目录
- HtmlWebpackPlugin							创建html文件去承载输出的bundle
- UglifyjsWebpackPlugin						压缩JS
- ZipWebpackPlugin								将打包出的资源生产一个zip包

```js
plugins:[
    new HtmlWebpackPlugin({
        template:'./src/index.html'		//	放入plugins数组里
    })
]
```

Mode

> 指定当前构建环境		production,development还是none(默认production)

```js
development		//	设置process.env.NODE_ENV值为development,开启NamedChunksPlugin,NameModulesPlugin

production		//	设置process.env.NODE_ENV值为production,开启			FlagDependencyUsagePlugin,FlagIncludedChunksPlugin,ModuleConcateationPlugin,NoEmitOnErrorsPlugin,OccurrenceOrderPlugin,SideEffectsFlagPlugin和TerserPlugin

node			//	不开启任何优化选项
```





热更新 

```js
//	webpack.config.js
const webpack = require('webpack')  
plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  devServer: {
    contentBase: './dist',
    hot: true
  }
//	package.json	
scripts:{
    "dev":"webpack-dev-server --open"
}
```

文件指纹

```js
//	js文件指纹,使用[chunkhash]
filename:'[name][chunkhash:9].js'

//	css文件指纹,设置MiniCssExtractPlugin的filename,使用[contenthash]
//	与style-loader互斥
plugins:[
    new MiniCssExtractPlugin({
    filenameL:'[name][contenthash:8].css`
})
]

//	图片文件指纹,file-loader的name,使用[hash]
name:'img/[name][hash:8].[ext]'
```

代码压缩

```js
//	js文件压缩
//	内置了	uglifyjs-webpack-plugin

//	css文件压缩
//	使用optimize-css-assets-webpack-plugin,同时使用cssnano
plugins:[
    new OptimizeCSSAssetsPlugin({
        assetNameRegExp:/\.css$/g,
        cssProcessor:reuire('cssnano')
    })
]

//	html文件压缩
//	修改html-webpack-plugin,设置压缩参数
new HtmlWebpackPlugin({
      template: path.join(__dirname, 'src/search.html'),
      filename: 'search.html',
      // 指定html使用的chunks
      chunks: ['search'],
      // 自动注入html
      inject: true,
      minify: {
        html5: true,
        collapseWhitespace: true,
        preserveLineBreaks: false,
        minifyCSS: true,
        minifyJS: true,
        removeComments: false
      }
    })
```

自动补齐CSS3前缀

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')  
{
        test: /.less$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'less-loader', {
            loader: 'postcss-loader',
            options: {
              plugins: () => {
                require('autoprefixer')({
                  browsers: ['last 2 version', '>1%', 'ios 7']
                })
              }
            }
          }
        ]
      },
```

px转rem

```js
//	webpack css loader   
{
            loader: 'px2rem-loader',
            options: {
              // 多少分辨率倍数
              remUnit: 75,
              // px转rem后精确后8位小数点
              remPrecesion: 8
            }
          }

//	index.html
<script type="text/javascript" >
    (funciont(win,lib){
     //	使用淘宝的	lib-flexible.js
     //	需要放置head前处,并立即执行
     })()
</script>

```

