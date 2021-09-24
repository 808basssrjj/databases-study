# 一.连接查询

> **引入**：如果想要将多个表中的数据一起来显示完整的数据，就好像是把多张表`连接`到一起，即字段拼接到一张表中。这个时候可以使用SQL中的连接查询来完成。在MySQL中，连接查询分为四种

* 交叉连接：没有任何条件的连接
* 内连接：条件完全匹配的连接
* 外连接：主表匹配连接
* 自然连接：自动匹配条件的连接

语法：

  *select 查询列表*

  *from 表1 别名 【连接类型】*

  *join 表2 别名* 

  *on 连接条件*

  *【where 筛选条件】*

  *【group by 分组】*

  *【having 筛选条件】*

  *【order by 排序列表】*



## 1.内连接

**定义**：内连接，关键字inner join，表示的是一种需要两张表连接，而且连接关系是一张表中的数据都与另外一张表中的数据一定能对应上的连接方式。连接成功的保留，连接失败的不保留。

```mysql
#一）等值连接
#案例1.查询员工名、部门名

SELECT last_name,department_name
FROM departments d
JOIN  employees e
ON e.`department_id` = d.`department_id`;

#案例2.查询名字中包含e的员工名和工种名（添加筛选）
SELECT last_name,job_title
FROM employees e
INNER JOIN jobs j
ON e.`job_id`=  j.`job_id`
WHERE e.`last_name` LIKE '%e%';

#3. 查询部门个数>3的城市名和部门个数，并按个数降序（添加分组+筛选）
SELECT city,COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id`=l.`location_id`
GROUP BY city
HAVING COUNT(*)>3;
ORDER BY COUNT(*) DESC;

#5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）
SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.`department_id`=d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`

ORDER BY department_name DESC;


#二）非等值连接
#查询员工的工资级别
SELECT salary,grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
 
#查询工资级别的个数>20的个数，并且按工资级别降序
SELECT COUNT(*),grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
GROUP BY grade_level
HAVING COUNT(*)>20
ORDER BY grade_level DESC;
```



## 2.外连接

**定义**：外连接（outer join）是指以一张表为主表，主表的每一条记录去匹配另外一张表（从表）的所有记录，匹配成功不成功都会保留，只是成功就会有匹配的数据，没有成功只有主表的数据，从表的数据就置空NULL

```mysql
#左外连接
 SELECT b.*,bo.*
 FROM boys bo
 LEFT OUTER JOIN beauty b
 ON b.`boyfriend_id` = bo.`id`
 WHERE b.`id` IS NULL;
 

 #案例1：查询哪个部门没有员工
 #左外
 SELECT d.*,e.employee_id
 FROM departments d
 LEFT OUTER JOIN employees e
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL;
 
 #右外
 SELECT d.*,e.employee_id
 FROM employees e
 RIGHT OUTER JOIN departments d
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL;
 
 #全外
 USE girls;
 SELECT b.*,bo.*
 FROM beauty b
 FULL OUTER JOIN boys bo
 ON b.`boyfriend_id` = bo.id;
```



## 3.交叉连接

**定义**：交叉连接也称cross join，就是两张表无条件连接。结果就是一张表的每一条记录与另外一张表的每一条记录去匹配，并且保留所有结果。

交叉连接的结果也称之为`笛卡尔积`，笛卡尔积是数学上的一种交叉结果。

```mysql
#交叉连接
SELECT b.*,bo.*
FROM beauty b
CROSS JOIN boys bo;
```



# 二.子查询

> **引入**：在复杂查询的时候，真就需要从其他表查询出来的结果作为条件来实现另外一个查询，这种查询中嵌套的查询就被称之为子查询。外部的select查询通常称之为外查询，而内部的select称之为子查询（或者内查询）。在MySQL中根据子查询的使用方式人为的将子查询分成了四类

* 标量子查询：子查询的结果返回的只有一个数据
* 列子查询：子查询的结果返回是一列数据
* 行子查询：子查询的结果返回是一行数据
* 表子查询：子查询的结果返回是一个表

