---
layout:     keynote
title:      "Hello World with Spark"
subtitle:   "Keynote: Hello World with Spark"
iframe:     
date:       2016-06-17
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - 数据相关
    - Spark
---
# Spark

## 简介

## Scala 语言

Spark 是用Scala（发音为 /ˈskɑːlə, ˈskeɪlə/）语言开发的.Scala是一种多范式的编程语言，设计意图是要继承面向对象变成和函数式编程的各种特性。由于不满Java语言复杂的语法，瑞士洛桑联邦理工学院奥德斯基教授带领小组在2001年创建 了Scala语言（任性~）。

Scala 运行在Java虚拟机上，也就是说Scala会被便以为和Java编译后的 class 一样的字节码。也改变着 Scala 和 Java 之间可以相互调用变并且他们可以联合编译，不过实际上 Scala 调用 Java 容易一些，而java调用 scala有时会遇到一些问题。

至少在Spark应用中，Scala比Java的开发效率更高。

启动 Scala 环境

```shell
$ cd ~/spark
$ ./bin/spark-shell
```

### 常量与变量

在 Scala 中定义一个变量使用 `var` 关键字:

```scala
scala> var name = "steve"
name:java.lang.String = steve

scala> name = "marius"
name: java.lang.String = marius

```

如果需要定义一个常量，使用 `val` 关键字，常量在首次赋值之后不可以更改:

```scala
scala> val two = 1 + 1
two: Int = 2
```

### 函数定义与调用

在 Scala 中，使用`def`关键字定义一个函数，等号左侧是函数名、参数列表和返回值，右侧是函数体实现和表达式

定义函数对传入整型参数加1并返回:

```scala
scala> def addOne(m:Int): Int = m + 1
addOne: (m:Int)Int
```

使用函数名，传入参数并进行函数调用

```scala
scala > var x = addOne(123)
x : 124
```

如果函数体需要多个表达式才能实现，可以使用`代码块`将多个表达式抱起来:

```scala
scala> def sum(n:Int):Int = {
  for(i <- 1 to 10)
    r=r*1

  r //or return r
}
```

如果在函数体中不使用`return`返回函数值，那么`最后一个表达式`的值就是函数返回值

### 超级重要的匿名函数

Scala支持匿名函数，创建匿名函数不需要使用 `def` 关键字，符号 `=>` 的左侧定义参数列表，右侧定义函数体实现。

下面的匿名函数为x的变量加1并返回结果：

```scala
scala>(x:Int) => x+1
res2:(Int) => Int = ...

```

函数可以赋值给变量或常量，这就是`函数式变成`的一个特点，下面将匿名函数赋值给 `addOne` 常量，后续代码中就可以使用 `addOne` 进行调用了:

```scala
scala:val addOne = (x:Int) => x+1
addOne:(Int) => Int = ...

scala:addOne(1)
res4:Int=2
```

如果匿名函数体实现包含多行表达式，就可以使用 `{}` 来包围代码:

```scala
scala> {
  i : Int =>
  println("hello world");
  i * 2

}
res0:(Int) => Int =...
```

### 对象定义

Scala 支持面向对象变成，使用 `class` 定义一个类，在类定义中使用 `val` 定义成员变量，用 'def' 定义成员方法:

```scala
class Calculator {
  var brand: String = "HP"
  def add(m:Int,n:Int):Int = m + n
}
defined class Calculator
```

使用 `new` 关键字创建一个对象:

```scala

scala> val calc = new Calculator
calc: Calculator = Calculator@e75a11

scala> calc.add(1,2)
res1:Int = 3

scala> calc.brand
res2:String = "HP"

```

## 交互分析

### Spark Shell

Spark Shell 提供一个简单订单方式来学习 Spark 框架的API，同时也是一个可以用于交互数据分析的强大工具。

#### 启动 Spark Shell

```shell
$ cd ~/spark
$ ./bin/spark-shell

```

Spark - Shell 是一个 `REPL` 解释器，我们输入 Scala 表达式后，按 `回车` 就可以看到效果。

比如我们要看 Spark 的版本，提示符输入:

```shell
scala> sc.version
res2:String = 1.1.0
```

执行结果反馈总是以 `变量名:类型 = 值` 的形式来显示。在上面的结果中可以看到，执行结果被放入一个临时变量 `res2` 中，类型是 String 值为1.1.0  

    Spark Shell 有 Scala 和 Python 两个版本

### 集群对象:SparkContext

当我们启动 Spark-Shell ，就会自动获得一个 SparkContext 对象的实例，这个对象呗存入变量 `sc` 中。

在提示符下输入：sc ，就可以看到 sc 的类型：`org.apache.spark.SparkContext`:

```scala
scala> sc
res1: org.apache.spark.SparkContext = org.apache.spark.SparkContext@3c5a3436
```
SparkContext 对象代表整个 Spark 集群，是 Spark 框架的功能入口，可以用来在集群中创建 RDD、累加器变量和广播变量。SparkContext 对象创建时可以指明链接到哪个集群管理器上，在 Spark-Shell 启动时，默认连接到本地的集群管理器。

使用 SparkContext 对象 （在Shell里就是 `sc` 变量）的 master 方法，可以查看当前连接的集群管理器:

```shell
scala> sc.master
res10: String = local[*]

```

显示结果表明，我们的确连接到了本地的集群管理器上，`*` 代表不明确指定在每个计算节点上使用的 CPU 核心数(`资源限额`)

### 分布数据集：RDD

Spark 的核心抽象是一个分布式数据集，被称为 `弹性分布数据集(RDD)`，代表一个`不可变`的、`可分区`、可被`并行处理`的成员集合。

RDD对象需要利用 SparkContext 对象的方法创建，Spark 支持从多种来源创建 RDD 对象，比如：从本地文本文件创建，从Hadoop的HDFS文件创建，或者通过其他 RDD 进行变换获得新的 RDD。

下面的示例使用本地 Spark 目录下的 `README.md` 文件创建一个新的 RDD:

```scala
scala> val textFile = sc.textFile("README.md")
textFile: spark.RDD[String] = spark.MappedRDD@2ee9b6e3

```

我们看到执行结果是返回了一个 `Spark.RDD` 类型的变量 textFile，RDD是一个 `模板类`，方括号里的 String 代表这个RDD对象 `成员的类型`。由于是一个对象，因此值用地址表示:spark.MappedRDD@2ee9b7e3 。

SparkContext 对象的 textFile 方法创建的 RDD 中，一个成员对应原始文件的一行。我们看到在执行结果中返回一个 RDD，成员类型为 String，我们将这个对象保存在变量 textFile 中。

使用 SparkContext 对象创建 RDD 数据集，然后才能干点有意义的事。

### RDD：变换与动作

RDD的内部实现了分布式计算的功能，我们在 RDD 上执行的操作是透明的在整个集群上执行的。也就是说，当 RDD 建立后，这个 RDD 就不属于本地了，它在整个集群中有效。当在 RDD 上执行一个操作， RDD内部需要和 `集群管理器` 进行沟通协商。

对一个　RDD 可以进行两种操作：动作（`action`）和变换（`transformation`）。动作总是从集群中 `取回数据` ，变换总是获得一个新的RDD，这时两种操作的字面上的差异

事实上，当在RDD上执行一个 `变换` 时，RDD仅仅记录要做的变换，只有当RDD上需要执行一个 `动作` 时，RDD 才通过集群管理器 启动实质的分布计算。

这有点像拍电影，`变换` 操作只是剧本，只有导演喊 Action 的时候，真正的电影才开始制作。

### 不一样的动作和变换
























#
