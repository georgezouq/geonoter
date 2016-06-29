---
layout:     keynote
title:      "Correlation Analysis"
subtitle:   "Correlation Analysis"
iframe:     
date:  2016-06-27 09:27:35
author:     ""
header-img: "post-bg-js-version.jpg"
tags:
    - Analysis
---

# FP-Group 算法

## 引言

在关联分析中，频繁项集的挖掘最常用到的就是 Aprior 算法.Aprior 算法是一种先产生 `候选项集` 在检验是否频繁的 `产生-测试` 的方法。这种方法有种弊端：当数据集很大的时候，需要不断扫描数据集造成运行效率很低。而 `FP-Group` 算法就很好的解决了这个问题。他的思路是吧数据集中的事务映射到一棵 `FP-Tree` 上面，在根据这颗树找出频繁项集。`FP-Tree` 的构建过程只需要扫描两次数据集。

## 示例

某店铺购物篮的数据:


TID	Items
001	Cola, Egg, Ham
002	Cola, Diaper, Beer
003	Cola, Diaper, Beer, Ham
004	Diaper, Beer

TID 代表交易流水号，Items代表一次交易的商品。

首先，FP-Growth 算法的任务hi找出数据集中的 `频繁项集`
然后，FP-Growth 算法的步骤大体上可以分为两步:(1)FP-Tree 的构建 (2)FP-Tree 上频繁项集的挖掘

## FP-Tree 的构造

1. 扫描一遍数据库，找出频繁项的列表 L ,然后按照 `支持度计数`递减排序。即：

    L = <(COla:3),(Diaper:3),(Beer:3),(Ham:2)>

2. 再次扫描数据库，由每个事物不断构建 TP-Tree:

  (1) TP-Tree 的根节点为 NULL

  (2) 从数据库中取出事物，按照 L 排序，然后把每个项逐个添加到 FP-Tree 的分支上去。例如事物 001 排序后为 `{Cola,Ham}`，在根节点上加一颗子树 `Cola-Ham`；事物 002 排序后为 `{Cola,Diaper,Beer}`，因为根节点上一节有个子树节点 `Cola`，所以可以共用该节点，在 `Cola` 节点上加一颗子树 `Diaper-Beer`，同时 Cola 的计数加 1；事物003可与树通用节点 Cola-Diaper-Deer，所以只需要在 Beer后面加上一个 Ham，同时把 Cola,Diaper,Beer的计数加1即可...

3. FP-Tree 还有一样东西-头节点表。作用是将所有相同的项链接起来，这样比较容易遍历。
  最后得到的FP-Tree如下:
  ![FP-Tree](1.jpg)

构造 FP-Tree 的伪代码如下：

```
算法：FP-Tree 构造算法
输入：事务数据集D，最小支持度阈值 min_sup
输出：FP-Tree
(1) 扫描事务数据集 D 一次，获得频繁项的集合 F 和其中每个频繁项的支持度，对F 中的所有频繁项按其支持度进行降序排序，结果为频繁项表 L;

(2) 创建一个 FP-Tree 的根节点 T，标记为“null”；
(3) for 事务数据集 D 中每个事物 Trans do
(4)   对 Trans 中的所有频繁项按照 L 中的次序排序；
(5)   对排序后的频繁项表以[p|P]格式表示，其中p是第一个元素，而p是频繁项表除去p后剩余元素组成的项表；
(6)   调用函数 insert_tree([p|P],T);
(7)  end for

insert_tree([p|P],root){
  if root 有孩子节点 N and N.item-name = p.item-name then
  Else
    创建新节点 N;
    N.item-name = p.item-name;
    N.count++;
    p.parent=root;
    将 N.node-link 指向树种与他同项目名的节点;
  end if
  if P 非空 then
    把 P 的第一项目赋值给p，并把它从 P 中删除；
    递归调用 insert_tree([p|P],N);
  end if
}
```

