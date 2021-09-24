

# **MySQL数据库字段**

> **思考**：MySQL数据库的基本存储单元是表中的字段，MySQL数据库需要高效、安全的维护数据，并且保证数据的有效性，是如何实现的呢？

> **引入**：作为数据想要保证操作效率，就需要事先知道如何管理；想要保证有效性，就需要让数据能够有一些规范。在MySQL中，通过字段类型和字段属性来完成这两个目的

* 字段类型：限定数据的规范格式
* 字段属性：在字段类型基础上更细致规范数据



> **总结**：MySQL中字段拥有字段类型和字段属性，类型是大致规范数据格式，保证数据的管理和操作效率；字段属性在类型之上再进行细分规范，从而保证数据的有效性。



## **一、MySQL数据库字段类型**

> **思考**：MySQL数据库作为一种高效率存储和管理数据的工具，是如何保证数据的管理效率的呢？

> **引入**：数据的管理效率最好的一种手段就是让数据变得精确，而让数据变精确的方式就是给数据一种明确的格式，从而在进行数据的存取时，可以按照设定的格式来进行快速操作。因此，MySQL对于数据表中的字段进行了类型规范。



### **1.MySQL字段（列）类型【掌握】**

> **定义**：MySQL字段类型是MySQL为了更加有效的数据管理而对表中字段的数据本身进行了类型限定，是在创建表的时候，就进行了相应的字段规范。

1. MySQL中根据业务的需求，将数据类型分成了四大类：

* 整数类型：存储整数数据
* 小数类型：存储浮点型数据
* 时间日期型：存储时间日期型数据
* 字符串型：存储字符数据

> **总结**

1. MySQL为了提升数据存取效率，规定了数据表中的字段必须指定数据类型
2. 字段类型一旦确定，就只能存储相应类型的数据



***



> **思考**：在PHP中整型就是一个使用8个字节存储的具体数字，MySQL中是否一样呢？

> **引入**：MySQL是用来持续（长期）存储数据的，意味着数据一旦规定格式就要分配指定长度的磁盘空间来存储数据，这些磁盘空间不能用来做其他任何存储。这就意味着数据即使不需要那么多空间来存储，也需要对应的开销。为了提升磁盘空间利用率，MySQL根据业务需求对整型进行了再次细分。

### **2. 整数类型【掌握】**

> **定义**：整数类型，就是使用整型规范的字段里，只能存储相应的整数数据。MySQL为了提升磁盘利用率，根据具体业务需求对整型进行了5类细分：迷你整型（tinyint），短整型（smallint），中整型（mediumint），标准整型（int）和大整型（bigint）

1. 迷你整型：tinyint，只用一个字节存储，存储范围是-128~127

```Mysql
mysql> create table my_tinyint(
    -> id tinyint
    -> )charset utf8;
Query OK, 0 rows affected (0.83 sec)
```

2. 短整型：smallint，用2个字节存储

```Mysql
mysql> create table my_smallint(
    -> id smallint
    -> )charset utf8;
Query OK, 0 rows affected (0.40 sec)
```

3. 中整型：mediumint，使用3个字节存储

```Mysql
mysql> create table my_mediumint(
    -> id mediumint
    -> )charset utf8;
Query OK, 0 rows affected (0.35 sec)
```

4. 标准整型：int，使用4个字节存储

```Mysql
mysql> create table my_int(
    -> id int
    -> )charset utf8;
Query OK, 0 rows affected (0.66 sec)
```

5. 大整型：bigint，使用8个字节存储

```Mysql
mysql> create table my_bigint(
    -> id bigint
    -> )charset utf8;
Query OK, 0 rows affected (0.32 sec)
```

6. MySQL中默认都是有符号类型，即支持负数，如果某些数据不需要负数，那么可以使用unsignd标志无符号类型

```Mysql
mysql> create table my_unsigned(
    -> id1 tinyint,
    -> id2 tinyint unsigned			#明确当前虽然也是迷你整型，但是属于无符号
    -> )charset utf8;
Query OK, 0 rows affected (0.70 sec)
```



> **总结**

1. MySQL中为了保证磁盘空间的有效利用，给整型提供了5中类型
2. MySQL中数值默认是有符号类型，如果想要使用无符号类型应该使用unsigned关键字修饰
3. 具体业务可以根据具体需求预判需要的数据类型，在保证空间能够完全满足数据的情况下，最大化的节省磁盘空间



***



> **思考**：MySQL中所有的整型在类型显示的时候，都在括号后有一个数字，这个数字到底代表什么含义呢？

> **引入**：在MySQL中，数值后的括号内数组代表数字的显示宽度，即表示数字最长能够显示多少个宽度。



### **3.  显示宽度【了解】**

> **定义**：显示宽度，即对应**整型**数据类型在表中最多能够显示的宽度，通常显示的最大数所占领的宽度。

1. MySQL中显示宽度是根据整型所能表示的最大值的数字个数以及是否有符号

```Mysql
mysql> desc my_unsigned;
+-------+---------------------+------+-----+---------+-------+
| Field | Type                | Null | Key | Default | Extra |
+-------+---------------------+------+-----+---------+-------+
| id1   | tinyint(4)          | YES  |     | NULL    |       |	#4个宽度是因为-127
| id2   | tinyint(3) unsigned | YES  |     | NULL    |       |	#3个宽度是因为0~255
+-------+---------------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

2. 数字的显示宽度可以在实际创建表的时候来根据需求设定

```Mysql
mysql> create table my_wide(
    -> id1 tinyint,
    -> id2 tinyint(2)
    -> )charset utf8;
Query OK, 0 rows affected (0.65 sec)
```

3. 显示宽度不会改变数据类型所能表示的大小：即数据长度超过显示宽度，数据依然有效

```Mysql
mysql> desc my_wide;
+-------+------------+------+-----+---------+-------+
| Field | Type       | Null | Key | Default | Extra |
+-------+------------+------+-----+---------+-------+
| id1   | tinyint(4) | YES  |     | NULL    |       |
| id2   | tinyint(2) | YES  |     | NULL    |       |
+-------+------------+------+-----+---------+-------+
2 rows in set (0.05 sec)

