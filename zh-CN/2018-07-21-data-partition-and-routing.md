---
slug: 2018-07-21-data-partition-and-routing
id: 2018-07-21-data-partition-and-routing
title: "数据分区与路由"
date: 2018-07-20 11:54
comments: true
tags: [系统设计]
description: "实施数据分区与路由的优点是可用性和读取效率，而一致性则是其弱点。路由抽象模型本质上是两张地图：键-分区图和分区-机器图。"
---

## 为什么要进行数据分区与路由？

大数据集 ⟶ 扩展 ⟶ 数据分片 / 分区 ⟶ 1) 数据访问的路由 2) 可用性的副本

- 优点
    - 可用性
    - 读取（并行化，单次读取效率）
- 缺点
    - 一致性

## 如何进行数据分区与路由？

路由抽象模型本质上只有两张地图：1) 键-分区图 2) 分区-机器图



### 哈希分区

1. 哈希和取模
    - (+) 简单
    - (-) 灵活性（紧耦合两张地图：添加和移除节点（分区-机器图）会破坏现有的键-分区图）

2. 虚拟桶：键--(哈希)-->虚拟桶，虚拟桶--(表查找)-->服务器
    - 使用案例：Membase 也称为 Couchbase，Riak
    - (+) 灵活性，解耦两张地图
    - (-) 集中式查找表

3. 一致性哈希和 DHT
    - [Chord] 实现
    - 虚拟节点：用于异构数据中心的负载均衡
    - 使用案例：Dynamo，Cassandra
    - (+) 灵活性，哈希空间解耦两张地图。两张地图使用相同的哈希，但添加和移除节点==只影响后续节点==。
    - (-) 网络复杂性，难以维护



### 范围分区

按主键排序，按主键范围分片

范围-服务器查找表（例如 HBase .META. 表）+ 本地基于树的索引（例如 LSM，B+）

(+) 搜索范围
(-) log(n)

使用案例：Yahoo PNUTS，Azure，Bigtable