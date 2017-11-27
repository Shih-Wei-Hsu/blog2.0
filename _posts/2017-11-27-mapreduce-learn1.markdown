---
layout: post
title:  "大数据技术原理与应用之MapReduce(一)"
date:   2017-11-27 16:00:00 +0800
tag: MapReduce
category: 2017年11月
description: "大数据技术原理与应用之MapReduce(一),包括概述和工作流程"
keywords: MapReduce,Map,Reduce,Hadoop
type: coding
user: kevis
---

### MapReduce 概述
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MapReduce是一种并行编程模型，用于大规模数据集（大于1TB）的并行运算，它将复杂的，运行于大规模集群上的并行计算过程高度抽象到两个函数：Map和Reduce，这两个函数及其核心思想都源自函数式编程语言。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在MapReduce中，一个存储在分布式文件系统中的大规模数据集会被切分成许多独立的小数据块，这些小数据块可以被多个Map任务并行处理。MapReduce框架会为每个Map任务输入一个小数据子集，Map任务生成的结果会继续作为Reduce任务的输入，最终由Reduce任务输出最后结果，并写入分布式文件系统。<strong>适合用MapReduce来处理的数据集需要满足一个前提条件：待处理的数据集可以分解成许多小的数据集，而且每一个小数据集都可以完全并行地进行处理。 </strong>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MapReduce设计的一个理念就是“计算向数据靠拢”，而不是“数据向计算靠拢”，因为移动数据需要大量的网络传输开销，尤其是在大规模数据环境下，这种开销尤为惊人，所以<strong>移动计算要比移动数据更加经济</strong>。本着这个理念，在一个集群中，只要有可能，MapReduce框架就会将Map程序就近地在HDFS数据所在的节点运行，即将计算节点和存储节点放在一起运行，从而减少了节点间的数据移动开销。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MapReduce模型的核心是Map函数和Reduce函数，二者都是由应用程序开发者负责具体实现的。MapReduce编程之所以比较容易，是因为我们只需要关注如何实现Map和Reduce函数，而不需要处理并行编程中的其他各种复杂问题，如分布式存储、工作调度、负载均衡、容错处理、网络通信等，这些问题都会由MapReduce框架负责处理。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Map函数和Reduce函数都是以<key,value>作为输入，按一定的映射规则转换成另一个或一批<key,value>进行输出。


函数 | 输入 | 输出 | 说明
---------|---------|---------|--------------
Map  | <k1,v1> | List(<k2,v2>) |  (1)将小数据集进一步解析成一批<key,value>对，输入Map函数中进行处理  (2)每一个输入的<k1,v1>会输出一批<k2,v2>，<k2,v2>是计算的中间结果
Reduce | <k2,List(v2)> | <k3,v3> | 输入的中间结果<k2,List(v2)>中的List(v2)表示是一批属于同一个k2的value

<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Map函数的输入来自于分布式文件系统的文件块，这些文件块的格式是任意的，可以是文档，也可以是二进制格式的。文件块是一系列元素的集合，这些元素也是任意类型的，同一个元素不能跨文件块存储。Map函数将输入的元素转换成<key,value>形式的键值对，键和值的类型也是任意的，其中键不同于一般的标志属性，即键没有唯一性，不能作为输出的身份标识，即使是同一个输入元素，也可通过一个Map任务生成具有相同键的多个<key,value>。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reduce函数的任务就是将输入的一系列具有相同键的键值对以某种方式组合起来，输出处理后的键值对，输出结果会合并成一个文件。用户可以指定Reduce任务的个数（如n个），并通知实现系统，然后主控进程通常会选择一个Hash函数，Map任务输出的每个键都会经过Hash函数计算，并根据哈希结果将该键值对输入相应的Reduce任务来处理。对于处理键为k的Reduce任务的输入形式为<k,<v1,v2,...,vn>>,输出为<k,V>。  

### MapReduce的工作流程
#### 工作流程概述

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;大规模数据集的处理包括分布式存储和分布式计算两个核心环节。  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MapReduce的输入和输出都需要借助于分布式文件系统进行存储，这些文件被分布存储到集群中的多个节点上。MapReduce的核心思想可以用“分而治之”来描述，也就是把一个大的数据集拆分成多个小数据块在多台机器上并行处理，也就是说，一个大的MapReduce作业，首先会被拆分成许多个Map任务在多台机器上并行执行，每个Map任务通常运行在数据存储的节点上，这样，计算和数据就可以放在一起运行，不需要额外的数据传输开销。当Map任务结束后，会生成以<key,value>形式表示的许多中间结果。然后，这些中间结果会被分发到多个Reduce任务在多台机器上并行执行，具有相同key的<key,value>会被发送到同一个Reduce任务那里，Reduce任务会对中间结果进行汇总计算得到最后结果，并输出到分布式文件系统中。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;需要指出的是，不同的Map任务之间不会进行通信，不同的是Reduce任务之间也不会发生任何信息交换；用户不能显式地从一台机器向另一台机器发送消息，所有的数据交换都是通过MapReduce框架自身去实现的。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在MapReduce的整个执行过程中，Map任务的输入文件、Reduce任务的处理结果都是保存在分布式文件系统中的，而Map任务处理得到的中间结果则保存在本地存储中（如磁盘）。另外，只有当Map处理全部结束后，Reduce过程才能开始；只有Map需要考虑数据局部性，实现“计算向数据靠拢”，而Reduce则无需考虑数据局部性。  

#### MapReduce的各个执行阶段

##### 一个MapReduce算法的执行过程：  

```
    (1) MapReduce框架使用InputFormat模块做Map前的预处理，比如验证输入的格式是否符合输入定义；然后，将输入文件切分为逻辑上的多个InputSplit,InputSplit是MapReduce对文件进行处理和运算的输入单位，只是一个逻辑概念，每个InputSplit并没有对文件进行实际切割，只是记录了要处理的数据的位置和长度。
    (2) 因为InputSplit是逻辑切分而非物理切分，所以还需要通过RecordReader(RR)根据InputSplit中的信息来处理InputSplit中的具体记录，加载数据并转换为适合Map任务读取的键值对，输入给Map任务。
    (3) Map任务会根据用户自定义的映射规则，输出一系列的<key,value>作为中间结果。
    (4) 为了让Reduce可以并行处理Map的结果，需要对Map的输出进行一定的分区(Portition)、排序(Sort)、合并(Combine)、归并(Merge)等操作，得到<key,value>到有序的<key,value-list>，这个过程用Shuffle(洗牌)来称呼是非常形象的。



```








