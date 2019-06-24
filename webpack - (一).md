# webpack4

- [概念](#1)
  - [为什么需要构建工具](#1.1)
  - [构建工具的演变](#1.2)
  - [实现一个简单webpack的demo](#1.3)
- [核心概念](#2)
  - [entry - 入口](#2.1)
  - [Loader](#2.2)
  - [plugins](#2.3)
  - [mode](#2.4)
- [资源解析](#3)
  - [解析ES6](#3.1)
  - [解析React JSX](#3.2)
  - [解析CSS](#3.3)
  - [解析less和sass](#3.4)
  - [解析图片](#3.5)
  - [解析字体](#3.6)
  - [使⽤用 url-loader](#3.7)
  - [webpack 中的⽂文件监听](#3.8)
  - [热更更新:webpack-dev-server](#3.9)
  - [文件指纹](#3.10)
  - [代码压缩](#3.11)
  - [](#3.12)
  - [](#3.13)
  

![image.png](https://upload-images.jianshu.io/upload_images/5531021-7daf31d6f594c0e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<h3 id="1">概念</h3>
本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

<h4 id="1.1">为什么需要构建工具</h4>

构建工具在日常的开发中，显得尤为的重要 - 

1. 转换ES6语法
2. 转换JSX
3. CSS前缀补全/预处理器
4. 压缩混淆
5. 图片压缩

等等。

<h4 id="1.2">构建工具的演变</h4>

![image.png](https://upload-images.jianshu.io/upload_images/5531021-780e98e1a527c07e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么为什么选择webpack呢？

webpack的插件和配置比较灵活，且社区比较活跃。


![image.png](https://upload-images.jianshu.io/upload_images/5531021-62cfd3c4b05eb56f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/5531021-b4b0f3200239cb27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然要想去理解webpack ，先从webpack的四个概念入手

* 入口(entry)
* 输出(output)
* loader
* 插件(plugins)


<h4 id="1.3">实现一个简单webpack的demo</h4>

>首先

```js
mkdir my-project

cd my-project

npm init -y

npm install webpack webpack-cli -D
```

新建文件夹 src ,在src下新建index.js文件  写入代码

``` document.write("hello world"); ```

在webpack.config.js 里写入


```
const path = require('path');

module.exports = {
    entry:'./src/index.js',
    output:{
        path:path.join(__dirname,'dist'),
        filename:'bundle.js'
    },
    mode:'production'
}
```

之后运行npm run build (这里要在package.json的scrpit里修改一下)

运行之后会生成一个dist 文件夹，其中会生成bundle.js的文件，这个时候，新建一个html文件。引入这个 bundle.js,查看文件效果。

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <script src="./bundle.js"></script>
</body>
</html>
```

<h3 id="2">核心概念</h3>

<h4 id="2.1">entry - 入口</h4>

**入口起点(entry point)**指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为 bundles 的文件中。

可以通过在 webpack 配置中配置 entry 属性，来指定一个入口起点（或多个入口起点*单入口是一个string，多入口是一个对象。*

单入口:

```js

module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

多入口:

```js
module.exports = {
  entry: {
    'index': './src/view/index/index.js',
    'login': './src/view/login/login.js',
  }
}
```


<h4 id="2.2">Loader</h4>

webpack是开箱即用，只支持JS和JSON两种文件类型，通过loader去支持其他文件类型，并且把它们转化成有效的模块，并且可以添加到依赖中。

本身是一个函数，接受源文件作为参数，返回转换类型的结果。

> 使用

```js
rules:[
            {
                test:/.js$/,
                use:'babel-loader'
            },
            {
                test:/.css$/,
                use:[
                    'style-loader',
                    'css-loader'
                ]
            },
            {
                test:/.less$/,
                use:[
                    'style-loader',
                    'css-loader',
                    'less-loader'
                ]
            },
            {
                test:/\.(jpg|png|gif|svg|jpeg)$/,
                use:[
                    {
                        loader:'file-loader',
                        options:{
                            name:'img/[name].[ext]',    //可以重写css中引入图片部分
                            publicPath:'../assets/'
                            // outputPath:'assets/'     //当路径换成publicPath的时候，不管用
                        }
                    }
                ]
            }
        ]
```


*test 指定匹配规则，use 指定使⽤用的 loader 名称*


**常见的loader有哪些？**

![image.png](https://upload-images.jianshu.io/upload_images/5531021-b923f46b23cf4fe3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<h4 id="2.3">plugins</h4>

插件⽤用于 bundle ⽂文件的优化，资源管理理和环境变量量注⼊入，作⽤用于整个构建过程。

**常见的plugins有哪些？**

![image.png](https://upload-images.jianshu.io/upload_images/5531021-f7b0655d7c1b428a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>使用

```js
const path = require('path');

module.exports = { output: {
    filename: 'bundle.js' },
    plugins: [
        new HtmlWebpackPlugin({template: './src/index.html'})
    ] };
```




<h4 id="2.4">Mode</h4>

Mode ⽤用来指定当前的构建环境是:production、development 还是 none 设置 mode 可以使⽤用 webpack 内置的函数，默认值为 production

**mode的内置函数功能**

![image.png](https://upload-images.jianshu.io/upload_images/5531021-ce315e4bf750e19d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<h3 id="3">资源解析</h3>


<h4 id="3.1">解析ES6</h4>

使用的是`babel-loader`
babel的配置文件是`.babelrc`

**安装**

```
npm install @babel/core @babel/preset-env babel-loader -D
```

在webpack.config.js文件里添加

```js
const path = require('path');
module.exports = { entry: './src/index.js', output: {
filename: 'bundle.js',
path: path.resolve(__dirname, 'dist') },
+
+ +{
+ test: /\.js$/,
+ use: 'babel-loader' +}
+]
+}
};
```
新增`.babelrc`文件。在文件内添加内容

```js
{
    "presets": [
        "@babel/preset-env"
    ]
}
```

<h4 id="3.2">解析React JSX</h4>

**安装**

```js
npm install @babel/preset-react -D
```

**使用**

```js
{
    "presets": [
        "@babel/preset-env",
  +     "@babel/preset-react"
    ]
}
```


<h4 id="3.3">解析CSS</h4>

- css-loader 是用于加载.css文件，并且转换成common.js对象

- style-loader 将样式通过 <style> 标签插⼊入到 head 中


```js
 module:{
        rules:[
            {
                test:/.js$/,
                use:'babel-loader'
            },
            {
                test:/.css$/,
                use:[
                    'style-loader',
                    'css-loader'
                ]
            }
        ]
    }
```
###### 注：这里的style-loader 必须在css-loader之上

 
<h4 id="3.4">解析less和sass</h4>

less-loader是用于将less转化成css
sass-loader是用于将sass转化成sass

```js
npm install sass-loader node-sass webpack --save-dev

npm install less-loader --save-dev

```

**sass**

```js
module.exports = {
	...
    module: {
        rules: [{
            test: /\.scss$/,
            use: [
                "style-loader", // creates style nodes from JS strings
                "css-loader", // translates CSS into CommonJS
                "sass-loader" // compiles Sass to CSS, using Node Sass by default
            ]
        }]
    }
};
```

**less**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.less$/,
        loader: 'less-loader', // compiles Less to CSS
      },
    ],
  },
};
```

<h4 id="3.5">解析图片</h4>

**file-loader ⽤用于处理理⽂文件**

```js
$ npm install file-loader --save-dev
```


```js
module.exports = {
  module: {
    rules: [
            {
                test:/\.(jpg|png|gif|svg|jpeg|tiff)$/,
                use:[
                    {
                        loader:'file-loader',
                        options:{
                            name:'img/[name].[ext]',    //可以重写css中引入图片部分
                            publicPath:'../assets/'
                            // outputPath:'assets/'     //当路径换成publicPath的时候，不管用
                        }
                    }
                ]
            }
    ],
  },
};
```



<h4 id="3.6">解析字体</h4>

**解析字体也用 file-loader**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: [
          {
            loader: 'file-loader',
            options: {},
          },
        ],
      },
    ],
  },
};

```


<h4 id="3.7">使⽤用 url-loader</h4>

**使⽤用 url-loader**

url-loader 也可以处理理图⽚片和字体，可以设置较⼩小资源⾃自动 base64

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}
```

上面代码的意思是，当文件大小小于8192 byte的时候，会自动转化成base64的格式放置在文件中，打个比方，一个react.js的大小 是100kb ，里面有个图片大小是20kb ，这个时候如果使用file-loader去打包，那么这个react.js的文件就100kb（没使用文件压缩的前提下），如果使用了url-loader打包的情况下，那么这个react.js的大小就是120kb。 

<h4 id="3.8">webpack 中的⽂文件监听</h4>

⽂文件监听是在发现源码发⽣生变化时，⾃自动重新构建出新的输出⽂文件。

webpack开启监听有两种方式：

1. 启动 webpack 命令时，带上 --watch 参数
2. 在配置 webpack.config.js 中设置 watch: true 

[具体的watch可以看官网上的操作](https://webpack.js.org/configuration/watch/#watch)

在watchOptions 里可以设置 ignored，poll，aggregateTimeout

- ignored是忽略监听的文件 ，可以是[],string类型,
    
  ```js
   watchOptions: {
    ignored: ['files/**/*.js', 'node_modules']
  }
  ```
  
  这里是不去监听file 下的某个js，和node_modules文件
   
- poll 是 监听轮询时间
  
  ```js
    watchOptions: {
         poll: 1000 // 每秒去监听你的文件目录
    }
  ```
  
- aggregateTimeout 是监听到变化发生后会多少ms再去执行
  
  ```js
  //监听到变化发生后会等300ms再去执行，默认300ms
    aggregateTimeout: 300,
  ```
  
  
  这里监听的原理是 :
  
  *轮询判断⽂文件的最后编辑时间是否变化 某个⽂文件发⽣生了了变化，并不不会⽴立刻告诉监听者，⽽而是先缓存起来，等 aggregateTimeout*
  
  唯⼀一缺陷:每次需要⼿手动刷新浏览器器

<h4 id="3.9">热更更新:webpack-dev-server</h4>

**热更更新:webpack-dev-server **

WDS 不不刷新浏览器器
WDS 不不输出⽂文件，⽽而是放在内存中
使⽤用 HotModuleReplacementPlugin插件（这时webpack内置的，所以只需引入webpack即可）

**热更更新:使⽤用 webpack-dev-middleware**

WDM 将 webpack 输出的⽂文件传输给服务器器

*适⽤用于灵活的定制场景*

```js
  //安装使用express 、webpack-dev-middleware
  npm install --save-dev express webpack-dev-middleware
  
```
*使用*

```js
    const express = require('express');
    const webpack = require('webpack');
    const webpackDevMiddleware = require('webpack-dev-middleware');
    
    const app = express();
    const config = require('./webpack.config.js');
    const compiler = webpack(config);
    
    // Tell express to use the webpack-dev-middleware and use the webpack.config.js
    // configuration file as a base.
    app.use(webpackDevMiddleware(compiler, {
      publicPath: config.output.publicPath
    }));
    
    // Serve the files on port 3000.
    app.listen(3000, function () {
      console.log('Example app listening on port 3000!\n');
    });
```

**热更新原理**

![image.png](https://upload-images.jianshu.io/upload_images/5531021-9e45385574f2df40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**步骤**

1. webpack Compile：将JS编译成成Bundle
2. HMR Server：将热更新的文件输出给HMR Runtime（这里应该是websocket的原理）
3. Bundle Server：提供给文件在浏览器的访问
4. HMR Runtime：会被注入到浏览器中，更新文件的变化
5. bundle.js：构建输出的文件。


<h4 id="3.10">文件指纹</h4>

打包后输出的⽂文件名的后缀

![image.png](https://upload-images.jianshu.io/upload_images/5531021-95535d4427754890.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**文件指纹如何⽣生成**

- Hash:和整个项⽬目的构建相关，只要项⽬目⽂文件有修改，整个项⽬目构建的 hash 值就会更更改
- Chunkhash:和 webpack 打包的 chunk 有关，不不同的 entry 会⽣生成不不同的 
- chunkhash 值 Contenthash:根据⽂文件内容来定义 hash ，⽂文件内容不不变，则 contenthash 不不变

**JS的文件指纹设置**

1. 设置output的filename,使用[chunkhash]。
 ![image.png](https://upload-images.jianshu.io/upload_images/5531021-ee3c1cf55952e828.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 **CSS 的⽂文件指纹设置**
 1. 设置 MiniCssExtractPlugin 的 filename， 使⽤[contenthash]
 ![image.png](https://upload-images.jianshu.io/upload_images/5531021-6a262fb585f45fcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 **图⽚片的⽂文件指纹设置**
 
 ![image.png](https://upload-images.jianshu.io/upload_images/5531021-c96c29e32ca72b8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ![image.png](https://upload-images.jianshu.io/upload_images/5531021-eebcf82c5a7b0374.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<h4 id="3.11">代码压缩</h4>

1. JS ⽂文件的压缩

内置了了 uglifyjs-webpack-plugin 

2. HTML 压缩

![image.png](https://upload-images.jianshu.io/upload_images/5531021-b99a6f519e56a9c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. css压缩，使⽤ optimize-css-assets-webpack-plugin 同时使⽤cssnano

![image.png](https://upload-images.jianshu.io/upload_images/5531021-a4c9f396d2f6a813.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


以上内容参考了：极客时间-webpack内容。感兴趣的小伙伴可以考虑一下，因为老师讲解的内容非常细致。

![image.png](https://upload-images.jianshu.io/upload_images/5531021-6a17b7c99ce8dd0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







