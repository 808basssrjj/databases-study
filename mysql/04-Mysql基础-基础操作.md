

# 一、数据库操作



## **1.查看数据库**

> **定义**：查看数据库，即将系统已经存在的数据库显示出来。在SQL操作中，分两种情况查看数据库：一是查看所有数据库，二是查看匹配数据库

1. 查看所有数据库：show databases;

2. 查看部分数据库：使用匹配方式匹配相近数据库，语法结构为：show databases like 'pattern';

* 占位符\_（下划线）：匹配固定位置单个字符，_a，可以是Aa\aa\sa，但不能是aaa

* 占位符%（百分号）：匹配多个字符，%a，可以是任何以a结尾的字符

```mysql
show databases like '_a'; #下划线匹配
show databases like '%a'; #模糊匹配
```



## **2.创建数据库**

1. 创建数据库：create database 数据库名字;

   ```mysql
   mysql> create database my_database;
   Query OK, 1 row affected (0.09 sec)    #Query OK表示指令正确执行
   ```

2. 数据库命名规范

* 数据库名字由字母、数字和下划线组成
* 数据库不强制要求数字不能开头（但是不建议）
* 数据库命名应当见名知意（与项目对应）
* 数据库多单词数据库命名建议使用下划线法
* 数据库名字如果使用到了SQL内部关键字或者保留字应该使用反引号包裹（数字键1左边对应的英文状态``，但是**不建议使用冲突关键字作为名字**）

3. 数据库创建过程中可以指定数据库字符集：create database 数据库名字 charset 字符集;

```mysql
mysql> create database my_project charset utf8;
Query OK, 1 row affected (0.00 sec)
#指定字符集为utf8，注意mysql中utf-8用utf8表示（不允许使用中划线-）
```



##  3.修改数据库

1. 修改数据库字符集：alter database 数据库名字 charset 新字符集;
2. 数据库字符集的修改是针对以后在数据库创建的结构的默认字符集，不会修改已经存在的数据表对应的字符集

```mysql
#更改库的字符集
ALTER DATABASE books CHARACTER SET gbk;
```



## 4.删除数据库

1. 删除数据库：drop database 数据库名字;

```mysql
drop database my_project;
```

2. 数据库删除会直接清空该数据库内的所有内容，而且不可找回，因此一定要慎重（操作之前先做安全备份）    
3. 数据库的删除只允许一次删除一个，不能删除多个    





# 二、数据表操作



## 1.创建数据表

> **定义**：创建数据表，即在数据库中创建更小的数据存储的结构。数据表是一个二维结构表，不只是有关表自身结构，而且还与字段是一体的。因此所有数据表的操作就意味着有数据字段的操作。

1. 创建数据表语法结构

   ```mysql
   create table 表名(
   字段名 字段类型 [字段属性],        #每个字段有对应的英文逗号“,”分隔
   字段名 字段类型 [字段属性],        #字段属性并非强制要求
   ...          
   字段名 字段类型 [字段属性]         #最后一个字段名不需要逗号结束          
   )[表选项];                        #表选项可有可无，都有默认值
   ```

   * 表名：描述表内数据的关键字，由数字、字母和下划线组成（不建议使用数字开头）；如果使用关键字或者保留字作为表名应该使用反引号``；表名字在同一个数据库下不能重复
   * 字段名字：二维表列名字，描述该列数据的关键字；同一表内字段名字不能重复
   * 字段类型：MySQL为了方便数据的规范，对每列数据都强制规定数据的类型
   * 表选项：表的额外规范，包括存储引擎（默认InnoDB）、字符集（默认数据库字符集）和校对集（默认数据库对应校对集）

   

## 2.查看数据表

> **定义**：数据表的存在是与字段和类型一体的，因此查看表的情况相对来说种类比较多，可以是查看表名也可以是查看表结构

1. 查看全部已存在表：show tables; #进入数据库环境

2. 查看部分匹配表名：show tables like 'pattern';    

   匹配模式也是‘_’单个字符匹配和‘%’多个字符模糊匹配

3. 查看表结构，就是显示表中所有的字段细信息等：desc/describe/[show columns from] 表名;



## 3.更新数据表

> **定义**：更新数据表即根据需求更新表本身以及表内部字段部分的内容

​	语法:  alter table 表名 add|drop|modify|change column 列名 【列类型 约束】

1. 更新表名，表的名字可以修改：rename table 旧表名 to 新表名

2. 修改列名

   ```mysql
   ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME;
   ```

3. 修改列的类型或约束

   ```mysql
   ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
   ```

4. 添加新列

   ```mysql
   ALTER TABLE author ADD COLUMN annual DOUBLE; 
   ```

5. 删除列

   ```mysql
   ALTER TABLE book_author DROP COLUMN  annual;
   ```



## 4.删除数据表

> **定义**：删除表结构，即将创建表以及表中的数据进行一次性删除。

