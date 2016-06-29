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

# Correlation Analysis

## Instrution

关联分析是指从数据集中挖掘出频繁项集，然后从频繁项集中提取出事物之间的强关联规则，辅助决策。

eg：下面是一个超市的几名顾客交易信息：

TID         Item
001       Cola,Egg,Ham
002	      Cola, Diaper, Beer
003	      Cola, Diaper, Beer, Ham
004	      Diaper, Beer

TID 代表流水号，Items代表一次交易的商品

我们对这个数据集进行关联分析，可以找出关联规则 `{Diaper}->{Bear}`。它代表的意义是：购买了 Diaper 的顾客会购买 Beer。这个关系不是必然的，但是可能性很大，这就已经足够用来辅助商家调整 Diaper 和 Beer 的摆放位置了，或者进行捆绑促销来提高销售量。

## 关联分析 相关名词定义

 1. 事务：每一条交易称为一个事务，例如上述实例中的数据集就是四个事务

 2. 项：交易的每一个物品称为一个项，例如Cola、Egg等

 3. 项集：包含另个或多个项的集合叫做项集，例如 {Cola,Egg,Ham}

 4. k-项集：包含k个项的集合叫做`k-项集`，例如 `{Cola}` 叫做 `1-项集`，
 `{Cola,Egg}` 叫做 `2-项集`。

 5. 支持度计数：一个项集出现在几个事务当中，它的支持度计数就是几。例如 `{Diaper,Beer}` 出现在事务 002、003、004 中，所以他的支持度计数是 3

 6. 支持度：支持度计数除于总的事物数。例如上述实例中总的事物数为4，`{Diaper,Beer}` 的支持度计数为3，所以他的支持度是 `3/4=75%`，说明有 75% 的人同时买了 Diaper 和 Beer

 7. 频繁项集：支持度大于或等于某个阈值的项集就叫做 `频繁项集`。例如阈值设为 50% 时，因为{Diaper,Beer} 的支持度是 75%，所以它是频繁项集。

 8. 前件和后件：对于规则 {Diaper}->{Beer}，{Diaper}叫做前件，{Beer}叫做后件。

 9. 置信度：对于规则 {Diaper}->{Beer}，{Diaper,Beer}的支持度除于 {Diaper} 的支持度计数，为这个规则的置信度。例如规则 {Diaper}->{Beer}，{Diaper,Beer}的支持度计数除于{Diaper}的支持度计数，为这个规则的置信度。例如规则{Diaper}->{Beer} 的置信度为 3/3=100%。说明买了Diaper 的人 100% 也买了 Beer。

 10. 强关联规则：大于或等于最小支持度阈值和最小置信度阈值的规则叫做强关联规则。关联分析的最终目标就是要找出强关联规则。

 我们容易发现，如果一个项集是频繁项集，则它的子项也都是频繁项集。如果一个项集是非频繁项集，则它的 超集也一定是非频繁项集。例如 {Diaper, Beer}是频繁项集，则{Diaper}、{Beer}也都是频繁项集。{Egg}是非频繁项集，则{Cola, Egg}也是非频繁项集。

### 关联分析

关联分析的两个步骤

    1. 利用支持度找出数据集中的 `频繁项集`
    2. 利用置信度从频繁项集中提取出 `强关联规则`

## 频繁项集的挖掘

### Apriori Algorithm

Apriori Algorithm 的思路是先找出 `候选项集`，然后根据 `最小支持度阈值` 筛选出 `频繁项集`。

    eg. 先找出所有的 `1-项集`，然后筛选出里面的 `频繁1-项集`；根据`频繁1-项集` 生成候选的 `2-项集`，然后筛选里面的 `频繁2-项集`；再根据 `频繁2-项集` 生成候选的 `3-项集`，从里面筛选出频繁 `3-项集`；.......

Apriori 算法的缺点是需要不断扫描数据集，不断地求候选集的支持度从而判断它时候是频繁项集，当数据量大的时候，这种算法的效率将会非常低。

### FP-Growth Algorithm

FP-Growth 算法只需要扫描两次数据集，它的思路是把构造一棵 FP-Tree ，把数据集中的数据映射到树上，在根据这颗 `FP-Tree` 找出所有频繁项集。

关于FP-Growth，[FP-Growth算法的介绍](http://blog.csdn.net/Bone_ACE/article/details/46669699)、[FP-Growth算法的Python实现](http://blog.csdn.net/bone_ace/article/details/46746727)

转载于 [CSDN](http://blog.csdn.net/bone_ace/article/details/46648965) 感谢原作者
