# gulp打包

### 步骤

1. 使用`npm install` 或者 `cnpm install` 去加载依赖包
2. 当包全部加载完成之后，如果是本地运行，可使用`gulp debug` ，如果放置在服务器上（测试|生产）则运行`gulp build`
3. 运行完成之后会生成一个dist包，将包内的内容拖拽至服务上方可。

### 包说明

```
1. gulp      //打包工具
2. gulp-minify-css    //压缩css
3. gulp-uglify      //压缩js
4. gulp-imagemin     //压缩图片
5. gulp-htmlmin     //压缩html
6. runSequence      //异步运行
7. del              //删除包
8. gulp-replace     //替换文件内的string
9. gulp-rename      //重命名
10. gulp-notify     // 输出信息

```

### 特殊说明

1. 项目内的操作分 activity包和非activity包。因此可选择是否需要打包activity包
2. 项目输出都为.htm结尾，除了main的后缀名是.html，为了区分新旧项目。
3. 项目运行gulp build运行时间较长，主要是花费在图片上的时间，操作图片的两个task(任务)为`images`,`imagesac` 。因此如果打包过一次，下次变动除了图片外，可以删除这两个task。
4. 如果其他需求增加，可以在`gulpfile.js`文件内加上task，在build或debug的数组内添加此task

###### eg: 添加css前缀

```
var autoprefixer  = require('gulp-autoprefixer') //引入相应的插件


gulp.task('autocss',function(){
    return gulp.src('src/css/*.css')
        .pipe(autoprefixer({
            browsers:['last 2 versions'],
            cascade:false
        }))
        .pipe(gulp.dest('dist/css'))
})

```

###### 目前gulp的版本是3.9.1。尽量别升级，升级到最新版本会出现`gulp.on(...).on(...).on(...) start is not a function` 的报错，是因为runSequence是基于3.xx.x版本下的gulp.start。异步操作。如果是4版本，则使用gulp的最新的语法。





