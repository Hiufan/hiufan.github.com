---
layout: post
title:  "webpack入门"
date:   2016-01-13
categories: tool
---

###webpack 入门

#### 一、什么是webpack
> [webpack](https://github.com/webpack)是近期最火的一款模块加载器兼打包工具，支持commonJS和AMD模块。 支持很多模块加载器的调用，可以使模块加载器灵活定制，比如babel-loader加载器，该加载器能使我们使用ES6的语法来编写代码。 可以通过配置打包成多个文件，有效的利用浏览器的缓存功能提升性能。 使用模块加载器，可以支持sass，less等处理器进行打包且支持静态资源样式及图片进行打包。

#### 二、安装和配置

#### 安装
-**在项目里安装**  ：`npm install webpack --save-dev`
-**全局安装**  ：`npm install webpack -g` 
 
 
#### 配置

来一个例子


	//javascript
	var webpack = require('webpack');
	var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
	module.exports = {
		//插件项
		plugins: [commonsPlugin],
		//页面入口文件配置
		entry: {
			index : './src/js/page/index.js'
		},
		//入口文件输出配置
		output: {
			path: 'dist/js/page',
			filename: '[name].js'
		},
		module: {
			//加载器配置
			loaders: [
				{ test: /\.css$/, loader: 'style-loader!css-loader' },
				{ test: /\.js$/, loader: 'jsx-loader?harmony' },
				{ test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
				{ test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
			]
	},


{% highlight js linenos %}
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
module.exports = {
	//插件项
	plugins: [commonsPlugin],
	//页面入口文件配置
	entry: {
		index : './src/js/page/index.js'
	},
	//入口文件输出配置
	output: {
		path: 'dist/js/page',
		filename: '[name].js'
	},
	module: {
		//加载器配置
		loaders: [
			{ test: /\.css$/, loader: 'style-loader!css-loader' },
			{ test: /\.js$/, loader: 'jsx-loader?harmony' },
			{ test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
			{ test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
		]
},
{% endhighlight %}
>>>>>>> 848f74520b1b0acc3937d6fcfcdd7a115b576f3f

每个项目下都必须配置有一个 webpack.config.js ，它的作用如同常规的 gulpfile.js/Gruntfile.js ，就是一个配置项，告诉 webpack 它需要做什么。

 `plugins` 是插件项，这里我们使用了一个 CommonsChunkPlugin的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。
 
 `entry`  入口文件，是页面入口文件配置，output 是对应输出项配置 （即入口文件最终要生成什么名字的文件、存放到哪里）
	//javascript
	entry: {
		page1: "./page1",
		//支持数组形式，将加载数组中的所有模块，但以最后一个模块作为输出
		page2: ["./entry1", "./entry2"]
		   },
	output: {
		path: "dist/js/page",    //输出文件目录
		filename: "[name].bundle.js"   //输出文件命名
		}
	}

该段代码最终会生成一个 page1.bundle.js 和 page2.bundle.js，并存放到 ./dist/js/page 文件夹下。


`module` 指各种资源文件，如js、css、图片、svg、scss、less等等，一切资源皆被当做模块,它告知 webpack 每一种文件都需要使用什么加载器来处理。
	//javascript
	module: {
		//加载器配置
		loaders: [
			//.css 文件使用 style-loader 和 css-loader 来处理
			{ test: /\.css$/, loader: 'style-loader!css-loader' },
			//.js 文件使用 jsx-loader 来编译处理
			{ test: /\.js$/, loader: 'jsx-loader?harmony' },
			//.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
			{ test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
			//图片文件使用 url-loader 来处理，小于8kb的直接转为base64
			{ test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
		]
	}

`-loader`其实是可以省略不写的，多个loader之间用“!”连接起来。

所有的加载器都需要通过 npm 来加载
比如 url-loader 它会将样式中引用到的图片转为模块来处理，使用该加载器需要先进行安装：

`npm install url-loader -save-dev`

配置信息的参数`?limit=8192`表示将所有小于8kb的图片都转为base64形式 （其实应该说超过8kb的才使用 url-loader 来映射到文件，否则转为data url形式） 。

Webpack 是类似 Browserify 那样在本地按目录对依赖进行查找的,可以构造一个例子， 用 `--display-error-details` 查看查找过程，例子当中 `resolve.extensions` 用于指明程序自动补全识别哪些后缀,,注意一下，`extensions` 第一个是空字符串! 对应不需要后缀的情况。

	//javascript
	resolve: {
		//查找module的话从这里开始查找
		root: 'E:\racing\biketo_racing\www\source\vendor', //绝对路径
		//自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
		extensions: ['', '.js', '.json', '.less'],
		//模块别名定义，方便后续直接引用别名，无须多写长长的地址
		alias: {
			dialog: 'js/artDialog/dialog-min.js',//后续直接 require('dialog') 即可
			jquery: 'js/jquery/jquery.min.js',
			kindeditor: 'js/kindeditor/kindeditor-min.js'
		}
	}


#### 三、运行

运行webpack
`$ webpack --display-error-details`
后面的参数`--display-error-details`是推荐加上的，方便出错时能查阅更详尽的信息（比如 webpack 寻找模块的过程），从而更好定位到问题。

其他参数
>`$ webpack --config XXX.js` //使用另一份配置文件（比如webpack.config2.js）来打包
`$ webpack --watch` //监听变动并自动打包
`$ webpack -p` //压缩混淆脚本
`$ webpack -d` //生成map映射文件，告知哪些模块被最终打包到哪里了

#### 四、怎么用

直接引入webpack最终生成的脚本就好，不用再写什么 data-main 或seajs.use 了

来个例子  html
	//html 
	<html>
	  <body>
	    <script src="bundle.js"></script>
	  </body>
	</htmL>


打包后连样式都不用引入，执行`bundle.js`会动态生成`<style>`标签并插入到head里

js

各脚本模块可以直接使用 commonJS 来书写，并可以直接引入未经编译的模块，比如less，jsx等（只要你在 webpack.config.js 里配置好了对应的加载器）。

	//javascript
	mport React from 'react';
	import { render } from 'react-dom';
	import { Router, Route, Link, IndexRoute } from 'react-router';
	import createBrowserHistory from 'history/lib/createBrowserHistory';
	
	require('./app.css');	//引入样式
	
	var App = React.createClass({…
	var Dashboard = React.createClass({…
	var Inbox = React.createClass({…
	var Calendar = React.createClass({…
	let history = createBrowserHistory();
	
	render((
	  <Router history={history}>
	    <Route path="/" component={App}>
	      <IndexRoute component={Dashboard}/>
	      <Route path="app" component={Dashboard}/>
	      <Route path="inbox" component={Inbox}/>
	      <Route path="calendar" component={Calenda}/>
	      <Route path="*" component={Dashboard}/>
	    </Route>
	  </Router>
	), document.body);


在 AMD/CMD 中，我们需要对不符合规范的模块（比如一些直接返回全局变量的插件）进行 shim 处理，这时候我们需要使用 exports-loader 来帮忙：

	//{ test: require.resolve("./src/js/tool/swipe.js"), loader: "exports?swipe"}
之后在脚本中需要引用该模块的时候，这么简单地来使用就可以了：
	//javascript
	require('./tool/swipe.js');
	swipe(); 

##### 独立打包样式
有时候可能希望项目的样式能不要被打包到脚本中，而是独立出来作为.css，然后在页面中以<link>标签引入。这时候我们需要` extract-text-webpack-plugin `来帮忙：

	//javascript
	var webpack = require('webpack');
	var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
	var ExtractTextPlugin = require("extract-text-webpack-plugin");
	module.exports = {
	plugins: [commonsPlugin, new ExtractTextPlugin("[name].css")],
	entry: {
	//...省略其它配置

webpack会把样式文件提取出来

#### 五、与gulp混搭

	//javascript
	gulp.task("webpack", function(callback) {
		// 运行webpack
		webpack({
			// webpack配置文件
		}, function(err, stats) {
			if(err) throw new gutil.PluginError("webpack", err);
			gutil.log("[webpack]", stats.toString({
				// output options
		}));
		callback();
		});
	});

这样子不需要写webapck.config.js了，直接写在task回调函数里。

#### 六、实战
这里是引用阮老师的一个入门demo，基本上够用了
https://github.com/ruanyf/webpack-demos

开发中遇到的问题，可以去扫文档
http://webpack.github.io/docs/

#### 七、其他
做移动应用我们可以用webpack-dev-server来更改ip地址和端口

webpack-dev-server 默认是localhost:8080

可以通过`--host` 和`--port`来分别设置ip和端口

webpack-dev-server --host 172.16.0.213 --port 9090
