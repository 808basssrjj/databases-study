# 一. 变量

## 1、系统变量

说明：变量由系统定义，不是用户定义，属于服务器层面

注意：全局变量需要添加global关键字，会话变量需要添加session关键字，如果不写，默认会话级别

1. 查看所有系统变量

   show global|【session】variables;

2. 查看满足条件的部分系统变量

   show global|【session】 variables like '%char%';

3. 查看指定的系统变量的值

   select @@global|【session】系统变量名

4. 为某个系统变量赋

   方式一    set global|【session】系统变量名=值

   方式二    set @@global|【session】系统变量名=值;



1》全局变量

作用域：针对于所有会话（连接）有效，但不能跨重启

1. 查看所有全局变量

   SHOW GLOBAL VARIABLES;

2. 查看满足条件的部分系统变量

   SHOW GLOBAL VARIABLES LIKE '%char%';

3. 查看指定的系统变量的值

   SELECT @@global.autocommit;

4. 为某个系统变量赋值

   SET @@global.autocommit=0;

   SET GLOBAL autocommit=0;

2》会话变量

作用域：针对于当前会话（连接）有效

1. 查看所有会话变量

   SHOW SESSION VARIABLES;

2. 查看满足条件的部分会话变量

   SHOW SESSION VARIABLES LIKE '%char%';

3. 查看指定的会话变量的值

   SELECT @@autocommit;

   SELECT @@session.tx_isolation;

4. 为某个会话变量赋值

   SET @@session.tx_isolation='read-uncommitted';

   SET SESSION tx_isolation='read-committed';





## 2、自定义变量

说明：变量由用户自定义，而不是系统提供的



1》用户变量

作用域：针对于当前会话（连接）有效，作用域同于会话变量

1. 声明并初始化

   SET @变量名=值;

   SET @变量名:=值;

   SELECT @变量名:=值;

2. 赋值（更新变量的值）

   方式一：

   ​	SET @变量名=值;

   ​    SET @变量名:=值;

   ​    SELECT @变量名:=值;

   方式二：

   ​    SELECT 字段 INTO @变量名

   ​    FROM 表;

3. 使用（查看变量的值）

   SELECT @变量名;

2》局部变量

作用域：仅仅在定义它的begin end块中有效

应用在 begin end中的第一句话

1. 声明

   DECLARE 变量名 类型;

   DECLARE 变量名 类型 【DEFAULT 值】;

2. 赋值更新变量的值）

   方式一：

   ​	SET 局部变量名=值;

   ​	SET 局部变量名:=值;

   ​	SELECT 局部变量名:=值;

   方式二：

     SELECT 字段 INTO 具备变量名

     FROM 表;

3. 使用（查看变量的值）

   SELECT 局部变量名;





# 二. 流程控制

## 1、分支结构

1. if函数

   语法：

   ```mysql
   if(条件,值1，值2)
   ```

   功能：实现双分支

   应用在begin end中或外面

2. case结构

   语法：

   ```mysql
   #情况1：类似于switch
   case 变量或表达式
   when 值1 then 语句1;
   when 值2 then 语句2;
   ...
   else 语句n;
   end
   
   #情况2：
   case
   when 条件1 then 语句1;
   when 条件2 then 语句2;
   ...
   else 语句n;
   end
   ```

   应用在begin end 中或外面

3. if结构

   语法：

   ```mysql
   if 条件1 then 语句1;
   elseif 条件2 then 语句2;
   ...
   else 语句n;
   end if;
   ```

   功能：类似于多重if

   只能应用在begin end 中

   

案例:

```mysql
#案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D
CREATE FUNCTION test_if(score FLOAT) RETURNS CHAR
BEGIN
	DECLARE ch CHAR DEFAULT 'A';
	IF score>90 THEN SET ch='A';
	ELSEIF score>80 THEN SET ch='B';
	ELSEIF score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END IF;
	RETURN ch;
END $

SELECT test_if(87)$


#案例2：创建存储过程，如果工资<2000,则删除，如果5000>工资>2000,则涨工资1000，否则涨工资500
CREATE PROCEDURE test_if_pro(IN sal DOUBLE)
BEGIN
	IF sal<2000 THEN DELETE FROM employees WHERE employees.salary=sal;
	ELSEIF sal>=2000 AND sal<5000 THEN UPDATE employees SET salary=salary+1000 WHERE employees.`salary`=sal;
	ELSE UPDATE employees SET salary=salary+500 WHERE employees.`salary`=sal;
	END IF;
END $

CALL test_if_pro(2100)$


#案例3：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D
CREATE FUNCTION test_case(score FLOAT) RETURNS CHAR
BEGIN 
	DECLARE ch CHAR DEFAULT 'A';
	
	CASE 
	WHEN score>90 THEN SET ch='A';
	WHEN score>80 THEN SET ch='B';
	WHEN score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END CASE;
	
	RETURN ch;
END $

SELECT test_case(56)$
```



## 2、循环结构

分类：while、loop、repeat

循环控制：

- iterate类似于 continue，继续，结束本次循环，继续下一次
- leave 类似于 break，跳出，结束当前所在的循环



1. while

   语法：

   ```mysql
   [标签:]while 循环条件 do
     循环体;
   end while[标签];
   ```

2. loop

   语法：

   ```mysql
   [标签:]loop
     循环体;
   end loop [标签:];
   ```

   可以用来模拟简单的死循环

3. repeat

   ```mysql
   [标签:]repeat
     循环体;
   until 结束循环的条件
   end repeat [标签:];
   ```



案例

```mysql
#1.没有添加循环控制语句
#案例：批量插入，根据次数插入到admin表中多条记录
DROP PROCEDURE pro_while1$
CREATE PROCEDURE pro_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('Rose',i),'666');
		SET i=i+1;
	END WHILE;
	
END $

CALL pro_while1(100)$


#2.添加leave语句
#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	a:WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		IF i>=20 THEN LEAVE a;
		END IF;
		SET i=i+1;
	END WHILE a;
END $

CALL test_while1(100)$


#3.添加iterate语句
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a:WHILE i<=insertCount DO
		SET i=i+1;
		IF MOD(i,2)!=0 THEN ITERATE a;
		END IF;
		
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		
	END WHILE a;
END $

CALL test_while1(100)$
```

