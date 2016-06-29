---
layout:     keynote
title:      "Gulp Getting Started"
subtitle:   "Keynote: Gulp Getting Started"
iframe:     
date:  2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - 前端相关
    - 前端工具
---
# Getting Start

## 简介

  Gulp是基于Node.js的前端构建工具，通过Gulp的插件可以实现前端代码的编译(sass/less)、压缩、测试；图片的压缩；浏览器自动刷新，还有很多强大的功能可以在[这里](http://gulpjs.com/plugins/)查找，Gulp比起Grunt不仅简单，而且更容易阅读,我们可以做一个对比:

  Grunt:
```javascript
  sass: {
    dist: {
      style: 'expanded'
    },
    files: {
      'dist/assets/css/main.css':'src/styles/main.scss'
    }
  },
  autoprefixer: {
    dist: {
      options: {
        browsers: [
          'last 2 version','safari 5','ie8','ie9'
        ]
      }
    }
  },
  grunt.registerTask('styles',['sass','autoprefixer']);

```

  在Grunt里面，每个插件使用的方式相对独立，正如上面的代码通过sass插件将`main.sass`文件编译成了`main.css`文件，接着autoprefixer插件再对编译好的`main.css`文件进行修改，最后覆盖`main.css`。那么覆盖文件就是多余的了，有没有办法走到sass和autoprefixer一并处理完在生成`main.css`？看看Gulp是如何做到的:

```javascript
gulp.task('sass',function(){
  return gulp.src('src/style/main.sass')
    .pipe(sass({style:'compressed'}))
    .pipe(autoprefixer('last 2 version','safari 5' ...))
    .pipe(gulp.dest('dist/assets/css'))
});
```
使用Gulp我们只需要放一个路径，通过管道方式使用插件，最后生成文件，是不是有种JQuery的感觉。这种方式不仅提高效率而且一眼就认清输入和输出文件

## Getting Started

#### Install gulp to your project devDependencies:

```shell
$npm install gulp --save-dev
```

#### Create `gulpfile.js` at the root of your project:

```javascript
var gulp = require('gulp');

gulp.task('default',function(){
  //place code for your default task here
});
```

#### Run gulp

```shell
$gulp
```

## 安装Gulp插件

  - sass的编译([gulp-ruby-sass](https://github.com/sindresorhus/gulp-ruby-sass))
  - 自动添加css前缀([gulp-autoprefixer](https://github.com/Metrime/gulp-autoprefixer))
  - 压缩css([gulp-minify-css](https://github.com/jonathanepollack/gulp-minify-css))
  - JS代码校验([gulp-jshint](https://github.com/spalger/gulp-jshint))
  - 合并JS代码([gulp-gulp-concat](https://github.com/contra/gulp-concat))
  - 压缩JS代码([gulp-uglify](https://github.com/terinjokes/gulp-uglify))
  - 压缩图片([gulp-imagemin](https://github.com/sindresorhus/gulp-imagemin))
  - 自动刷新页面([gulp-livereload](https://github.com/vohof/gulp-livereload))
  - 图片缓存,只有图片替换了才压缩([gulp-cache](https://github.com/jgable/gulp-cache))
  - 更新提醒([gulp-notify](https://github.com/mikaelbr/gulp-notify))

安装这些插件需要运行:

```shell
$ npm install  gulp-ruby-sass gulp-autoprefixer gulp-minify-css gulp-jshint gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-cache del --save-dev

```

## 加载插件

在`gulpfile.js` 中加载插件:

```javascript
var gulp = require('gulp'),
    sass = require('gulp-ruby-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload'),
    del = require('del');
```

## 建立任务

### 编译Sass、自动化添加CSS前缀和压缩

首先我们编译Sass，添加前缀，保存到我们制定的目录下面，还没结束，我们还要压缩，给文件添加`.min`后缀后再输出压缩文件到制定目录，最后提醒任务完成:

```javascript
gulp.task('style',function(){
  return gulp.src('src/styles/main.scss')
    .pipe(sass({ style:'expanded' }))
    .pipe(autoprefixer('last 2 version', 'safari 5', 'ie 8', 'ie 9', 'opera 12.1', 'ios 6', 'android 4'))
    .pipe(gulp.dest('dist/assets/css'))
    .pipe(rename({suffix:'.min'}))
    .pipe(minifycss())
    .pipe(gulp.dest('dist/assets/css'))
    .pipe(notify({message:'Style task completed'}));

})
```

  `gulp.task`这个API主要用来创建任务，在命令行下可以输入 `$gulp styles` 来执行上面的任务

  `gulp.src`这个API设置需要处理的文件的路径，可以多个文件以数组方式`[main.scss,vender.scss]`传入，也可以使用正则`/**/*.scss`.

  `.pipe`这个API将根据需要处理的文件导向Sass插件，哪些插件的用法可以在Github上找到

  `gulp.dest()`API设置生成文件的路径，一个任务可以有多个生成路径，一个可以输出未压缩的版本，一个可以输出压缩后的版本。

### JS 代码校验、合并和压缩

```javascript
gulp.task('scripts',function(){
  return gulp.src('src/scripts/**/*.js')
    .pipe(jshint('.jshintrc'))
    .pipe(jshint.reporter('default'))
    .pipe(concat('main.js'))
    .pipe(gulp.dest('dist/assets/js'))
    .pipe(rename({sufffix:'.min'}))
    .pipe(uglify())
    .pipe(gulp.dest('dist/assets/js'))
    .pipe(notify({ message:'Scripts task complete' }));
});
```

需要提醒的是，我们要设置JSHint 的reporter方式，上面使用`default`默认方式，了解更多请点击[这里](http://jshint.com/docs/reporters/)

### 压缩图片

```javascript
gulp.task('image',function(){
  return gulp.src('src/images/**/*')
    .pipe(imagemin({optimizationLevel:3,progressive:true,interlaced:true}))
    .pipe(gulp.dest('dist/assets/img'))
    .pipe(notify({ message:'Images task complete'}));
})
```

这个任务使用`imagemin`插件把所有在`src/images/`目录以及其子目录下的所有图片(文件)进行压缩,我们可以进一步优化，利用缓存保存已压缩过的图片，使用之前装过的`gulp-cache`插件，不过要修改一下上面的代码

```javascript
.pipe(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true }))
```
改为
```javascript
.pipe(cache(imagemin({ optimizationLevel: 5, progressive: true, interlaced: true })))
```

这样，只有新建或者被修改过的图片才会被压缩

### 清除文件

  在任务执行之前，最好先清除之前生成的文件：

  ```javascript
  gulp.task('clean',function(cb){
    del(['dist/assets/css','dist/assets/js','dist/assets/img'],cb);
  })
  ```

  这里没必要使用Gulp插件，可以使用NPM提供的插件，我们用一个回调函数(`cb`)确保在退出前完成任务

### 设置默认任务(default)

我们在命令行下输入`gulp`执行的就是默认任务,现在我们为默认任务指定执行上面写好的三个任务:

```javascript
gulp.task('default',['clean'],function(){
  gulp.start('styles','scripts','images');
})
```

在这个例子里，clean任务执行完成了才会去运行其他的任务，在`gulp.start()`里的任务执行的顺序是不确定的，所以将要在他们之前执行的任务写在数组里面。