mysql> insert into my_wide values(-128,-128);
Query OK, 1 row affected (0.48 sec)
```

4. 显示宽度的目的不是用来限定数据，而是用来配合==zerofill==实现数据不足宽度时用前导0补充至指定宽度：filed 字段类型（显示宽度） zerofill; 

```Mysql
mysql> create table my_wide1(
    -> id1 tinyint(2) zerofill,
    -> id2 tinyint(2) unsigned zerofill
    -> )charset utf8;
Query OK, 0 rows affected (0.69 sec)
```

**注意**：zerofill要求数据必须为unsigned，如果不是默认也会自动转变成unsigned

```Mysql
mysql> desc my_wide1;
+-------+------------------------------+------+-----+---------+-------+
| Field | Type                         | Null | Key | Default | Extra |
+-------+------------------------------+------+-----+---------+-------+
| id1   | tinyint(2) unsigned zerofill | YES  |     | NULL    |       |
| id2   | tinyint(2) unsigned zerofill | YES  |     | NULL    |       |
+-------+------------------------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

在有zerofill前导0属性后，数据不足显示宽度的时候会自动进行前导0补充

```Mysql
mysql> insert into my_wide1 values(127,1);
Query OK, 1 row affected (0.44 sec)

mysql> select * from my_wide1;
+------+------+
| id1  | id2  |
+------+------+
|  127 |   01 |
+------+------+
1 row in set (0.04 sec)
```



> **总结**

1. 数值类型（整型）Type栏里括号内部的数字表示显示宽度
2. 显示宽度默认表示的是数据类型能表示的最大值对应的宽度
3. 可以通过设定实现显示宽度的设定
4. 设置的显示宽度不会影响对应类型本身表示的数值大小
5. 设置宽度通常是配合zerofill来实现前导0补充
6. 除非数据本身要增加前导0，一般不会刻意去修改和使用显示宽度



***



> **思考**：并非所有的业务对应的都是整型数据，如果碰到小数点的时候该如何存储呢？

> **引入**：小数在数据库中是非常常见的一种数据，MySQL中对这类数据规定为小数类型。

### **4. 小数类型【掌握】**

> **定义**：小数类型，即数据最终的表示是带有小数点方式，而小数部分在数据库中涉及到精确性的问题，MySQL为了保证数据的精确性，将小数类型有拆分为浮点型和定点型小数。

1. 浮点型：即一种不能确保精确度的小数类型，在MySQL中有float和double两种

* float：单精度类型，使用4个字节存储数据，有效精度7~8位
* double：双精度类型，使用8个字节存储数据，能够存储的数据比float更大，有效精度为15~16位

```Mysql
mysql> create table my_float(
    -> f1 float,
    -> f2 double
    -> )charset utf8;
Query OK, 0 rows affected (0.70 sec)
```

2. 浮点型数据会虽然能表示很大的数据，但是有丢失精度的可能：超过精度部分进行四舍五入

```Mysql
mysql> insert into my_float values(9999999999,9999999999);
Query OK, 1 row affected (0.42 sec)

mysql> select * from my_float;
+-------------+------------+
| f1          | f2         |
+-------------+------------+
| 10000000000 | 9999999999 |
+-------------+------------+
1 row in set (0.04 sec)
```

3. 浮点型可以指定整数部分和小数部分的位数：float/double(总长度,小数部分长度);

```Mysql
mysql> create table my_float1(
    -> f1 float(10,2),
    -> f2 float(10,4)
    -> )charset utf8;
Query OK, 0 rows affected (0.35 sec)
```

* 这个时候数据就不能超过指定的数据的大小：小数部分超过指定范围会进行四舍五入（精度范围内），而整数部分长度则不允许超过

```Mysql
mysql> insert into my_float1 values(999999.9999,888888.8888),(99.9999,88.88888);
Query OK, 2 rows affected (0.06 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from my_float1;
+------------+-------------+
| f1         | f2          |
+------------+-------------+
| 1000000.00 | 888888.8750 |			#超过精度范围，精度外出现无效数据
|     100.00 |     88.8889 |			#有效精度内，四舍五入
+------------+-------------+
2 rows in set (0.00 sec)
```

* 如果整数部分超出指定长度，则会报错

```Mysql
mysql> desc my_float1;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| f1    | float(10,2) | YES  |     | NULL    |       |
| f2    | float(10,4) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> insert into my_float1 values(99999999.99,99999999.9999);
ERROR 1264 (22003): Out of range value for column 'f2' at row 1
# 错误：给定的值超过了f2所能表示的范围
```

**注意**：如果是因为进位超过整数部分长度，系统默认允许

```Mysql
mysql> desc my_float1;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| f1    | float(10,2) | YES  |     | NULL    |       |
| f2    | float(10,4) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> insert into my_float1 values(99999999.99,999999.9999);
Query OK, 1 row affected (0.52 sec)

mysql> select * from my_float1;
+--------------+--------------+
| f1           | f2           |
+--------------+--------------+
|   1000000.00 |  888888.8750 |
|       100.00 |      88.8889 |
| 100000000.00 | 1000000.0000 |
+--------------+--------------+
3 rows in set (0.00 sec)
```

4. 定点型：decimal，是一种可以确保精确不丢失的小时类型，本质是因为decimal不是使用固定字节存储，而是根据数据的大小来自适应字节长度。大致是每9个数字使用4个字节存储。

```Mysql
mysql> create table my_decimal(
    -> d1 decimal,				#默认整数长度10位，小数部分0位
    -> d2 decimal(10,2)			 #整数长度8，小数部分2位
    -> )charset utf8;
Query OK, 0 rows affected (0.68 sec)
```

