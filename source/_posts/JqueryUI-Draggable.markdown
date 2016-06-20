---
layout:     keynote
title:      "JqueryUI-Draggable"
subtitle:   "Keynote: JqueryUI-Draggable"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - JQuery 插件
---
# JQuery UI 实例 - 拖动 (Draggable)

## 默认功能

在任意的DOM元素上启用draggable功能，通过鼠标点击并在视区中拖动来移动draggable对象。

```html
  ...
  <script>
    $(function(){
      $('#draggable').draggable();
    });
  </script>

  <div id="draggable" class="ui-widget-content">
    <p>请拖动我!</p>
  </div>

```

## 自动滚动

当draggable移动到视区之外时自动滚动文档，设置scroll选项为true来启动自动滚动，当滚动触发时进行微调，滚动速度是通过scrollSensitivity和scrollSpeed选项来设置的。

· main.js
```javascript
  $('#draggable').draggable({scroll:true});
  $('#draggable2').draggable({scroll:true,scrollSensitivity:100});
  $('#draggable3').draggable({scroll:true,scrollSpeed:100});

```

· index.html

```html
<div id="draggable" class="ui-widget-content">
  <p>Scroll 设置为 true，默认设置</p>
</div>

<div id="draggable2" class="ui-widget-content">
  <p>scrollSensitivity 设置为 100</p>
</div>

<div id="draggable3" class="ui-widget-content">
  <p>scrollSpeed 设置为 100</p>
</div>

<div style="height: 5000px; width: 1px;"></div>
```

## 约束运动

  通过定义draggable 区域的边界约束每个draggable的运动，设置`axis`选项来限制draggable的路径为`x轴` 或者`y轴`，或者使用`containment`选项来指定一个父级的DOM或者一个JQuery选择器，比如'document'.

· main.js
```javascript
  $('#draggable').draggable({axis:'y'});
  $('#draggable2').draggable({axis:'x'});

  $('#draggable3').draggable({containment:'#containment-wrapper',scroll:false});
  $('#draggable5').draggable({containment:'parent'});
```

· index.html

```html
<h3>沿着轴约束运动：</h3>

<div id="draggable" class="draggable ui-widget-content">
  <p>只能垂直拖拽</p>
</div>

<div id="draggable2" class="draggable ui-widget-content">
  <p>只能水平拖拽</p>
</div>

<h3>或者在另一个 DOM 元素中约束运动：</h3>
<div id="containment-wrapper">
  <div id="draggable3" class="draggable ui-widget-content">
    <p>我被约束在盒子里</p>
  </div>

  <div class="draggable ui-widget-content">
    <p id="draggable5" class="ui-widget-header">我被约束在父元素内</p>
  </div>
</div>

```
## 光标样式 (cursorAt)

  当拖拽对象时定位光标，默认情况下，光标是出现在被拖拽对象中间，使用cursorAt选项来指定相对于draggable的另一个位置（指定一个相对于top、right、bottom、left的像素值）。通过提供一个带有有效的css光标值的cursor选项来自定义光标外观，有效的css光标值包括：default、move、pointer、crosshair等

· main.js
```javascript
$(function() {
  $( "#draggable" ).draggable({ cursor: "move", cursorAt: { top: 56, left: 56 } });
  $( "#draggable2" ).draggable({ cursor: "crosshair", cursorAt: { top: -5, left: -5 } });
  $( "#draggable3" ).draggable({ cursorAt: { bottom: 0 } });
});
```
· index.html
```html

<div id="draggable" class="ui-widget-content">
  <p>只有把我拖拽了 20 像素后，拖拽才开始</p>
</div>

<div id="draggable2" class="ui-widget-content">
  <p>不管 distance 是多少，您都必须拖拽并等待 1000ms 后拖拽才开始</p>
</div>

```

## 拖动（Draggable） + 排序（Sortable）

Draggable 与 Sortable 的无缝交互。

· main.js

```javascript

$(function(){
  $('#sortable').sortable({
    revert : true
  });
  $('#draggable').draggable({
    connectToSortable:'#sortable',
    help:'clone',
    revert:'invalid'
  });
  $('ul, li').disableSelection();
});

```

· index.html
```html
<ul>
  <li id="draggable" class="ui-state-highlight">请拖拽我</li>
</ul>

<ul id="sortable">
  <li class="ui-state-default">Item 1</li>
  <li class="ui-state-default">Item 2</li>
  <li class="ui-state-default">Item 3</li>
  <li class="ui-state-default">Item 4</li>
  <li class="ui-state-default">Item 5</li>
</ul>

```
