# 一.基础查询

## 1、查询表中的单个字段

```mysql
SELECT last_name FROM employees;
```

## 2、查询表中的多个字段

```mysql
SELECT last_name,salary,email FROM employees;
```

## 3、查询表中的所有字段

```mysql
SELECT
    `employee_id`,
    `first_name`,
    `last_name`,
    `phone_number`,
    `last_name`,
    `job_id`,
    `phone_number`,
    `job_id`,
    `salary`,
    `commission_pct`,
    `manager_id`,
    `department_id`,
    `hiredate` 
FROM
    employees ;
```

```mysql
SELECT * FROM employees;
```

## 4、查询常量值

```mysql
 SELECT 100;
 SELECT 'john';
```

## 5、查询表达式

```mysql
 SELECT 100%98;
```

## 6、查询函数

```mysql
SELECT VERSION();
```

## 7、起别名

```mysql
SELECT 100%98 AS 结果;
SELECT last_name AS 姓,first_name AS 名 FROM employees;
```

```mysql
SELECT last_name 姓,first_name 名 FROM employees;xxxxxxxxxx SELECT last_name 姓,first_name 名 FROM employees;SELECT 100%98 AS 结果;SELECT last_name AS 姓,first_name AS 名 FROM employees;
```

```mysql
SELECT salary AS "out put" FROM employees;
```

## 8、去重(distinct)

```mysql
SELECT DISTINCT department_id FROM employees;
```

## 9、+运算符

*mysql中的+号：*

*仅仅只有一个功能：运算符*

*select 100+90; 两个操作数都为数值型，则做加法运算*

*select '123'+90;只要其中一方为字符型，试图将字符型数值转换成数值型*

​     *如果转换成功，则继续做加法运算*

*select 'john'+90;  如果转换失败，则将字符型数值转换成0*

*select null+10; 只要其中一方为null，则结果肯定为null*

```mysql
SELECT CONCAT('a','b','c') AS 结果;

SELECT 
	CONCAT(last_name,first_name) AS 姓名
FROM
	employees;
```



# 二.条件查询

## 1、按条件表达式筛选

简单条件运算符：> < = != <> >= <=*

```mysql
#案例1：查询工资>12000的员工信息
SELECT *FROM employees WHERE salary>12000;
	
#案例2：查询部门编号不等于90号的员工名和部门编号
SELECT last_name, department_id FROM employees WHERE department_id<>90;
```



## 2、按逻辑表达式筛选

```mysql
#案例1：查询工资z在10000到20000之间的员工名、工资以及奖金
SELECT last_name, salary, commission_pct FROM employees WHERE salary>=10000 AND salary<=20000;

#案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT * FROM employees WHERE NOT(department_id>=90 AND  department_id<=110) OR salary>15000;
```



## 3、模糊查询

### 3.1 like

```mysql
#案例1：查询员工名中包含字符a的员工信息
select * from employees where last_name like '%a%';#abc

#案例2：查询员工名中第三个字符为e，第五个字符为a的员工名和工资
select last_name, salary FROM employees WHERE last_name LIKE '__e_a%';

#案例3：查询员工名中第二个字符为_的员工名 (转义)
SELECT last_name FROM employees WHERE last_name LIKE '_$_%' ESCAPE '$';
```

### 3.2 between and 

*①使用between and 可以提高语句的简洁度*

*②包含临界值*

*③两个临界值不要调换顺序*

```mysql
#案例1：查询员工编号在100到120之间的员工信息
SELECT * FROM employees WHERE employee_id BETWEEN 120 AND 100;
```

### 3.3  in

*①使用in提高语句简洁度*

  *②in列表的值类型必须一致或兼容*

  *③in列表中不支持通配符*

```mysql
#案例：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT last_name, job_id FROM employees WHERE job_id IN( 'IT_PROT' ,'AD_VP','AD_PRES');
```

### 3.4  is null

*=或<>不能用于判断null值*

*is null或is not null 可以判断null值*

```mysql
#案例1：查询没有奖金的员工名和奖金率
SELECT last_name, commission_pct FROM employees WHERE commission_pct IS NULL;

#案例2：查询有奖金的员工名和奖金率
SELECT last_name, commission_pct FROM employees WHERE commission_pct IS NOT NULL;
```

### 3.5  安全等于 <=>

```mysql
#案例1：查询没有奖金的员工名和奖金率
SELECT last_name, commission_pct FROM employees WHERE commission_pct <=>NULL;
	
#案例2：查询工资为12000的员工信息
SELECT last_name, salary FROM employees WHERE  salary <=> 12000;
```



IS NULL:仅仅可以判断NULL值，可读性较高，建议使用

<=>  :既可以判断NULL值，又可以判断普通的数值，可读性较低



# 三.排序查询

*1、asc代表的是升序，可以省略*

*desc代表的是降序*

*2、order by子句可以支持 单个字段、别名、表达式、函数、多个字段*

*3、order by子句在查询语句的最后面，除了limit子句*



## 1、按单个字段排序

```mysql
SELECT * FROM employees ORDER BY salary DESC;
```

## 2、添加筛选条件再排序

```mysql
#案例：查询部门编号>=90的员工信息，并按员工编号降序
SELECT * FROM employees WHERE department_id>=90 ORDER BY employee_id DESC;
```

## 3、按表达式排序

```mysql
#案例：查询员工信息 按年薪降序
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) FROM employees ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;
```

## 4、按别名排序

```mysql
#案例：查询员工信息 按年薪升序
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) 年薪 FROM employees ORDER BY 年薪 ASC;
```

## 5、按函数排序

```mysql
#案例：查询员工名，并且按名字的长度降序
SELECT LENGTH(last_name),last_name 
FROM employees
ORDER BY LENGTH(last_name) DESC;
```

## 6、按多个字段排序

```mysql
#案例：查询员工信息，要求先按工资降序，再按employee_id升序
SELECT *
FROM employees
ORDER BY salary DESC,employee_id ASC;
```





