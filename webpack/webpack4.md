## 概念

https://www.webpackjs.com/concepts/



## 安装

生成 `package.json`

```bash
npm init -y 
```



安装`webpack` 和 `webpack-cli`

```bash
#-D  开发依赖，上线不需要
yarn add webpack webpack-cli -D
```



打包文件命令

```bash
npx webpack
#这个指令直接就默认生成dist的main.js，默认是生产环境
#支持模块化  可以用commonJS语法
```



安装常用 `loader`  和`plugin`

```bash
# webpack-dev-server是webpack官方提供的一个小型Express服务器。使用它可以为webpack打包生成的资源文件提供web服务。
# webpack-dev-server 主要提供两个功能：
# - 为静态文件提供服务
# - 自动刷新和热替换(HMR)
yarn add webpack-dev-server 
```



## 基础配置 `webpack.config.js`

### `html` 插件

安装 `html-webpack-plugin`

```bash
#安装 挂载到内存的插件
yarn add html-webpack-plugin -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

const htmlPlugin = new HtmlWebpackPlugin({
 template: './src/index.html' ,//模板,导出的html文件将以此文件作为模板
 filename: 'index.html',
    
 /* //压缩 html ，上线版本可以使用
 minify: {
 //删除属性双引号
 removeAttributeQuotes:true,
 
 //变成一行
 collapseWhitespace:true,
 },
 
 //有一个hash值
 hash : true */
})

/*  配置 html-webpack-plugin的第一种方法  一般不用  直接在package.json中配置就可以
devServer: {
//开发服务器的配置
port: 3000,
progress: true,
contentBase: "./src",
//启动gzip压缩
compress: true
}, */

mode: "development", //模式，有2种：production(默认)、development
entry: "./src/index.js", //入口
output: {
	// filename: "bundle.js.[hash:8]", //配置hash  :8只显示8位
	filename: "bundle.js", //打包后的文件名
	path: path.resolve(__dirname, "dist") , //路径必须是一个绝对路径,默认是在dist目录
	// publicPath:  'http://www.hanke.com' //公共的路径, 导出html文件里的所有src属性值都会带有该域名前缀 
},

plugins: [   //数组 放着所有webpack的插件
	htmlPlugin
],
    
module: {   //模块
    rules: [  
        //配置 html读取img的src
        {
            test: /\.html$/,  // .html的文件，匹配文件后缀
            use: 'xxx-loader' // 使用某个loader
            // loader的特点是功能单一
            // loader的用法  字符串只用一个loader  多个需要数组
            // loader 的顺序  默认从右到左执行 从下到上执行
        },
    ]
};
```



导出的 `./dist ` 里面的 `bundle.js` 的文件：

```js
(function(modules) { // webpackBootstrap   webpack入口函数
	// The module cache   定义一个缓存
	var installedModules = {};
	// The require function    配置实现了 require方法
	function __webpack_require__(moduleId) {
		// Check if module is in cache   检查是否在缓存中
		if(installedModules[moduleId]) {
			return installedModules[moduleId].exports;
		}
		// Create a new module (and put it into the cache)
		var module = installedModules[moduleId] = {
			i: moduleId,
			l: false,
			exports: {}
		};
		// Execute the module function    call方法
		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
		// Flag the module as loaded
		module.l = true;
		// Return the exports of the module   返回module.exports
		return module.exports;
	}
return __webpack_require__(__webpack_require__.s = "./src/index.js");//入口模块
})
({
"./src/a.js":   //key->模块的路径
/*! no static exports found */
(function(module, exports) {  //value函数
	eval("console.log('我能行')\n\n//# sourceURL=webpack:///./src/a.js?");
}),
"./src/index.js":
(function(module, exports, __webpack_require__) {
//递归 先执行./src/a.js  再执行index.js下边的方法
	eval("__webpack_require__(/*! ./a.js */ \"./src/a.js\")\r\n\r\nconsole.log('ok')\n\n//# sourceURL=webpack:///./src/index.js?");
})
});
```



### 样式处理

需要安装的模块：

```bash
#安装 css-loader style-loader 解析css文件
yarn add css-loader style-loader -D

#安装 less less-loader 解析less文件（前边的css以及style都用的到 还有一个顺序问题）
yarn add less less-loader -D 

#安装 node-sass sass-loader 解析sass文件
yarn add node-sass sass-loader -D 

#安装 mini-css-extract-plugin 抽离css  将css变成link的形式引入
yarn add mini-css-extract-plugin -D

#安装 postcss-loader autoprefixer自动为css添加私有化前缀 
yarn add postcss-loader autoprefixer -D

#安装 optimize-css-assets-webpack-plugin 来自动压缩css文件
yarn add optimize-css-assets-webpack-plugin -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

// 将css变成link引入的插件
const MiniCssExtractPlugin = require("mini-css-extract-plugin")

// 压缩css的插件
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');

const htmlPlugin = new HtmlWebpackPlugin({
  template: './src/index.html',
  filename: 'index.html',
})

mode: "development",
  entry: "./src/index.js",
  output: {
  	// filename: "bundle.js.[hash:8]",
  	filename: "bundle.js",
    path: path.resolve(__dirname, "build"),
},

plugins: [
  htmlPlugin,
  new MiniCssExtractPlugin({
    filename: './css/main.css',
  }),
],

module: {   //模块
  //规则    css-loader 处理css文件 解析@import这种语法
	// style-loader  把css标签插入到header中
  rules: [
    {
      test: /\.css$/,
      use: [
        {
          loader: 'style-loader',
          options: {
            //将style标签插入到顶部
            insertAt: 'top'
          }
        },
        'css-loader',
        //为loader添加私有化前缀
        'postcss-loader',
      ]
    },
    /* //配置less
		{
			test:/\.less$/,
			use:[
 			  {
    			loader:'style-loader',
    			options: {
     			 //将style标签插入到顶部
      			insertAt: 'top'
    			}
  		  },
  			'css-loader',
  			'less-loader' //less -> css
			]
		} */
//抽离为
    {
      test: /\.less$/,
      use: [
        MiniCssExtractPlugin.loader,
        'css-loader',
        'postcss-loader',
        'less-loader' //less -> css
      ]
    },
  ]
};
```



**在 `./src` 目录中，**

`index.css` 文件：

```css
@import './a.css';
body {
  background-color: antiquewhite;
  transform: rotate(45deg);
}
```

`a.css`  文件：

```css
body {
  color: red;
}
body {
  div {
    border: 1px solid gray;
  }
}
```

`index.less` 文件：

```less
body {
  div {
    border: 1px solid gray;
  }
}
```

在`./build` 目录下，有 `bundle.js` 、 `index.html`  以及 `./build/css` 的 `main.css`



### 转换 `ES6` 语法

需要安装的模块：

```bash
#安装 babel-loader @babel/core @babel/preset-env 来转换es6 语法
yarn add babel-loader @babel/core @babel/preset-env -D

#安装 @babel/plugin-proposal-class-properties 来解析类语法
yarn add @babel/plugin-proposal-class-properties -D

#安装 @babel/plugin-proposal-decorators 来解析 @log(decorators-legacy)这种语法 （这个没见过)
#这里的配置出现了不少错误  还是不怎么熟练 
yarn add @babel/plugin-proposal-decorators -D

#安装 @babel/plugin-transform-runtime 来处理js语法
yarn add @babel/plugin-transform-runtime -D
yarn add @babel/runtime  #什么都不加 这个上线也用得到

#安装 @babel/polyfill 来解决高级用法的问题(如 字符串的includes用法)
yarn add @babel/polyfill  #没有 -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

const htmlPlugin = new HtmlWebpackPlugin({
  template: './src/index.html',
  filename: 'index.html',
})

mode: "development",
  entry: "./src/index.js",
  output: {
  	// filename: "bundle.js",
  	filename: "bundle.js",
    path: path.resolve(__dirname, "build"),
},

plugins: [
  htmlPlugin,
  new MiniCssExtractPlugin({
    filename: './css/main.css',
  }),
],

module: {
  rules: [
    /* 配置babel */
		{
  		test: /\.js$/,
    	use: [
      	{
        	loader: 'babel-loader',
        	options: {
          	presets: ['@babel/preset-env'],
          	plugins: [
            	// '@babel/plugin-proposal-class-properties'  解析class
            	// 上边边解析  @log
            	["@babel/plugin-proposal-decorators", { "legacy": true }],
            	['@babel/plugin-proposal-class-properties', { "loose": true }],
            	'@babel/plugin-transform-runtime'

         	  ]
        	}
      	}
    	],
      //包括
      include: path.resolve(__dirname, 'src'),
      //排除
      exclude: /node_modules/
		}
  ]
};
```

在 `./src` 目录中的 `index.js` 文件：

```js
// 此处都是 es6 语法，由于有些浏览器不支持es6标准，所以要用babel转成es5的代码
require('@babel/polyfill')
require("./a.js");

let fn = () => {
  console.log(2222)
}

fn()

function *gen () {
  yield 1;
}
console.log(gen().next())


console.log('aaa'.includes('a'))

class B {
  c = 2;
}

@log
class A {
	a = 1;
}
var a = new A()
console.log(a.a)

function log(target) {
	console.log(target)
}
```



### `js ` 语法校验

需要安装的模块：

```bash
#安装 eslint eslint-loader 来校验js语法
yarn add eslint eslint-loader -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

const htmlPlugin = new HtmlWebpackPlugin({
  template: './src/index.html',
  filename: 'index.html',
})

mode: "development",
  entry: "./src/index.js",
  output: {
  	// filename: "bundle.js",
  	filename: "bundle.js",
    path: path.resolve(__dirname, "build"),
},

plugins: [htmlPlugin],

module: {
  rules: [
   {
			test: /\.js$/,
      use:[
  			{
    			loader: 'eslint-loader',
    			options: {
      			//强制 pre 之前执行  post 之后  未设置为normal 普通的loader
      			enforce: 'pre'
    			}
  			}
			],
      //排除
      exclude: /node_modules/
		}
  ]
};
```



`.eslintrc.json`  文件：

> 直接在`eslint`官网生成 : https://cn.eslint.org/demo/



### 全局变量的引入

### 

需要安装的模块：

```bash
#安装 jquery 来 测试暴露全局的loader
yarn add jquery -D

#安装 expose-loader 来暴露全局
yarn add expose-loader -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

const htmlPlugin = new HtmlWebpackPlugin({
  template: './src/index.html',
  filename: 'index.html',
})

mode: "development",
  entry: "./src/index.js",
  output: {
  	// filename: "bundle.js",
  	filename: "bundle.js",
    path: path.resolve(__dirname, "build"),
},

plugins: [htmlPlugin],

module: {
  rules: [
    {
			// 添加loader规则 就不再需要import $ from 'expose-loader?$!jquery' 这样，可以直接 import 
    	test: require.resolve('jquery'),
    	use: 'expose-loader?$',
   },
  ]
};
```



在 `./src` 中的 `index.js`  文件：

