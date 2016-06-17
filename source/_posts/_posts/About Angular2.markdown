---
layout:     keynote
title:      "Transitioning from Server to Client Side Web Development with Angular 2"
subtitle:   "Keynote: JavaScript Modularization Journey"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - Angular
---
# Angular 2 Quick Start

## ES6 工具链

Angular2是面向未来的科技，要求浏览器支持ES6+，我们现在要尝试的话，需要加一些`垫片`来抹平当前浏览器与ES6的差异:

  · angular2 polyfills - 为ES5浏览器提供ES6特性支持，比如promise等。
  · systemjs - 通用模块加载器，支持AMD、CommonJS、ES6等各种格式的JS模块加载
  · typescript - TypeScript转化器，将TypeScript代码转换为当前浏览器支持的ES5代码，在后面，systemjs被配置为只用TypeScript转码器
  · reactive exetension - Javascript版本的`反应式编程/Reactive Programming`实现库，被打包为systemjs的包格式以便systemjs动态加载
  · angular2 - Angular2框架，被打包为systemjs的包格式，以便systemjs动态加载模块
处于方便代码书写的考虑，我们将这些基本依赖打包到一个压缩文件中

    angular2.beta.stack.min.js

## 初识 Angular2

写一个Angular2的Hello World应用相当简单，分为三步:

1.引入Angular2预定义类型

```typescript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser";

```

`import` 是ES6的关键字，用来从模块中引入类型定义。在这里，我们从angular2模块库中引入了两个类型:Component类和bootstrap函数

2.实现一个Angular2组件

实现一个Angular2组件也很简单，定义一个类，然后给这个类添加`注解`：

```typescript
@Component({
    selector:"ez-app",
    template:"<h1>Hello,Angular2</h1>"
  })

class EzApp{}
```

`class` 也是ES6的关键词，用来定义一个类。`@Component`是给类`EzApp`附加的元信息，被称为`注解/Annotation`。

@Component 最重要的作用是:

  1.通过`selector`属性（值为CSS选择器），告诉Angular2框架这个组件渲染到哪个DOM对象上。
  2.通过`template`属性，告诉Angular2框架，使用什么模板进行渲染。

在alpha版本中的注解`@View`依然存在并可以使用，比如:
```typescript
@Component({selector:"ez-app"})
@View({template:"<h1>Hello,Angular2</h1>"})

class EzApp{}
```
## 注解/Annotation

你一定好奇`@Component`和`@View`到底是怎么回事？看起来像其他语言(比如python)的`装饰器`，是这样吗?ES6中其实并没有装饰器，这其实是利用了`转码器`提供的一个特性:`注解`。给一个类添加注解等同于设置这个类的`annotations`属性:
```typescript
//注解写法
@Component({selector:"ez-app"})
class EzApp{...}
```

```typescript
class EzApp{...}

EzApp.annotations = {new Component({selector:"ez-app"})}

```

很显然，`注解`可以看做转码器(typescript/traceur/babel)层面的`语法糖`，但是与python的`装饰器`不同,`注解`在编译转码时仅仅被放在类对象的`annotations`属性里，编译器并不进行解释展开 —— 这个解释的工作是Angular2框架完成的:

[baidu]:https://github.com/georgezouq/geoblog/raw/master/img/annotations.png

据称，注解/Annotation的功能就是Angular2团队向编译器开发团队提出的，因此这通常不是 编译器的默认选项，因此你 看到， 我们配置systemjs在使用TypeScript转码时打开注解：

```typescript
System.config({
    transpiler:'typescript',
    typescriptOptions:{ emitDecoratorMetadata:true}
  });
```

## 组件开发 - 声明元数据

### Selector 选择符

使用 `Component` 注解的 `selector` 属性来告诉 Angular2 框架，当编译、链接模板时，遇到对应元素就实例化一个组件对象。

#### 标签名选择符

```typescript
@Component({selector:"ez-one",template:"TAGNAME-SELECTOR"})
class EzOne{}
```

将匹配：`<ez-one>...</ez-one>`

#### CSS 类选择符

```typescript
@Component({selector:".ez-two",template:"CSSCLASS-SELECTOR"})
class EzTwo{}
```

将匹配： `<any class="ez-two">...</any>`

#### 属性选择符

```typescript
@Component({selector:"[ez-three]",template:"ATTR-SELECTOR"})
class EzThree{}
```

将匹配：`<any ez-three>...</any>`

#### 属性值选择符

```typescript
@Component({selector:"[ez-four=123]",template:"ATTRVAL-SELECTOR"})
class EzFour{}
```

将匹配：`<any ez-four='123'>...</any>`

### template/templateUrl - 声明模板

组件的Component注解最重要的属性两个属性之一就是`template` - 模板。 Angular2的模板是兼容HTML语法的，这意味着你可以使用任何标准的HTML标签编写 组件模板。

所以，在最简单的情况下，一个Angular2组件的模板由标准的HTML元素构成，看起来就是 一段HTML码流。Angular2将原封不同地渲染这段模板：

有两种方法为组件指定渲染模板：

#### 1. 内联模板

可以使用组件的View注解中的template属性直接指定内联模板：

```typescript
@Component({
    template : `<h1>hello</h1>
                <div>...</div>`
})
```

#### 2. 外部模板

也可以将模板写入一个单独的文件：

```html
<!--ezcomp-tpl.html-->
<h1>hello</h1>
<div>...</div>
```

然后在定义组件时，使用templateUrl引用外部模板：

```typescript
@Component({
    templateUrl : "ezcomp-tpl.html"
})
```

