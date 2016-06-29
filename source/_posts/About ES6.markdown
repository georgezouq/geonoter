---
layout:     keynote
title:      "ES6 Getting Start - Commend"
subtitle:   "Keynote: Commend with ES6"
iframe:     
date: 2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - ES6
---
# ES6 部分方法点评

## 箭头函数(Arrow Functions)

  箭头函数时ES6的语法糖之一:

```javascript
//ES5
var selected = allJobs.filter(function(job){
  return job.isSelected();
});

//ES6
var selected = allJobs.filter(job => job.isSelected());

```

  多行方法体的方式:

```javascript
//ES5
$('#confetti-btn').click(function(event){
  playTrumpet();
  fireConfettiCannon();
});

//ES6
$("#confetti-btn").click(event => {
  playTrumpet();
  fireConfettiCannon();
})
```

语法大体是这样的:

    ([函数的形参,多个参数则以逗号分隔]) => [函数返回的值/表达式]

此外，还有最重要的一点，箭头函数并没有自己的`this`值，箭头函数内的`this`值继承自外围作用域。

```javascript
  {
    add: function(piece){},
    ...
    addAll:function addAll(prieces){
      var self = this;
      _.each(pieces,function(price){
        self.add(piece);
      });
    }

  }

  //ES6
  {
    add:function(piece){},
    ...
    addAll:function addAll(pieces){
      _.each(pieces,piece => this.add(price));
    },
    ...
  }

```

## LET

ES6 `let`关键字的出现意味着JavaScript迎来了块级作用域({},for,if),可以有效的防止临时变量污染到外层变量:

```javascript
  function f1(){
    let n = 5;
    if(true){
      let n = 10;
    }
    console.log(n); //5
  }

```

## CONST

  `const`用来定义常量，一旦定义了常量就不可修改。

## DESTRUCTURING

  destructuring 解构，ES6允许按照一定的模式，从数组和对象中提取值。对变量进行赋值，这成为解构(Destructuring):

```javascript
'use strict';

//对数组的解构赋值
let [foo,[[bar],baz]] = [1,[[2],3]];
console.log(foo); //1
console.log(bar); //2
console.log(baz); //3

//对象的解构赋值
var {foo,bar} = {foo:"aaa",bar:"bbb"};
console.log(foo); //"aaa"
console.log(bar); //"bbb"

//字符串的解构赋值
const [a,b,v,d,e] = "hello";
console.log(a+b+c+d+e); //'hello'


```

跟箭头函数一样，这也是一个全新的语法糖，它的用处有很对，例如在我们封装参数的时候：

```javascript
// ES5 二逼青年写法
function study(id, name, sex, grade, nickname, age, address) {
    console.log(id);
    console.log(name);
    console.log(sex);
    console.log(grade);
    console.log(nickname);
    console.log(age);
    console.log(address);
}
// ES5 正常青年写法
function study(params) {
    console.log(params.id);
    console.log(params.name);
    console.log(params.sex);
    console.log(params.grade);
    console.log(params.nickname);
    console.log(params.age);
    console.log(params.address);
}
```

```javascript
//ES6
function study({id, name, sex, grade, nickname, age, address}) {
    console.log(id);
    console.log(name);
    console.log(sex);
    console.log(grade);
    console.log(nickname);
    console.log(age);
    console.log(address);    
}
study({
    id: 1,
    name: '林有德',
    sex: '男',
    grade: '一年级',
    nickname: '布莱德',
    age: 12,
    address: '木马号'
});
```
