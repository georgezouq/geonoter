---
layout:     keynote
title:      "First Line for TypeScript"
subtitle:   "Keynote: First Line for TypeScript"
iframe:     
date:  2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - 前端开发
    - TypeScript
---
# 什么是TypeScript?

Javascript 是一种弱类型语言，在开发一些小项目，UI交互的时候很灵活方便。但是当项目变得庞大之后，就显露出很明显的弊端.

TypeScript把类型带入Javascript的世界，他是Javascript的超集，即所有合法的JS都是Typescript。但是他可以有类型，运行的时候Typescript被tsc编译器翻译成可读性很高的Javascript在浏览器中执行。

### typescript并没有太多对于类型的限制：

  1.它支持类型自动推断，一些很明显的类型，编译器能够智能推断出来 `var x = 1`;
  2.好在它的类型是可选的，你可以全携程any类型，编译器会对该变量放弃类型检查，及时类型比配失败，依然会生成Javascript，你可以逐渐把JS代码迁移到Typescript上。

## 基本类型

TypeScript 提供了`boolean`,`number`,`string`,`array`,`enum`,`any`,`void`几种基本类型

```javascript
var flag:boolean = true;
var s:string = 'hello';
var n:number = 123;

enum Color {Red = 1,Green,Blue};
var color:Color = Color.Green;

function log(msg:string):void{
  console.log(`${new Date().toUTCString()} ${msg}`);
}
```

## 类

类的语法和ES6的Class类似，有constructor，可以继承，这里说几点不同:

### 属性声明

ES6的class属性是直接赋值给`this.varName`声明的，tyoescript里面需要显示声明。

```javascript
class Point{
  x:number;
  y:number;
}
```

### 构造函数属性声明

属性也可以在构造函数中添加`public`,`private`描述声明

```javascript
class Point{
  constructor(public x:number,public y:number){}
}
```

### 支持public ,private ,static 描述

ES6的static只支持在函数上声明，而typescript支持方法和属性。

```typescript
  class Popup{
    static inst:Popup;

    constructor(){
      if(!Popup.inst){
        Popup.inst = this;
      }
    }
  }

  Popup.getInstance();

```

### 函数

#### - 可选/默参数

``` typescript

  function reset(hard?:boolean){
    if(hard){
      resetDB();
    }
    resetUI();
  }
  reset();
  reset(true);
```

#### - 剩余参数

  可变数目的参数需要使用剩余参数的写法。获得的参数是一个数组。

```typescript
  function max(...nums:number[]){
    ...
  }
```

#### - 箭头函数

  这一点与ES6一致，箭头函数内部保持和外部一样的this上下文

```typescript

var Scheduler = {
  queue:[],
  schedule(t:Task){
    setTimeout(() => {
      this.queue.push(t);
    },0);
  }
};

Scheduler.schedule(new Task());

```

#### - 重载

JS函数支持不同的参数进行重载，例如jquery的css函数

```typescript
css({
  width:'100px'
  })
  css('width','100px')
```
可以这样调用，我们可以为该函数声明重载:

```typescript
function css(config:object);

function css(config:string,value:string);

function css(config:any,value?:any){
  if(typeof config == 'string'){
    ...
  }else if(typeof config == 'object'){
    ...
  }
}

```

这个函数有两个重载，编译器会判断参数类型是否符合其中一个。

#### - 装饰器(decorator)

Typescript的装饰器和ES6里面一致，可以修改已有的类或类的方法，也可以在他们的基础上提供一层封装。Angular2里面大量使用装饰器，为组件注册元数据

1.类的装饰器

```typescript
@decorator
class A{}

```

这段代码等同于：

```typescript
class A {}
A = decorator(A) || A;

function decorator(klass){

  klass.meta = 'my awesome class';
}

```

Angular 里面的装饰器可以传入meta信息，如下：

```typescript
@Component({
  selector:'my-app'
  })
  class MyApp{

  }

```

Component 其实是angular库中实现的一个装饰器工厂，接受一个meta信息，返回一个装饰器,类似如下实现:

```typescript
function Component(meta){
  return function(klass){
    ...
  }
}
```

2.类的方法的装饰器

  还有一种装饰器是类的方法的装饰器

  ```typescript

    class Persion{
      @readonly
      name(){
        return `${this.first} ${this.last}`
      }
    }
  ```
  以上readonly方法被如下方式调用
  ```typescript
    function readonly(target,name,descriptor){
      descriptor.writable = false;
      return descriptor;
    }

    readonly(Person.prototype,'name',descriptor);

    // descriptor对象的初始值如下
    // {
    //   value: oneFunction,
    //   enumerable: false,
    //   configurable: true,
    //   writable: true
    // };
  ```

3.函数的装饰器呢？可惜的是由于函数存在提升，没有函数的装饰器、

## 模块

Typescript 模块包括内部模块和外部模块两种

### 内部模块

内部模块创建一个封闭的作用域，共一个js文件内 代码使用(也可以使用///引入其他文件的内部模块)

```typescript
  module com.gf.Utils{
    export function foo(msg:string){
      alert(msg);
    }
    export var name = 'some random tools';

  }

  import Utils = com.osamu.Utils; //alias

  Utils.foo('hi')
  Utils.name = 'Jack';

```