### style/styleUrls - 设置样式

组件既然处于UI层，就应当好看些，好看是构造良好用户体验的一部分。Angular2的 组件模板基于HTML，那么显然，我们需要通过样式表/CSS来调整组件的外观。

和模板类似，我们有两种方法为组件设置CSS样式：

#### 1. 内联样式

可以使用组件Component注解的styles属性来设置内联样式：

```typescript
@Component({
    styles:[`
        h1{background:#4dba6c;color:#fff}     
    `]
})

```

#### 2.外部样式

也可以把样式定义在单独的样式文件中：

```typescript
/*ez-greeting.css*/
h1{background:#4dba6c;color:#fff}     

```

然后使用 `Component注解` 的 `styleUrls` 属性来引入外部样式:

```typescript
@Component({
    styleUrls:["ez-greeting.css"]
  })
```

### proerties - 声明属性

属性是组件暴露给外部世界调用的接口，调用者通过设置不同的属性值来定制组件的行为与外观。

在 Angular2 中为组件增加 `属性` 只需要在 Component 注解的 `properties` 属性中声明组件的 `成员变量` 就可以了:

```typescript
@Component({
    properties:["name","country"]
  })
```

上面的代码将组件的成员变量 `name` 和 `country` 暴露为同名属性，这意味着在 `EzApp` 的模板中，可以直接使用中括号来设置 `EzCard` 对象的属性:

```typescript
@Component({
    directives:[EzCard],
    template:'<ez-card [name]="'leof'" [country]="'中国'"></ez-card>'
  })

```

### events - 声明事件

与属性相反，事件从组件的内部流出，用来通知外部世界发生了一些事情

在 Angular2 中为组件增加事件接口也非常简单：定义一个 `事件源/EventEmitter` ，然后通过 `Component注解` 的 `events` 接口暴露出来

```typescript
//EzCard
@Component({
    events:["change"]
  })
class EzCard{
  constructor(){
    this.change = new EventEmitter();
  }
}
```

上面的代码将组件 `EzCard` 的事件源 `change` 暴露为 `同名事件`，这意味着调用者 `EzApp` 组件的模板中，可以直接使用 `小括号` 语法挂接事件监听函数:

```typescript
@Componen({
    template:"<ez-card (change)="onChange()"></ez-card>"
  })

```

每次 `EzCard` 触发 `change` 事件时，`EzApp` 的 `onChange()` 方法都将被调用.

### directives - 引用指令

在Angular2中，一个组件的模板内除了可以使用标准的HTML元素，也可以使用自定义的组件！

这是相当重要的特性，意味着Angular2将无偏差地对待标准的HTML元素和你自己定义的组件。这样， 你可以建立自己的领域建模语言了，这使得渲染模板和视图模型的对齐更加容易，也使得模板的语义性 更强：

声明要在模板中使用的组件

不过，在使用自定义组件之前，必需在组件的Component注解中通过directives属性声明这个组件：

```typescript
@Component({
    directives : [EzComp],
    template : "<ez-comp></ez-comp>"
})
```

你应该注意到了，directives属性的值是一个数组，这意味着，你需要在这里声明所有你需要在模板 中使用的自定义指令。

## 组件开发 - 模板基本语法

### {{model}} - 文本插值

在模板中使用可以 `{{表达式}}` 的方式绑定组件模型，当表达式变化时，Angular2 将会自动更新对应的DOM对象:

```typescript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";

@Component({
    selector:"ez-app",
    template:`
      <div>
        <h1>{{title}}</h1>
        <div>
          <span>{{data}}</span>
          来源<span>{{source}}</span>
        </div>
        <p>{{content}}</p>
      </div>
    `
  })
class EzApp{
    constructor(){
      this.title = "证监会：对恶意做空是有监测的";
  this.date = "2015年07月11日 15:32:35";
  this.source = "北京晚报";
  this.content = `
证监会新闻发言人邓舸昨天表示，近期，证监会要求所有上市公司制定维护股价稳定的方案，举措包括大股东增持、董监高增持、公司回购、员工持股计划、股权激励等，相关方案应尽快公布，并通过交易所平台向投资者介绍生产经营管理情况、加强投资者关系管理、维护股价稳定、做好投资者沟通工作。他介绍，该措施得到了上市公司大股东的积极响应，包括北京创业板董事长俱乐部、创业板首批28家公司实际控制人、浙江24家公司董事长等多个上市公司联盟以及大连、青岛、湖南等多地上市公司集体发声，宣布通过积极增持、回购、暂不减持等方式稳定公司股价。截至9日，两市已有655家公司公布股份增持、回购计划，积极维护公司股价稳定。
  `;
    }
}

bootstrap(EzApp);
```

### [property] - 属性绑定

在模板中，也可以使用一对 `中括号` 将HTML元素或组件的 `属性` 绑定到组件模型的某个 `表达式` ，当表达式的值变化时，对应的DOM对象也会进行更新。

```typescript

import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform.brower";

@Component({
      selector:"ez-app",
      template:`<h1 [style.color]="color"><Hello,Angular2</h1>`
  })
class EzApp{
    constructor(){
        this.color = "red";
    }
}

```

等价的，你也可以使用 `bind-` 前缀进行属性绑定:

```typescript
@Component({template:`<h1 bind-textContent="title"></div>`})
```

很容易理解，通过属性，应用相关的数据 `流入` 组件，并影响组件的外观与行为。需要注意的是，属性的值总是被当做 `调用者` 模型中的 `表达式` 进行绑定，当表达式变化时，被调用的组件自动得到更新。如果希望将属性绑定到一个常量字符串，别忘了给字符串加引号或者去掉中括号

```typescript

//错误，Angular2将找不到表达式 Hello,Angular2
@Component({template:`<h1 [textContent]="Hello,Angualr2"></h1>`})
//正确，Angular2识别出常量字符串表达式 'Hello,Angular2'
@Component({template:`<h1 [textContent]="'Hello,Angular2'"></h1>`})
//正确，Angular2识别出常量字符串作为属性textContent的值
@Component({template:`<h1 textContent="Hello,Angular2"></h1>`})
```

### (event) - 事件绑定

在模板中为元素添加事件监听很简单，使用一对 `小括号` 包裹 `事件` 名称，并绑定到表达式即可:

```typescript

import {Component,View} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";

@Component({selector:"ez-app"})
@View({
      template:`
        <h1>Your turn!<b>{{sb}}</b></h1>
        <button (click)="roulette()" >ROULETTE</button>
      `
  })
class EzApp{
    constructor(){
        this.name = ["Jason","Mary","Linda","Lincoln"];
        this.roulette();
    }
    //轮盘赌
    roulette(){
        var idx = parseInt(Math.random() * this.name.length);
        this.sb = this.names[idx];
    }
}
```

### var - 局部变量

有时模板中的不同元素之间可能需要相互调用，Angular2 提供一种简单的语法将元素映射为 `局部变量` : 添加一个以 `#` 或 `var-` 开始的属性，后续部门表示变量名，这个变量对应元素的实例。

在下面代码示例中，我们为元素 `h1`定义了一个局部变量 `v_h1`，这个变量指向该元素对应的DOM对象，你可以在模板中的其他地方调用其方法和属性：

```typescript

@Component({
    template:`
      <h1 #v_h1>hello</h1>
      <button (click) = "v_h1.textContent" = 'HELLO'">
      test</button>
    `
  })

```

如果在一个组件元素上定义局部变量，那么其对应的对象为组件的实例:

```typescript

@Component({
    directives:[EzCalc],
    template:"<ez-calc #c></ez-calc>"
  })

在上面的实例中，模板内局部变量 `c` 指向EzCalc的实例。

```

一个栗子

```typescript

import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser";

@Component({
    selector: "ez-app",
    template: `
        <h1>
            <button>变色</button>
            I choose
            <b #v_who>WHO?</b>
        </h1>
        <button (click)="v_who.textContent = 'Jason'">Jason</button>
        <button (click)="v_who.textContent = 'Mary'">Mary</button>
        <button (click)="v_who.textContent = 'Linda'">Linda</button>
        <button (click)="v_who.textContent = 'Lincoln'">Lincoln</button>
        <button (click)="v_who.textContent = 'Jimmy'">Jimmy</button>
        <button (click)="v_who.textContent = 'Albert'">Albert</button>
      `
  })
class EzApp{}

bootstrap(EzApp);

```

### NgStyle - 内联样式

我们可以使用样式绑定的方法设置单一样式。但如果要同时设置多个样式值，可以使用 `NgStyle` 指令，将内联样式绑定到组件的属性上。

`NgStyle` 指令的选择符是 `[ngStyle]` ，下面的示例将div元素的样式绑定到组件的 `style` 属性:

```typescript
<div [ngStyle]="styles">...</div>
```

`style`属性应当一个JSON对象，其键为样式名，值为样式值:

```typescript
//EzApp class
this.style={
    "color" : "red",
    "font-style" : "italic",
    "background-color" : "black"
}
```

样式名可以使用HTML语法中的属性名写上（同上），也可以使用JS语法中的 `驼峰/camelCase` 书写方法:

```typescript
//EzApp class
this.styles = {
    color: "red"
    fontStyle : "italic",
    backgroundColor : "black"
}
```

通常来讲，在JavaScript中设置DOM元素的CSS样式，都会使用驼峰名称，因此，建议 采用后一种写法。

### NgClass - 样式类

在WebApp中，我们通常采用动态添加或删除 `样式类` 的方法，来改变DOM元素的外观表现。

当然我们可以使用HTML元素的 `class` 属性绑定到组件实例的属性上，然后通过改变实例的属性，实现动态修改HTML元素的 `样式类`
:

```typescript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser"

@Component({
    selector:"ez-app",
    template:`
      <h1 [ngClass]="cns">EZ-APP</h1>
    `,
    styles:[`
      .colorful{color:red;}
      .italic{font-style:italic}
      .bold{font-weight:bold}
    `]
  })
class EzApp{
    constructor(){
        this.cns = {
            colorful:true,
            italic:true,
            bold:true
        }
    }
}

bootstrap(EzApp);
```

不过，如果一次要删除或添加多个样式类，Angular2 内置的 `NgClass` 指令会更简单。`NgClass` 指令的选择符是 `ngClass`，下面的示例将div元素的class属性绑定到组件的cns属性:

```typescript
<div [ngClass]="cns">...</div>
```

`cns`属性是一个JSON对象，每个键代表 `样式类名`，对应的值为true是表示HTML元素添加该样式类，为false时表示删除该样式类，如果cns的值如下:

```typescript
//EzApp class
this.cns = {
    colorful:true,
    italic:false,
    bold:true
}

```

那么示例模板渲染后的结果是:

```typescript
<div class="colorful bold">...</div>
```
### NgIf - 条件逻辑

我们需要模板的一部分在满足一定条件时才显示，比如下面的`EzReader`组件，对于试用用户，他将在正文之上额外显示一个广告:

```typescript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser";

@Component({
    selector:"ez-reader",
    properties:["trial"],
    template:`
      <img [src]="banner" *ngIf="trial == true" class="banner">

      <pre>{{content}}</pre>
    `
  })
class EzReader{
      constructor(){
          var self = this;
          this._trial = true;
          this.banner = "img/banner.jpg";
          this.content = `“没关系，我已经没有放射性了。”史强对坐在旁边的汪森说，“这两天，我让人家像洗面口袋似的翻出来洗了个遍。`;
      }
  }

@Component({
    selector:"ez-app",
    directives:[EzReader],
    template:`
      <ez-reader [trial]="true"></ez-reader>
    `
  })
class EzApp{}

bootstrap(EzApp);

```

这是指令 `NgIf` 发挥作用的场景，他评估属性 `ngIf` 的值是否为真，来决定是否渲染 `template` 元素的内容:

```typescript
@Component({
    template:`
      <!-- 根据变量trial的值决定是否显示广告图片 -->
      <template [ngIf]="trial==true">
        <img src="ad.jpg">
      </template>
    `
  });
```

Angular2 同时提供了两种语法糖，让`NgIf`写起来更简单，下面的两种书写方式和上面的正式语法是等效的:

```typescript
//使用template attribute
<img src="ad.jpg" template="ngIf tiral==true">
//使用*前缀
<img src="ad.jpg" *ngIf="trial==true">

```

看起来，显然 `*ngIf` 的书写方式更加有人情味，不过无论采用哪种书写方式，都将转换成上面的正式写法在进行编辑。

### NgSwitch - 分支逻辑

如果组件的模板需要根据某个表达式的不同取值真是不同的片段，可以使用 `NgSwitch` 系列指令来动态切分模板。比如下面示例广告组件`EzPromotion`，需要根据来访者性别的不同推送不同的广告：

```typescript
import {Component,} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";

@Component({
    selector:"ez-promotion",
    properties:["gender"],
    template:`
      <div [ngSwitch]="gender">
          <template ngSwitchWhen="Male">
              <img src="img/male-ad.jpg" class="banner">
          </template>
          <template ngSwitchWhen="Female">
            <img src="img/female-ad.png" class="banner">
          </template>
          <template ngSwitchDefault>
            <h1>Learn Something, NOW!</h1>
          </template>      
      </div>
    `
  })
```

#### NgSwitch

`NgSwitch` 指令可以应用在任何HTML元素上，它评估元素的ngSwitch 属性值，并根据这个值决定应用哪些 template 的内容（可以同时显示多个分支）

#### ngSwitchWhen

`NgSwitchWhen`指令必须应用在NgSwitch指令的字template元素上，它指定一个表达式，如果表达式结果为true，那么显示这个template的内容

#### NgSwitchDefault

NgSwitchDefault指令必须应用在NgSwitch指令的子template元素上，当没有NgSwitchWhen指令匹配 时，NgSwitch将显示这个template的内容:

```typescript
<ANY [ngSwitch]="...">
    <template ngSwitchDefault>...</template>
</ANY>
```
### NgFor - 循环逻辑

如果希望利用一组可遍历的数据动态构造模板，那么应当使用NgFor指令

```typescript

import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser";

@Component({
    selector:"ez-star",
    template:`
      <div>
        <h2>{{actor}} - Films</h2>
        <ul>
            <li *ngFor="#film of films">{{film}}</li>
        </ul>
      </div>
    `
  })

```

### 迭代

NgFor指令应用在template元素上，对`ngForOf`属性指定的数据集中的每一项 实例化一个template的内容：

```typescript
<template ngFor [ngForOf]="items" >
    <li>----------</li>
</template>
```

如果items数据集有3条记录，那么会生成3个li对象，就像这样：

```html
<li>----------</li>
<li>----------</li>
<li>----------</li>
```

不过这没多大用。

### 使用数据项

好在我们还可以为数据集的每一项声明一个局部变量，以便在模板内引用：

```typescript
<template ngFor [ngForOf]="items" #item>
    <li>{{item}}</li>
</template>
```

假如items数据集是一个数组：`["China","India","Russia"]`，那么 现在生成的结果就有点用了：

```html
<li>China</li>
<li>India</li>
<li>Russia</li>
```

#### 使用数据项索引

有时还需要数据项在数据集中的索引，我们也可以为数据集的每一项的索引声明一个 局部变量，以便在模板内引用：

```typescript
<template ngFor [ngForOf]="items" #item #i="index">
    <li>[{{i+1}}] {{item}}</li>
</template>
```

现在生成的结果更规矩了：

```html
<li>[1] China</li>
<li>[2] India</li>
<li>[3] Russia</li>
```

#### 语法糖

与NgIf类似，Angular2也为NgFor提供了两种语法糖：

```typescript
//使用template attribute
<ANY template="ngFor #item of items;#i=index">...</ANY>
//使用*前缀
<ANY *ngFor="#item of items;#i=index">...</ANY>
毫无疑问，应当尽量使用*ngFor的简便写法，这可以提高模板的可读性。
```

## 组件开发 - 在模板中使用管道

### 管道

从视图模型中直接获得的数据，不一定完全适合于人们查看，比如当我们需要显示一个班级所以学生的平均分的时候:

```html
<div>average:{{avgScore() }}</div>
```

运行起来，一切都很正常，只是这个数开起来有些小刺眼 `90.9999999999` - 毫无必要的小数。

这时就 Angular2 的管道就能派上用场了：在模板中对输入数据进行变换，并输出变换后的结果。在你模板中，我们使用 `|` 来调用一个管道操作，并使用 `:` 来向管道传入参数。

```html
{{ data | <pipename>:<arg1>:<arg2> }}
```

我们使用预制的 `DecimalPipe` 对平均数进行处理，保留两位小数：

```html
<div>average : {{ avgScore() | number:'2.2-2' }}</div>
```

`number` 对应着 `DecimalPipe` 对象， `'2.2-2'` 表示调用这个管道时传入的参数为 `'2.2-2'`，对于DecimalPipe，这三个2分别表示最小整数位、最小小数位和最多小数位。

现在，平均分显示为:`90.06`

### 预置参数

除了 `DecimalPipe` ，Angular2 还预置了以下的管道来对常见数据类型进行转换：

#### DatePipe - 对日期/时间数据进行格式转换

在模板中使用 `date` 来引入 `DatePipe` 。参数用来指定格式：

```html
<!-- day:new Date('2014-01-01') 结果：'01/01/14' -->
{{ day | date:'yyMMdd' }}

```

#### JsonPipe - 将 JSON 对象转化为字符串

在模板中使用 `json` 来引用 `JsonPipe` ，其实现基于 `JSON.stringify()`。这个管道主要用于调试。

```html
<!-- 结果: '{x:1,y:2}' -->
{{ {x:1,y:2} | json }}
```

#### PercentPipe - 将数值转化为百分比

在模板中使用 `percent` 来引用 `PercentPipe`。参数格式同 `DecimalPipe`

```html
<!-- 结果:123.456% -->
{{ 1.23456 | percent:'1.2 - 3' }}
```

#### SlicePipe - 提取输入字符串中的指定切片

在模板中使用 `slice` 来引用 SlicePipe。第一个参数指定切片的起始索引，第二个参数指定切片的终止索引的下一个。

```html
<!-- 结果 '123' -->
{{ '01234567890 | slice:1:4' }}

```

#### UpperCasePipe - 将输入字符串变换为大写

```html
{{ "this is a demo" | uppercase }}
```

#### LowerCasePipe - 将输入字符串转换为小写

```html
{{ "WHAT A WONDERFUL WORLD" | lowercase }}
```

### 管道级联

```html
<!-- 结果:'WHAT' -->
{{ "what a wonderful day" | slice:0.4 | uppercase }}

```

### 定制管道

Angular2 预置的管道并不是特别丰富，不过好在很容易自己实现一个管道。

1.声明元数据

和一个组件类似，一个管道也是具有特定元数据的类：

```typescript
@Pipe({name:'ezp'})
class EzPipe{...}

```

`Pipe` 注解为被装饰的类附加了管道元数据，其最重要的属性是 `name`，也就是我们在模板中调用这个管道时使用的名称。上面定义的管道，我们可以在模板中这样使用：{{ data | ezp }}

#### 2.实现 transform 方法

管道必须实现一个预定的方法 `transform(input,args)`这个方法的 `input` 参数代表输入数据，args参数代表输入参数，返回值江北作为管道的输出。

```typescript
import {Component,Pipe} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";

@Pipe({name:"title"})
class TitlePipe{
  transform(input,args){
    return input.split(" ")
        .map(word => word[0].toUpperCase() + word.slice(1)).join(" ");

  }
}

@Component({
      selector:"ez-app",
      template:`
        <h1>管道/Pipe</h1>
        <p>{{text | title}}</p>
      `,
      pipes:[TitlePipe]
  })
  class EzApp{
      constructor(){
        this.text = "what a wonderful world !";
      }
  }

```

下面的实例简单地将输入数据与错有参数拼接在一起：

```typescript
@Pipe({name:'ezp'})
class EzPipe{
  transform(input,args){
    return input + " " + args.join(" ");
  }
}
```

#### 3.使用自定义管道

在组件的模板中使用自定义管道之前，需要预先声明一下，以便 Angular2 注入，使用 `Component` 的注解的 `pipes` 属性进行声明: `pipes:[EzPipe]`。

现在我们就可以使用这个自定义管道了:

```typescript
<!-- 结果:"call join mary linda" -->
{{ "call" | ezp:'john':'mary':'linda' }}
```

### 有状态管道

我们之前了解的管道，包括 Angular2 预置的管道以及我们自己实现的管道，都有一个特点，就是输出仅仅依赖于输入，这样的管道在 Angular2 中被称为 `无状态管道/Stateless Pipe`。

对于无状态管道，当输入没有变化时，Angular2 框架不会重新计算管道的输出。但也许有些时候，我们希望及时输入没有变化，也持续的检测管道的输出。例如，我们设计了一个倒计时管道，向他输入一个秒数，会自动多次输出直至0秒：

```typescript
{{ 10 | countdown }}
```

实现 countdown 的逻辑很简单，记录起始值，然后开一个1秒1次的计时器，逐次减至0秒即可。

关键在于，在默认情况下，Angular2 框架仅仅执行一次管道的 `transform()` 方法，我们需要使用 `Pipe` 注解的 `pure` 属性为`false`，要求 Angular2 框架在每个变化检查周期都执行管道的 `transform()` 方法：

```typescript
@Pipe({
    name:"countdown",
    pure:false
  })
class EzCountdown{...}
```

很显然，`countdown` 管道的输出不仅依赖于输入，还依赖于其内部的运行状态。因此，这样的管道在 Angular2 中被称为 `有状态管道/Stateful Pipe`。

### AsyncPipe

AsyncPipe 是 Angular2 框架预置的一个有状态管道，它的输入是一个异步对象：`Promise`对象，`Observable`对象，`EventEmitter`对象。
每当异步对象产生新的值，`AsyncPipe` 会返回这个值，因此，AsyncPipe 需要 Angular2 框架 持续进行变化检测，它的 `Pipe` 注解的 `pure` 属性值为 false

## NgForm - 表单指令

`NgForm` 指令为 `表单元素/form` 建立一个控件组对象，作为控件的容器；而 `NgControlName` 指令则为 `宿主input元素` 建立一个控件对象，并将该控件加入到 `NgForm` 指令建立的控件组中：

#### 局部变量

通过使用 `#` 符号，我们创建了一个引用控件组对象（注意，不是form元素！）的局部变量 `f`。这个变量最大的作用是：它的value属性是一个简单的JSON对象，键对应于input元素的 `ng-control` 属性，值对应于input元素的值:

### NgForm - 表单指令

NgForm 指令为表单元素/form 建立一个组件对象，作为空间的容器；而NgControlName 指令则为 `宿主input元素` 建立一个控件对象，并将该控件加入到 `NgForm` 指令建立的控件组中:

```typescript
@Component({
    selector:"ez-app",
    directives:[FORM_DIRECTIVES,CORE_DIRECTIVES],
    template:`
      <form #f="ngForm" (submit)="search(f.value)">
        <select>
          <option value="web">网页</option>
          <option value="news">新闻</option>
          <option value="image">图片</option>
        </select>
        <input type="text" ngControl="kw">
        <button type="submit">搜索</button>
      </form>
      <!-- 给个简单的反馈 -->
      <h1 *ngIf="kw != ''">正在搜索{{kw}}...</h1>
    `,
    styles:[`form{background:#90a4ae;padding:5px;}`]
  })