## 从 FP-Tree 提取频繁项集

相对而言，FP-Tree 的构造比较简单，而从 FP-Tree 提取频繁项集比较难理解。其中出现了几个新名词，下面针对购物篮的 FP-Tree 进行讲解吧。

#### 求以"Ham"为后缀的频繁项集:

 · 根据头节点表找出 "Ham" 结尾的路径：<Cola:3,Ham:1> 和 <Cola:3,Diaper:2,Beer:2,Ham:1>，带表的意义是：原数据集中 (Cola,Ham) 和 (Cola,Diaper,Beer,Ham) 各出现一次。

 · "Ham" 的两个前缀路径 {(Cola:1),(Cola Diaper Beer:1)} 构成了 Ham 的 `条件模式基`，注意条件模式基的计数都定义为了 Ham 的计数。

 · 根据 `条件模式基` 构建 Ham 的 `条件 FP-树`：因为 Ham 的条件模式基中 Diaper、Beer 只出现了一次，Coal 出现了两次，所以 Diaper、Beer 是 `非频繁项`，不包含在 Ham 的条件FP-树种。

 · Ham 的条件FP-树只有一个分支 <Cola:2>，得到 `条件频繁项集` {Cola:2}。
 · 条件频繁项集 Cola:2 和后缀模式 Ham 合并，得到 `频繁项集` {Cola Ham:2}。

求以 Beer 为后缀的频繁项集：

  · Beer 的 `条件模式基` 有 {(Cola Diaper:2),(Diaper:1)}。
  · Beer 的 `条件FP-树`如下：
  · Beer 为后缀的频繁项集为 {Cola Diaper Beer:2}、{Diaper Beer:2}、{Cola Beer:2}

![Beer 条件FP-树](2.jpg)

求以 Diaper 为后缀的频繁项集：

  条件模式基为 {(Cola:2)}，最后得到频繁项集 {Cola Diaper:2}。

综上，得到的频繁项集有:{Cola Ham:2}、{Cola Beer:2}、{Diaper Beer:2}、{Cola Diaper:2}、{Cola Diaper Beer:2}

从 FP-Tree 提取频繁项集的主要步骤是:

    对于每个频繁项，通过以下步骤来求他的`条件频繁项集`:
        · 找出它的 `条件模式基`
        · 把 `条件模式基` 当做事务集去建造一棵树，这棵树不叫 `FP-Tree`，而是叫做 `条件FP-Tree`。
        · 对这颗 `条件FP-Tree` 递归以上操作，即找这颗`条件FP-Tree` 上的 `子条件频繁项集`。
    · 以上找到的都是该`频繁项`的`条件频繁项`而已，所以每次递归都需要把 `条件频繁项集` 和 该`频繁项集`拼接起来才是我们最终要求的 `频繁项集`。

伪代码：

```
算法：FP-Growth(FP-Tree,α);
输入：已构造好的 FP-Tree，项集 α (初始值为空)，最小支持度 min_sup;
输出：事务数据集 D 中的频繁项集 L;
  L初始值为空
  if Tree 只包含单个路径 P then
    for 路径 P 中节点的每个组合 (记为 β) do
      产生项目集 α ∪ β ，其支持度 support 等于 β 中节点的最小支持度数;
      return L = L ∪ 支持度大于 min_sup 的项目集合 β ∪ α
    else  //包含多个路径
      for Tree 的头表中的每个频繁项 αf do
        产生一个项目集 β = αf ∪ α ，其支持度等于 αf 的支持度；
        构造 β 的条件模式基 B，并根据该条件模式基 B 构造 β 的条件FP-树 Treeβ;
        if Treeβ ≠ ∅ then
          递归调用 FP-Growth(Treeβ,β);
        end if
      end for
    end if
````


本文转载，感谢原作者（原文链接：http://blog.csdn.net/bone_ace/article/details/46669699）



















 哦