5. 定点型数据不会丢失精度（小数部分超过会自动截取，截取方式是四舍五入），整数部分超过长度都会操作失败

```Mysql
mysql> desc my_decimal;
+-------+---------------+------+-----+---------+-------+
| Field | Type          | Null | Key | Default | Extra |
+-------+---------------+------+-----+---------+-------+
| d1    | decimal(10,0) | YES  |     | NULL    |       |
| d2    | decimal(10,2) | YES  |     | NULL    |       |
+-------+---------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> insert into my_decimal values(99999999,99999999.98999);	#小数部分超长
Query OK, 1 row affected, 1 warning (0.07 sec)				   #依然执行成功	

mysql> show warnings;
+-------+------+-----------------------------------------+
| Level | Code | Message                                 |
+-------+------+-----------------------------------------+
| Note  | 1265 | Data truncated for column 'd2' at row 1 |		#数据超过范围
+-------+------+-----------------------------------------+
1 row in set (0.00 sec)
```

**注意**：定点型即便是因为进位导致整数部分超过指定长度，也不允许数据插入

```Mysql
mysql> insert into my_decimal values(99999999,99999999.99999); #进位导致整数部分达到9位
ERROR 1264 (22003): Out of range value for column 'd2' at row 1
```



> **总结**

1. MySQL中对应小数提供了两种存储方式：浮点型和定点型
2. 浮点型适合存储数据较大但是不要求精度的数据，定点型存储需要保证精度的数据
3. 一般大型数据使用浮点型，具体小数使用定点型



***



> **思考**：平常在开发时经常需要记录用户的操作时间信息，那么这类信息在数据库中是怎么存储的呢？

> **引入**：时间日期数据也算是开发中非常常用的一类数据，因此MySQL中也专门提供了时间日期类型来帮助数据存储。并且根据实际业务需求，也将时间日期细分了好几类。

### **5. 时间日期类型【了解】**

> **定义**：时间日期类型是MySQL为了方便用户时间日期而设定的类型，MySQL将时间日期类型细分成了5种类型：时间日期（datetime），日期（date），时间（time），时间戳（timestamp），年（year）

1. 年year：使用1个字节来存储年份，可以使用year和year(4)来实现，表示的范围是1901~2155

```Mysql
mysql> create table my_year(
    -> y1 year,
    -> y2 year(4)
    -> )charset utf8;
Query OK, 0 rows affected (0.71 sec)
```

year只能表示1901到2155年，而且MySQL的year支持两种方式插入数据：2位年和4位年

```Mysql
mysql> insert into my_year values(1916,2020),(69,70);
Query OK, 2 rows affected (0.14 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

**注意**：MySQL中year字段类型使用2位数字插入时，在69以前是追加2000，70以后是追加1900



2. 时间戳timestamp：从格林威治时间开始的时间

```Mysql
mysql> create table my_timestamp(
    -> t timestamp
    -> )charset utf8;