class EzApp{
  constructor(){
    this.kw = "";
  }
  search(val){
    this.kw = val.kw;
    //假装在搜索，2秒钟后返回
    setTimeout(() => this.kw = "",2000);
  }
}
bootstrap(EzApp);
```

### NgControlName - 命名控件指令

如上所述，`NgControlName` 指令必须作为 `NgForm` 或 `NgFormModel` 的后代使用，因为这个指令需要将创建的控件对象添加到祖先(NgForm 或 NgFormModel)所创建的控件组中。

`NgControlName` 指令为宿主的DOM对象创建一个控件对象，并将这个对象以`ngControl`属性指定的名称绑定在DOM对象上:

```html
<form #f="ngForm">
    <input type="text" ngControl="user">
    <input type="text"ngControl="pass" [(ngModel)]="data.pass">
</form>
```
ngModel 即是 `NgControlName` 指令的属性，也是他的事件，所以下面的两种写法是等价的:

```html
<input type="text" ngControl="user" [(ngModel)]="data.user">
<input type="text" ngControl="user" [(ngModel)]="data.user" (ngModel)="data.user">
```

### NgFormControl - 绑定已有控件对象

与 `NgControlName` 指令不同,`NgFormControl` 将已有的 `控件/Control` 对象绑定到DOM元素上。当需要对输入的值进行初始化时，可以使用`NgFormControl`指令将DOM元素绑定到组件 `EzComp` 的成员，变量`movie`上，我们需要在构造函数中先创建这个`Control`对象:

```typescript
@View({
    //将输入元素绑定到已经创建的控件对象上
    template:`<input type="text" [NgFormControl]="movie">`
  })
