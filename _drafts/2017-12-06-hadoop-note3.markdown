---
layout: post
title:  "大数据技术原理与应用之Hadoop再探讨"
date:   2017-12-06 16:00:00 +0800
tag: hadoop
category: 2017年12月
description: "大数据技术原理与应用之Hadoop再探讨。介绍hadoop的优化与发展，HDFS2.0的新特性，新一代资源管理调度框架YARN，Hadoop生态系统中具有代表性的功能组件"
keywords: yarn,hadoop,hdfs2.0
type: coding
user: kevis
---
### Hadoop的优化与发展
#### Hadoop的局限与不足

```
Hadoop1.0的核心组件仅指MapReduce和HDFS，主要存在以下不足。
1.抽象层次低。需要手工编写代码来完成，有时只是为了实现一个简单的功能，也需要编写大量的代码。
2.表达能力有限。MapReduce把复杂分布式编程工作高度抽象到两个函数上，即Map和Reduce，在降低开发人员程序开发复杂度的同时，却也带来了表达能力有限的问题，实际生产环境中的一些应用是无法用简单的Map和Reduce来完成的。
3.开发者自己管理作业之间的依赖关系。一个作业（job）只包含Map和Reduce两个阶段，通常的实际应用问题需要大量的作业进行协作才能顺利解决，这些作业之间往往存在复杂的依赖关系，但是MapReduce框架本身并没有提供相关的机制对这些依赖关系进行有效管理，只能由开发者自己管理。