*特点：*

1. 子查询放在小括号内

2. 子查询一般放在条件的右侧

3. 标量子查询，一般搭配着单行操作符使用  

    > < >= <= = <>

4. *列子查询，一般搭配着多行操作符使用*

   in、any/some、all

5. 子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果



## 1. 标量子查询（单行子查询）★

> **定义**：标量子查询，即子查询得到的结果是一个数据

```mysql
#案例1：谁的工资比 Abel 高?
SELECT *
FROM employees
WHERE salary>(
	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
);

#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143
);

#案例3：查询最低工资大于50号部门最低工资的部门id和其最低工资
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50
);
```

> **总结**

1. 标量子查询是子查询的结果返回的只有一个字段一个值
2. 标量子查询通常用于外部查询的条件匹配



## 2. 列子查询（多行子查询）★

**定义**：列子查询，即子查询的结果返回的是一个字段，不过字段中有多个数据

```mysql
#案例1：返回location_id是1400或1700的部门中的所有员工姓名
#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)
#②查询员工姓名，要求部门号是①列表中的某一个
SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);


#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
#①查询job_id为‘IT_PROG’部门任一工资
SELECT DISTINCT salary
FROM employees
WHERE job_id = 'IT_PROG'
#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';
#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';


#案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';
#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';
```

> **总结**

1. 列子查询查询的结果是一列数据（一个字段多个数据）
2. 列子查询也是用于外部查询的条件
3. 列子查询中可以使用any、some和all来进行条件筛选



## **3. 行子查询**

> **定义**：行子查询，就是子查询的结果是返回的是多个字段数据，而且行子查询的典型特征是必须构建行元素进行条件匹配

```mysql
#案例：查询员工编号最小并且工资最高的员工信息
SELECT * 
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);
```

> **总结**

1. 行子查询返回的结果通常是多列，而且只有一行记录
2. 行子查询的典型特征是需要构造行元素
3. 行子查询是用于外部查询的条件（通常是多字段条件）



## **4. 表子查询**

> **定义**：表子查询，即子查询得到的结果是一个表，有多行多列

```mysql
#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

SELECT * FROM job_grades;


#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal
SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
```

> **总结**

1. 表子查询就是子查询返回的结果是多行多列

2. 表子查询实际使用上可以作为数据源跟from关键字或者作为条件跟where关键字



## 5. exists后面（相关子查询）

语法：exists(完整的查询语句)

结果：1或0

```mysql
#案例1：查询有员工的部门名
#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees
)
#exists
SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`
);


#案例2：查询没有女朋友的男神信息
#in
SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
)

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`
);
```



# 三. 联合查询

**定义**：联合查询是指将多次select查询的结果联合一起显示。

特点:

1. 要求多条查询语句的查询列数是一致的！

2. 要求多条查询语句的查询的每一列的类型和顺序最好一致

3. union关键字默认去重，如果使用union all 可以包含重复项

```mysql
#引入的案例：查询部门编号>90或邮箱包含a的员工信息
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;


#案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息
SELECT id,cname FROM t_ca WHERE csex='男'
UNION ALL
SELECT t_id,tname FROM t_ua WHERE tGender='male';

```



# 四. 分页查询

*语法：*

  *select 查询列表*

  *from 表*

  *【join type join 表2*

  *on 连接条件*

  *where 筛选条件*

  *group by 分组字段*

  *having 分组后的筛选*

  *order by 排序的字段】*

  *limit 【offset,】size;*

  

  offset要显示条目的起始索引（起始索引从0开始）

  size 要显示的条目个数

*特点：*

1. limit语句放在查询语句的最后

2. 公式    (page-1)\*size,size

```mysql
#案例1：查询前五条员工信息
SELECT * FROM  employees LIMIT 0,5;
SELECT * FROM  employees LIMIT 5;

#案例2：查询第11条——第25条
SELECT * FROM  employees LIMIT 10,15;

#案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT 
    * 
FROM
    employees 
WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC 
LIMIT 10 ;
```