class EzComp{
  constructor(){
    //创建控件对象
    this.movie = new Control("Matrix II - Reload");
  }
}
```

`控件/Control` 是 Angular2 中对表单输入元素的抽象，我们使用其`value`属性，就可以获得对应的输入元素的值

与`NgControlName`指令的另一个区别是，`NgFormControl`不需要`NgForm`或`NgFormModel`的祖先

```typescript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";
import {Control,FORM_DIRECTIVES} from "angular2/common";

@Component({
    selector:"ez-app",
    directives:[FORM_DIRECTIVES],
    template:`
      <div>
          <ul>
            <!-- 将输入元素绑定到已经创建的控件对象 -->
            <li>姓名:<input type="text" [ngFormControl]="name"></li>
            <li>地址:<input type="text" [ngFormControl]="address"></li>
            <li>电话:<input type="text" [ngFormControl]="telephone"></li>
          </ul>
      </div>
      <!-- 调试:转储模型信息 -->
      <pre>{{dump()}}</pre>
    `,
    style:[`
      form{background:#e1f5fe;}
      ul{list-style:none;padding:10px;margin:0px}
      li{line-height:30px;}
    `]
  })
class EzApp{
  constructor(){
    this.name = new Control("Jason");
    this.address = new Control("London U.K.");
    this.telephone = new Control("114");
  }
  dump(){
    //读取控件对象的值
    var val = {
      name : this.name.value,
      adddress : this.address.value,
      telephone : this.telephone.value
    }
    return JSON.stringify(val,null,"\t");
  }
}
bootstrap(EzApp);
```

### NgFormModel - 邦定已有组件组

`NgFormModel` 指令类似于 `NgControlGroup` 指令，都是为控件提供容器。但区别在于`NgFormModel` 指令将已有的控件组绑定到DOM对象上:

```typescript
@View({
    template:`
      <!-- 绑定控件组与控件对象 -->
      <div [ngFormModel]="controls">
        <input type="text" ngControl="name">
        <input type="text" ngControl="age">
      </div>
    `
  })
