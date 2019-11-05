title: webpack系统梳理
author: 丨yanwei丨
author_id: ''
tags:
  - webpack
language: ''
categories:
  - webpack
date: 2019-01-12 12:31:00
---
# webpack4.0

## 什么是webpck
webpack可以看做是模块打包机：它做的事情是，分析你的项目结构，找到JavaScript模块以及其他的一些浏览器不能直接运行的拓展语言(less,TypeScript等），并将其打包成合适的格式以供浏览器使用。
![image.png](https://cdn.nlark.com/yuque/0/2019/png/450565/1572058782389-518dee0b-f9d6-4f31-9e56-39b42de2f650.png#align=left&display=inline&height=236&name=image.png&originHeight=708&originWidth=1502&search=&size=102360&status=done&width=500.6666666666667)
可以做的事情：
> 代码转换、文件优化、代码分割、模块合并、自动刷新、代码校验、自动发布


## webpack常见配置
webpack可以进行0配置，但是功能非常弱。所以大部分情况下都需要手动配置。
默认配置文件：webpack.config.js

```javascript
// webpack是node语法
const path = require('path'); //引入node核心模块
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')
const MiniCssExtractPlugin = require('min-css-extract-plugin');
const TerserJSPlugin = require('terser-webpack-plugin'); //用来压缩js，代替UglifyJsPlugin
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const webpack = require('webpack')
module.export = {
  optimization: { // css优化项
    minimizer: [
      new TerserJSPlugin({
        cache: true,
        parallel: true, // 并发打包
        sourceMap: true  // 用与高级js语法向低级转换之后的源码映射，帮助调试  
      }),
      new OptimizeCSSAssetsPlugin({})],
  },
  devServer: { // 开发服务器配置
    port: 3000,
    progress: true,
    contentBase: './build',  // 设置静态服务的目录
    compress: true
  },
  mode: 'development', // 模式 默认两种 production development
  entry: './src/index.js', // 入口
  output: {
    filename: 'budle.[hash:8]js', // 打包后文件名
    path: path.resolve(__dirname, 'dist'), // 必须是绝对路径，resolve把相对路径解析成绝对路径，__dirname在当前目录下生产dist目录 
    publicPath: 'http://wwww.localhost:3000/' //生成所有资源文件时加上地址前缀 
  },
  resolve: { // 解析第三方包
    modules: [path.resolve('node_modules')],
    extensions: ['.js', '.css'], //设置无后缀情况下寻找的扩展名
    // mainFilelds: ['style','main'],
    // mainFiles: [], //入口文件名字 默认index
    alias: { // 别名
      bootstrap: 'bootstrap/dist/css/bootstrap.css'
    }
  },
  devServer: {  //配置代理，解决跨域的三种方法
    proxy: {
      //使用代理调用服务端接口
      '/api': {
        target: 'http://localhost:3000',
        pathRewrite: {'/api': ''}
      },
      // 不用服务，本地mock数据
      before(app){
        app.get('/user',(req,res) => {
          res.json({name: '珠峰架构'})
        })
      },
      // 不用代理访问服务端，使用中间件webpack-dev-middleware，在服务端启动webpack
    }
  },
  watch: true, //改动内容后实时编译
  watchOption: {
    poll: 1000,//每秒检测1000次
    aggregateTimeout: 500, //防抖,500毫秒内，
    ignored: /node_modules/ // 不需要监控的文件   
  },
  // 源码映射
  devtool: 'source-map', // 增加映射文件 可以帮我们调试源代码，标识当前出错的列和行
  // devtool: 'eval-source-map', // 集成到文件中，不会产生单独的映射文件， 仍可以标识当前出错的列和行
  // devtool: 'cheap-module-source-map', // 不会定位到具体列，生成的是一个单独的映射文件，你可以保留起来，手动查找错误
  // devtool: 'cheap-module-eval-source-map', //集成到文件中，不会产生单独的映射文件，可以标出错误位于哪一行
  plugins: [
    new webpack.DefinePlugin({ //定义环境变量
      DEV: JSON.stringify('dev'), // 转成字符串
      FLAG: 'true' // 刚好被解析成Boolean对象
    }),
    new HtmlWebpackPlugin({
      template: '/src/index.html',
      filename: 'index.html',
      minify: {
      },
      hash: true
    }),
    new MiniCssExtractPlugin({
      filename: 'css/main.css'
    }),
    new webpack.providePlugin({ //2.在每个模块中都注入$对象
      $: 'jquery'
    }),
    new CleanWebpackPlugin('./dist'), // 先清空，再打包
    new CopyWebpackPlugin([
      { from: './doc', to: './' } // 打包时将文档目录中的文件拷贝到打包的目录
    ]),
    new webpack.BannerPlugin('make by xxx'), // 给打包后的文件添加版权声明的注释

    // 从moment中引入内容时，忽略locale，减小moment.js打包大小,代码中设置语言包时需要手动引入
    // import 'moment/locale/zh-cn';
    // moment.locale('zh-cn');
    new webpack.IgnorePlugin(/\.\/locale/, /moment/), 
  ],
  externals: { //3.告诉webpack这个模块是外部引入的（比如cdn），并不需要打包
    jquery: 'jQuery',
  },
  module: { // 有些内容不能进行模块加载，所以可以利用loader进行转化
    // loader,默认顺序从右到左，从下到上
    noParse: /jquery/, // 独立的模块，打包时不去解析内部的依赖关系，提升打包速度
    rules: [ // 规则匹配
      {
        test: /\.html$/,
        use: 'html-withimg-loader'
      },
      {
        test: /\.(png|jpg|gif)$/,
        use: 'url-loader',  //当图片小于多少k时，用base64来转化,减少http请求，否则用file-loader产生真实图片 
        options: {
          limit: 1,
          outputPath: '/img/', // 输入文件目录
          publicPath: 'http://localhost:3000', // 只针对图片资源添加地址前缀
        }
      },
      {
        test: require.resolve('jQuery'), //1.将引用的第三方模块暴露到window上
        use: 'expose-loader?$'
      },
      {
        test: /\.js$/,
        use: {
          loader: 'eslint-loader',
        },
        enforce: 'pre' // 在下一个loader之前执行，前置loader
      },
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            persets: [
              "@bable/preset-env"
            ],
            plugins: [
              '@babel/plugin-proposal-class-properties'
              //  ……
            ]
          },
          include: path.resolve(__dirname, 'src'),
          exclude: /node_modules/
        }
      },
      // css-loader,解析@import语法
      // style-loader, 把css插入到head标签中
      { test: /\.css$/, use: 'css-loader' }, // 用法1： 字符串只能写一个
      { test: /\.css$/, use: ['css-loader', 'postcss-loader', 'style-loader'] }, //数组方式
      { // 对象方式
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader', // 处理less-loader时改成less-loader
            options: {
              insertAt: 'top'
            }
          },
          'css-loader',
          'postcss-loader'
        ]
      },
      { // css抽离,可以通过link标签插入到页面中  
        test: /\.less$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader', // 项目根目录下建一个postcss.config.js
          'less-loader'
        ]
      }
    ]
  }
}

// postcss.config.js

module.exports = {
  plugins: [require('autoprefixer')]
}
```

### 多入口
```javascript
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  // 多入口
  mode: 'development',
  entry: {
    home: './src/index.js',
    other: './src/other.js'
  },
  output: { 
    // [name] home,other
    filename: '[name].js',
    path: path.resolve(__dirname,'dist')
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'home.html',
      chunks: ['home'] // 只引入对应模块
    }),
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'other.html',
      chunks: ['other']
    })
  ]
}
```
### 动态链接库
单独打包react和react-dom包

1. 项目目录下新建webpack.config.react.js
```javascript
const path = require('path'); //引入node核心模块

module.export = {
  mode: 'development', // 模式 默认两种 production development
  entry: {
  	react: ['react','react-dom']
  },
  output: {
  	filename: '_dll_[name].js', // 产生的文件名
    path: path.resolve(__dirname,'dist'),
    library: '_dll_[name]', // _dll_react
    libraryTarget: 'var' //
  },
  plugins: [
  	new webpack.DllPlugin({ // name = library
    	name: '-dll_[name]',
			path: path.resolve(__dirname, 'dist', 'manifest.json') // dist目录下会生成一个清单文件
    })  
  ]
}
```

2. 执行单独的打包命令`npx webpack --config webpack.config.react.js` 会在dist目录下生成打包文件_dll_react.js和清单文件manifest.json
2. 在HTML中引入打包好文件名

`<script src="/_dll_react.js"></script> `

3. 正式的配置文件中引入动态链接库
```javascript
const path = require('path'); //引入node核心模块
module.export = {
  mode: 'development', // 模式 默认两种 production development
  entry: './src/index.js',
  output: {
  	filename: 'bundle.js', // 产生的文件名
    path: path.resolve(__dirname,'dist')
  },
  plugins: [
  	new webpack.DllReferencePlugin({ // 打包时先去查找清单，清单找不到在进行打包
    	manifest: path.resolve(__dirname, 'dist','manifest.json),
    })  
  ]
}
```

### 多线程打包

```javascript
// webpack.config.js
let path = require("path");
let HappyPack = require('happypack');
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  devServer: {
    ...
  },
  output: {
    ...
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        include: path.resolve("src"),
        use: 'happy/loader?id=js'   //此处将之前配置的babel的一些预设什么的替换为happy/loader。id=js，因为happy也可以打包css,
      },
      {
        test: /\.css$/,
        use: 'happy/loader?id=css' 
      }
    ]
  },
      
  plugins: [
    new HappyPack({
      id: 'js',
      use: [{     // 将js的具体规则放置在此处
        loader: 'babel-loader',
        options: {
          presets: [
            '@babel/preset-env',
            '@babel/preset-react'
          ]
        }
      }]
    }),
    new HappyPack({
      id: 'css',
      use: ['style-loader', 'css-loader']
    })
 ]
};
```


## loadger 
有些内容不能进行模块加载，所以可以利用loader进行转化



## 
