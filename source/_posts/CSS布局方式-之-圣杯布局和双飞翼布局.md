---
title: CSS布局方式 之 圣杯布局和双飞翼布局
date: 2016-06-27 09:27:35
subtitle:   "CSS布局方式 之 圣杯布局和双飞翼布局"
iframe:     
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - HTML布局
    - 前端
---

# CSS布局方式 之 圣杯布局和双飞翼布局

## 圣杯布局

在各种布局的技术实现中，可以发现以下三种技术被经常使用：

 - 浮动 float
 - 负边距 negative margin
 - 相对定位 relative position

这时实现布局的三个最基本的原子技术，只要巧妙组合就可以拼出各种布局的实现方案。

DOM 结构：
```html

<div id="page">
  <div id="hd"></div>
  <div id="bd">
    <div class="main"></div>        
    <div class="sub"></div>        
    <div class="extra"></div>    
  </div>   
  <div id="ft"></div>
</div>

```

利用浮动元素的负边距来定位：

```css

.main{
  float:left;
  width:100%;
}
.sub{
  float:left;
  width:190px;
  margin-left:-100%;
}
.extra{
  float:left;
  width:190px;
  margin-left:-190px;
}

```

可以看出以上代码已经让 `sub` 和 `extra` 到达了正确的位置。剩下的问题是如何让 `main` 也定位到正确的位置。一个自然的想法是，给 `main` 的容器 `#bd` 添加 `padding`:

```CSS
#bd{
  padding:0 230px 0 190px;
}
```

这样能让 `main` 定位到正确的位置，但是 sub 和 extra 的位置就不对了，这时一个思考关卡，既然 sub 和 extra 的位置不对，那就想办法调整到正确的位置，这里可以使用相对定位：

```css

.sub{
  float:left;
  width:190px;
  margin-left:-100%;
  position:relative;
  left:-190px;

}

.extra{
  float:left;
  width:230px;
  margin-left:-230px;
  position:relative;
  right:-230px;
}
```

很显然，这就是经典的 `圣杯布局` 在不添加任何额外标签的假设上圣杯布局是我所有的想法中最接近完美的。

## 双飞翼布局

既然不添加额外标签时完美布局实现如此困难，那么如果允许添加一个额外变迁呢？在淘宝 UED 内部的讨论中，给 `main` 添加了一层包裹：

```html
<div id="main" class="column">
  <div id="main-content">#main</div>
</div>

```

里层 `main-content` 的作用就是将 main 定位到合适的位置，并方便设置 padding 等属性：

```html
<div id="page">
  <div id="bd">
    <div class="main"></div>
  </div>
</div>
```

CSS仅需增加一行:

```CSS
.main-wrap{  margin:0 230px 0 190px;}
```

双飞翼布局目前只用到了浮动和负边距，如果引入相对定位，还可以实现三栏布局的各种组合：

```css
.extra{
  float:left;
  width:230px;
  margin-left:-100%;
  position:relative;
  left:190px;
}
.main-wrap{
  margin-left:430px;
}
```