class EzComp{
  constructor(){
    //创建组件组及控件对象
    this.controls = new ControlGroup({
        name:new Control("Jason"),
        age:new Control("45")
      });
  }
}
```

`NgFormModel` 指令可以包含 `NgControlGroup` 指令，以便将不同性质的输入分组

## Directive - 定义指令元数据

`指令` 是 Angular 对HTML进行拓展的基本手段，与Angular1x不同，在 Angular2 中，指令被明确地划分为三种类型：

- 组件 - 组件其实就是带有模板的指令
- 属性指令 - 属性指令用来改变所在元素的外或行为，例如 `NgClass` 和 `NgStyle` 指令
- 结构指令 - 结构指令用来向DOM中添加或删除元素，例如`NgIf`和`NgFor`指令.

组件使用 `Component` 注解来装饰组件类，而属性指令和结构指令则使用 `Directive` 注解来装饰指令类。

#### 1.Directive 注解 - 声明指令元数据

Directive 注解最重要的属性就是 `selector` ，它指定了触发Angular2框架生成指令实例的CSS选择器。

```typescript
@Directive({selector:"[ez-h]"})
class EzHilight{...}
```

模板中具有 `en-h` 的元素，Angular2 框架都将为其生成一个 `EzDirective` 类实例。例如，下面的模板中框架将为Div元素实例化EzHilight:
```html
<div ez-h></div>
```

2.ElementRef - 获取指令所在DOM对象

很显然，我们需要在EzDirective类的实现中进行DOM操作，这需要告诉Angular2框架向我们注入ElementRef对象，其nativeElement属性就是对应的DOM对象:

```Javascript
class EzHilight{
  constructor(@Inject(ElementRef) er){
    var el = er.nativeElement;
    el.style.background="yellow";
  }
}