```js
// import $ from 'expose-loader?$!jquery'; // 内联loader
import $ from 'jquery';
console.log($);
// expose-loader 暴露全局的loader
// pre： 前面执行的loader， normal：普通loader，内联loader，后置postloader 
console.log(window.$);
```



### 图片处理

需要安装的模块：

```bash
#安装 file-loader 默认生成一张新的图片到build目录下 生成图片名字返回
yarn add file-loader -D

#安装 html-with-loader 来解决 img src='' 这样的问题
yarn add html-withimg-loader -D

#安装 url-loader 来处理图片 (一般不用file-loader来处理图片 )
yarn add url-loader -D
```



`webpack.config.js` 文件:

```js
const path = require("path");

// html挂载到内存的插件
const HtmlWebpackPlugin = require("html-webpack-plugin");

const htmlPlugin = new HtmlWebpackPlugin({
  template: './src/index.html',
  filename: 'index.html',
})

mode: "development",
  entry: "./src/index.js",
  output: {
  	// filename: "bundle.js",
  	filename: "bundle.js",
    path: path.resolve(__dirname, "build"),
},

plugins: [htmlPlugin],

module: {
  rules: [
    //配置 html读取img的src
		{
			test: /\.html$/,
 			use: 'html-withimg-loader'
		},
		//配置 file-loader来读取图片
		{
			test: /\.(png|jpg|gif)$/, 
  		//做一个限制  当小于多少k 用base64来转化 base64文件可以减少http请求 但是比原文件大3分之1
			// 否则用file-loader来产生真实的图片
 			use: {
  			loader: 'url-loader',
  			options: {
    		limit: 1,
    		//输出的路径
    		outputPath: 'img/',
    		//只在图片中有一个公共的路径，在解析img的loader中单独配置
    		publicPath: 'http:/111'
  			}
 			}
		}
  ]
};
```



**在 `./src` 中**

 `index.js`  文件：

```js
//file-loader 图片引入 返回一个新图片地址
import logo from './logo.jpg'
console.log(logo)
let img = new Image()
img.src = logo
document.body.appendChild(img)
```

`index.css`  文件：

```css
div {
  background: url('./logo.jpg');
  height: 73px;
  width: 103px;
}
```

### 其他

配置 `package.json`

> ```json
> //重要内容 注意  这样是不对的   json标准中不允许有解析
> "scripts": {
>  "build": "webpack",
>  "dev": "webpack-dev-server --open --port 3000 --hot "
> }
> ```



**为什么是webpack.config.js**

> ```js
> //node_modules 下的webpack-cli 下的bin下的config中的config-yargs.js的这一句话
> 	defaultDescription: "webpack.config.js or webpackfile.js",
> ```



**webpack.config.js改名之后运行**

> ```shell
> npx webpack --config webpack.config.my.js  
> ```
>
> 或者  **package.json中配置**
>
> ```json
> "scripts": {
> "build" : "webpack --config webpack.config.my.js"
> }
> //执行  npm run build  
> "scripts": {
> "build" : "webpack "
> }
> //执行 npm run build -- --config webpack.config.my.js
> ```



**postcss.config.js配置（添加私有化前缀时需要配置）**

> ```js
> module.exports = {
> plugins: [require('autoprefixer')]
> }
> ```



## 配置

### 常用的小插件

**安装**

```bash
# 安装clean-webpack-plugin 每次打包先清除dist目录下的文件
yarn add clean-webpack-plugin -D
# 安装copy-webpack-plugin  打包时可以把一些文件夹的内容拷贝到其他文件夹
yarn add copy-webpack-plugin -D
# webpack 自带的插件 可以加入注释声明谁开发的  webpack.BannerPlugin()
# 安装webpack-dev-middleware webpack开发服务的一个中间件
yarn add webpack-dev-middleware -D
# webpack 自带的插件 判断开发环境   webpack.DefinePlugin()
# 安装webpack-merge 来区分不同的环境 有webpack.base.js webpack.dev.js webpack.pro.js
yarn add webpack-merge -D
```

```js
plugins: [
    new CleanWebpackPlugin(),
    new CopyPlugin([
      {from: 'doc', to: './'}
    ]),
    new webpack.BannerPlugin('make by zww'),
  ]
```



### 打包多页应用

```js
//多入口需要写成一个对象
//两个入口
mode: 'development',
entry: {
  //首页
  home: './src/index.js',
  //other页相关
  other: './src/other.js' 
},
//两个出口
output:{
  // name代表 home或者other  .[hash] 给文件加一个hash串
  // filename: '[name].[hash].js',
  filename: '[name].js',
  path: path.resolve(__dirname,'dist')
},
```



### dev-tool的四个选项

```js
//  增加 devtool 源码映射 可以很方便的调试源代码  
//  源码映射 单独生成一个 source-map文件 出错会标识出错的列和行 大和全
devtool:'source-map',

//  不会单独生成一个文件 但会显示行和列
devtool: 'eval-source-map',

//  不会产生单独列 但会生成一个映射文件
devtool: 'cheap-module-source-map', //保留 后来调试用
//  不会单独生成文件 集成在打包文件中 也不产生列
devtool: 'cheap-module-eval-source-map',
```



### webpack跨域问题

```js
//  server.js 
//  1
const express = require('express')
let app = express()
app.get('/api/user',(req,res)=>{
res.json({
	name: 'myname222'
	})
})
app.listen(3500)
//2
const express = require('express')
let app = express()
app.get('/user',(req,res)=>{
res.json({
	name: 'myname222'
})
})
app.listen(3500)
```



**对应解决方法**

```js
devServer:{
//这是 服务器为 /api/user  1解决方法
proxy : {
'/api':'http://localhost:3500'
} 
// /user的用法   2解决方法
proxy: {
'/api': {
  target: 'http://localhost:3500',
  pathRewrite: {
    '/api':'/'
  }
}  
}
//前端只想单纯模拟方法
before(app){ //提供的方法 相当于钩子 
//写这些代码就不存在跨域问题 
app.get('/user',(req,res)=>{
  res.json({
    name: 'myname-before'
  })
})
}
 
//有服务端，但是不用代理来处理 在服务器端开启webpack 端口用服务端端口
 //server.js   后端加前端合在一起解决跨域
 //webpack自带一个express
let express = require('express')
let app = express()
let webpack = require('webpack')
//需要使用中间件  需要安装
let middle = require('webpack-dev-middleware')
let config = require('./webpack.config.js')
let compiler = webpack(config)
app.use(middle(compiler))
app.get('/api/user',(req,res)=>{
   res.json({
     name: 'myname222'
   })
})
app.listen(3500)
```



### watch监视

```js
//监控 实时打包   类似node里边那个实时监控的
watch: true,
//监控的选项
watchOptions: {
 poll: 1000, //每秒问1000次
 aggregateTimeout: 500 ,//防抖 （类似于函数防抖）
 ignored: /node_modules/ //忽略哪个文件
}, 
```



### resolve属性

```js
resolve: {
 //指定解析的模块
 	modules: [path.resolve('node_modules')],
 // mainFiles: [],//入口文件的名字 默认找index.js
 //或者用 mainFields  入口的字段 先找style 再找main
 	mainFields: ['style','main'],
 //扩展名 可以省略 需配置 extensions  依次解析
 	extensions: ['.js','.css','.json']
 //别名  如 vue的vue-runtime和那个@
 // alias: {
 //   bootstrap: 'bootstrap/dist/css/bootstrap.css'
 // }
}
```



### 生产或者开发环境配置

```js
//webpack.pro.js
let {smart} = require('webpack-merge')
let base = require('./webpack.config.js')
module.exports = smart(base,{
	mode:'production'
})
//webpack.dev.js
let {smart} = require('webpack-merge')
let base = require('./webpack.config.js')
module.exports = smart(base,{
	mode:'development'
})
```



### webpack.config.js

```js
const path = require("path");
const HtmlWpackPlugin = require('html-webpack-plugin')
//内置插件  版权归谁谁谁所有 先导入webpack
const webpack = require('webpack')


//copy插件
const copyWpackPlugin = require('copy-webpack-plugin')

// bannerPlugin 版权声明
const bannerPlugin = new webpack.BannerPlugin('make by hanke ,i will become success!')
// 这个是错误写法  正确写法 应该是解构赋值那样写 说明这个插件包含许多东西
// const CleanWebpackPlugin = require('clean-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

//拷贝文件
const copyPlugin = new copyWpackPlugin(
//接受一个数组 可以多个文件
[{from:'./doc',to:'./'}]
)

// 三个其他的小插件
// 1. cleanWebpackPlugin  // 每次打包会把dist目录下的文件都删除 重新打包
// 2. copyWebpackPlugin
// 3. bannerPlugin
// 前两个 需要第三方模块 第三个内置


// 判断开发环境的插件  DefinePlugin
const definePlugin = new webpack.DefinePlugin({
	DEV: JSON.stringify('production')
})
// 也可以传入一个数组 告诉清理哪些文件夹
const cleanPlugin = new CleanWebpackPlugin()

const htmlPlugin1 = new HtmlWpackPlugin({
template: './src/index.html',
//多个 html	
filename: 'home.html',
//代码块
chunks: ['home']
})
const htmlPlugin2 = new HtmlWpackPlugin({
template: './src/index.html',
//多个 html
filename: 'other.html',
//代码块  放置引入的东西
chunks: ['other']
})

const htmlPlugin = new HtmlWpackPlugin({
template: './src/index.html',
//多个 html
filename: 'index.html',
})
module.exports = {
//多入口需要写成一个对象
//两个入口
mode: 'development',
entry: {
 /* //首页
 home: './src/index.js',
 //other页相关
 other: './src/other.js' */

 index: './src/index.js'
},
//两个出口
output:{
 // name代表 home或者other  .[hash] 给文件加一个hash串
 // filename: '[name].[hash].js',
 filename: '[name].js',
 path: path.resolve(__dirname,'dist')
},
/* //监控 实时打包   类似node里边那个实时监控的
watch: true,
//监控的选项
watchOptions: {
 poll: 1000, //每秒问1000次
 aggregateTimeout: 500 ,//防抖 （类似于函数防抖）
 ignored: /node_modules/ //忽略哪个文件
}, */
//增加 devtool 源码映射 可以很方便的调试源代码  
//  源码映射 单独生成一个 source-map文件 出错会标识出错的列和行 大和全
// devtool:'source-map',

//  不会单独生成一个文件 但会显示行和列
// devtool: 'eval-source-map',

//  不会产生单独列 但会生成一个映射文件
//devtool: 'cheap-module-source-map', //保留 后来调试用
//  不会单独生成文件 集成在打包文件中 也不产生列
devtool: 'cheap-module-eval-source-map',
plugins: [
 // 打包多页面 
 // htmlPlugin1,
 // htmlPlugin2

 //学习 source-map使用
 htmlPlugin,
 // 清除插件
 cleanPlugin,
 // 复制插件
 copyPlugin,
 // 内置插件 添加注释
 bannerPlugin,
 // 内置插件 判断开发环境
 definePlugin
],
//解析第三方模块 commen
resolve: {
 //指定解析的模块
 modules: [path.resolve('node_modules')],
 //或者用 mainFields  入口的字段 先找style 再找main
 // mainFiles: [],//入口文件的名字 默认找index.js
 mainFields: ['style','main'],
 //扩展名 可以省略 需配置 extensions  依次解析
 extensions: ['.js','.css','.json']
 //别名  如 vue的vue-runtime和那个@
 // alias: {
 //   bootstrap: 'bootstrap/dist/css/bootstrap.css'
 // }
},
module: {
 rules: [
   {
     test: /\.css$/,
     use:['style-loader','css-loader']
   },
   {
     test: /\.js$/,
     use: {
       loader: 'babel-loader',
       options: {
         presets: ['@babel/preset-env']
       }     
     },
     exclude: /node_modules/
   }
 ]
},
//跨域问题的设置
devServer:{
 //这是 服务器为 /api/user
 /* proxy : {
   '/api':'http://localhost:3500'
 } */
 // /user的用法
 /* proxy: {
   // 重写的方式 把请求代理到express服务器上
   '/api': {
     target: 'http://localhost:3500',
     pathRewrite: {
       '/api':'/'
     }
   }  
 } */
 //前端只想单纯模拟方法
 /* before(app){ //提供的方法 相当于钩子 
   //写这些代码就不存在跨域问题 
   app.get('/user',(req,res)=>{
     res.json({
       name: 'myname-before'
     })
   })
 } */
 //有服务端，但是不用代理来处理 在服务器端开启webpack 端口用服务端端口
}
}
```



