

### 数据库操作

##### 创建数据库

```
create database 数据库名;

create database runoob;
123
```

##### 选择数据库

```
use 数据库名;

use runoob;
123
```

##### 删除数据库

```
drop database 数据库名;

drop database runoob;
123
```

### 数值类型
![](https://upload-images.jianshu.io/upload_images/25392987-a45435d9fafe7a7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 数据表

##### 创建数据表

```
CREATE TABLE 数据表名 (字段名 字段类型);

CREATE TABLE `runoob_tbl`(
   `runoob_id` INT UNSIGNED AUTO_INCREMENT,
   `runoob_title` VARCHAR(100) NOT NULL,
   `runoob_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
123456789
```

##### 删除数据表

```
DROP TABLE 数据表名 ;

DROP TABLE runoob_tbl ;
123
```

### 数据操作

##### 增

```
INSERT INTO table_name ( field1, field2,...fieldN ) 
VALUES 
( value1, value2,...valueN );

INSERT INTO runoob_tbl (runoob_title, runoob_author, submission_date)
VALUES
("学习 PHP", "菜鸟教程", NOW());
1234567
```

##### 删

```
DELETE FROM table_name [WHERE Clause];

DELETE FROM runoob_tbl WHERE runoob_id=1;
123
```

##### 改

```
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause];

UPDATE runoob_tbl SET runoob_title='学习 C++' WHERE runoob_id=1;
1234
```

##### 查

```
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
[WHERE condition1 [AND [OR]] condition2.....

SELECT * from runoob_tbl WHERE runoob_author='菜鸟教程';
1234
```

### 查询

##### WHERE
![](https://upload-images.jianshu.io/upload_images/25392987-ff10e922127ce3ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### ORDER BY

```
SELECT field1, field2,...fieldN table_name1, table_name2...
ORDER BY field1, [field2...] [ASC [DESC]];

SELECT * from runoob_tbl ORDER BY submission_date ASC;
1234
```

##### GROUP BY

GROUP BY 语句根据一个或多个列对结果集进行分组。

在分组的列上我们可以使用 COUNT, SUM, AVG,等函数。

```
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;

SELECT name, COUNT(*) FROM   employee_tbl GROUP BY name;

```

##### JOIN

JOIN 按照功能大致分为如下三类：

INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。


```
SELECT a.runoob_id,
       a.runoob_author,
       b.runoob_count
FROM   runoob_tbl a
       INNER JOIN tcount_tbl b
               ON a.runoob_author = b.runoob_author

```

### MySQL 事务

##### 事务介绍

MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你即需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！

在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
事务用来管理 insert,update,delete 语句

##### 事务控制语句

BEGIN或START TRANSACTION；显式地开启一个事务；

COMMIT；也可以使用COMMIT WORK，不过二者是等价的。COMMIT会提交事务，并使已对数据库进行的所有修改成为永久性的；

ROLLBACK；有可以使用ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；

###  字段操作

##### 增

```
ALTER TABLE 数据表名 ADD 新增字段 字段类型;

ALTER TABLE runoob_tbl ADD status tinyint(1) NOT NULL DEFAULT '0' COMMENT '状态 0正常 1删除';
123
```

##### 删

```
ALTER TABLE 数据表名 DROP 字段名;

ALTER TABLE runoob_tbl  DROP status;
123
```

##### 改

```
#例如，把字段 c 的类型从 CHAR(1) 改为 CHAR(10)，可以执行以下命令:
ALTER TABLE testalter_tbl MODIFY c CHAR(10);
ALTER TABLE testalter_tbl CHANGE c c CHAR(10);

#修改字段类型及名称
#在 CHANGE 关键字之后，紧跟着的是你要修改的字段名，然后指定新字段名及类型。尝试如下实例：
#例如，把字段 c 改成 字段 j ,类型从 CHAR(1) 改为 CHAR(10)，可以执行以下命令:
ALTER TABLE testalter_tbl CHANGE c j CHAR(10);

#ALTER TABLE 对 Null 值和默认值的影响
ALTER TABLE testalter_tbl MODIFY j BIGINT NOT NULL DEFAULT 100;

#修改字段默认值
ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;

#修改表名
ALTER TABLE testalter_tbl RENAME TO alter_tbl;

123456789101112131415161718
```

### 索引

##### 创建普通索引

```
#这是最基本的索引，它没有任何限制。它有以下几种创建方式：
#(1)创建索引

CREATE INDEX indexName ON mytable(username(length)); 
#如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length。

#(2)创建表的时候直接指定
CREATE TABLE mytable(  
	ID INT NOT NULL,   
	username VARCHAR(16) NOT NULL,  
	INDEX [indexName] (username(length)) 
); 
123456789101112
```

##### 创建唯一索引

```
#它与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。它#有以下几种创建方式：

#(1)创建索引
CREATE UNIQUE INDEX indexName ON mytable(username(length)) ;

#(2)创建表的时候直接指定
CREATE TABLE mytable( 
	ID INT NOT NULL,  
	username VARCHAR(16) NOT NULL,  
	UNIQUE [indexName] (username(length))  
);  
1234567891011
```

##### 删除索引

```
DROP INDEX [indexName] ON mytable; 
1
```

##### 使用ALTER 命令添加和删除索引

```
有四种方式来添加数据表的索引：
#(1)该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
ALTER TABLE tbl_name ADD PRIMARY KEY (column_list);

#(2)这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。
ALTER TABLE tbl_name ADD UNIQUE index_name (column_list);

#(3)添加普通索引，索引值可出现多次。
ALTER TABLE tbl_name ADD INDEX index_name (column_list);

#(4)该语句指定了索引为 FULLTEXT ，用于全文索引。
ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list);

#添加索引实例
ALTER TABLE testalter_tbl ADD INDEX (c);

#删除索引实例
ALTER TABLE testalter_tbl DROP INDEX c;
```