@Component({
  selector:"ez-app",
  directives:[EzHilight],
  template:`
    <p>Now it is a <i ez-h>good</i> time to learn some <i ez-h>wonderful</i> things!</p>
  `
})
class EzApp{}

bootstrap(EzApp);
```

### inputs - 声明属性值映射

很显然，在定义组件模板时，我们通常会给属性设定一个值，比如我们希望一下模板片段中，将指令所在的DOM对象的背景设置为指定颜色:

```html
<div [ez-h]="'black'">...</div>
```

通过使用`Directive`注解的`inputs`属性，我们可以将DOM对象的属性映射到指令对象的属性，例如对于下面定义的指令:

```typescript
@Directive({
    selector:["ez-h"],
    inputs:["bgColor:ez-h"]
  })
class EzHilight{...}
```
当在模板中使用这个指令时，EzHilight对象的`bgColor`属性自动绑定到模板中`div`元素的`ez-h`属性值。对于指令而言，这时一个输入，每当ez-h发生变化时，Angular2都会自动设置`EzHilight` 的 `bgColor` 属性。

我们可以使用ES6中的setter，在EzHilight中捕捉每个变化的时刻:

```typescript
class EzHilight{
  set bgColor(v){
    this.el.style.background = v;
  }
}
```

### host - 声明事件监听

如果指令的实现需要监听所在DOM元素的时间，可以使用`Directive`注解的`host`属性。

```typescript
@Directive({
    selector:"[ez-h]",
    host:{
      '(click)':'onMyClick()',
      '(mouseover)':'onMyMouseOver()'
    }
  })