## 优化配置

### 安装

```bash
#实现安装
cnpm i webpack webpack-cli html-webpack-plugin @babel/core babel-loader @babel/preset-env @babel/preset-react webpack-dev-server -D
#安装jquery进行优化测试
cnpm i jquery
#安装moment 时间插件
cnpm i moment
#webpack内置插件 IgnorePlugin 优化
#安装 react react-dom 来测试动态链接库
cnpm i react react-dom
#webpack内置插件 DllPlugin 产生一个manifest.json
#webpack内置插件 DllReferencePlugin 得到一个manifest.json
#安装 happypack来多线程打包webpack  适合项目比较大的时候使用
cnpm i happypack -D
#安装 @babel/plugin-syntax-dynamic-import -D 来转换es6草案语法
cnpm i @babel/plugin-syntax-dynamic-import -D
#webpack 自带插件 HotModuleReplacementPlugin和 NamedModulesPlugin实现热更新
```



### webpack优化项

```js
//1. 不去解析jquery中的依赖库 
noParse: /jquery/,
//2. webpack自带的IgnorePlugin 忽略掉moment中locale引入的东西
	const ignorePlugin = new webpack.IgnorePlugin(/\.\/locale/,/moment/)
//3. 包含与排除
	exclude: /node_modules/,
	include: path.resolve('src')
//4. 连接库的建立 (和后续webpack.react.js以及 DllPlugin DllReferencePlugin使用)
//5. happypack多线程打包webpack
//6. webpack自带优化
// tree-shaking
     import calc from './test'
     console.log(calc.sum(1,2))
 // import 在生产环境下会自动清除没用的东西
 // 相当于tree-shaking 没用代码自动删除
 // import 可以 但是 require就不行
// scope hosting 作用域提升  
      let a = 1
      let b = 2
      let c = 3
      let d = a+b+c //webpack会自动省略可以简化的代码
   console.log(d+'------')
```



### 抽取公共代码

```js
// webpack.config.js中的配置  抽取公共模块
// index引入 a b   other也引入 a b
optimization: {
splitChunks:{  //分割代码块
cacheGrops:{  //缓存组
  common:{    //公共模块 
    chunks: 'initial',  //入口从哪找
    minSize: 0 , //大于0个字节
    minChunks: 0 //引用0次
  }
}
},
vendor: {  //抽离第三方模块 比如jquery
  priority: 1, //先抽离第三方模块
  test: /node_modules/, //引入
  chunks: 'initial',  //
  minSize: 0 , //
  minChunks: 0 //
}
}
```



### webpack懒加载

```js
//需要babel配置 (@babel/plugin-syntax-dynamic-import)
// 生成 两个文件  用另一个时候再加载  （http访问）
let button = document.createElement('button')
button.innerHTML = 'hello'
//vue react懒加载 都是这样
button.addEventListener('click',()=>{
//es6草案中的语法 实现jsonp动态加载文件
import ('./source.js') .then(data=>{
 console.log(data.default)
})
})
document.body.append(button)
```



### webpack热更新

```js
//热更新的概念  只更新页面的一部分 不全部更新
import str from './source.js'
console.log(str)
if(module.hot) {
module.hot.accept('./source.js',()=>{
 //import只能写在页面的顶端
 let str = require('./source.js')
 console.log(str.default)
})
}
```



### webpack.config.js

```js
const path = require('path')
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 模块 happypack 来多线程打包webpack  进程（node中线程与进程关系） 打包文件会加快（文件很小时可能会变慢）
// const Happypack = require('happypack')

const htmlPlugin = new HtmlWebpackPlugin({
	template: './public/index.html',
})

//webpack自带的IgnorePlugin 忽略掉moment中locale引入的东西
const ignorePlugin = new webpack.IgnorePlugin(/\.\/locale/,/moment/)

//webpack自带的             动态引入链接库
const dllReferencePlugin = new webpack.DllReferencePlugin({
	manifest: path.resolve(__dirname,'dist','manifest.json')
})
//  happyplugin配置
// const happyPlugin = new Happypack({
//   id:'js',
//   use: [{
//     loader:'babel-loader',
//     options: {
//       presets: [
//       '@babel/preset-env',
//       '@babel/preset-react',
//       ]
//     }
//   }]
// })

// 热更新所需插件
const hotPlugin = new webpack.HotModuleReplacementPlugin()
const namePlugin = new webpack.NamedModulesPlugin() //打印名字
module.exports = {
mode: "development",
/* optimization: {
splitChunks:{  //分割代码块
cacheGrops:{  //缓存组
  common:{    //公共模块 
    chunks: 'initial',  //入口从哪找
    minSize: 0 , //大于0个字节
    minChunks: 0 //引用0次
  }
},
vendor: {  //抽离第三方模块
  priority: 1, //先抽离第三方模块
  test: /node_modules/, //引入
  chunks: 'initial',  //入口从哪找
  minSize: 0 , //大于0个字节
  minChunks: 0 //引用0次
}
}
}, */
entry: {
index:'./src/index.js',
//测试抽取公共代码用
// other:'./src/other.js'
},
output: {
filename: 'index.js',
path: path.resolve(__dirname,'dist'),
},
//在package.json中配置 好像不起作用  不知道为啥子 不默认打开dist目录 先用这种方法吧
devServer:{
//热更新
hot: true,
port: 3000,
open: true,
contentBase: './dist'
},
plugins: [
htmlPlugin,
//忽略插件
ignorePlugin,
//引入链接库插件
dllReferencePlugin,
//多线程打包文件 
// happyPlugin
// 热更新的两个插件
hotPlugin,
namePlugin
],
module:{
//不去解析jquery中的依赖库 
noParse: /jquery/,
rules:[
{
  test: /\.js$/,
  //指定一个id  可能css也需要多线程打包
  // use: 'Happypack/loader?id=js',
  use: {
    loader:'babel-loader',
    options: {
      presets: [
      '@babel/preset-env',
      '@babel/preset-react',
      ],
      plugins: [
        '@babel/plugin-syntax-dynamic-import'
      ]
    }   
  },
  exclude: /node_modules/     
}
]
}

}
```



### webpack.react.js(输出manifest.json)

```js
const path = require('path')
const webpack = require('webpack')

//webpack自带插件  变成动态链接库
const dllPlugin = new webpack.DllPlugin({ //name==library
   name: '_dll_[name]',
   //manifest.json就是一个任务清单
   path: path.resolve(__dirname,'dist','manifest.json')
})

module.exports = {
mode: 'development',
entry: {
 // test: './src/test.js'
 react: ['react','react-dom']
},
output: {
 filename: '_dll_[name].js', //产生文件名
 path: path.resolve(__dirname,'dist'),
 //指定 var a = '...'
 library: '_dll_[name]',
 //配置commonjs 会变成export["ab"] 配置umd会变成umd模式 可配置 commonjs var this 主要用var(默认就是)
 //libraryTarget: 'var'
},
plugins: [
 //导出 manifest.json 以及 _dll_react.js
 dllPlugin
]
}
```



## Tapable

> 介绍：本质是一种事件流的机制，核心是把各个插件串联起来，实现这一核心的就是Tapable，类似于nodejs的events库，采用的发布订阅的模式  

![Tapable](../images/webpack4.assets/Tapable.webp)

