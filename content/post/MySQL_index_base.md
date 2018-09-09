---
title: 高性能 MySQL - 索引基础
date: 2018-09-01 12:00:00
categories: 
- 读书笔记
tags:
- MySQL
---

索引（在MySQL中也叫做“键(key)” 是**存储引擎**用于快速找到记录的一种数据结构。

MySQL 中使用索引：先在索引中找到对应值，然后根据匹配的索引记录找到对应的数据行。

## 索引基本使用

查看表的索引使用情况，命令如下：

```mysql
show index from table_name;
```

<!-- more -->

### 主键索引 (primary key)

一种特殊的唯一索引，不允许有空值。

```mysql
-- 查看索引
show index from person \G
*************************** 1. row ***************************
        Table: person                                         
   Non_unique: 0                                              
     Key_name: PRIMARY                                        
 Seq_in_index: 1                                              
  Column_name: id                                             
    Collation: A                                              
  Cardinality: 0                                              
     Sub_part: NULL                                           
       Packed: NULL                                           
         Null:                                                
   Index_type: BTREE                                          
      Comment:                                                
Index_comment:                                                
      Visible: YES                                            
```

### 唯一索引 (unique)

唯一索引列的值必须唯一，允许有空值。若为组合索引，那么组合的值唯一。

```mysql
-- 添加索引 alter table table_name add unique (column)
alter table person add unique (name);
-- 组合索引
alter table person add unique uniqu_name_age (name,age);
-- 索引信息
*************************** 2. row ***************************
        Table: person
   Non_unique: 0
     Key_name: name
 Seq_in_index: 1
  Column_name: name
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES
```

### 普通索引

基本索引，无特殊的限制。

```mysql
-- 添加索引 alter table table_name add index index_name (column);
alter table person add index index_name (name);
-- 组合索引
alter table person add index index_name_age (name,age);
```

### 删除索引

删除索引：

```mysql
drop index index_name on table_name;
```

以上是索引的基本使用，主要是表创建后添加索引的用法和删除索引的过程。通常，我们应该在设计表时添加索引

```mysql
[unique][fulltext][index] 别名 (column...)
```

## 索引类型

MySQL中，索引类型是在存储引擎层而不是服务器层实现的，MySQL支持的索引类型有以下几种：

### B-Tree 索引

MySQL InnoDB 索引使用的是 B+Tree 索引，大概的抽象结构如图所示：

> B-Tree 索引能够加快访问数据的速度，因为存储引擎不再需要进行全表扫描来获取需要的数据，取而代之的是从索引根节点开始进行索引，然后依据根节点中存放的指向子节点的指针，以及比较查找的值和节点页的值，向下层查找，最终，要么查到所需的值，要么记录不存在。

### 哈希索引 (hash index)

基于哈希表实现，只有精确匹配索引所有列的查询才有效。

在 MySQL 中只有 Memory 引擎显式支持哈希索引，也是 Memory 引擎表的默认索引类型，Memory 引擎同时也支持 B-Tree 引擎。

### 空间数据索引 (R-Tree)

MyISAM 表支持空间索引，可以用作地理数据存储。这类索引无需前缀查询，空间索引会从所有维度索引数据。MySQL 需要使用 GIS 相关函数来维护数据，支持的并不完善，解决方案比较好的是 PostgreSQL 的 PostGIS。

### 全文索引

一种特殊类型的索引，查找的是文本中的关键词。



