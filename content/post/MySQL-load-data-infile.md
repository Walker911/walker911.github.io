---
title: MySQL load data infile简介
date: 2018-07-28 15:34:00
tags: [MySQL]
categories: [MySQL]
---

最近，需要将Excel里面的数据导入到MySQL，数据量大概有200万左右，于是就查询MySQL是否有快速导入数据的方法，然后发现MySQL提供一种高效导入数据的方法，即是`load data infile`。这里记录用到的一些参数。

## 环境

- OS: Windows 10
- MySQL Version: 5.7.19

<!--more-->

## 基本语法

```mysql
LOAD DATA [LOW_PRIORITY | CONCURRENT] [LOCAL] INFILE 'file_name'
    [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [PARTITION (partition_name [, partition_name] ...)]
    [CHARACTER SET charset_name]
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
    [IGNORE number {LINES | ROWS}]
    [(col_name_or_user_var
        [, col_name_or_user_var] ...)]
    [SET col_name={expr | DEFAULT},
        [, col_name={expr | DEFAULT}] ...]
```

## 示例

```mysql
LOAD DATA LOCAL INFILE 'c:/project/demo.csv' 
REPLACE INTO TABLE table_name 
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
ESCAPED BY '^' 
LINES TERMINATED BY '\r\n' 
IGNORE 1 LINES 
(application_id,vin,@retail_date,retail_type,external_comments,@submission_date,@check)
SET retail_date = STR_TO_DATE(@retail_date,'%d/%m/%Y'),
submission_date = STR_TO_DATE(@submission_date,'%d/%m/%Y %H:%i')
```

- `LOCAL`: 表明从客户端读取文件，这里使用的是绝对路径，也可以使用相对路径。
- `REPLACE`: 指定`REPLACE`方式导入，导入时会替换具有相同主键或唯一索引的数据。
- `TERMINATED BY`: 指定字段分隔符，这里使用的是','
- `ENCLOSED BY`: 字段括起字符，这里使用是因为有个字段内容里面含有','，导入时会出现分隔错位的情况，当使用`ENCLOSED BY`后，这个字段内容会被'"'括起，完整导入到数据库
- `ESCAPED BY`: 指定转义字符，默认反斜杠，这里指定为'^'，是因为某个字段出现了'中文\\中文'的内容，如果不将默认转义字符更改的话，导入时会出现`Invalid utf8 character string error`，所以需要将转义字符修改为字段内容中未出现的字符
- `LINES TERMINATED BY`: 指定换行符，和系统相关。
- `IGNORE x LINES`: 忽略几行数据，这里忽略一行数据，也就是忽略标题行
- `@RetailDate`: 在字段前面加`@`符号，然后可以对字段内容进行处理，这里是为了转换时间，以便导入；另外，文件中有某列数据不需要导入的话，也可以使用`@`，例如：最后一列数据不需要导入，这里用`@check`变量，数据库表并不指定字段，这样就将最后一列数据忽略

## 注意

- 编码问题