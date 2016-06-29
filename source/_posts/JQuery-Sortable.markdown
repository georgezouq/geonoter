---
layout:     keynote
title:      "JQuery-Sortable"
subtitle:   "Keynote: JQuery-Sortable"
iframe:     
date:  2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - JQuery 插件
---
# JQuery UI Sortable

## 默认功能

  在任意的DOM元素上启用sortable功能，通过鼠标点击并拖拽元素到列表中的一个新的位置其他的条目会自动调整。默认情况下，sortable各个条目共享draggable属性

· main.js

```javascript

$(function() {
  $( "#sortable" ).sortable();
  $( "#sortable" ).disableSelection();
});

```

· index.html

```html
  ...

  <ul id="sortable">
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 1</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 2</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 3</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 4</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 5</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 6</li>
    <li class="ui-state-default"><span class="ui-icon ui-icon-arrowthick-2-n-s"></span>Item 7</li>
  </ul>

  ...
```

## 连接列表

  通过向connectWith 选项传递一个选择器，并把一个列表中的元素排序到两一个列表中，反之亦然。

```javascript

$(function() {
  $( "#sortable1, #sortable2" ).sortable({
    connectWith: ".connectedSortable"
  }).disableSelection();
});

```

## 显示为网格

当拖拽一个sortable条目到一个新的位置时，其他条目为该条目腾出空间。想placeholder选项传递一个class来定义可视化的空白的样式。使用布尔值的forcePlaceholderSize选项来设置占位符的尺寸
