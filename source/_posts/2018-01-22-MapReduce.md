---
layout: single
title: "MapReduce summary"
date: 2018-01-22
---



##摘要，Intro

[MapReduce](https://research.google.com/archive/mapreduce-osdi04.pdf): 一种编程模型，产生、处理大数据集

并行、分布式的实现细节对程序员透明

Map:对输入中的每一个Logical "record"，应用Map操作，计算出一组中间KVPairs

Reduce: 对中间KVpairs中key相同的pair进行适当的合并

[TOC]

## 编程模型

map由用户编写，对输入的kvp产生一组中间kvp。MR库将key相同的中间value打包，传给reduce(通过迭代器Iterator传递，从而允许不连续存放)

reduce由用户编写，将value合并为可能更小的value集合(通常只产生0或1个输出)。

> example: 统计单词出现次数
>
> ```python
> map(String key, String value):
>   # key: document name
>   # value: content
>   for w in value:
>     EmitIntermediate(w, "1")
> reduce(Stringkey, Iterator values):
>   # key: a word
>   # values: list of counts
>   result = 0
>   for v in values:
>     result += ParseInt(v)
>   Emit(AsString(result))
> ```

中间key与value的类型与输出相同，但与输入不同

Our(?) C++实现传递值均为字符串，由用户解释

分布式Grep、URL访问频数统计、转置Web连接图、统计每个主机的高频关键词，单词索引，分布式排序

## 实现

可有多种不同实现(针对不同设备)

机器错误经常出现

主节点(Master)、工作节点(Worker)

1. MapReduce库将输入分块，Cluster中多台机器运行一个副本
2. 副本中主结点分配Map与Reduce任务给其余结点
3. Map的结点读取输入块内容，获取KVP后传给map函数，中间KVP缓存于内存
4. 内存中的中间KVP被周期划分，存入磁盘，磁盘位置传给主结点再传给reduce结点
5. reduce结点获得位置后调用RPC读取中间KVP，按key排序(可能外部排序)
6. reduce结点将同key的values传给reduce函数，结果写到最终输出文件的末尾
7. reduce完成后主结点唤醒用户，mapreduce返回

### 容错

主结点周期ping所有结点

工作结点错误：已完成的map需重新进行，正在进行的map与reduce需重新进行(reduce结果存在Global File System)

reduce结点都将收到重执行的通知，从而从新结点重新读取

主结点错误：simple：定期保存恢复点。current：终止mapreduce，用户检查、重启

map与reduce输出的提交为原子的

输出被写入私有temp文件，reduce有一个这样的文件，map有R个

完成时：

* map完成时结点发给主节点临时文件名字。主结点收到已完成结点的消息，忽略；否则记录文件名
* reduce完成时，原子更名将临时输出改为最终输出。若因为出错、备用机制使同一个reduce任务在多个机器执行，底层FS提供的原子更名操作保证FS只含其中一个结果

确定性语义 不确定的语义

### 局部

GFS，3备份，map就近分配

### 粒度

MR比机器数大很多，动态平衡，加速失败恢复。上限。R由用户指定。M使输入分为16~64MB

### 备用任务

接近完成时，将剩余任务调度给其他机器同时执行

## 技巧

* 划分函数：根据key用不同方式划分给reduce任务
* 顺序：中间kvp增序
* 合并：中间kvp发出前先又map机器执行局部合并
* 输入输出类型：text例子
* **边界效应**：？
* 信号处理程序，catch出错，通过UDP发给主节点，下一次跳过该记录
* 本地调试
* http server，查看状态
* counter

## 结论

成功原因：

1. 首先，该模型即使是对于没有并行和分布式系统经验的程序员也是很易于使用的，因为它隐藏了并行化、容错机制、局部性优化、以及负载平衡的细节。
2. 其次，很多种类的问题都很容易表示成MapReduce计算。例如，MapReduce被用于为Google的网络搜索服务的数据产生、排序、数据挖掘、机器学习、以及许多其它系统。
3. 第三，我们已经开发了一个MapReduce的实现，可以扩展到包含上千台机器的大型集群。该实现可以高效使用这些机器的资源，因此适合于Google遇到的很多大型计算问题。

经验：

1. 首先，约束这个编程模型令并行和分布式计算，以及令这些计算可容错，变得简单了。
2. 其次，网络带宽是一种稀缺资源。我们系统中的很多优化都因此针对减少通过网络发送的数据总量：局部性优化允许我们从本地磁盘读，同时将中间文件写入本地磁盘也节省了网络带宽。
3. 第三，备用执行可以用于减小缓慢的机器的影响，及应对机器失败和数据丢失。