# 一. 事务



> **定义**：事务安全transaction safe是指有些操作是需要多次对表进行写操作（增删改），但是需要最后等所有结果都成功了才提交，其中一步错了，整个操作就无效，从而保证数据的整体性。



## 1. 事务的使用

1. 默认的MySQL是关闭事务的，即用户一提交操作，系统就自动将数据写入到数据表

```Mysql
# 在一个客户端写入数据，然后去另外一个客户端查看
insert into my_foreign2 values(null,'蜘蛛',188,5);
```

2. 如果想要开启事务可以使用两种方式

* 自动开启：修改系统事务控制变量autocommit
* 手动事务：使用事务命令来控制要执行的事务操作

**自动事务**

```Mysql
mysql> show variables like 'autocommit';			#查看系统事务控制，默认是自动提交的
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set, 1 warning (0.01 sec)

# 修改自动提交
mysql> set autocommit = off;
Query OK, 0 rows affected (0.35 sec)

# 此时所有的写操作就不会自动写入到数据表了
mysql> insert into my_foreign2 values(null,'自来也',50,2);
#需要使用指令commit
mysql> commit;
Query OK, 0 rows affected (0.10 sec)
```

**注意**：当前修改只是当前客户端当次连接有效，关闭退出就失效了。


3. 手动事务：即通过指令来控制事务的开启和结束

* 开启事务：start transaction;
* 执行事务：各类SQL写操作
* 关闭事务：
  * commit：操作成功全部提交
  * rollback：操作失败全部回退

```Mysql
# 开启事务
start transaction;

# 执行事务
insert into my_class values(null,'神童1班','4001');	#执行成功后执行第二天，此时其他客户端看不到
insert into my_foreign2 values(null,'阿童木',0,8);		#使用第一条的结果

# 结束事务：判断前面是否成功，如果有失败则回退
rollback;											#前面所有操作都失败

# 注意:如果前面新增数据的时候用到了自增长,而最后选择了rollback,那么对自增长的触发是不可逆的.
```

4. 事务回退：指事务操作过程中，某个节点成功了，但是后续未必会成功，那么可以设置节点，以后返回到该位置

* 设置节点：savepoint 名字;
* 回退到节点：rollback to 节点名字;

```Mysql
# 开启事务
start transaction;

# 执行事务
insert into my_class values(null,'神童1班','4001');	#执行成功后执行第二天，此时其他客户端看不到

# 设置节点
savepoint sp1;

# 继续执行
insert into my_foreign2 values(null,'阿童木',0,6);		#使用第一条的结果

# 假设执行失败，回退到成功节点
rollback to sp1;									#第二天执行无效

# 继续执行
insert into my_foreign2 values(null,'阿童木',0,9);

# 提交事务
commit;		
```



## 2. 事务特点：ACID

* 原子性（Atomicity ）：一个事务中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
* 一致性（Consistency）：事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
* 隔离性（Isolation ）：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
* 持久性（Durability ）：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。



## 3. 事务的隔离级别

| 隔离级别                     | 脏读（Dirty Read） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） |
| ---------------------------- | ------------------ | -------------------------------- | -------------------- |
| 读未提交（Read uncommitted） | 可能               | 可能                             | 可能                 |
| 读已提交（Read committed）   | 不可能             | 可能                             | 可能                 |
| 可重复读（Repeatable read）  | 不可能             | 不可能                           | 可能                 |
| 可串行化（Serializable ）    | 不可能             | 不可能                           | 不可能               |

InnoDB默认是可重复读级别的

- 脏读: 脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。
-  不可重复读:是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读。
-  幻读:第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样，幻读是数据行记录变多了或者少了。

总结：脏读是指读取了未修改完的记录，不可重复读指因为被其它事务修改了记录导致某事务两次读取记录不一致，而幻读是指因为其它事务对表做了增删导致某事务两次读取的表记录数不一致问题。



## 4. 事务安全原理

* 默认事务关闭：SQL写操作一旦执行，DBMS就会写入到数据表（其他客户立即可见）
* 开启事务：系统将当前事务的操作记录到事务日志，当前用户自己查看的是DBMS加工结果数据
* 提交事务：事务成功提交会一次性同步到表，而如果失败提交就会全部清空当前事务记录

> **总结**

1. 事务安全是一种保障连续操作完整性的机制

2. 事务安全可以是自动事务（默认关闭）和手动事务，一般使用手动事务

3. 事务处理过程中可以设置回滚点，从而实现部分回退

4. 事务处理有四个特点：ACID

5. MySQL中InnoDB支持事务，MyIsam不支持

   



# 二.视图

**定义**：视图View，是一种虚拟表结构，由select查询语句的结果组成。

**应用场景**：

1. 多个地方用到同样的查询结果  该结果sql语句比较复杂
2. 将我们数据库的数据提交给其他系统访问（仅允许访问）



## 1. 定义视图

create view 视图名字 as select查询语句;

```Mysql
create view student_class 
as 
select s.*,c.name class_name,c.room class_room
from my_student2 s left join my_class c on s.class_id = c.id;
```

## 2. 视图查看

视图创建后可以使用表查看的所有方式进行查看

```Mysql
show tables;
desc student_class;
show create table/view student_class;
```

**注意**：视图是一种虚拟表，会产生表结构文件，但是没有数据和索引文件

## 3. 修改视图

* 修改结构：alter view修改
* 创建替换：create or replace view

```Mysql
alter view 视图名字 as 新select语句

# 创建替换
create or replace view student_view as select * from my_student2; #没有就新增，有就替换
```

## 4. 删除视图

drop view 视图名字;

## 5. 视图使用

视图有select语句组成结果，是提供数据的，可以像查询表一样查询数据

```Mysql
select * from student_class;
```

## 6. 视图的插入修改和删除

视图也可以进行以上操作,但一般不会这么做.

注意具备以下特点的视图不允许更新

- 包含以下关键字的语句：分组函数、distinct、group by、having、union或者union all
- 常量视图
- select中包含子查询