[Webpack 核心模块 tapable 解析](https://www.jianshu.com/p/273e1c9904d2)

安装

```bash
# 安装tapable
yarn add tapable
```



### 同步钩子

#### SyncHook

```js
class SyncHook{ // 同步的钩子(实现)
  constructor(args) { // args 起一个限制作用
    this.tasks = [];
  }
  tap(name,task){
    this.tasks.push(task);
  }
  call(...args){
    this.tasks.forEach(task=>task(...args));
  }
}
let hook = new SyncHook(['name']);
hook.tap('node',function (name) {
  console.log('node',name);
}); // tap用来注册事件的 events.on 
hook.tap('react', function (name) {
  console.log('react', name);
}); 
hook.call('jw');
```



```js
// 同步的方法（同步钩子的实例）
let {SyncHook} = require('tapable');

// 核心就是发布订阅
class Lesson {
  constructor(){
    this.hooks = {
      arch: new SyncHook(['name','age']) // 限制绑定函数的参数
    }
  }
  tap(){ // 希望调用这个方法来在钩子上注册事件
    this.hooks.arch.tap('node', function (name) { // 第一个参数是注释作用 没有实际意义
      console.log('node', name);
    });
    this.hooks.arch.tap('react', function (name) {
      console.log('node', name);
    });
  }
  start(){
    this.hooks.arch.call('jw');
  }
}
let l = new Lesson();
l.tap();
l.start();
```



#### SyncBailHook

```js
class SyncBailHook{ // 同步的钩子(实现)
  constructor(args) { // args 起一个限制作用
    this.tasks = [];
  }
  tap(name,task){
    this.tasks.push(task);
  }
  call(...args){
    let index = 0; // 取任务队列中的第一个
    let ret;
    do{ // 至少做一次
      ret = this.tasks[index++](...args);
    }while(ret === undefined && index < this.tasks.length)
  }
}
let hook = new SyncBailHook(['name']);
hook.tap('node',function (name) {
  console.log('node',name);
 // return '停一停有事'
}); // tap用来注册事件的 events.on 
hook.tap('react', function (name) {
  console.log('react', name);
}); 
hook.call('jw');
```

### 

```js
// 同步的方法（同步钩子的实例）
let {SyncBailHook} = require('tapable');
// Bail保险 熔断型的
// 核心就是发布订阅
class Lesson {
  constructor(){
    this.hooks = {
      arch: new SyncBailHook(['name','age']) // 限制绑定函数的参数
    }
  }
  tap(){ // 希望调用这个方法来在钩子上注册事件
    this.hooks.arch.tap('node', function (name) { // 第一个参数是注释作用 没有实际意义
      console.log('node', name);
      return false; // 如果当前函数有返回值则不会继续执行
    });
    this.hooks.arch.tap('react', function (name) {
      console.log('react', name);
    });
  }
  start(){
    this.hooks.arch.call('jw');
  }
}
let l = new Lesson();
l.tap();
l.start();
```



#### SyncWaterfallHook

```js
class SyncWaterfallHook{ // 同步的钩子(实现)
  constructor(args) { 
    this.tasks = [];
  }
  tap(name,task){
    this.tasks.push(task);
  }
  call(...args){
    let [first,...others] = this.tasks;
    // first就是第一个任务
    // reduce
    others.reduce((prev,next)=>{
      return next(prev);
    }, first(...args))
  }
}
let hook = new SyncWaterfallHook(['name']);
hook.tap('node',function (name) {
  console.log('node',name);
  return 'node还不错'
}); 
hook.tap('react', function (data) {
  console.log('react', data);
  return 'react ok'
}); 
hook.tap('webpack', function (data) {
  console.log('webpack', data);
}); 
hook.call('jw');
```

### 

```js
// 同步的方法（同步钩子的实例）
let {SyncWaterfallHook} = require('tapable');
// 瀑布的意思就是上一个监听函数的结果是下一个人的输入
class Lesson {
  constructor(){
    this.hooks = {
      arch: new SyncWaterfallHook(['name','age']) 
    }
  }
  tap(){ // 希望调用这个方法来在钩子上注册事件
    this.hooks.arch.tap('node', function (name) { 
      console.log('node', name);
      return 'node学的不错'
    });
    this.hooks.arch.tap('react', function (data) {
      console.log('react', data);
      return  'react 更好'
    });
    this.hooks.arch.tap('webpack', function (data) {
      console.log('webpack', data);
    });
  }
  start(){
    this.hooks.arch.call('jw');
  }
}
let l = new Lesson();
l.tap();
l.start();
```



#### SyncLoopHook

```js
class SyncLoopHook{ // 同步的钩子(实现)
  constructor(args) { 
    this.tasks = [];
  }
  tap(name,task){
    this.tasks.push(task);
  }
  call(...args){
    this.tasks.forEach(task => {
      let ret;
      do{
        ret = task(...args);
      } while (ret !== undefined)
    });
  }
}
let total = 0;
let hook = new SyncLoopHook(['name']);
hook.tap('node',function (name) {
  console.log('node',name);
  return ++total == 3?undefined :'1'
}); 
hook.tap('react', function (name) {
  console.log('react', name);
  
}); 
hook.call('jw');
```

### 

```js
let {SyncLoopHook} = require('tapable'); //（同步钩子的实例）
// 某个监听事件 如果返回了值 这个方法会再次执行，只有返回undefined这个方法才会停止执行
class Lesson {
  constructor(){
    this.index = 0;
    this.hooks = {
      arch: new SyncLoopHook(['name','age']) 
    }
  }
  tap(){ 
    this.hooks.arch.tap('node',  (name) => { 
      console.log('node', name);
      return ++this.index == 3?undefined:'再来一次'
    });
    this.hooks.arch.tap('react',  (data) =>{
      console.log('react', data);
    });
    this.hooks.arch.tap('webpack', function (data) {
      console.log('webpack', data);
    });
  }
  start(){
    this.hooks.arch.call('jw');
  }
}
let l = new Lesson();
l.tap();
l.start();
```



### 异步钩子

#### AsyncParralleHook

```js
class AsyncParralleHook { // AsyncParralleHook 的实现
  constructor() {
    this.tasks = [];
  }
  tapAsync(name, task) {
    this.tasks.push(task);
  }
  callAsync(...args) { // express 中的中间件的原理
    let finalCallback = args.pop();
    let index = 0; // Promise.all方法
    let done = () => { // 提供给用户一个done方法
      index++;
      if (index === this.tasks.length) finalCallback();
    }
    this.tasks.forEach(task => task(...args, done));
  }
}
// 实例
let hook = new AsyncParralleHook(['name']);
hook.tapAsync('node', function (name, cb) {
  setTimeout(() => {
    console.log('node');
    cb();
  }, 1000);
});
hook.tapAsync('react', function (name, cb) {
  setTimeout(() => {
    console.log('react');
    cb();
  }, 1000);
});

hook.callAsync('jw', function () {
  console.log('all')
});
```

AsyncParralleHook 的示例

```js
let { AsyncParallelHook } = require('tapable');
// 异步的钩子（串行）并行 需要等待所有并发的异步时间执行后再执行回调函数
// 同时发送多个请求
// tapable库中有3种注册方法为 tap（同步）、tapAsync（异步）、tapPromise(注册的是promise)
// call、callAsync、promise
class Lesson {
  constructor() {
    this.hooks = {
      arch: new AsyncParallelHook(['name'])
    }
  }
  tap() {
    this.hooks.arch.tapAsync('react', (name, cb) => {
      setTimeout(() => {
        console.log('react', name);
        cb('出错了'); // BailHook
      }, 2000);
    });
    this.hooks.arch.tapAsync('react', (name, cb) => {
      setTimeout(() => {
        console.log('react', name);
        cb();
      }, 2000);
    });
  }
  start() {
    this.hooks.arch.callAsync('jw', function () {
      console.log('end');
    });
  }
}
let l = new Lesson();
l.tap();
l.start();

```



#### AsyncParralleHook（promise）

```js
// let {AsyncParralleHook} = require('tapable');

// 绑定事件的三种方式 tap 绑定同步 tapAsync 异步的 tapPromise
//                  call         callAsync      promise
class AsyncParralleHook {
  constructor() {
    this.tasks = [];
  }
  tapPromise(name, task) {
    this.tasks.push(task);
  }
  promise(...args) { // express 中的中间件的原理
   let promises = this.tasks.map(task=>task());
   return Promise.all(promises)
  }
}
// 示例
let hook = new AsyncParralleHook(['name']);
hook.tapPromise('node', function (name) {
  return new Promise((resolve,reject)=>{
    setTimeout(() => {
      console.log('node');
      resolve();
    }, 1000);
  })
});
hook.tapPromise('react', function (name) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('react');
      resolve();
    }, 1000);
  })
});
hook.promise('jw').then(function () {
  console.log('all')
});
```



#### AsyncParallelBailHook（promise）的示例

```js
let { AsyncParallelBailHook } = require('tapable'); // 实现AsyncParallelBailHook
class Lesson {
  constructor() {
    this.hooks = {
      arch: new AsyncParallelBailHook(['name'])
    }
  }
  tap() {
    this.hooks.arch.tapPromise('react', (name) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('react', name);
          resolve();
        }, 2000);
      })
    });
    this.hooks.arch.tapPromise('node', (name) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('node', name);
          resolve();
        }, 2000);
      })
    });
  }
  start() {
    this.hooks.arch.promise('jw').then(function () {
      console.log('end');
    });
  }
}
let l = new Lesson();
l.tap();
l.start();
```



#### AsyncSeriesHook

```js
class AsyncSeriesHook {
  constructor(args) {
    this.tasks = []
  }
  //实现tapAsync
  tapAsync(name,task) {
    this.tasks.push(task)
  }
  callAsync(...args) {
    let finalCallback = args.pop()
    let index = 0
    let next = () => {
      if(index === this.tasks.length) return finalCallback()
      let task = this.tasks[index++]
      task(...args,next)
    }
    next()
  }
}

let h = new AsyncSeriesHook ()
h.tapAsync('node',(name,cb)=>{
  setTimeout(()=>{
    console.log('node',name)
    //cb()对应着 next
    cb()
  },1000)
})
h.tapAsync('react',(name,cb)=>{
  setTimeout(()=>{
    console.log('react',name)
    cb()
  },1000)
})
h.callAsync('hanke',function(){
  console.log('end')
})
```



#### AsyncSeriesHook（promise）

```js
class AsyncSeriesHook {
  constructor() {
    this.tasks = [];
  }
  tapPromise(name, task) {
    this.tasks.push(task);
  }
  promise(...args) { 
    let [first,...others] = this.tasks;
    return others.reduce((p,n)=>{ // 跟redux源码类似
      return p.then(()=>n()); // 把所有的promise串连起来
    }, first(...args));
  }
}
let hook = new AsyncSeriesHook(['name']);
hook.tapPromise('node', function (name) {
  return new Promise((resolve,reject)=>{
    setTimeout(() => {
      console.log('node');
      resolve();
    }, 1000);
  })
});
hook.tapPromise('react', function (name) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('react');
      resolve();
    }, 1000);
  })
});
hook.promise('jw').then(function () {
  console.log('all')
});
```

AsyncSeriesHook（promise）的示例

```js
let { AsyncSeriesHook } = require('tapable');
// AsyncSeriesHook 串行执行 tapAsync + callAsync
//                         tapPromise + promise
class Lesson {
  constructor() {
    this.hooks = {
      arch: new AsyncSeriesHook(['name'])
    }
  }
  tap() {
    this.hooks.arch.tapPromise('react', (name) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('react', name);
          resolve();
        }, 2000);
      })
    });
    this.hooks.arch.tapPromise('node', (name) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('node', name);
          resolve();
        }, 2000);
      })
    });
  }
  start() {
    this.hooks.arch.promise('jw').then(function () {
      console.log('end');
    });
  }
}
let l = new Lesson();
l.tap();
l.start();
```



#### AsyncSeriesWaterfallHook

```js
class AsyncSeriesWaterfallHook {
  constructor(args) {
    this.tasks = []
  }
  //实现tapPomise
  tapPromise(name,task) {
    this.tasks.push(task)
  }
  promise(...arg){
    //redux源码类似（还没开始学习呢）  真的太强了  我完全想不出来  
    // 简直无敌啊
    let [first,...others] = this.tasks
    return others.reduce((promise,next)=>{
      return promise.then(data=> {
        if(data===undefined) {
          return next(...arg)
        }
        else {
          return next(data)
        }  
      })
    },first(...arg))     
  }
  tapAsync(name,task) {
    this.tasks.push(task)
  }
  callAsync(...args) {
    let finalCallback = args.pop()
    let index = 0
    let next = (err,data) => {
      let task = this.tasks[index]
      if(!task || err==='error'){
        return finalCallback()
      }else {
        if(index===0) {
          task(...args,next)
        }else {
          if(data===undefined){
            task(...args,next)
          } else {
            task(data,next)
          }
        }
      }
      index++
    }
    next()
  }
}
let h = new AsyncSeriesWaterfallHook ()
/* h.tapAsync('node',(name,cb)=>{
  setTimeout(()=>{
    console.log('node',name)
    //cb()对应着 next
    cb(null,'结果')
  },1000)
})
h.tapAsync('react',(name,cb)=>{
  setTimeout(()=>{
    console.log('react',name)
    cb()
  },1000)
})
h.callAsync('hanke',function(){
  console.log('end')
}) */
h.tapPromise('node',name=>{
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      console.log('node',name)
      resolve()
    },1000)
  })
})
h.tapPromise('react',name=>{
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      console.log('react',name)
      resolve()
    },1000)
  })
})
h.promise('hanke').then(()=>{
  console.log('end')
})
```

AsyncSeriesWaterfallHook 的示例

```js
let { AsyncSeriesWaterfallHook } = require('tapable')

// 异步串行 先执行完一个再执行下一个 类似于express中间件 先执行完才可以再执行0
// tapAsync  tapPromise都可以
class lesson {
  constructor() {
    this.hooks = {
      arch: new AsyncSeriesWaterfallHook(['name'])
    }
    this.index = 0
  }
  //发布订阅的方式  这个得认真学一学
  //异步方法的第一种 tapAsync
  /* tap() {   //注册 监听函数
    this.hooks.arch.tapAsync('node',(name,cb) => {
      setTimeout(()=>{
        console.log('node',name)
        cb()
      },1000)
    })
    this.hooks.arch.tapAsync('react',(name,cb) => {
      setTimeout(()=>{
        console.log('react',name)
        cb()
      },1000)
    })
  }
  start() {
    this.hooks.arch.callAsync('hanke',function(){
      console.log("end")
    })
  } */
  tap() {
    this.hooks.arch.tapPromise('node', name => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('node', name)
          resolve('err')
        }, 1000)
      })

    })
    this.hooks.arch.tapPromise('react', name => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('react', name)
          resolve()
        }, 1000)
      })
    })
  }
  start() {
    this.hooks.arch.promise('hanke').then(function () {
      console.log("end")
    })
  }
}

let l = new lesson()
l.tap() //注册这两个事件
l.start() //启动钩子函数
```



## 手写 webpack

> 示例项目：https://github.com/cheungww/hanke-webpack

### 项目目录

> 手写webpack项目目录
> ```
> hanke-webpack
> ├── bin
> │   └── hanke-webpack.js
> ├── lib
> │   └── Compiler.js
> └── package.json
> ```
>
> 要使用 手写webpack 的项目目录
>
> ```
> webpack-dev
> ├── node_modules
> │   └── 
> ├── src
> └── package.json
> ```
>
> 

### 安装

```shell
cnpm i webpack webpack-cli -D
#如果 之前没有安装过 npx
npm i npx -g
#安装 babelon 把源码解析成AST @babel/traverse  遍历节点 @babel-types 节点替换 @babel-generator 生成
cnpm i babylon @babel/traverse @babel/types @babel/generator
#安装ejs来渲染模板(类似于tempalte-active)
cnpm i ejs
#安装less 来测试loader
cnpm i less
#安装tapable 来测试plugins
cnpm i tapable
```

### package.json

> ```
> //  hanke-webpack/package.json
> "bin":{
>  "hanke-webpack": "./bin/hanke-webpack.js"
> }
> ```
>
> bin相当于运行哪一个命令，执行哪一个文件

### npm link的流程

> ```shell
> #在hanke-webpack目录下  注意package.json中的bin配置以及后边是bin目录下的hanke-webpack.js 
> npm link 
> # 运行结果为：C:\Users\zww\AppData\Roaming\npm\node_modules\hanke-webpack -> C:\Users\zww\Desktop\fe\webpack\hanke-webpack
> # 即 npm 全局的安装路径下的node_modules\hanke-webpack 指向了 自定义的hanke-webpack目录
> 
> #在webpack-dev下
> npm link hanke-webpack 
> # 运行结果为：C:\Users\zww\Desktop\fe\webpack\webpack-dev\node_modules\hanke-webpack -> C:\Users\zww\AppData\Roaming\npm\node_modules\hanke-webpack -> C:\Users\zww\Desktop\fe\webpack\hanke-webpack
> # 即 项目中hanke-webpack模块 指向了 npm 全局的安装路径下的node_modules\hanke-webpack，而全局下的node_modules\hanke-webpack 又指向了 自定义的hanke-webpack目录
> 
> #注意 这样npm link后可以直接使用hanke-webpack.cmd  就可以在webpack-go5下边运行了
> ```



### hanke-webpack.js最开始配置

> ```js
> //  这里实际上就是 在自己电脑 nodejs中的global (npm link 后)添加了一个 hanke-webpack.cmd 和一个 hanke-webpack文件  然后 可以 hanke-webpack这样直接执行 
> // 因为路径写错了导致一直出错
> #! F:/nodejs/node.exe   //这里对应着自己node的下载地方
> /* 1、需要找到当前执行配置的项目路径，拿到webpack.config.js的配置*/
> let path = require('path')
> 
> // 导入 config配置文件（即 webpack.config.js）
> let config = require(path.resolve('webpack.config.js'))
> 
> let Compiler = require('../lib/Compiler')
> 
> let compiler = new Compiler(config)
> 
> //入口函数
> compiler.hooks.entryOption.call()
> // 标识运行编译
> compiler.run()
> ```

### compile.js

> ```js
> const fs = require('fs')
> const path = require('path')
> const babylon = require('babylon')
> const types = require('@babel/types')
> //es6模块  需要.defalut
> const traverse = require('@babel/traverse').default
> const generator = require('@babel/generator').default
> //babelon 把源码解析成AST 可在 https://astexplorer.net/ 查看 ast的导出
> //@babel/traverse   遍历节点
> //@babel/types   节点替换
> //@babel/generator 生成
> 
> const {SyncHook } = require('tapable')
> const ejs = require('ejs')
> //引入ejs
> 
> class Compiler{
>   constructor(config){
>     // entry output
>     this.config = config
>     // 保存文件路径
>     this.entryId //'./src/index.js'
>     // 保存所有模块依赖
>     this.modules = {}
>     // 入口路径
>     this.entry = config.entry
>     //可能输出多个文件
>     this.assets = {}
>     //表示 工作路径
>     this.root = process.cwd()
>     
>     /* ---------- 与 plugin 相关 ----------*/
>     //模拟webpack的生命周期
>     this.hooks = {
>       entryOption: new SyncHook(),
>       compile: new SyncHook(),
>       afterCompile: new SyncHook(),
>       afterPlugins: new SyncHook(),
>       run: new SyncHook(),
>       emit: new SyncHook(),
>       done: new SyncHook()
>     }
>     let plugins = this.config.plugins
>     //如果是数组
>     if(Array.isArray(plugins)){
>       plugins.forEach(plugin=>{
>         plugin.apply(this)
>       })
>     }
>     this.hooks.afterPlugins.call()
>     
>   }
>   
>   // 得到文件内容
>   getSource(modulePath) {  
>     let content = fs.readFileSync(modulePath,'utf-8')
>     //处理 ./index.less
>     let rules = this.config.module.rules
>     rules.forEach(rule=>{
>       let {test,use} = rule
>       let len = use.length - 1
>       
>       /* ---------- 与 loader 相关 ----------*/
>       if(test.test(modulePath)){ // 正则匹配这个模块是否需要通过loader来转化
>         // loader获取对应的loader函数
>         (function normalLoader() {
>           //后边是一个绝对路径
>           let loader = require(use[len--])
>           content = loader(content)
>           // 递归调用loader实现转化功能
>             if(len>=0){
>               normalLoader()
>             }
>         })()
>        
>       }
>     })
>     
>     return content
>   }
>   
>   // 解析源码
>   parse(source,parentPath) { //主要靠AST解析语法树
>     let ast = babylon.parse(source)
>     let dependencies =  []//数组依赖
>     traverse(ast,{
>       // 调用表达式  a执行  require执行
>       CallExpression(p){
>         let node = p.node //对应的节点
>         if(node.callee.name === 'require') {
>           node.callee.name = '__webpack_require__'
>           let moduleName = node.arguments[0].value // 取到的就是模块的引用名字
>           moduleName = moduleName + (path.extname(moduleName)? '':'.js')
>           moduleName = './' + path.join(parentPath,moduleName) //'src/a.js'
>           dependencies.push(moduleName)
>           //节点替换
>           node.arguments = [types.StringLiteral(moduleName)]
>         }
>       }
>     })
>     let sourceCode = generator(ast).code
>     return { sourceCode, dependencies }
>   }
>   
>   // 构建模块
>   buildModule(modulePath,isEntry){
>     // 获取模块内容
>     let source = this.getSource(modulePath)
>     // 模块id（即模块的相对路径） moduleName  = modulePath - this.root  // path.relative对应 此方法
>     let moduleName = './' + path.relative(this.root,modulePath)
>     if(isEntry) {
>       this.entryId = moduleName // 保存入口名字
>     }
>     
>     // 解析 需要把source源码进行改造  返回一个依赖列表
>     let { sourceCode, dependencies } = this.parse(source, path.dirname(moduleName))
>     // 把相对路径和模块中的内容对应起来
>     this.modules[moduleName] = sourceCode
>     dependencies.forEach(dep=>{
>       //附模块的加载  递归加载
>       this.buildModule(dep,false)
>     })
>   }
>   emitFile() { //发射文件
>     //数据渲染
>     //看的是webpack.config.js中的output
>     fs.access(this.config.output.path,function(err){
>       // 文件和目录不存在的情况下；
>       if(err){
>         fs.mkdirSync(this.config.output.path);
>         // 这里只能创建单层目录，而不能创建多层目录，创建多层目录可看
>         // https://blog.csdn.net/m0_37263637/article/details/95640248
>       }
>     })
>     let main = path.join(this.config.output.path,this.config.output.filename)
>     //读取模板
>     let templateStr = this.getSource(path.join(__dirname,'main.ejs'))
>     //渲染
>     let code = ejs.render(templateStr,{entryId:this.entryId,modules:this.modules})
>     //拿到输出到哪个目录下
>     //资源中 路径对应的代码
>     this.assets[main] = code
>     fs.writeFileSync(main,this.assets[main])
>   }
>   run(){
>     /* ---------- 与 plugin 相关 ----------*/
>     //执行  解析文件依赖
>     this.hooks.run.call()
>     //编译 调用
>     this.hooks.compile.call()
>     /* --------------------*/
>     
>     //执行  并且创建模块依赖关系  
>     this.buildModule(path.resolve(this.root,this.entry),true)
>     
>     /* ---------- 与 plugin 相关 ----------*/
>     // 发射一个文件 打包后的文件
>     this.hooks.afterCompile.call()
>     this.emitFile()
>     this.hooks.emit.call()
>     this.hooks.done.call()
>   }
> }
> module.exports = Compiler
> ```

### webpack.config.js

> ```js
> const path = require('path')
> //模拟插件
> class P{
>   apply(compiler){
>     //发射 订阅
>     compiler.hooks.emit.tap('emit',()=>{
>       console.log('emit事件')
>     })
>   }
> }
> class P1{
>   apply(compiler){
>     //发射 订阅
>     compiler.hooks.afterPlugins.tap('emit',()=>{
>       console.log('afterPlugins')
>     })
>   }
> }
> module.exports = {
>   mode: 'development',
>   entry: './src/index.js',
>   output:{
>     filename: 'bundle.js',
>     path: path.resolve(__dirname,'dist')
>   },
>   module:{
>     rules:[
>       {
>         test: /\.less$/,
>         use:[
>           path.resolve(__dirname,'loader','style-loader'),
>           path.resolve(__dirname,'loader','less-loader')
>         ]
>       }
>     ]
>   },
>   plugins:[
>     new P(),
>     new P1()
>   ]
> }
> ```

### less-loader.js

> ```js
> let less = require('less')
> let css = ''
> function loader(source){
>   less.render(source,(error,c)=>{
>     css = c.css
>   })
>   //正则 将/n替换成//n 不然会被当做转义字符来处理 不换行
>   css = css.replace(/\n/g,'\\n')
>   return css
> }
> module.exports = loader
> ```

### style-loader.js

> ```js
> let style = ''
> function loader(source){
>   style = `let style = document.createElement('style')
>   style.innerHTML = ${JSON.stringify(source)}
>   document.head.appendChild(style)`
>   return style
> 
>   //style.innerHTML = JSON.stringify(loader) 可以将index.less的代码转为一行
> }
> 
> module.exports = loader
> ```

### main.ejs

> ```
> (function(modules) { // webpackBootstrap   webpack入口函数
> 	var installedModules = {};
> 	function __webpack_require__(moduleId) {
> 		if(installedModules[moduleId]) {
> 			return installedModules[moduleId].exports;
>     }
>     var module = installedModules[moduleId] = {
> 			i: moduleId,
> 			l: false,
> 			exports: {}
> 		};
> 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
> 		return module.exports;
> 	}
> return __webpack_require__(__webpack_require__.s = "<%-entryId%>");//入口模块
> })
> 
> ({
>   <%for(let key in modules) {%>
>     "<%-key%>":   //key->模块的路径
>     (function(module, exports,__webpack_require__) {  
>       eval(`<%-modules[key]%>`);
>     }),
>   <%}%>
> });
> ```



## loader 配置

webpack 只能处理 JavaScript 的模块，如果要处理其他类型的文件，需要使用 loader 进行转换。loader 是指用来将一段代码转换成另一端代码的 webpack 加载器。

### 三种配置loader路径方式

在项目根目录下创建loader 文件夹，增加 `loader1.js` ，loader 就是一个函数参数是匹配到文件的内容，返回的结果会作为最新的内容。可用以下三种方式配置自定义loader 的路径：

1. 直接在 `use` 中使用path.resolve

   ```js
   let path = require('path')
   module.exports = {
     ...
     module: {
       rules: [
         {
            test: /\.js$/,
            use: path.resolve(__dirname, 'loaders', 'loader1.js') // 使用loaders目录下的loader1.js
         }
       ]
     }
   }
   ```

2. 配置 loader别名 

   ```js
   let path = require('path')
   module.exports = {
     ...,
     resolveLoader: {
     	alias:{
         'loader1': path.resolve(__dirname,'loader','loader1.js')
       }
   	},
     module: {
       rules: [
         {
            test: /\.js$/,
            use: 'loader1'
         }
       ]
     }
   }
   ```

3. 配置modules  默认去node_modules下找 找不到去loader文件夹下找

   ```js
   let path = require('path')
   module.exports = {
     ...,
     resolveLoader: {
     	modules:['node_modules',path.resolve(__dirname,'loader')]
   	},
     module: {
       rules: [
         {
            test: /\.js$/,
            use: 'loader1'
         }
       ]
     }
   }
   ```



### 配置多个 loader

```js
let path = require('path')
module.exports = {
  ...,
  resolveLoader: {
  	modules:['node_modules',path.resolve(__dirname,'loader')]
	},
  module: {
    rules: [
      { test: /\.js$/, use: 'loader1' },
      { test: /\.js$/, use: 'loader2' },
      { test: /\.js$/, use: 'loader3' }
      // 或者直接用数组
      // { test: /\.js$/, use: ['loader1', 'loader2','loader3'] }
    ]
  }
}
```

**默认**执行顺序是 **从下到上**，**从右到左**，上面loader的执行顺序为：'loader3' —> 'loader2' —> 'loader1'

### loader的分类

前置loader：pre

普通loader：normal

后置loader：post

loader的标准顺序：pre —> normal —> inline(行内) —> post

#### **示例**

目录

```
test
├── src
│   ├──index.js
│   └── a.js
│ 
├── loader
│   ├── inline-loader.js
│   ├── loader1.js
│   ├── loader2.js
│   └── loader3.js
│ 
├── webpack.config.js
└── package.json
```

webpack.config.js

```js
let path = require("path");
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filename: "build.js",
    path: path.resolve(__dirname, "dist")
  },

  resolveLoader: {
    modules: ["node_modules", path.resolve(__dirname, "loader")]
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: 'loader1', // 使用loaders目录下的loader1.js
        enforce: 'pre'
      },
      {
        test: /\.js$/,
        use: 'loader2'
      },
      {
        test: /\.js$/,
        use: 'loader3',
        enforce: 'post'
      }
    ]
  }
};
```

inline-loader.js、loader1.js、loader2.js、loader3.js

```js
// inline-loader.js、
function loader(source){ //loader的参数就是源代码
  console.log('inline-loader~~~~~')
  return source;
}

module.exports = loader

// loader1.js、
function loader(source){ //loader的参数就是源代码
  console.log('loader1~~~~~')
  return source;
}

module.exports = loader

// loader2.js、
function loader(source){ //loader的参数就是源代码
  console.log('loader2~~~~~')
  return source;
}

module.exports = loader

// loader3.js
function loader(source){ //loader的参数就是源代码
  console.log('loader3~~~~~')
  return source;
}

module.exports = loader
```

index.js、a.js

```js
// index.js
console.log('hello');

let str = require('./a.js')

// a.js
module.exports = 'it is a.js'
```

在控制台执行 `npx webpack`，则控制台会出现以下打印

```shell
loader1~~~~~
loader2~~~~~
loader3~~~~~
loader1~~~~~
loader2~~~~~
loader3~~~~~
# 因为有 2 个js文件，所以执行了2遍loader,并且设置了 pre、post，所以顺序是1、2、3
```



#### 内联(inline) 

可以在 `import` 语句或任何 [等同于 "import" 的方法](https://webpack.docschina.org/api/module-methods) 中指定 loader。使用 `!` 将资源中的 loader 分开。每个部分都会相对于当前目录解析。

```js
import Styles from 'css-loader!./styles.css';
// 或者
let str = require('inline-loader!./a.js')
```



还是用上面的实例，改变 `index.js` 文件的内容

```js
let str = require('inline-loader!./a.js')
```

再执行 `npx webpack`，则控制台会出现以下打印

```shell
loader1~~~~~
loader2~~~~~
loader3~~~~~
loader1~~~~~
loader2~~~~~
inline-loader~~~~~
loader3~~~~~
```

因为在引入 `a.js` 时，前面添加了 `inline-loader!` 使用了内联（inline）loader，所以顺序是：pre —> normal —> inline(行内) —> post



##### 符号的含义

> `-!` 禁用前置（pre）和正常（normal）loader
>  `!` 禁用普通（normal）loader
> `!!` 禁用前置、后置和正常 loader 
>
> 即：
>
> 所有普通 loader 可以通过在请求中加上 `!` 前缀来忽略（覆盖）。
>
> 所有普通和前置 loader 可以通过在请求中加上 `-!` 前缀来忽略（覆盖）。
>
> 所有普通，后置和前置 loader 可以通过在请求中加上 `!!` 前缀来忽略（覆盖）。
>
> 不应该使用行内 loader 和 `!` 前缀，因为它们是非标准的。它们可在由 loader 生成的代码中使用。



改变 `index.js` 文件的内容

```js
let str = require('!inline-loader!./a.js')
```

再执行 `npx webpack`，则控制台会出现以下打印

```shell
loader1~~~~~
loader2~~~~~
loader3~~~~~
loader1~~~~~
inline-loader~~~~~
loader3~~~~~
```

因为在 `inline-loader!` 的前面添加了 `!` ，则不使用 normal loader，而 loader2 是 normal loader，所以没有使用 loader2 。因此 loader 执行顺序是：pre —> inline(行内) —> post



改变 `index.js` 文件的内容

```js
let str = require('-!inline-loader!./a.js')
```

再执行 `npx webpack`，则控制台会出现以下打印

```shell
loader1~~~~~
loader2~~~~~
loader3~~~~~
inline-loader~~~~~
loader3~~~~~
```

因为在 `inline-loader!` 的前面添加了 `-!` ，则不使用 normal loader，而 loader1 是 pre loader， loader2 是 normal loader，所以没有使用 loader2 。因此 loader 执行顺序是：inline(行内) —> post



改变 `index.js` 文件的内容

```js
let str = require('!!inline-loader!./a.js')
```

再执行 `npx webpack`，则控制台会出现以下打印

```shell
loader1~~~~~
loader2~~~~~
loader3~~~~~
inline-loader~~~~~
```

因为在 `inline-loader!` 的前面添加了 `!!` ，则只用 inline loader，而 inline-loader是 inline loader，所以没有使用 loader1 、loader2 、 loader3 。因此 loader 执行顺序是：inline(行内)



### pitchLoader 和 normalLoader

每个 loader 都有两部分组成：pitchLoader 和 normalLoader，pitch 和 normal 的执行顺序正好相反，当 pitch 没有定义或者没有返回值时，会先执行 pitch 再获取资源执行 Loader，如果定义的某个 pitch 有返回值则会跳过读取资源和自己的 loader。

> 所有一个接一个地进入的 loader，都有两个阶段：
>
> 1. **pitching** 阶段：loader 上的 pitch 方法，按照 `后置(post)、行内(normal)、普通(inline)、前置(pre)` 的顺序调用。更多详细信息，请查看 [越过 loader(pitching loader)](https://webpack.docschina.org/api/loaders/#pitching-loader)。
> 2. **normal**阶段：loader 上的 常规方法，按照 `前置(pre)、行内(normal)、普通(inline)、后置(post)` 的顺序调用。模块源码的转换，发生在这个阶段。
>
> 参考：https://webpack.docschina.org/configuration/module/#rule-enforce

>```
>user: [loader3, loader2, loader1]
>
>----------pitch loader 无返回值---------  
>pitch:   loader3 → loader2 → loader1  
>                                    ↘ 
>                                      资源
>                                    ↙
>normal:  loader3 ← loader2 ← loader1 
>
>----------pitch 有返回值---------
>
>---------pitch loader 有返回值 --------
>pitch:   loader3 → loader2  loader1  
>                     ↙               
>               有返回值               资源
>               ↙                      
>normal:  loader3   loader2  loader1
>```
>
>示例
>
>```js
>//loader2.js
>function loader(source){ //loader的参数就是源代码
>  console.log('loader2~~~~')
>  return source;
>}
>loader.pitch = function(){
>  return 'ok'
>}
>module.exports = loader
>
>// 这样的运行结果：
>// loader3~~~~~
>```



## 自己实现 loader

> 示例项目：https://github.com/cheungww/loader_test

示例目录

```
test
├── README.md
├── node_modules
├── package.json
├── webpack.config.js
├── src
│   ├── a.js
│   ├── index.js
│   ├── index.less
│   └── shop.jpg
└── loader
    ├── babel-loader.js
    ├── banner-loader.js
    ├── file-loader.js
    ├── url-loader.js
    ├── babel-loader.js
    ├── babel-loader.js
    └── babel-loader.js
```

### babel-loader

此 package 允许你使用 [Babel](https://github.com/babel/babel) 和 [webpack](https://github.com/webpack/webpack) 转译 `JavaScript` 文件。

>**Babel 是一个 JavaScript 编译器**
>
>Babel 是一个工具链，主要用于在旧的浏览器或环境中将 ECMAScript 2015+ 代码转换为向后兼容版本的 JavaScript 代码：
>
>- 转换语法
>- Polyfill 实现目标环境中缺少的功能 (通过 [@babel/polyfill](https://babel.docschina.org/docs/en/babel-polyfill))
>- 源代码转换 (codemods)
>- 更多！（查看[视频](https://babel.docschina.org/videos.html)）

**预先安装**

```shell
# 安装 webpack
yarn add webpack webpack-cli -D

#安装 @babel/core @babel/preset-env来测试 babel-loader
yarn add @babel/core @babel/preset-env

#安装 loader-utils 来
yarn add loader-utils
```



babel-loader.js

```js
let babel = require("@babel/core");
//loaderUtils 拿到预设  便于后期转化代码
let loaderUtils = require("loader-utils");
function loader(source) { // this loaderContext
  //Object.keys方法  属性转成一个数组
  let options = loaderUtils.getOptions(this);
  // console.log(this.resourcePath)  运行时返回一个绝对路径
  //console.log(options);
  // options 打印为{ presets: [ '@babel/preset-env' ] }
  let cb = this.async() // loader上下文 默认有async这个方法 异步执行 在哪执行调用cb就可以
  //babel的transform有三个参数  第一个 转换哪些代码  第二个 转换选项  第三个 异步回调函数
  babel.transform(source,{
    ...options, //对象展开
    sourceMap: true, //调试工具 需要webpack.config.js 也要配置 source-map
    filename:  this.resourcePath.split('/').pop()//文件名  不然运行时 webpack下边是unkown

  },function(err,result){
    //异步回调  return source就不起作用了
    cb(err,result.code,result.map)  // 异步 cb(123) 会错误 必须严格按照参数来 
    // 第一个 错误  第二个  代码  第三个 sourceMap
  })
  // return source; 不起作用
}
module.exports = loader;
```

webpack.config.js

```js
module.exports = {
  ...
  devtool: 'source-map',
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  }
}
```



### banner-loader

此 package 允许你给指定类型文件添加注释

预先安装

```shell
#提前安装
yarn add webpack webpack-cli -D

#安装 loader-utils 来
yarn add loader-utils

#安装 schema-utils 骨架校验
yarn add schema-utils
```



banner-loader.js

```js
let loaderUtils = require('loader-utils')
//骨架校验
let validateOptions = require('schema-utils')
//读取文件模块
let fs = require('fs')
function loader(source) {
  this.cacheable && this.cacheable() //一般这样用 
  //this.cacheable(false) //缓存  自动缓存
  let options = loaderUtils.getOptions(this)
  let cb = this.async() //异步必备
  let schema = {
    type: 'object',
    properties: {
      text: {
        type: 'string',

      },
      filename: {
        type: 'string'
      }
    }
  }
  //将骨架和参数对比   'banner-loader'出问题（如果报错）
  validateOptions(schema, options, 'banner-loader')
  if (options.filename) {
    this.addDependency(options.filename) //自动地 添加文件依赖   加入这一句话 开启实时监控 webpack也会监控这个文件 这个文件更新也会实时更新
    fs.readFile(options.filename, 'utf-8', function (err, data) {
      cb(err, `/**${data}**/${source}`)
    })
  } else {
    //同步也得 cb调用了
    cb(null, `/**${options.text}**/${source}`)
  }
  // return source 又是异步  需要创建一个cb
}
module.exports = loader
```



webpack.config.js

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.js$/,
        use:{
          loader: 'banner-loader',
          options:{
            text: '给js文件添加注释',
            //如果没有给text的时候,可以把注释内容写在指定的文件，例banner.js
            // filename: path.resolve(__dirname,'banner.js')
          }
        }
      }
    ]
  }
}
```



banner.js

```js
banner-loader 默认的注释
```



### file-loader

 `file-loader` 将文件上的 `import`/`require()` 解析为url，并将该文件发射到输出目录中。

**预先安装**

```shell
# 安装 webpack
yarn add webpack webpack-cli -D

# 安装 loader-utils 来获取webpack.config.js里loader的options
yarn add loader-utils
```



file-loader.js

```js
let loaderUtils = require('loader-utils')
function loader(source) {
  //根据当前格式 来生成图片路径 
  let filename = loaderUtils.interpolateName(this, '[hash].[ext]', { content: source }) //根据当前内容来产生hash值
  this.emitFile(filename, source)  //内部方法 发射文件
  //处理 图片 需要返回一个模块
  return `module.exports = "${filename}"`
}
loader.raw = true  //返回二进制
module.exports = loader
```



webpack.config.js

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.jpg$/,
        // 目的 根据图片生成一个 md5串 发射到dist目录下 file-loader还会返回当前路径
        use:{
          loader: 'file-loader',
        }
      }
    ]
  }
}
```



### url-loader

url-loader 可以将文件转换为base64 URI

**预先安装**

```shell
# 安装 webpack
yarn add webpack webpack-cli -D

# 安装 loader-utils 来获取webpack.config.js里loader的options
yarn add loader-utils

# 安装 mime 来解析文件后缀名
yarn add mime
```



url-loader.js

```js
let loaderUtils = require('loader-utils')
// 获取后缀名
let mime = require('mime')
function loader(source) {
  let { limit } = loaderUtils.getOptions(this)
  if (limit && limit > source.length) {
    //转成base64编码
    return `module.exports = "data:${mime.getType(this.resourcePath)};base64,${source.toString('base64')}"`
  } else {
    // 返回file-loader  this和source通过call传入 防止参数变乱
    return require('./file-loader').call(this, source)
  }
}
loader.raw = true //转为二进制
module.exports = loader
```



webpack.config.js

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.jpg$/,
        //符号中文  会报Invalid or unexpected token
        use:{  
          loader: 'url-loader', //url-loader会处理路径并且交给file-loader
          options:{
            // 如果图片小于200k，则转成 base64，
            // 即 <img src="data:image/jpeg;base64,/9j/...>
            // 如果图片大于200k，则交给file-loader来处理
            limit: 200*1024 
          }
        }
      }
    ]
  }
}
```



index.js

```js
import p from './shop.jpg'
console.log(p)
let img = document.createElement('img')
img.src = p
document.body.appendChild(img)
```



### style-loader、css-loader、less-loader

style-loader 通过注入`<style>` 标签将CSS添加到 DOM 中

css-loader会像 `import / require（）` 一样解释 `@import` 和 `url（）` ，并将解析它们。

less-loader 是处理 less 的 webpack loader。将 Less 编译为 CSS。

**预先安装**

```shell
# 安装 webpack
yarn add webpack webpack-cli -D