class EzHilight{...}
```

你看到，host 属性的值应当是一个JSON对象，其键为一对小括号包裹的事件名称，书写方法与在模板中一致；值为时间处理表达式，通常是对指令类中方法的调用。例如:

```typescript
class EzHilight{
  onMyClick(){...}
  onMyMouseOver(){...}
}
```

### renderer - 使用渲染器

在 EzHilight 指令的实现中，我们是直接通过 `ElementRef` 对象的 `nativeElement` 属性来直接操作浏览器DOM的，不过 Angular2 其实不希望我们这么做，因为这样使得我们的代码与浏览器纠缠不清，有违 Angular2 的跨平台本意。

在 Angular2 中，引入了 `渲染器/renderer` 的概念，它定义了一组规范的接口 `Renderer`，对于不同的平台，有不同的实现。比如浏览器对应的 Renderer 实现是 `DomRenderer`。

在指令的构造函数中，我们可以要求 Angular2 框架注入当前使用的渲染器对象:

```typescript
class EzHilight{
    constructor(@Inject(ElementRef) el,@Inject(Renderer) renderer){
        this.el = el;
        this.renderer = renderer;
    }
}
```

Angular2 希望我们使用 `Renderer` 来代替直接的DOM操作，这将保证我们的代码会的跨平台特性，现在我们使用 Renderer 的 `setElementStyle()` 方法来修改样式:

```javascript
import {Component,Directive,ElementRef,Renderer,Inject} from "angular/core";
import {bootstrap} from "angular2/platform/browser";

@Directive({
      selector:"[ez-h]",
      inputs:["bgColor:ez-h"],
      host:{
        "(click)":"onclick($event)"
      }
  })
class EzHilight{
    constructor(@Inject(ElementRef)er,@Inject(Renderer) renderer){
        this.renderer = renderer;
        this.er = er;
        renderer.setElementStyle(er,"cursor","pointer");
        this.flag = true;
    }
    set bgColor(v){
        this._bgColor = v;
        this.renderer.setElementStyle(this.er,"background",v);
    }
    get bgColor(){
        return this._bgColor;
    }
    onClick($event){
        if(this.flag)
            this.renderer.setElementStyle(this.er,"background","transparent");
        else
            this.renderer.setElementStyle(this.er,"background",this.bgColor);
        this.flag = !this.flag;  
    }  
}
//组件定义
@Component({
    selector:"ez-app",
    directives:[EzHilight],
    template:`
      <p>Now it is a <i [ez-h]="'red'">good</i> time to learn some <1 [ez-h]="'green'">wonderful</i> things!</p>
    `
  })
