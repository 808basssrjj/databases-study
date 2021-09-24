# 一. 存储过程

含义：一组预先编译好的SQL语句的集合，理解成批处理语句

- 提高代码的重用性

- 简化操作

- 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率*

  

## 1、创建存储过程

```mysql
create procedure 存储过程名(参数列表)

begin
	存储过程体（一组合法的SQL语句）
end
```

注意：

1、参数列表包含三部分   参数模式 参数名 参数类型

举例：in stuname varchar(20)

参数模式：

- in：该参数可以作为输入，也就是该参数需要调用方传入值
- out：该参数可以作为输出，也就是该参数可以作为返回值
- inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值

2、如果存储过程体仅仅只有一句话，begin end可以省略

存储过程体中的每条sql语句的结尾要求必须加分号。

存储过程的结尾可以使用 delimiter 重新设置

语法：delimiter 结束标记

案例：delimiter $



## 2、调用存储过程

CALL 存储过程名(实参列表);

1. 创建空参列表的存储过程

   ```mysql
   #1.空参列表
   #案例：插入到admin表中五条记录
   DELIMITER $
   CREATE PROCEDURE myp1()
   BEGIN
   	INSERT INTO admin(username,`password`) 
   	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
   END $
   #调用
   CALL myp1()$
   ```

2. 创建带 in 模式参数的存储过程

   ```mysql
   #2.创建带in模式参数的存储过程
   #案例1：创建存储过程实现 根据女神名，查询对应的男神信息
   CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
   BEGIN
   	SELECT bo.*
   	FROM boys bo
   	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
   	WHERE b.name=beautyName;
   END $
   #调用
   CALL myp2('柳岩')$
   
   
   #案例2 ：创建存储过程实现，用户是否登录成功
   CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
   BEGIN
   	DECLARE result INT DEFAULT 0;#声明并初始化
   	SELECT COUNT(*) INTO result#赋值
   	FROM admin
   	WHERE admin.username = username
   	AND admin.password = PASSWORD;
   	
   	SELECT IF(result>0,'成功','失败');#使用
   END $
   #调用
   CALL myp3('张飞','8888')$
   ```

3. 创建带 out 模式参数的存储过程

   ```mysql
   #案例1：根据输入的女神名，返回对应的男神名
   CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
   BEGIN
   	SELECT bo.boyname INTO boyname
   	FROM boys bo
   	RIGHT JOIN
   	beauty b ON b.boyfriend_id = bo.id
   	WHERE b.name=beautyName ;
   END $
   #调用
   set @bname
   CALL myp6('小昭',@bname)$
   select @bname$
   
   #案例2：根据输入的女神名，返回对应的男神名和魅力值
   CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
   BEGIN
   	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
   	FROM boys 
   	RIGHT JOIN
   	beauty b ON b.boyfriend_id = boys.id
   	WHERE b.name=beautyName ;
   END $
   #调用
   CALL myp7('小昭',@name,@cp)$
   SELECT @name,@cp$
   ```

4. 创建带 inout 模式参数的存储过程

   ```mysql
   #案例1：传入a和b两个值，最终a和b都翻倍并返回
   CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
   BEGIN
   	SET a=a*2;
   	SET b=b*2;
   END $
   
   #调用
   SET @m=10$
   SET @n=20$
   CALL myp8(@m,@n)$
   SELECT @m,@n$
   ```

   

## 3、删除存储过程

```mysql
#语法：
drop procedure 存储过程名;
```



## 4、查看存储过程

```mysql
#语法：
show create procedure 存储过程名;
```





# 二. 函数

存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新

函数：有且仅有1 个返回，适合做处理数据后返回一个结果(查询)



## 1、创建函数

```mysql
create function函数名(参数列表) returns 返回类型
begin
	函数体
end
```

注意：

1. 参数列表 包含两部分：参数名 参数类型

2. 函数体：肯定会有return语句，如果没有会报错，如果return语句没有放在函数体的最后也不报错，但不建议

3. 函数体中仅有一句话，则可以省略begin end

4. 使用 delimiter语句设置结束标记



## 2、调用函数

SELECT 函数名(参数列表) 

1. 无参有返回

   ```mysql
   #案例：返回公司的员工个数
   CREATE FUNCTION myf1() RETURNS INT
   BEGIN
   	DECLARE c INT DEFAULT 0;#定义局部变量
   
   	SELECT COUNT(*) INTO c#赋值
   	FROM employees;
   
   	RETURN c;
   END $
   
   SELECT myf1()$
   ```

2. 有参有返回

   ```mysql
   #案例1：根据员工名，返回它的工资
   CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
   BEGIN
   	SET @sal=0;#定义用户变量 
   
   	SELECT salary INTO @sal   #赋值
   	FROM employees
   	WHERE last_name = empName;
   
   	RETURN @sal;
   END $
   
   SELECT myf2('k_ing') $
   
   
   #案例2：根据部门名，返回该部门的平均工资
   CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
   BEGIN
   	DECLARE sal DOUBLE ;
   
   	SELECT AVG(salary) INTO sal
   	FROM employees e
   	JOIN departments d ON e.department_id = d.department_id
   	WHERE d.department_name=deptName;
   
   	RETURN sal;
   END $
   
   SELECT myf3('IT')$
   ```

   

## 3、查看函数

```mysql
SHOW CREATE FUNCTION myf3;
```



## 4、删除函数

```mysql
DROP FUNCTION myf3;
```