# 安装 loader-utils 来获取webpack.config.js里loader的options
yarn add loader-utils

# 安装 mime 来解析文件后缀名
yarn add mime

# 安装less
yarn add less
```

less-loader.js

```js
let less = require('less')
function loader(source) {
  let css
  less.render(source, (err, r) => {
    css = r.css
  })
  return css
}

module.exports = loader
```

css-loader.js

```js
function loader(source) {
  let reg = /url\((.+?)\)/g
  //第一次查找肯定是从零开始查找
  let pos = 0
  let arr = ['let list = []']
  while (current = reg.exec(source)) {
    let [matchUrl, g] = current
    //reg.lastIndex 对应着匹配最后一个字符的长度 matchUrl对应着url('./lala.jpg')的长度
    //相减可以得到前边的不包含 url的值
    let last = reg.lastIndex - matchUrl.length
    arr.push(`list.push(${JSON.stringify(source.slice(pos, last))})`)
    //把 g 替换成ruquire的写法
    arr.push(`list.push('url('+ require(${g}) +')')`)
    pos = reg.lastIndex
    // 添加后边的
    arr.push(`list.push(${JSON.stringify(source.slice(pos))})`)
    arr.push(`module.exports = list.join(' ')`)
    return arr.join('\r\n')
  }
  return source
}