1. 删除表结构语法：drop table 表名;

   ```mysql
   DROP TABLE IF EXISTS book_author;
   
   #通用的写法：
   DROP TABLE IF EXISTS 旧表名;
   CREATE TABLE  表名();
   ```

   **注意**：删除表操作会删除表中所有的数据，因此一定要做好删除前的准备工作（删除确认以及对应数据备份）

2. 删除表的时候MySQL允许同时删除多张表：drop table 表名1,表名2...;



## 5.复制数据表

```mysql
#1.仅仅复制表的结构
CREATE TABLE copy LIKE author;

#2.复制表的结构+数据
CREATE TABLE copy2 
SELECT * FROM author;

#只复制部分数据
CREATE TABLE copy3
SELECT id,au_name
FROM author 
WHERE nation='中国';

#仅仅复制某些字段
CREATE TABLE copy4 
SELECT id,au_name
FROM author
WHERE 0;
```





# 三、数据操作



## 1.新增数据

> **定义**：新增数据，就是往数据表中对应的空行中填充对应字段所需要的数据。

1. 新增一条完整数据：对表中对应的一条空白记录处所有字段数据：insert into 表名 values（字段1对应的值1,字段2对应的值2...,字段N对应的值N）

   ```mysql
   insert into my_table values('0000000001',20,'Jim');
   ```

2. 新增指定字段数据：给表中对应的一条空白记录处指定字段数据：insert into 表名 (字段1,字段2...字段N) values(值1,值2...值N)；

   ```mysql
    insert into my_table (name,number) values('Tom','0000000002');
   ```

* 值元素的数量要与指定的字段数量一致
* 值元素的顺序要与指定字段的顺序一致（字段顺序可以不和表字段顺序一致）
* 没有被选中的其他表字段不能因为没有数据出错

3. 新增多条记录：可以是完整的或者指定字段的多条记录：insert into 表名 [(字段列表)] values(值列表1),(值列表2),...(值列表N);

   ```mysql
   insert into my_table values('0000000003',20,'Lily'),
   ('0000000004',18,'LiLei'),
   ('0000000005',28,'Lycy'); #最后一次分号，表示结束，前面使用逗号“,”分隔
   ```

   

## 2.查询数据

1. 查看所有数据：select * from 表名; #*号属于通配符，表示匹配所有字段信息
2. 查看指定字段数据：select 字段名1,字段名2... from 表名;
3. 匹配数据查看：在查询数据的时候根据适当的条件筛选数据：select 字段列表/* from 表名 where 条件表达式;



## **3.更新数据**

> **定义**：更新数据，就是根据某些条件（可以没有条件）对指定字段数据进行更新操作。

1. 更新全部数据的某个字段信息：update 表名 set 字段名 = 新值;

   ```mysql
   #案例：修改beauty表电话为13899888899
   UPDATE beauty SET phone = '13899888899'
   ```

   **注意**：在实际操作中应该尽量避免此类更新，这样的更新会让某些数据变得完全一样，从而失去实际价值。应该根据具体的需求去更新对应的记录。

2. 根据更新条件实现部分记录更新：update 表名 set 字段 = 新值 where 条件表达式;

   ```mysql
   #案例：修改boys表中id好为2的名称为张飞，魅力值 10
   UPDATE boys SET boyname='张飞',usercp=10 WHERE id=2;
   ```

3. 修改多表记录

   ```mysql
   #案例 1：修改张无忌的女朋友的手机号为114
   UPDATE boys bo
   INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
   SET b.`phone`='119',bo.`userCP`=1000
   WHERE bo.`boyName`='张无忌';
   
   #案例2：修改没有男朋友的女神的男朋友编号都为2号
   UPDATE boys bo
   RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
   SET b.`boyfriend_id`=2
   WHERE bo.`id` IS NULL;
   ```

   

## 4.删除数据

1. 删除全部数据：delete from 表名；

2. 删除部分条件匹配数据：delete from 表名 where 条件表达式;

   ```mysql
   DELETE FROM beauty WHERE phone LIKE '%9';
   ```

3. 多表的删除

   ```mysql
   #案例：删除张无忌的女朋友的信息
   DELETE b
   FROM beauty b
   INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
   WHERE bo.`boyName`='张无忌';
   
   #案例：删除黄晓明的信息以及他女朋友的信息
   DELETE b,bo
   FROM beauty b
   INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
   WHERE bo.`boyName`='黄晓明';
   ```

4. truncate 清空

   ```mysql
   #案例：将魅力值>100的男神信息删除
   TRUNCATE TABLE boys ;
   ```

   1. delete 可以加where 条件，truncate不能加

   2. truncate删除，效率高一丢丢

   3. 假如要删除的表中有自增长列，

      ​	如果用delete删除后，再插入数据，自增长列的值从断点开始，

      ​	而truncate删除后，再插入数据，自增长列的值从1开始。

   4. truncate删除没有返回值，delete删除有返回值

   5. truncate删除不能回滚，delete删除可以回滚.