class EzApp{}

bootstrap(EzApp);
```

## 服务 - 封装可复用代码

在 Angular2 中，`服务` 用来封装可复用的功能代码，比如 `http` 服务，封装了 `ajax` 请求的细节，在不同的组件中，我们只需要调用 `http` 服务的 API 接口就可以给组件增加 `ajax` 请求的功能。

Angular2 中 实现一个服务非常简单直接：定义一个类，然后他就是服务了：

```javascript
class EzAlgo{
    add(a,b){return a + b;}
    sub(a,b){return a + b;}
}
```
上面的代码定义了一个相当弱智的算法服务 `EzAlgo`，它有两个API - add() 用来计算两个数相加的`和`，`sub()` 用来计算两个数的 `差`

```javascript
import {Component} from "angular2/core";
import {bootstrap} from "angular2/platform/brower";

//定义一个简单的算法服务
class EzAlgo{
    add(a,b){ return a + b; }
    sub(a,b){ return a - b; }
}
//组件定义
@Component({
    selector:"ez-app",
    template:`
      <form>
          <input type="text" ngControl="a" [(ngMpdel)]="a"> +
          <input type="text" ngControl="b" [(ngModel)]="b">
          =
          {{add()}}
      </form>`
})
class EzApp{
    constructor(){
        this.a = 37;
        this.b = 128;
        this.algo = new EzAlgo();
    }
    add(){
        var a = +this.a;
        var b = +this.b;
        return this.algo.add(a,b);
    }
}
bootstrap(EzApp);
```
### 注入服务 - Providers

在上一节中，组件 `EzAlgo` 直接在构造函数中实例化了一个 `EzAlgo` 对象，这造成了  `EzApp` 和 `EzAlgo` 的强耦合，我们可以使用 Angular2 的 `注入器/Injector` 进行解耦。

`EzApp` 组件使用 `Component注解` 和 `appInjector` 属性向 Angular2 框架声明其依赖于 `EzAlgo`，并在其构造函数的参数表中使用 `Inject注解` 声明注入点，而后剩下的事就可以交给 Angular2 办了。

```javascript
import {Inject,Component} from "angular2/core";
import {bootstrap} from "angular2/platform/browser";

//定义一个简单的算法服务类
class EzAlgo{
    add(a,b){ return a + b;}
    sub(a,b){ return a - b;}
}
//组件定义
@Component({
    selector:"ez-app",
    providers:[EzAlgo] //声明依赖
    template:`
<form>
    <input type="text" ngControl="a" [(ngModel)]="a">
    +
    <input type="text" ngControl="b" [(ngModel)]="b">
    =
    {{add()}}
</form>`,
    style:[`
        *{font-size:30px;font-weight:bold;}
        input{width:100px;}
      `]
})
class EzApp{
    //Angular2 框架负责需要注入订单对象
    constructor(@Inject algo){
        this.a = 37;
        this.b = 128;
        this.algo = algo;
    }
    add(){
        var a = +this.a,
            b = +this.b,
        return this.algo.add(a,b);
    }
}
bootstrap(EzApp);
```

### Inject - 注入器

在大多数情况下，我们只要在类的构造函数列表中使用 `Inject` 注解，就可以告诉 Angular2 框架向我们的类代码中注入正确的对象。

Angular2 的依赖注入机制实现的核心是一个座位第三方的 `注入器/Injector`。通常使用 Injector 类的静态方法 `resolveAndCreate()` 类实例化一个注入器，在实例化时需要指定所有依赖项：

```javascript
class A{...}
class B{...}
class C{...}
var Injector = Injector.resolveAndCreate({A,B,C});
```

一旦获得了注入器实例，就可以使用其 `get()` 方法来获得指定的对象：

```javascript
var a = Injector.get(A); //A的实例
var b = Injector.get(B); //B的实例
```
值得指出的是，对于一个注入器而言，其仅仅维护每个依赖项的单一实例，也就是说，无论你调用 `Injector.get(A)` 多少次，他总是会返回同一个对象。

每当Angular2框架引导启动一个组件时，会自动根据组件 `Component` 注解的 `providers` 属性创建注入器，然后根据组件类的 `Inject` 注解，调用注入器的 `get()` 获得对应的实例注入到组件类的构造函数中:

```javascript
import {Imjector,Inject} from "angular2/core";

class Canvas{
    constructor(){
        var canvas = document.createElement("canvas");
        canvas.setAttribute("width","600px");
        canvas.setAttribute("height","400px");
        var host = document.querySelector("ez-app");
        host.innerHTML = "";
        host.appendChild(canvas);
        this.ctx = canvas.getContext("2d");
    }
}
class Face{
    constructor(@Inject(Canvas) canvas){
        this.canvas = canvas;
    }
    render(){
        with(this.canvas.ctx){
            beginPath();
            strokeStyle="black";
            fillStyle="black";
            arc(100,100,100,0,Math.PI*2);
            stroke();
            beginPath();
            arc(70,70,20,0,Math.PI*2);
            arc(130,70,20,0,Math.PI*2);
            fill();
            beginPath();
            arc(100,110,50,.5,Math.PI-.5);
            stroke();
        }
    }
}
+function bootup(){
    var injector = Injector.resolveAndCreate([Canvas,Face]);
    face.render();
}();
```


















r