module.exports = loader
```



style-loader.js

```js
let loaderUtils = require('loader-utils')
let style = ''
function loader(source) {
  console.log(source)
  return `style = document.createElement('style')
  style.innerHTML = ${JSON.stringify(source)}
  document.head.appendChild(style)`
}
//在 style-laoder pitch上写一个返回值  就不执行后边  没有pitch source是一个css解析完成的字符串
loader.pitch = function (remainingRequest) {
  //loaderUtils.stringifyRequest(this,'!!'+remainingRequest) 会把绝对路径转化为相对路径 
  // 这样做是为了 相当于引入一个inline-loader来处理css-loader返回的值  不需要再走其他的loader 需要加'!!'
  // 参照 pitch用法以及inline-loader使用方式来理解
  // 就是 把 css转化的 list数组 转化为 style-loader能看懂的 字符串 
  return `style = document.createElement('style')
  style.innerHTML = require(${loaderUtils.stringifyRequest(this, '!!' + remainingRequest)})
  document.head.appendChild(style)`
}
module.exports = loader
```



webpack.config.js

```js
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.less$/,
        use:[
          'style-loader',
          'css-loader',
          'less-loader'  
        ]
      },
      {
        test: /\.jpg$/,
        //符号中文  会报Invalid or unexpected token
        use:{  
          loader: 'url-loader', //url-loader会处理路径并且交给file-loader
          options:{
            // 如果图片小于200k，则转成 base64，
            // 即 <img src="data:image/jpeg;base64,/9j/...>
            // 如果图片大于200k，则交给file-loader来处理
            limit: 200*1024 
          }
        }
      }
    ]
  }
}
```



index.js

```js
import './index.less';
```



index.less

```less
@color: red;

