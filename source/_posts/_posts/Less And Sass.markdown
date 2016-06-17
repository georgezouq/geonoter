---
layout:     keynote
title:      "Transitioning from Server to Client Side Web Development with Angular 2"
subtitle:   "Keynote: JavaScript Modularization Journey"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - 前端开发
    - CSS
---
# Less与Sass解析

  首先我们要明确一个观点，尽管Less和Sass语法上有一些共性，但是Less !== Sass。当我们构建一个网站的时候，
我们可能会需要调色板这个功能，即拥有提供固定的可选择样式，避免样式跳跃过大。这个时候我们就用到了Less和Sass，
事实证明，他们的功能要比这个多得多。

### Less 和 Sass 语法共性

1.混入（Minxins）—— class中的class
2.参数混入 —— 可以传递参数的class
3.嵌套规则 —— Class中嵌套class，从而较少重复的代码
4.运算 —— CSS中用上数学
5.颜色功能 —— 可编辑颜色
6.命名空间（namespace） —— 分组样式
7.作用域 —— 局部修改样式
8.JavaScript赋值 - 在CSS中使用JavaScript表达式赋值

Less和Sass的主要不同就是他们的实现方式，Less是基于JavaScript，所以是在客户端处理。而
Sass是基于Ruby在服务器端处理的。很多开发者不会选择Less因为JavaScript引擎需要额外的时
间来处理代码然后输出修改过的CSS到浏览器。关于这个有很多种方式，我选择的是只在开发环节使用
LESS，一旦我完成开发，就复制然后粘贴Less输出的到一个压缩器，然后到一个独立的CSS文件来替代
Less文件。另外，我选择使用LESS.app来编译和压缩Less文件。两个选择都将最小化你的样式输出，
从而避免由于用户的浏览器不支持JavaScript而可能引起的任何问题。

# Less Is More

## Sass 安装

### 安装

  如果已经安装好了Ruby，接着在命令行下输入

  ```shell
    gem install sass
  ```

### 使用

  将Sass文件(.sass)转化为`css`代码

  ```shell
    sass test.scss
  ```

  如果要将显示结果保存成未见，后面再跟一个`.css`文件名

  ```shell
  sass test.scss test.css
  ```

  Sass 提供四个编译风格选项：

  1.nested : 嵌套缩进的css代码，他是默认值
  2.expanded : 没有缩进的、拓展css代码
  3.compact : 简洁格式的css代码
  4.compressed : 压缩后的css代码

  在生产环境当中，一般使用最后一个选项

  sass --style compressed test.sass test.css

  也可以让sass监听某人文件或目录，一旦源文件有变化，就自动生成编译后的文件
```shell
  //watch a filter
  sass --watch input.scss:output.css

  //watch a directory
  sass --watch app/sass:public/stylesheets

```  
## Less 安装

在项目中引入less.js

1.下载less.js
2.创建一个文件来存放Less代码，如style.less
3.添加以下代码到你的HTML的<head>中

```HTML
<link rel="stylesheet/less" type="text/css" href="styles.less">
<script src="less.js" type="text/javascript"></script>
```

