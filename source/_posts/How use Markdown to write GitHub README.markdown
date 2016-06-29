---
layout:     keynote
title:      "How use Markdown to write GitHub README"
subtitle:   "Keynote: How use Markdown to write GitHub README"
iframe:     
date:  2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - 工具使用
---
#Github Readme 排版解析

>README 文件的后缀名是md，md是markdown的缩写。markdown是一种编辑博客的语言，
>Github在语法标准markdown上做了修改，称之为Github Flavored Markdown GFM 不是 GFW=.=

##一、关于标题

大标题  
====  
中标题
-----

在文本下面加上等于号=，那么上方的文本就变成大标题。等于号的个数无限制，但一定要大于0。
比大标题低一级的是中标题，也就是显示出来比大标题小一点。在文本下面加上------ 那么上方的文本就变成中标题，同样的下划线的个数无限制。
除此之外，你也会发现大，中标题下面都有一条横线，没这就是= 和-的显示结果。如果你只输入=，那么他就会显示一条横线。

除此之外，标题还有等级表示法，分为六级，显示的文本大小一次减少，不同等级之间以井号#的个数来标识
```mark
#一级标题
##二级标题
###三级标题
####四级标题
#####五级标题
######六级标题
```
##显示文本

###普通文本
    直接输入文字就是普通文本，但是要注意的是换行的时候不能直接通过回车来换行，需要使用<br>(或者<br/>)。事实上，markdown支持一些html标签，当然如果完全使用html写就失去了markdown的意义
```bash
这是一行<br>
普通的文本<br/>
换行啦\<br>
```
注意第三行的<br>前添加的反斜杠\，目的就是像其他语言那样实现转译，也就是<的转译
此外，要显示一个超链接的话，就直接输入这个链接的url就好。显示出来会自动变成可链接的形式。
####显示空格的小Tip
默认的文本行首行都会被忽略的，但是如果你想用空格来排一下，那就把输入法由半角改为全角就OK啦
###单行文本
```bash
        使用两个Tab符来实现单行文本。
```
注意前面有两个Tab，在Github上单行文本显示效果如图：


###多行文本
多行文本和单行文本异曲同工，只要在每行行首加两个Tab即可

##使部分文字高亮显示
可以使用` ` 将需要高亮显示的文字包围起来即可
Thank `You` . Please `Call` Me `Coder`

##文字超链接
给一段文字添加超链接的格式： [要显示的文字](超链接的地址  "悬停显示") 比如
```mark
[我的博客](http://blog.csdn.net "blog")
```
##插入符号
###圆点符
```mark
* 这是一个圆点符号
* 这是另一个圆点符号
```

```mark
* 编程语言
    * 脚本语言
         * Python
```
第二行一个Tab，第三行两个Tab。这样用来表示层级结构就更清晰了吧，看效果：

        如果你觉得三级结构表达还不够清楚，我们可以试着换另一种方式
```mark
>数据结构
>>树
>>>二叉树
>>>>平衡二叉树
>>>>>满二叉树
```
显示效果：

当然比这个更一般的用法是这样。常常能在书籍里面看到的效果，比如引用别人的文章。直接看效果。


##插入图片
###来自网络的图片
这个方括号里的baidu并不会对图像显示造成任何改动，如果你想达到鼠标悬停显示提示信息，那么可以仿照前面介绍的文本中的方法，就是这样
```mark
![baidu](http://www.baidu.com/img/bdlogo.gif "百度logo")
```
在URL后面，加一个双引号包围的字符串，显示效果如图：


###Github仓库里的图片
有时候我们想显示一个Github仓库里面的图片，其实图片显示的格式与上面基本一致，所不同的就是括号里的url怎么写
https://github.com/georgezouq/MyImages/raw/master/Logo/foryou.gif
###给图片加上超链接
```mark
[![baidu]](http://baidu.com)
[baidu]:http://www.baidu.com/img/bdlogo.git "百度LOGO"
```
插入代码片断



##使用Github的Gist
Gist是以文件为单位的，不是以项目为单位的。而且与普通的GitHub上建的仓库不同，Gist是private的哦。普通的项目默认都是public的，要想弄成private貌似还要交钱的样子。既然是private那么用来写写日记，是极好的。
GitHub网页的顶部有：

点进去:

这就是你可以编辑的私有文件，它不仅支持Text文本，还支持各种编程语言呢！当然也包括markdown。输入文件名：

最后保存，选中 Create Secret Gist 就是私有的喽。