body {
  background-color: @color;
  background: url('./shop.jpg');
}
```



## 自己实现 plugin

### webpack如何调用plugin

node_modules下的webpack下的lib中的Compiler.js

```js
...
class Compiler extends Tapable {
	constructor(context) {
		super();
		this.hooks = {
			/** @type {SyncBailHook<Compilation>} */
			shouldEmit: new SyncBailHook(["compilation"]),
			/** @type {AsyncSeriesHook<Stats>} */
			done: new AsyncSeriesHook(["stats"]),
			/** @type {AsyncSeriesHook<>} */
			additionalPass: new AsyncSeriesHook([]),
      ...
    }
  }
}
...
const childCompiler = new Compiler(this.context);
if (Array.isArray(plugins)) {
  for (const plugin of plugins) {
    plugin.apply(childCompiler);
  }
}
...
```

定义一个插件类，然后再该插件类定义一个 `apply` 属性，因为在 `Compiler.js` 中给 `apply` 传入了 `childCompiler`，所以自定义的插件类的 `apply` 属性就可以利用 `childCompiler` 的 `this.hooks` 了



> 示例项目：https://github.com/cheungww/plugin_test

目录

```
test
├── README.md
├── node_modules
├── package.json
├── webpack.config.js
├── src
│   ├── index.js
│   ├── index.css
│   └── index.html
└── plugins
    ├── DonePlugin.js
    ├── AsyncPluginr.js
    ├── FileListPlugin.js
    ├── InlineSourcePlugin.js
    └── UploadPlugin.js
