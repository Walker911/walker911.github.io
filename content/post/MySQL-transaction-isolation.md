---
title: 高性能MySQL-事务隔离性
date: 2018-08-30 12:30:00
categories: 
- 读书笔记
tags: 
- MySQL
- 事务
---

## 环境

OS: Windows 10

MySQL version: 8.0

引擎：InnoDB

## 事务隔离性

通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。

### 事务隔离级别

事务隔离级别如下：

|         事务隔离级别         | 脏读 | 不可重复读 | 幻读 |
| :--------------------------: | :--: | :--------: | :--: |
| 读未提交（read-uncommitted） | yes  |    yes     | yes  |
|   提交读（read-committed）   |  no  |    yes     | yes  |
| 可重复读（repeatable-read）  |  no  |     no     | yes  |
|    串行化（seriaizable）     |  no  |     no     |  no  |

> yes: 可能出现
>
> no: 不会出现

<!-- more -->

查看默认事务隔离级别，命令如下：

```mysql
-- 查看本次会话
select @@transaction_isolation;
-- 查看全局
select @@global.transaction_isolation;
```

结果如图，MySQL 默认事务级别为 `REPEATABLE-READ`

![](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fmysql_transaction_isolation.png)

#### READ-UNCOMMITTED (未提交读)

在这个级别，事务中的修改，即使没有提交，对其他事务也都是可见的。

事务可以读取未提交的数据，称为**脏读**(Dirty Read)。

示例 1：

修改事务隔离级别为`READ-UNCOMMITTED`

```mysql
-- 修改全局事务隔离级别
set global transaction_isolation='read-uncommitted';
-- session 级别
set session transaction_isolation='read-uncommitted'
```

开启两个 MySQL 客户端，同时关闭事务自动提交。

```mysql
-- client 1 and client 2
-- 查看autocommit
show variables like 'autocommit';
-- 修改autocommit
set autocommit='off';
```

Client 1 执行操作如下：

![](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fmysql_client1.png)

Client 2 执行操作如下：

![](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fmysql_client2.png)

可以发现，在 Client 1 事务未提交前，Client 2 可以看到未提交的数据，如果将这时候将拿到的数据去展示或者做其他用途，然后 Client 1做了`rollback`，那么就出现了脏读的现象。

#### READ-COMMITTED (提交读)

一个事务开始时，只能“看到”其他已经提交的事务所做的修改。

执行两次相同的查询，可能会得到不一样的结果，所以这个级别有时候也叫做不可重复读（nonrepeatable read）。

示例 2：

和示例 1 类似，修改事务隔离级别，开启两个客户端，并关闭事务自动提交。

```mysql
set global transaction_isolation='read-committed';
set session transaction_isolation='read-committed';
set autocommit='off';
```

执行过程：

```mysql
-- client 1 查询并插入一条数据
select * from user;
+----+------+------+ 
| id | name | age  | 
+----+------+------+ 
|  1 | jack |   18 | 
+----+------+------+ 
begin;
insert into user values(2,'james',20);
-- client 2 查询
select * from user;
+----+------+------+ 
| id | name | age  | 
+----+------+------+ 
|  1 | jack |   18 | 
+----+------+------+ 
-- client 1 提交事务
commit;
-- client 2 重新查询
select * from user;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | james |   20 |
+----+-------+------+
```

#### REPEATABLE-READ (可重复读)

MySQL的默认事务隔离级别。同一个事务中多次执行同样的查询，得到的结果是一致的。理论上，可重复读隔离级别还是无法解决另外一个幻读 (Phantom Read) 的问题。

> 在同一个事务内的查询都是事务开始时刻一致的。摘自[美团点评技术](https://tech.meituan.com/innodb_lock.html)

幻读指的是当某个事务在读取某个范围内的记录时，另外一个事务又在该范围内插入了新的记录，当之前的事务再次读取该范围的记录时，会产生幻行（Phantom Row）。

InnoDB 和 XtraDB 存储引擎通过多版本并发控制（MVCC, Multiversion Concurrency Control）解决了幻读的问题。

> MVVC：是一种乐观锁机制。需要详细了解乐观锁和悲观锁

示例 3：

同样的，修改事务隔离级别，开启两个客户端，并关闭事务自动提交。

```mysql
set global transaction_isolation='repeatable-read';
set session transaction_isolation='repeatable-read';
set autocommit='off';
```

执行过程：

```mysql
-- client 1 查询并插入一条数据
select * from user;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | james |   20 |
+----+-------+------+
begin;
insert into user values(3,'west',25);
-- client 2 查询
select * from user;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | james |   20 |
+----+-------+------+
-- client 1 提交事务
commit;
-- client 2 重新查询
select * from user;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | james |   20 |
+----+-------+------+
```

#### SERIALIZABLE (可串行化)

最高的隔离级别，强制事务串行执行。

简单来说，SERIALIZABLE 会在读取的每一行数据上都加锁，所以可能导致大量的超时和锁争用的问题。

