# webpack4

- [自动清理构建产物](#1)
- [CSS3的属性为什么需要前缀](#2)
- [px自动转rem](#3)
- [多页面打包基本思路](#4)
- [静态资源内联](#5)
- [使用sourcemap](#6)
- [提取页面公共资源](#7)
- [代码分割和动态import](#8)
- [webpack和ESLint结合](#9)
- [webpack实现SSR打包](#10)
- [优化构建时命令行显示日志](#11)
- [构建异常和中断处理](#12)




<h3 id="1">自动清理构建产物</h3>

**避免每次构建都要删除**

默认删除output每次构建的产物

#### 使用插件 clean-webpack-plugin

```js
module.exports = { entry: {
app: './src/app.js',
search: './src/search.js' },
output: {
filename: '[name][chunkhash:8].js', path: __dirname + '/dist'
},
plugins: [
    + new CleanWebpackPlugin() 
 };
```

<h3 id="2">CSS3的属性为什么需要前缀</h3>

![image.png](https://upload-images.jianshu.io/upload_images/5531021-96d8cb4e707dae78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```js
.box {
-moz-border-radius: 10px;
webkit-border-radius: 10px; 
o-border-radius: 10px; 
border-radius: 10px;
}
```

每次使用都需要添加，那么有什么方式可以不需要人工添加呢。

[插件链接](https://www.webpackjs.com/loaders/postcss-loader/)

使用autoprefixer插件

**PostCSS 插件 autoprefixer ⾃自动补⻬齐 CSS3 前缀**

![image.png](https://upload-images.jianshu.io/upload_images/5531021-84d80df46fc6eeae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<h3 id="3">px自动转rem</h3>

#### 使用插件：px2rem-loader

[px2rem-loader](https://www.npmjs.com/package/px2rem-loader)

[在vue中的实践](https://www.cnblogs.com/xiaobaibubai/p/8528744.html)

![image.png](https://upload-images.jianshu.io/upload_images/5531021-0f15b09fe1ae2925.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



<h3 id="5">静态资源内联</h3>

#### 资源内联的意义

**代码层面**

1. 页面框架的初始化脚本
2. 上报相关打点
3. css内联避免页面闪动

**请求层面：减少HTTP网络请求数**

* 小图片或者字体内联(url-loader)

**raw-loader 内联 html**
例如：把所有必须的meta内联成一个html

```js
<script>${require(' raw-loader!babel-loader!. /meta.html')}</script>
```

**raw-loader 内联 JS**

会需要babel-loader来支持

```
<script>${require('raw-loader!babel-loader!../node_modules/lib-flexible')}</script>
```

使用row-loader应该用0.5版本，最新版本目前是存在问题。

**CSS内联**

方案一 ： 借助style-loader

方案二 ： html-inline-css-webpack-plugin

![image.png](https://upload-images.jianshu.io/upload_images/5531021-343309f9cd3a31be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


<h3 id="4">多页面打包</h3>

1.如果是js文件可以考虑用webpack
2.如果多页面时html文件，可以考虑用gulp

*动态获取entry和设置html-webpack-plugin数量。*

利用glob.sync [glob库](https://www.npmjs.com/package/glob)

```js
· entry: glob.sync(path.join(__dirname, './src/*/index.js')),
```
eg:

```js
    module.exports = { entry: {
        index: './src/index/index.js',
        search: './src/search/index.js ‘ }
};
```

这里我们可以按照上面的方式写一个多页面打包的js

```js
const setMPA = ()=>{

    const entry = {},
    const htmlWebpackPlugins =[];

    const entryFiles = glob.sync(path.join(__dirname,'./src/*/index.js'))

    //动态设置entry

    Object.keys(entryFiles).map((index)=>{
        const entryFile = entryFiles[index];
        //写一个正则匹配规则

        const match = entryFile.match(/src\/(.*)\/index\.js);
        const pageName = match && match[1];
        htmlWebpackPlugins.push(
            new HtmlWebpackPlugin({
                template:path.join(__dirname,`./src/{pageName}/index.html`)
                filename：`{pageName}.html`，
                chunks:[pageName],
                inject:true,
                minify:{
                    html5:true,
                    collapseWhitespace:true,
                    preserveLineBreaks:false,
                    minifyCSS:true,
                    minifyJS:true,
                    removeComments；false，

                }
            })
        )
    })
    return {
        entry,
        htmlWebpackPlugins
    }
}

const {entry,htmlWebpackPlugins} = setMPA();
```

<h3 id="6">使用sourcemap</h3>
<h3 id="7">提取页面公共资源</h3>
<h3 id="8">代码分割和动态import</h3>
<h3 id="9">webpack和ESLint结合</h3>
<h3 id="10">webpack实现SSR打包</h3>
<h3 id="11">优化构建时命令行显示日志</h3>
<h3 id="12">构建异常和中断处理</h3>




