```



### DonePlugin、AsyncPlugin

插件 DonePlugin 的作用是当 webpack 编译完成后，在控制台打印提示语

插件 AsyncPlugin 的作用是当 webpack 发射文件时做一些异步操作

安装

```shell
yarn add webpack webpack-cli -D
```



DonePlugin.js

```js
class DonePlugin {
  apply(compiler) // compiler.hooks
  {
    //这样是一个同步代码
    console.log(1)
    //第一个参数无所谓，放啥都可以  tapable的时候也是
    compiler.hooks.done.tap('DonePlugin', (states) => {
      console.log('编译完成哦哦哦')
    })
  }
}
//还需要导出一下
module.exports = DonePlugin
```



AsyncPlugin.js

```js
class AsyncPlugin {
  apply(compiler) {
    //这样是一个同步代码
    console.log(2)
    compiler.hooks.emit.tapAsync('AsyncPlugin', (compliation, cb) => {
      setTimeout(() => {
        console.log('等一下')
        cb()
      }, 1000)
    })
    compiler.hooks.emit.tapPromise('AsyncPlugin', (compliation) => {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log('再等一下')
          resolve()
        }, 1000)
      })
    })
  }
}
module.exports = AsyncPlugin
```



webpack.config.js

```js
let path = require('path')
let DonePlugin = require('./plugins/DonePlugin.js')
let AsyncPlugin = require('./plugins/AsyncPlugin.js')
module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    new DonePlugin(),
    new AsyncPlugin()
  ]
}
```



执行 `npx webpack` 后，控制台会显示

```shell
1
2
等一下
再等一下
编译完成哦哦哦
```



### FileListPlugin

该插件的作用是输出一个文件，文件的内容是 打包后的所有的文件名及其大小

安装

```shell
yarn add webpack webpack-cli -D
yarn add html-webpack-plugin -D
```



FileListPlugin.js

```js
class FileListPlugin {
  constructor({ filename }) {
    this.filename = filename;
  }
  apply(compiler) {
    //文件准备好了，要进行发射
    compiler.hooks.emit.tapAsync("FileListPlugin", (compilation, cb) => {
      // compilcation有很多的属性
      // 打包的资源都会放在compilcation的assets属性上
      // console.log(compilation.assets)
      let content = `##  文件名    资源大小\r\n`;
      let assets = compilation.assets;
      //Object.entries() 可以把对象变成一个数组
      Object.entries(assets).forEach(([filename, staObj]) => {
        content += `-  ${filename}    ${staObj.size()}\r\n`;
      });

      assets[this.filename] = {
        source() {
          return content;
        },
        size() {
          return content.length;
        }
      };
      cb()
    });
  }
}
module.exports = FileListPlugin;
```

webpack.config.js

```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
let FileListPlugin = require('./plugins/FileListPlugin')
module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  plugins: [
    /* new DonePlugin(),
    new AsyncPlugin() */
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
     new FileListPlugin({
       filename: 'list.md'
     }),
  ]
}
```



执行 `npx webpack` 后，在 dist 目录下会多出一个 list.md 文件，内容：

```markdown
##  文件名    资源大小
-  bundle.js    3860
-  index.html    56
```



### InlineSourcePlugin

该插件是把外链标签变成内联

例如

打包后的 index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
<link href="main.css" rel="stylesheet"></head>
<body>
<script type="text/javascript" src="bundle.js"></script></body>
</html>
```

通过 InlineSourcePlugin 插件将 index.html 中的 `link` 和 `script` 的外链的样式和脚本导入到 index.html 中去

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
<style type="text/css">
  /*-----main.css中的内容----------*/
  body{ 
  	background-color: red;
	}
</style></head>
<body>
  
<script type="application/javascript">/******/ (function(modules) { // webpackBootstrap
/*------------bundle.js中的内容------------------*/ 
</script></body>
</html>
```



安装

```shell
yarn add css-loader mini-css-extract-plugin -D

# 安装最新版 html-webpack-plugin@next
yarn add html-webpack-plugin@next -D
```



InlineSourcePlugin.js

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
//把外链标签变成内联
class InlineSourcePlugin {
  constructor({ test }) {
    this.reg = test //正则
  }
  processTag(tag, compilation) {
    let newTag, url
    if (tag.tagName === 'link' && this.reg.test(tag.attributes.href)) {
      newTag = {
        tagName: 'style',
        attributes: {
          type: 'text/css'
        }
      }
      url = tag.attributes.href
    } else if (tag.tagName === 'script' && this.reg.test(tag.attributes.src)) {
      newTag = {
        tagName: 'script',
        attributes: {
          type: 'application/javascript'
        }
      }
      url = tag.attributes.src
    }
    if (url) {
      newTag.innerHTML = compilation.assets[url].source() //文件内容
      //删除这一个资源
      delete compilation.assets[url]
      return newTag
    }
    return tag
  }
  processTags(data, compilation) {
    let bodyTags = []
    let headTags = []
    data.headTags.forEach(headTag => {
      headTags.push(this.processTag(headTag, compilation))
    })
    data.bodyTags.forEach(bodyTag => {
      bodyTags.push(this.processTag(bodyTag, compilation))
    })
    return { ...data, headTags, bodyTags }
  }
  apply(compiler) {
    //要通过 HtmlWebpackPlugin的钩子来实现这一功能 根据官网文档修改
    compiler.hooks.compilation.tap('InlineSourcePlugin', (compilation) => {
      HtmlWebpackPlugin.getHooks(compilation).alterAssetTagGroups.tapAsync(
        'AfterPlugin',
        (data, cb) => {
          //将link变成内联  script变成内联
          data = this.processTags(data, compilation) //compilation.assets 资源
          cb(null, data)
        }
      )
    })
  }
}

module.exports = InlineSourcePlugin
```



webpack.config.js

```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
let FileListPlugin = require('./plugins/FileListPlugin')
let MiniCssExtractPlugin = require('mini-css-extract-plugin')
let InlineSourcePlugin = require('./plugins/InlineSourcePlugin')
module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
     new FileListPlugin({
       filename: 'list.md'
     }),
    new MiniCssExtractPlugin({
      filename: 'main.css'
    }),
    new InlineSourcePlugin({
      test: /(\.js|css)/
    })
  ]
}
```



### UploadPlugin

该插件的作用是打包后自动上传打包后的的文件到七牛

> 需要这几个参数
>
> ```
> bucket: ''  // 七牛的存储空间
> domain: '',
> accessKey: '', // 七牛云的两对密匙
> secretKey: '' // 七牛云的两对密匙
> ```
>
> 注册七牛，并在对象存储里面,新建存储空间列表`test`,`bucket: 'test'`
>
> 内容管理外链接默认域名 `domain: 'xxxxxxxx'`
>
> 右上角个人面板里面个人中心,密钥管理分别对应`accessKey`和`secretKey`
>
> [进入开发者中心](https://developer.qiniu.com/) -> SDK&工具 -> 官方SDK -> Node服务端文档 —> 文件上传
>
> [node文件上传](https://developer.qiniu.com/kodo/sdk/1289/nodejs)
>
> ```
> npm install qiniu
> ```
>
> [compiler-hooks](https://webpack.docschina.org/api/compiler-hooks)

安装

```shell
yarn add webpack webpack-cli -D
yarn add html-webpack-plugin -D
yarn add qiniu -D
```



UploadPlugin.js

```js
let qiniu = require('qiniu')
let path = require('path')

class UploadPlugin {
  constructor(options = {}) {
    // 参考 https://developer.qiniu.com/kodo/sdk/1289/nodejs
    let { bucket = '', domain = '', accessKey = '', secretKey = '' } = options
    let mac = new qiniu.auth.digest.Mac(accessKey, secretKey)
    let putPolicy = new qiniu.rs.PutPolicy({
      scope: bucket
    });
    this.uploadToken = putPolicy.uploadToken(mac)
    let config = new qiniu.conf.Config();
    this.formUploader = new qiniu.form_up.FormUploader(config)
    this.putExtra = new qiniu.form_up.PutExtra()
  }
  apply(compiler) {
    compiler.hooks.afterEmit.tapPromise('UploadPlugin', (complication) => {
      let assets = complication.assets
      let promise = []
      Object.keys(assets).forEach(filename => {
        promise.push(this.upload(filename))
      })
      return Promise.all(promise)
    })
  }

  upload(filename) {
    return new Promise((resolve, reject) => {
      let localFile = path.resolve(__dirname, '../dist', filename)
      this.formUploader.putFile(this.uploadToken, filename, localFile, this.putExtra, function (respErr,
        respBody, respInfo) {
        if (respErr) {
          reject(respErr)
        }
        if (respInfo.statusCode == 200) {
          resolve(respBody)
        } else {
          console.log(respInfo.statusCode)
          console.log(respBody)
        }
      });
    })
  }
}

module.exports = UploadPlugin
```



webpack.config.js

```js
let path = require('path')
let HtmlWebpackPlugin = require('html-webpack-plugin')
let FileListPlugin = require('./plugins/FileListPlugin')
let MiniCssExtractPlugin = require('mini-css-extract-plugin')
let InlineSourcePlugin = require('./plugins/InlineSourcePlugin')
let UploadPlugin = require('./plugins/UploadPlugin')

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: '打包后上传文件的网址' 
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
  plugins: [
    /* new DonePlugin(),
    new AsyncPlugin() */
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'main.css'
    }),
    new InlineSourcePlugin({
      test: /(\.js|css)/
    }),
    new UploadPlugin({
      bucket: 'test',  // 七牛的存储空间
      domain: 'poyrjyh1b.bkt.clouddn.com',
      accessKey: 'xxxxxx', // 七牛云的两对密匙
      secretKey: 'yyyyyy' // 七牛云的两对密匙
    })
  ]
}
```







