# Mysql下InnoDB的可重复读的事务测试

## 背景：

* mysql版本：Server version: 5.1.71
* 操作系统：CentOS 6.5 X64
* 事务隔离级别：不可重复读

## 实验前的准备：

```
mysql> use test;
mysql> create table test_table(id int primary key, name varchar(255)) ENGINE=InnoDB;
mysql> insert into test_table value(1, 'lily');
mysql> create table test_table2(id int primary key, name varchar(255)) ENGINE=InnoDB;
mysql> insert into test_table2 value(1, 'lucy');
```
## 实验1

问题：事务从什么时候开始？

测试两边同事开启事务，只在终端A中进行update操作，提交完成之后，在终端B中观察
```
步骤                      终端A                                        终端B
 1        mysql> set autocommit = off;                      mysql> set autocommit = off;
 2        mysql> update test_table set name = 'lucy';
 3        mysql> commit;
 4                                                          mysql> select * from test_table;
                                                            +----+--------+
                                                            | id | name   |
                                                            +----+--------+
                                                            |  1 | lucy   |
                                                            +----+--------+
                                                            1 rows in set (0.00 sec)
```
 

在终端B中直接可以看到终端A所做的修改，说明事务并不是从命令"set autocommit = off"执行时就开始；


还原影响数据：
```
mysql> update test_table set name = 'lily';
mysql> commit;
```
只在终端A中进行更新操作，同时在终端B中对库中的任意一个表进行一个操作，终端A提交之后，观察终端B中的数据；然后在终端B中执行commit之后，再次查看终端A中修改的数据。
```
 步骤                      终端A                                        终端B
 1        mysql> rollback;                                  mysql> rollback;
 2        mysql> update test_table set name = 'lucy';       mysql> select * from test_table2;
 3        mysql> commit;
 4                                                          mysql> select * from test_table;
                                                            +----+--------+
                                                            | id | name   |
                                                            +----+--------+
                                                            |  1 | lily   |
                                                            +----+--------+
                                                            1 row in set (0.00 sec)

                                                            mysql> commit;
                                                            mysql> select * from test_table;
                                                            +----+--------+
                                                            | id | name   |
                                                            +----+--------+
                                                            |  1 | lucy   |
                                                            +----+--------+
                                                            1 row in set (0.00 sec)
```

这里启动了事务，说明事务是以第一条SQL时开始的

## 实验2

问题：InnoDB在不可重复读隔离级别下，有没有幻读情况

还原影响数据：
```
mysql> update test_table set name = 'lily';
mysql> commit;
```
```
 步骤                      终端A                                        终端B
 1        mysql> rollback;                                  mysql> rollback;
 2        mysql> insert into test_table value(2, 'lucy');   mysql> select * from test_table2;
 3        mysql> commit;
 4                                                          mysql> select * from test_table;
                                                            +----+--------+
                                                            | id | name   |
                                                            +----+--------+
                                                            |  1 | lily   |
                                                            +----+--------+
                                                            1 row in set (0.00 sec)
                                                            mysql> rollback;
                                                            mysql> select * from test_table;
                                                            +----+--------+
                                                            | id | name   |
                                                            +----+--------+
                                                            |  1 | lily   |
                                                            |  2 | lucy   |
                                                            +----+--------+
                                                            2 rows in set (0.00 sec)
```
这里没有存在幻读的情况，说明InnoDB对幻读有处理。

## 实验3

问题：同事开启两个事务A和B，事务A中插入一条记录R，事务B中插入另一条记录R2，R2的主键与记录R相同，如何处理？

还原影响数据：
```
mysql> delete from test_table where id = 2;
mysql> commit;
```
```
步骤                      终端A                                        终端B
 1        mysql> rollback;                                  mysql> rollback;
 2        mysql> insert into test_table value(2, 'lucy');   mysql> select * from test_table2;
 3        mysql> commit;
 4                                                          mysql> insert into test_table value(2, 'lucy');
                                                            ERROR 1062 (23000): Duplicate entry '2' for key 'PRIMARY'
```
这种情况下，会报错。

## 实验4

问题：不同事务更新同一条记录。
```
步骤                      终端A                                                终端B
 1        mysql> rollback;                                              mysql> rollback;
 2        mysql> update test_table set name = 'lily2' where id = 1;       
 3                                                                      mysql> update test_table set name = 'lily3' where id = 1;
                                                                        ... 卡住 ... 在超过一定时间之后报错：
                                                                        ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```
两个事务在编辑同一条数据时，先编辑的事务会将该记录锁定，或编辑的事务会等待一定时间，如果一定直接内仍然被锁定则报错。