其实也有一个[服务器端的less版本](http://lesscss.org/#-server-side-usage)，在服务器上安装Less的最简单的办法就是使用[Node Package](https://github.com/npm/npm)Messager (NPM 一看 就知道是基于NodeJS的)

## 相同和不同


### 变量
    If you are developer,variables will be your best friend

我们可以将能重复使用到的值（本案例里是color）设置成变量，这样就可以避免重复的定义，减少很大的工作量

```less
  @blue : #00c;
  @light_blue : @blue + #333;
  @dark_blue : @blue - 333;
```

他们看起来：
[!less]:https://github.com/georgezouq/geoblog/raw/master/img/blue-gradient,jpg

关于变量 Less和Sass的唯一区别就是 Less使用`@` 而 Sass 使用`$`.同时还有一些作用域上的差别，我们之后会提到

### 混入(mixin)

有些时候我们会创建一些在样式表中重复使用的样式规则，通常会使用
```html
  <div class="border article"></div>
```

这种方式引用多种样式，但是有了less 我们可以

```less
.border{
  border-top : 1px dotted #333;
}
article.post{
  background:#333;
  .border;
}
ul.menu{
  background:#ccc;
  .border;
}

```

下面是在两个元素中分别添加`.border` 同样的效果，而且仅仅在样式变中就完成了

[!less]:https://github.com/georgezouq/geoblog/raw/master/img/bordered-elements,jpg

在Sass中，我们需要在样式规则前添加`@minxin`声明,然后通过`@include`来调用它

```Sass
  @mixin border {
    border-top:1px dotted #333;
  }
  article.post {
    background : #eee;
    @include border;
  }
  ui.menu {
    background:#ccc;
    @include border;
  }
```

### 参数混入

使用Less或Sass，我们可以使用函数处理Css中多余的工作。最好的例子就是我们正在经历的从Css2到css3的过渡过程中很多浏览器的私有前缀。Nettuts+有一篇Jeffrey Way写的[文章](http://code.tutsplus.com/tutorials/quick-tip-never-type-a-vendor-prefix-again--net-17589)
内容是包含参数的文件，他们可以附带大多数浏览器私有前缀Css3属性

```Less
.border-radius( @radius:3px ){
  -webkit-border-radius : @radius;
  -moz-border-radius : @radius;
  border-radius : @radius;
}
```

Sess的语法很像Less，只是只用$声明变量，然后使用前面提到的`@minxin` 和 `@include`来调用

### 选择器继承

  Less并没有提供这个功能，通过这个功能，你可以讲一个选择器附加到已经预先定义的选择器上，而无需在使用逗号
  将两者分开:

```Sass
.menu {
  border:1px solid #ddd;
}

.footer {
  @extend .menu;
}

/* 上面的写法等同于 */
.menu,.footer {
  border:1px solid #ddd;
}
```

### 嵌套规则

在Css中嵌套Class和ID是避免你的样式干扰或者被别的样式干扰的唯一方法，但这样会非常凌乱。使用一个类似于`#site-body.post.postheader h2`的选择器毫无吸引力，使用Less，你可以嵌套Id、class及标签:

```Less
#site-body{
  .post {
    .post-header {
      h2 {

      }
      a {
        &amp;:visited { ... }
        &amp;:hover { ... }
      }
    }
  }
}
```
上面的代码最终和上面的例子（长长的选择器）的效果一样，但是更容易阅读和理解

### 运算

在css中使用数字或变量来实现数学之美

```Less
@base_margin: 10px;
@double_margin: @base_margin * 2;
@full_page: 960px;
@half_page: @full_page / 2;
@quarter_page: (@full_page / 2) / 2;

```

Sass 在对数字的处理上更加专业，让已经可以换算单位了，Sass可以处理无法识别的度量单位并将其输出，这个特性明显是一个对未来的尝试 —— 证明W3C做出的一些改变

```Sass
/* Sass */
2in + 3cm + 2pc = 3.514in

/* Less */
2in + 3cm + 2pc = ERROR!
```

### Color 函数

在文章开头的时候我们提到了调色板，对此贡献最大的一部分就是颜色函数。加入你用一个标准的蓝色贯穿到你的样式中，然后你想在表单中用这个蓝色来做一个渐变的按钮。你可以打开PS或者其他编辑器来获取一个比蓝色浅的或者比较暗的HFX色值来作为渐变色，或者，你可以使用Less中的颜色函数

```Less
@blue: #369;

.submit {
  padding:5px 10px;
  border:1px solid @blue;
  background: -moz-linear-gradient(top, lighten(@blue, 10%), @blue 100%); /*Moz*/
	background: -webkit-gradient(linear, center top, center bottom, from(lighten(@blue, 10%)), color-stop(100%, @blue)); /*Webkit*/
	background: -o-linear-gradient(top, lighten(@blue, 10%) 0%, @blue 100%); /*Opera*/
	background: -ms-linear-gradient(top, lighten(@blue, 10%) 0%, @blue 100%); /*IE 10+*/
	background: linear-gradient(top, lighten(@blue, 10%) 0%, @blue 100%); /*W3C*/
  color: #fff;
  text-shadow: 0 -1px 1px rgba(0,0,0,0.4);
}
```

lighten函数明显就是用百分比值来减轻颜色，在这个例子中，他将减轻这个基础的蓝色的10%。这种方法可以让我们变阿华的元素或者其他任何元素的颜色值 —— 只是简单的改变基础颜色而已，这对于主题（模板）来说非常有用。而且，如果你使用参数功能，像上面提到的，你还可以更简单的应用到一些浏览器的私有前缀的生命中，如：
```Less
.linear-gradient(lighten(@blue),@blue,100%)
```

最终效果的确很赞

[!less]:https://github.com/georgezouq/geoblog/raw/master/img/091228yWj,jpg

Sass 貌似与更多的选项，如果你想了解更多，可以看一些[这篇很详细的介绍](http://nex-3.com/posts/89-powerful-color-manipulation-with-Sass)

### 条件语句与控制

这的确是一个很好用的功能，也是另一个Less不支持的功能，你可以使用if {} else {} 条件语句，for {} 循环。他甚至支持and、or和not，以及< , > , >= , <= 和 == 等操作符

```Sass

/* Sample Sass "if" statement */
@if lightness($color) &gt; 30% {
    backgroung-color: #000;
} @else {
    background-color: #fff;
}
/* Sample Sass "for" loop */
@for $i from 1px to 10px {
    .border-#{i} {
        border: $i solid blue;
    }
}
```

### 命名空间

命名空间可以有效的组织我们的Css，我们可以将一些公用的样式分组，然后在用的时候直接使用。例如，我们创建了一个名为default的样式分组，那我们就可以在用到的时候直接从该组中调用。

```Less
  .nav_list() {
    list-style : none;
    margin: 0;
    padding: 0;
  }
  .button(){ ... }
  .quote(){ ... }

```

然后，在我们的代码中，如果我们正好在一个nav元素中使用了ul元素，那么我们就会想到我们需要default的样式，然后我们就可以简单的调用它，他就会被直接使用

nav ul {
  #defaults &gt; .nav_list;
}

### 作用域

作用域是编程中的标配，LESS中也是。如果你在你的样式表的root级声明一个变量，他在整个文档中都是可以使用的。然而，如果你在一个选择器，比如ID或者CLASS中，重新定义了这个变量，那么，他就只能在这个选择器中可用了。

```Less
@color: #00c; /* 蓝色 */
#header {
  @color: #c00; /* 红色 */
  border: 1px solid @color; /* 红色边框 */
}

#footer {
  border: 1px solid @color; /* 蓝色边框 */
}
```

### 注释

这一部分比较基础，Less允许两种注释的写法`//注释`和 `/* 注释 */`

### 导入

标准的`@import: 'classes.less' ` 如果你想要引入其他的LESS文件，那么文件的拓展名是可选的，所以@import 'classes' ;也是可行的。如果你想要导入一些无需less处理的内容，你可以使用.css后缀

### 字符串插入

字符串也是可以用于变量中的，然后通过 `@{name}` 来调用

```Less
@base_url: 'http://www.ddd.com'
background-image: url("@{base_url}/images/background.png");

```

### 转义(Escaping)

我们通常需要引入一个css中非法或者less无法识别的值，通常一些IE的hack，要避免抛出异常并破坏Less，你需要避开他们

```less
.class{
  filter: ~"progid:DXImageTransform.Microsoft.Alpha(opacity=20)";
}
/* 实际上将会输出下面的代码 */
.class {
  filter:progid:DXImageTransform.Microft.Alpha(opacity=20);
}
```
### Javascript 赋值

在样式表中使用Javascript：

```less
@string: `'howdy'.toUpperCase()`; /* @string 变成 HOWDY */

/* 你也可以使用前面提到的值 */
@string: 'howdy';
@var: ~`'@{string}'.toUpperCase()`; /* 变为 HOWDY */

/* 获取文档的信息 */
@heigh = `document.body.clientHeight`;

```

### 输出格式

然而Less并没有输出设置，而Sass中提供四种选项：nested，compact，compressed 和 expanded






























http://blog.jobbole.com/24671/
