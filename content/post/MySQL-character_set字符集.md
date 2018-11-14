---
title: MySQL character_set字符集
date: 2018-07-26 22:08:10
tags: [MySQL]
categories: [MySQL]
---

## 查看变量

使用`cmd`查看MySQL character_set 变量：

```mysql
show variables like 'character_set%';
```

<!--more-->

结果如下：

![result](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fmysql_character.png)

当使用`Navicat`查看character_set时，结果：

![navicat-result](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fnavicat_mysql_character.png)

使用`cmd`查看MySQL character_set变量时，执行下面命令可以对`character_set_client`、`character_set_connection`以及`character_set_results`进行修改，

```mysql
set character_set_client=utf8;
set character_set_connection=utf8;
set character_set_results=utf8;
```

然后查看变量，修改成功，但是，当用`cmd`重新连接MySQL，发现变量并没有修改，之前的修改应该只是对那次连接有效。

从上述结果看：`character_set_client`、`character_set_connection`以及`character_set_results`这三个参数的值是由客户端连接进来时所决定，应该和服务端没有关系。

## 修改变量

我们可以在连接MySQL后，修改上述3个字符集，同时也可以在连接MySQL时指定变量要使用的编码，例如：

```mysql
mysql -h127.0.0.1 -uroot -proot --default-character-set=utf8
```

另外，也可以在配置文件`my.ini`里面进行修改。

## 变量说明

- `character_set_database`: 当前所在数据库的字符集

- `character_set_filesystem`: 把文件名转换为此字符集，默认`binary`，不做转换。

    > The file system character set. This variable is used to interpret string literals that refer to file names, such as in the LOAD DATA INFILE and SELECT ... INTO OUTFILE statements and the LOAD_FILE() function. Such file names are converted from character_set_client to character_set_filesystem before the file opening attempt occurs. The default value is binary, which means that no conversion occurs. For systems on which multibyte file names are permitted, a different value may be more appropriate. For example, if the system represents file names using UTF-8, set character_set_filesystem to 'utf8'.

- `character_set_system`

    > The character set used by the server for storing identifiers. The value is always utf8.

- `character_set_dir`

    > The directory where character sets are installed. 