Query OK, 0 rows affected (0.75 sec)
```

不同于PHP中时间戳是秒数，MySQL中的timestamp是YYYY-MM-DD HH:II:SS年月日时分秒格式，数据插入可以使用YYYY-MM-DD HH:II:SS 或者 YYYYMMDDHHIISS方式

```Mysql
mysql> insert into my_timestamp values('2022-12-12 12:12:12'),('20231212121212');
Query OK, 2 rows affected (0.08 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

**注意**：MySQL中timestamp字段是有默认当前时间的，而且只要所在记录被修改，那么该字段就会自动更新（可以用作记录记录最后更新时间）



3. 时间段time：表示一个时间点或者一个时间段

```Mysql
mysql> create table my_time(
    -> t1 time,
    -> t2 time
    -> )charset utf8;
Query OK, 0 rows affected (0.39 sec)
```

MySQL中的time类型数据，可以直接插入时间格式（HH:II:SS）也可以是非时间格式（日期 HH:II:SS/HHH:II:SS），且还可以使用负号“-”（HHH表示的范围是-850到850）

```Mysql
mysql> insert into my_time values('12:12:12','123:12:12'),('4 12:12:12','-3 12:12:12');
Query OK, 2 rows affected (0.45 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

4. 日期date：具体日期，格式为YYYY-MM-DD，表示范围为1000-01-01到9999-12-31

```Mysql
mysql> create table my_date(
    -> d1 date
    -> )charset utf8;
Query OK, 0 rows affected (0.54 sec)
```

5. 时间日期datetime：表示具体日期时间，表示范围为1000-01-01 00:00:00到9999-12-31 23:59:59

```Mysql
mysql> create table my_datatime(
    -> dt datetime
    -> )charset utf8;
Query OK, 0 rows affected (0.68 sec)
```



> **总结**

1. MySQL中时间日期类型分为5类：year年，时间time，日期date，时间戳timestamp和时间日期datetime
2. 时间日期类型的设定是符合当时年代SQL编程需求的
3. 随着服务器端语言地位上升，以及服务器端语言对于时间日期的灵活处理，MySQL的时间日期类型使用量大为减少，取而代之的是使用真正的时间戳整型秒数



***



> **思考**：每一种类型在MySQL中都会因为数据颗粒度或者业务情形而被拆分，作为最常见的数据字符串，MySQL又是如何存储的呢？

> **引入**：作为最常见的一种数据类型，MySQL对字符串同样进行了多维度拆分。拆分的逻辑就是字符串格式、字符串长短和字符串长度是否固定。

### **6. 字符串类型【掌握】**

> **定义**：字符串类型是MySQL为了存储不同类型的字符串，并且考虑到存储空间利用率以及数据访问效率而进行分拆管理的类型。MySQL字符串类型分为五种：定长字符串（char），变长字符串（varchar），文本字符串（text/blob），枚举字符串（enum）和集合字符串（set）



1. 定长字符串char：语法char(L)，即事先确认数据的存储长度L字符数，然后存储的实际数据不能超过指定长度，L字符数不超过255

```Mysql
mysql> create table my_char(
    -> id char(18)
    -> )charset utf8;
Query OK, 0 rows affected (0.40 sec)
```

定长字符串应用场景是确定某项数据的长度都是某个固定字符数，如身份证号码

```Mysql
mysql> insert into my_char values('51031019001010101X');
Query OK, 1 row affected (0.29 sec)
```

2. 变长字符串varchar：语法varchar(L)，即实现确认数据可能出现的最大长度L字符数，存储的实际数据不能超过指定长度，L字符数**理论值**为65535

```Mysql
mysql> create table my_varchar(
    -> name varchar(5)
    -> )charset utf8;
Query OK, 0 rows affected (0.46 sec)
```

变长字符串应用场景是某个数据长度可变，但是有最长区间字符数

```Mysql
mysql> insert into my_varchar values('迪丽热巴');
Query OK, 1 row affected (0.09 sec)

mysql> insert into my_varchar values('张三');
Query OK, 1 row affected (0.13 sec)
```

3. char与varchar对比

* char和varchar都是指定长度存储字符串数据
* char存储是数据长度差不多的字段，varchar是数据长度有差距的字段
* char占据的实际存储空间是由L和字符集共同确定，即L * 字符集对应字节数（数据不够的时候会使用空格填充，有空间浪费）；varchar是根据实际存储的数据计算存储空间，即实际存储字符 * 字符集对应字节数
* varchar因为根据数据长度来确定实际占用空间，因此需要一个额外的空间存储数据长度，规则是256个字符以内需要1个字节，超过256需要2个字节
* 理论上来讲，虽然char可以存储255个字符，varchar可以达到65535个字符，但是实际上固定长度数据并不多见，因此varchar的使用比char要常见的多

**注意**：varchar的理论长度是65535个字符，但是因为MySQL规定**记录长度**（所有字段占用的空间）不能超过**65535个字节**，因而且varchar还需要额外1~2个字节存储数据长度，所以永远达不到理论值。另外，字符还涉及到字符集问题，如GBK占用2个字节存储一个汉字，UTF-8占用3个字节存储一个汉字，所以对应的实际存储长度也会相应的缩短（GBK不能超过32767，UTF-8不能超过21845）。



4. 文本字符串text/blob：语法text/blob，是指存储较大的数据（通常是超过varchar的范围），其中text表示字符文本，而blob表示二进制文本。text和blob都有四种方式存储，而且都是自适应（不需要判定长度去选择哪种）

* TINYTEXT 可变长度，最多 255 个字符 
* TEXT 可变长度，最多 65535 个字符  
* MEDIUMTEXT 可变长度，最多 16777215（2^24 - 1）个字符  
* LONGTEXT 可变长度，最多 4294967295（2^32 - 1）（4G）个字符 
* TINYBLOB可变长度，最多255个字节
* BLOB可变长度，最多65535个字节
* MEDIUMTEXT可变长度，最多 16777215（2^24 - 1）个字节
* LONGBLOB可变长度，最多 4294967295（2^32 - 1）（4G）个字节

```Mysql
mysql> create table my_text(
    -> t1 text,						#通常指定text即可
    -> b1 blob
    -> )charset utf8;
Query OK, 0 rows affected (0.75 sec)
```

**注意**：text本身的数据不占用记录长度，所以能够存储较大数据

5. varchar、text和blob对比

* 三者都是变长存储数据
* varchar和text是存储字符，而blob存储的是字节
* varchar存储收MySQL记录长度限制（达不到理论值），text和blob不受限制
* varchar需要事先指定长度且数据不能超过长度，text和blob会自动根据数据调整长度



6. 枚举enum：语法enum(数据1,数据2...数据N)，使用1~2个字节存储，最多可以在enum中设定65535个数据，是一种提前规范可能出现的数据的字符串。允许设计人员将字段可能出现的数据罗列出来，然后在数据录入时只能选择列表数据中的某一个具体数据

```Mysql
mysql> create table my_enum(
    -> gender enum('男','女','保密')
    -> )charset utf8;
Query OK, 0 rows affected (0.68 sec)
```

枚举字段类似于单选框，设定的值在实际的选择时只能选择其中任意一个

```Mysql
mysql> insert into my_enum values('女');
Query OK, 1 row affected (0.43 sec)
```

**注意**：枚举是使用1~2个字节存储数据，但是实际上可以看出数据本身是字符串，那么1~2个字节根本不够存储。enum的存储原理是建立枚举数据与数值之间的映射表

| 枚举数据 | 映射值         |
| -------- | -------------- |
| 数据1    | 1              |
| 数据2    | 2              |
| ...      | ...            |
| 数据N    | N（小于65535） |

所以，在实际存储的时候，字段中本质存储的是映射值数字，这也就能解释为什么能够利用2个字节达到65535个数据列表了。可以使用select 枚举字段 + 0 from 枚举字段表；来检验字段是否是数值（+运算会自动类型转换）

```Mysql
mysql> select gender,gender + 0 from my_enum;
+--------+------------+
| gender | gender + 0 |
+--------+------------+
| 女     |          2 |
+--------+------------+
1 row in set (0.44 sec)
```

正是因为enum存储的本身是实际是数值映射，因此在进行数据插入的时候，可以直接使用对应的映射关系的数值来进行数据插入

```Mysql
mysql> insert into my_enum values(3);
Query OK, 1 row affected (0.08 sec)
```



7. 集合set：语法set(数据1,数据2...数据N)，使用1~8个字节存储，最多可以在set中设置64个数据，也是一种提前规范可能出现的数据的字符串。允许设计人员将字段可能出现的数据罗列出来，然后在数据录入时可以选择列表数据中的某一个或者多个具体数据

```Mysql
mysql> create table my_set(
    -> ball_hobby set('篮球','足球','羽毛球','乒乓球','保龄球','桌球','橄榄球','网球')
    -> )charset utf8;
Query OK, 0 rows affected (0.34 sec)
```

枚举字段类似于多选框，设定的值在实际选择的时候可以任选多个：使用一个字符串，中间用逗号分隔

```Mysql
mysql> insert into my_set values('篮球,桌球,网球');
Query OK, 1 row affected (0.08 sec)
```

**注意**：集合set是使用1~8个字节存储数据，具体使用空间是系统自动通过计算set中元素的个数来设定，每满8个元素使用一个位存储。set中数据与字节的映射关系是：具体位上的数据对应一个字节中的比特位

| 集合数据 | 映射位   |
| -------- | -------- |
| 篮球     | 00000001 |
| 足球     | 00000010 |
| ...      | ...      |
| 网球     | 10000000 |

所以，在实际存储的时候，字段中存储的本质是选中的数据组成对应的字节数值转换的十进制数值。如上述存储选中的是篮球、桌球和网球，分别对应的是一个字节中的第1位、6位和8位，那么被选中位为1，未被选中位为0，结果为：10000101，然后倒置过来10100001，再转换成十进制为：1 * 2 ^ 7 + 1 * 2 ^ 5 + 1 * 2 ^ 0 = 128 + 32 + 1 = 161 

```Mysql
mysql> select ball_hobby,ball_hobby + 0 from my_set;
+----------------+----------------+
| ball_hobby     | ball_hobby + 0 |
+----------------+----------------+
| 篮球,桌球,网球  |            161 |
+----------------+----------------+
1 row in set (0.00 sec)
```

同样的，因为集合实际存储的是数值，那么在进行数据插入的时候，也可以使用数值进行代替

```Mysql
mysql> insert into my_set values(255); #表示一个字节全选
Query OK, 1 row affected (0.42 sec)
```



> **总结**：

1. MySQL在字符串存储的时候提供了多种选择：char，varchar，text/blob，enum和set
2. char比较浪费空间，但是查询效率最快
3. varchar比text/blob效率高，能用varchar的地方不用text，但是要考虑varchar的实际范围
4. 二进制数据存储必须使用blob（通常涉及blob的是文件信息，而文件信息实际开发中更多使用varchar保存文件名字和路径，而文件存储到某个文件夹中）
5. 数据列表可以使用enum和set实现，enum代表单选框，set代表多选框
6. enum和set本身可以规范数据，保证字段不会出现规定数据以外的其他数据
7. enum和set创建过程中会建立映射关系，因此字段实际存储使用映射数值
8. enum和set对应的字段可以用数值进行数据插入（强烈不建议）



***



## **二、MySQL字段属性**



> **思考**：字段类型规范了表中字段对应的数据的基本格式，但是有些数据还有一些特殊的性质，如不能为空之类的，这块MySQL能不能实现呢？

> **引入**：MySQL为了方便用户进行数据管理规范，在除了使用字段类型进行数据限定以外，还额外增加了一些属性来让数据变得更加规范和安全。



### **1.MySQL字段属性【掌握】**

> **定义**：MySQL字段属性，即在字段类型限定的基础之上，使用一些额外的属性命令对数据加以限定，确保数据的有效性和安全性

1. 在MySQL中，给字段增加了额外的几种属性

* NULL/NOT NULL：数据为空或者不能为空
* default：默认值设定
* primary key：主键唯一性（索引）
* auto_increment：自动增长
* unique key：数据唯一性（索引）
* comment：字段描述



> **总结**：所有的字段类型都是主动或者被动的带有一些字段属性的，在实际开发中需要了解每一种属性的特点，从而能够根据需求来选择性的设定某些属性的值。



***



> **思考**：在PHP中我们知道，数据很多时候是不允许用户直接设置空Null的，因为这样的数据对于系统而言没有任何意义，那MySQL中知否也需要考虑这样的数据呢？

> **引入**：MySQL作为数据库是专门负责数据的存储和管理的，数据对于企业而言是非常重要的，尤其是在大数据分析和数据挖掘时代，因此MySQL中对重要数据通常都需要进行非空设定。



### **2.NULL/NOT NULL属性【掌握】**

> **定义**：NULL/NOT NULL，即对数据进行空或者非空设定，默认字段是允许为空的，但是如果数据都为空，那么这样的数据就没什么价值，因此大部分的时候需要使用NOT NULL来进行数据限定

1. 允许为空：默认情况下，不对字段类型进行任何属性控制的话，基本都是允许为空的

```Mysql
mysql> create table my_null1(
    -> id int,
    -> name varchar(10),
    -> price decimal(8,2),
    -> deal_date date
    -> )charset utf8;
Query OK, 0 rows affected (0.70 sec)

mysql> desc my_null1;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| id        | int(11)      | YES  |     | NULL    |       |
| name      | varchar(10)  | YES  |     | NULL    |       |
| price     | decimal(8,2) | YES  |     | NULL    |       |
| deal_date | date         | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
4 rows in set (0.01 sec)
```

2. 不允许为空：绝大部分情况下，数据为空没有意义，因此我们在进行字段设置的时候，都需要通过not Null来限定数据不能为空

```Mysql
mysql> create table my_null2(
    -> id int not null,
    -> name varchar(10) not null,
    -> price decimal(8,2) not null,
    -> deal_date date not null
    -> )charset utf8;
Query OK, 0 rows affected (0.28 sec)
```

3. NOT NULL不允许为空的字段在进行数据插入的时候，就不能插入NULL数据，或者在进行数据插入时跳过相关字段（不考虑设定默认值的情况）

```Mysql
mysql> insert into my_null1 values(null,null,null,null);	#my_null1表允许所有字段为空
Query OK, 1 row affected (0.09 sec)

mysql> insert into my_null2 values(null,null,null,null);	#my_null2表所有字段都不允许为空
ERROR 1048 (23000): Column 'id' cannot be null			    #错误：提示id字段不能为空
```



> **总结**：因为数据为空NULL就意味着数据没有价值，因此在实际开发中，通常都会指定字段属性not null来保证数据的有效性和价值



***



> **思考**：当用户需要提交很多数据的时候，有些不重要的数据用户可以选择性不填，这些数据通常不够重要或者价值不大，那么这种情况下，我们在组织SQL指令插入数据的时候，还是要对所有字段进行补充操作吗？

> **引入**：当某些数据如果不是特别重要，或者可以归类为某些常用的信息的时候，MySQL提供了一种方式可以让开发者在不操作的情况下也会有数据，那就是默认值。

### **3.Default属性【掌握】**

> **定义**：default默认值，就是当用户在进行数据操作的时候，可以通过强制或者不操作某个字段，而某个字段依然可以按照设定的规则那样得到相应的数据。

1. 每个字段本身都有默认值，在不指定的情况下默认值通常都是NULL

```Mysql
mysql> desc my_null2;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| id        | int(11)      | NO   |     | NULL    |       |
| name      | varchar(10)  | NO   |     | NULL    |       |
| price     | decimal(8,2) | NO   |     | NULL    |       |
| deal_date | date         | NO   |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
4 rows in set (0.01 sec)
#虽然该表设定所有字段都不能为空，但是其默认值Default依然是NULL
```



2. 默认值的设定就是在某个可能出现某个固定数据的字段后使用default 默认值 来实现

```Mysql
mysql> create table my_default(
    -> id int not null,
    -> gender enum('男','女','保密') default '保密'
    -> )charset utf8;
Query OK, 0 rows affected (0.68 sec)
```

3. 一旦设定了默认值之后，那么该字段进行数据插入的时候，就可以不用特别指定数据，系统会自动填充。但是当字段有数据插入的时候，默认值不会生效。

```Mysql
mysql> insert into my_default values(1,'男');	#指定数据，不会触发默认值
Query OK, 1 row affected (0.09 sec)
mysql> insert into my_default (id) values(2);	 #默认值字段不给数据，系统自动调用默认值
Query OK, 1 row affected (0.08 sec)
```

4. 数据在有了默认值的时候，有的时候一条SQL指令可能会因为想用某个默认值而不得不指定字段列表，这种操作将会非常麻烦且容易出错，因此也可以在进行数据插入的值部分时，使用Default关键字强制使用默认值

```Mysql
mysql> insert into my_default values(3,default);
Query OK, 1 row affected (0.43 sec)
```



> **总结**

1. default 值;可以设定某个字段常见数据的默认值，从而不需要开发者每次都去组织数据
2. default关键字可以用在数据插入insert语句的值列表中，强制触发默认值
3. 一般情况下，不设定默认值时，字段的默认值通常都是NULL



***



> **思考**：数据在进入数据库之后，更多的操作是查询操作。在查询操作的时候，怎么能够从那么多数据中快速的取出一条目标数据呢？

> **引入**：想要达到数据的快速获取，就需要对数据建立一个索引（目录）关系，通过关系进行数据获取就会效率很高。而索引关系的建立最有效率的一种方式，就是让每条记录本身具有唯一性。因此，MySQL中有一种能够让某个字段数据保证唯一的方式，叫做主键。

### **4.primary key属性【掌握】**

> **定义**：primary key也叫主键，是一种有效保障所设定字段所有数据具有唯一性的属性，也是最高效的一种数据表目录。

1. 主键可以通过在具体字段后使用primary key关键字实现

```Mysql
mysql> create table my_primary1(
    -> id char(18) primary key,
    -> name varchar(10) not null,
    -> age tinyint unsigned not null
    -> )charset utf8;
Query OK, 0 rows affected (0.72 sec)
```

2. 主键字段对应的数据不允许重复

```Mysql
mysql> insert into my_primary1 values('510310199910101010','Jim',18);
Query OK, 1 row affected (0.43 sec)

mysql> insert into my_primary1 values('510310199910101010','Tom',20);
ERROR 1062 (23000): Duplicate entry '510310199910101010' for key 'PRIMARY'	#错误：id字段重复
```

3. 主键字段默认不能为空

```Mysql
mysql> desc my_primary1;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | char(18)    | NO   | PRI | NULL    |       |	#PRI表示primary key，Null字段自动为NO
| name  | varchar(10) | NO   |     | NULL    |       |
| age   | int(11)     | NO   |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

4. 主键的创建也可以通过在所有字段后使用primary key（主键字段名）来设定

```MySQL
mysql> create table my_primary2(
    -> id char(18),
    -> name varchar(10) not null,
    -> age tinyint unsigned not null,
    -> primary key(id)
    -> )charset utf8;
Query OK, 0 rows affected (0.66 sec)
```

5. 主键可以是复合主键，即由多个字段共同组成一个主键，复合主键是通过在表所有字段后使用primary key(字段1,字段2...)组成，通常最多主键由两个字段组成

```Mysql
mysql> create table my_primary3(
    -> id char(18),
    -> name varchar(10),
    -> age tinyint unsigned not null,
    -> primary key(id,name)
    -> )charset utf8;
Query OK, 0 rows affected (0.75 sec)
```

6. 主键删除：如果一张表中已经设定主键，且不再需要主键了，那么可以通过修改表实现：alter table 表名 drop primary key;

```Mysql
mysql> alter table my_primary3 drop primary key;
Query OK, 0 rows affected (1.25 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

7. 追加主键：如果一张表设计之初没有主键，但是后期为了查询效率追加主键，也可以通过修改表实现：alter table 表名 add primary key(主键字段列表);（可以是复合主键）

```Mysql
mysql> alter table my_primary3 add primary key(id);
Query OK, 0 rows affected (1.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

**注意**：追加主键的字段要求对应字段的数据必须是唯一的且不能有NULL数据

8. 逻辑主键：通常在进行表设计的时候，主键是为了保障数据的唯一性，而且主键的更重要的使用方式是为了方便数据的高效查询。所以在创建主键的时候，通常不是针对具体的业务数据来设定主键，而是设定一个对应的无实际业务含义的字段（数字），这种主键称之为逻辑主键

```Mysql
mysql> create table my_primary4(
    -> id int primary key,
    -> id_card char(18) not null,
    -> name varchar(10) not null,
    -> age tinyint unsigned not null
    -> )charset utf8;
Query OK, 0 rows affected (0.65 sec)
```



> **总结**

1. 主键是一种保证表中数据具有唯一性的手段
2. 主键的方式可以在字段后跟primary key关键字，或者在所有字段后使用primary key(字段名)，抑或在创建表后使用修改表结构alter table 表名 add primary key(字段名)实现
3. 主键通常是使用逻辑主键实现
4. 主键会自动让对应字段的数据不能为空（NOT NULL）
5. 主键的作用不仅仅是让数据保证唯一性，还会让表的查询效率提升（通过主键条件查询）
6. 一张表只能有一个主键（除非是复合主键）



***



> **思考**：使用逻辑主键后，对应的字段数据是整数，这个数据每次都需要开发人员自己去实现，尤其是想保证数据是连续的情况下，还要在数据插入前先查看或者获取最大的主键，这样操作不是很麻烦吗？

> **引入**：如果说逻辑主键每次进行数据插入操作的时候，需要提前进行数据获取，那么操作的确会变得非常繁琐。针对逻辑主键这一特点，MySQL提供了一种机制，能够让系统自动创建有序数据来实现填充，这就是自增长属性。

### **5.auto_increment属性【掌握】**

> **定义**：auto_increment自增长，是针对某些特定情况下的==整数==，通过设定auto_increment属性，在进行数据插入操作的时候，无需手动指定其值，系统会自动找到当前最大的值然后+1实现。

1. 自增长必须搭配==整数类型==且对应字段必须存在==索引==（就是Key字段必须有值），所以通常自增长是配逻辑主键

```Mysql
mysql> create table my_auto(
    -> id int primary key auto_increment,
    -> name varchar(10) not null,
    -> age tinyint unsigned not null
    -> )charset utf8;
Query OK, 0 rows affected (0.31 sec)
```

2. 当字段有自增长属性后，可以在进行数据插入的时候，使用null替代对应字段（或者不指定相应字段）

```Mysql
mysql> insert into my_auto values(null,'Lily',19);
Query OK, 1 row affected (0.41 sec)
```

3. 自增长设定后，默认是从1开始自增的

```Mysql
mysql> insert into my_auto values(null,'Lily',19);
Query OK, 1 row affected (0.41 sec)

mysql> select * from my_auto;
+----+------+-----+
| id | name | age |
+----+------+-----+
|  1 | Lily |  19 |
+----+------+-----+
1 row in set (0.00 sec)
```

4. 一张表只能拥有一个自增长字段，自增长字段绑定后会出现在表选项中，可以通过show create table 表名查看

```Mysql
mysql> show create table my_auto\G
*************************** 1. row ***************************
       Table: my_auto
Create Table: CREATE TABLE `my_auto` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(10) NOT NULL,
  `age` tinyint(3) unsigned NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8
1 row in set (0.05 sec)
# AUTO_INCREMENT=2表示下一个值自动填充的话就是2
```

5. 自增长也可以被手动数据填充：即插入数据时明确指定自增长字段数据

```Mysql
mysql> insert into my_auto values(10,'Han',20);
Query OK, 1 row affected (0.44 sec)
```

**注意**：自增长是根据表中已有的最大值自动加1操作，因此当手动插入数据偏大时，系统下次自动操作的值也是从目前最大的值+1

```Mysql
mysql> insert into my_auto values(null,'Lilei',21);
Query OK, 1 row affected (0.43 sec)

mysql> select * from my_auto;
+----+-------+-----+
| id | name  | age |
+----+-------+-----+
|  1 | Lily  |  19 |
| 10 | Han   |  20 |
| 11 | Lilei |  21 |
+----+-------+-----+
3 rows in set (0.00 sec)
```

6. 自增长可以通过后期表字段进行维护：增删改

```Mysql
mysql> alter table 表名 modify 字段名 字段类型 [字段属性] auto_increment; #新增操作
#假设原有表中id为主键,没有自增长(逻辑主键)
alter table my_auto modify id int auto_increment;

mysql> alter table 表名 modify 字段名 字段类型 [字段属性];				#清除自增长
#假设原有ID有自动增长属性
alter table my_auto modify id int;

mysql> altr table 表名 auto_increment = 新值;					   #修改自增长下个值（只能大于当前最大的）
alter table my_auto  auto_increment = 5;	#改小不行
alter table my_auto  auto_increment = 15;	#改大可以
```

7. MySQL中自增长的控制：起始值和步长（每次变化多少），都是在MySQL系统中通过变量控制的，可以通过show variables like ‘auto_increment%';查看

```Mysql
mysql> show variables like 'auto_increment%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| auto_increment_increment | 1     |
| auto_increment_offset    | 1     |
+--------------------------+-------+
2 rows in set, 1 warning (0.52 sec)
```

**注意**：该值可以通过set 变量名 = 新值;改变，只是通常不会去改变



> **总结**

1. auto_increment能够使得字段会自动增加值，而不需要手动操作
2. auto_increment必须匹配整型字段以及字段本身必须是索引（通常是逻辑主键）
3. auto_increment的触发可以通过NULL来实现
4. auto_increment的初始值是1，步长也是1（MySQL变量控制）
5. auto_increment可以在后期通过表选项修改值
6. auto_increment一张表中只能设定一个字段



***



> **思考**：一张表最多有一个主键，而且主键通常还是逻辑主机与业务数据无关，那么如果还有其他字段也需要保证数据的唯一性，难道就没有其他办法了吗？

> **引入**：在实际开发工作中，有些内容是需要唯一保障的，如用户登录名、联系方式和邮箱等，这些都需要数据唯一性保证安全。主键作为一个一张表只能有一个的情况下，显然满足不了数据这块的要求。因此MySQL提供了另外一种不限量的唯一控制，那就是唯一键。

### **6. unique key属性【掌握】**

> **定义**：unique key唯一键，和primary key主键类似，目的就是保障对应字段的所有数据具有唯一性

1. 在字段后使用unique关键字描述字段

```Mysql
mysql> create table my_unique1(
    -> id int primary key auto_increment,
    -> username varchar(20) unique,
    -> email varchar(50) not null
    -> )charset utf8;
Query OK, 0 rows affected (0.75 sec)
```

2. unique唯一键的效果就是当数据插入重复的时候会报错

```Mysql
mysql> insert into my_unique1 values(null,'username1','user1@qq.com');
Query OK, 1 row affected (0.42 sec)

mysql> insert into my_unique1 values(null,'username1','user2@qq.com');
ERROR 1062 (23000): Duplicate entry 'username1' for key 'username'		#错误：username1已经存在
```

3. unique唯一键不会像主键一样强势，默认允许字段为空，且NULL数据不存在重复问题

```Mysql
mysql> desc my_unique1;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |	#主键不允许为NULL
| username | varchar(20) | YES  | UNI | NULL    |                |	#唯一键允许字段为NULL
| email    | varchar(50) | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```

```Mysql
mysql> insert into my_unique1 values(null,null,'user2@qq.com'),(null,null,'user3@qq.com');
Query OK, 2 rows affected (0.43 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

4. unique唯一键允许一张表中多个字段使用

```Mysql
mysql> create table my_unique2(
    -> id int primary key auto_increment,
    -> username varchar(20) unique,
    -> email varchar(50) unique
    -> )charset utf8;
Query OK, 0 rows affected (0.76 sec)
```

5. unique也可以在所有字段后使用unique key(字段名列表)一样设置唯一键，多个字段也可以组成复合唯一键

```Mysql
mysql> create table my_unique3(
    -> id int primary key auto_increment,
    -> username varchar(20) not null,
    -> email varchar(50) not null,
    -> unique key `username_index` (username),	#`username_index`为指定的名字
    -> unique key(email)				   	  #默认名字就是字段本身
    -> )charset utf8;
Query OK, 0 rows affected (0.43 sec)


#复合唯一键
mysql> create table my_unique4(
    -> id int primary key auto_increment,
    -> username varchar(20) not null,
    -> email varchar(50) not null,
    -> unique key `username_email` (username,email)
    -> )charset utf8;
Query OK, 0 rows affected (0.04 sec)
```

6. 唯一键删除：unique唯一键不像primary key那样可以直接删除，因为unique key一张表可以有多个，系统不清楚删除谁。unique key在表中被当做一个**普通索引**，因此删除方式为：alter table 表名 drop index 唯一键对应的索引名字

```Mysql
mysql> alter table my_unique3 drop index username_index;
Query OK, 0 rows affected (0.29 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

7. 唯一键修改：唯一键没有提供修改方式，可以通过先删除唯一键，然后再增加唯一键
8. 唯一键增加：alter table 表名 add unique (字段列表)；

```Mysql
mysql> alter table my_unique3 add unique(username);
Query OK, 0 rows affected (0.29 sec)
Records: 0  Duplicates: 0  Warnings: 0
```



> **总结**

1. 唯一键可以通过字段后unique、所有字段后unique key [名字] (字段列表)、alter table 表名 add unique(字段列表)三种方式实现
2. 唯一键的效果是能够让对应字段数据保证唯一性
3. 唯一键允许字段为NULL，且不把NULL作为唯一数据判定（如果确定数据不需要为空应该使用NOT NULL属性）
4. 唯一键的删除没有特殊性，使用普通索引删除方式alter table 表名 drop index 唯一键名字; （名字默认是字段名）
5. 唯一键可以在表中使用多次（也可以是复合唯一键）



***



> **思考**：进入企业后有部分情况是别人已经开发好的系统，那么考虑到一些个人英文能力问题，有的人设计的表可能字段表示的内容不是很清晰，这种时候应该怎么办呢？

> **引入**：表字段的设计通常是见名知意，这是行业的规矩。但是不乏有人在设计表的时候比较随意，导致要使用该表的人就比较痛苦。为了解决这一问题，MySQL建议在字段后增加对应的字段描述，以说明字段的数据内容。

### **7.comment属性【了解】**

> **定义**：comment说明的意思，意在给字段增加相应的==文字描述==，更清晰的表达设计者的意图。字段的描述不会对字段或者数据本身产生任何影响，只是方便开发者进行查阅。

1. 在字段后使用comment '描述说明'

```Mysql
mysql> create table my_comment(
    -> id int primary key auto_increment comment '逻辑主键',
    -> username varchar(10) not null unique comment '用户名不能为空且唯一',
    -> age tinyint unsigned not null comment '年龄不为空，区间为0-255'
    -> )charset utf8;
Query OK, 0 rows affected (0.74 sec)
```

2. 字段说明不会在其他任何地方看到，只能通过查看表创建语句查看

```Mysql
mysql> show create table my_comment\G
*************************** 1. row ***************************
       Table: my_comment
Create Table: CREATE TABLE `my_comment` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '逻辑主键',
  `username` varchar(10) NOT NULL COMMENT '用户名不能为空且唯一',
  `age` tinyint(3) unsigned NOT NULL COMMENT '年龄不为空，区间为0-255',
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



> **总结**：字段说明是为了方便开发者阅读设计者的想法，以及明确字段索要表示的内容。作为一名优秀的开发者，应当在尽量增加字段说明以便于团队协作开发。





