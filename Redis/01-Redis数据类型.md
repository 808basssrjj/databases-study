# 一. Redis 简介

Redis (**Remote **DI**ctionary **S**erver) 是用 C 语言开发的一个开源的高性能键值对（**key-value）数据库。

**Redis 是一个支持持久化的内存数据库**

> Redis能干吗 ?

1. 为热点数据加速查询（主要场景），如热点商品、热点新闻、热点资讯、推广类等高访问量信息等

2. 任务队列，如秒杀、抢购、购票排队等

3. 即时信息查询，如各位排行榜、各类网站访问统计、公交到站信息、在线人数信息（聊天室、网站）、设备信号等

4. 时效性信息控制，如验证码控制、投票控制等

5. 分布式数据共享，如分布式集群架构中的 session 分离

6. 消息队列

7. 分布式锁

   

> redis是单线程的 !

Redis是基于内存的,CPU不是Redis的性能瓶颈,Redis的瓶颈是根据机器的内存和网络带宽

**Redis为什么单线程还快?**  

CPU>内存>硬盘速度

多线程CPU上下文会切换,会耗时,内存系统不上下文切换就是最快的





# 二. Redis 数据类型

## 1. key通用操作

1. 检查给定 key 是否存在

   ```bash
   exists  key_name	
   ```

2. 为给定 key 设置过期时间

   ```
   expire  key_name seconds   
   ```

3. 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)

   ```
   ttl key_name	
   ```

4. 切换key从时效性转换为永久性

   ```
   persist key_name
   ```

5. 返回 key 所储存的值的类型

   ```
   type key_name
   ```

6. 在 key 存在时删除 key

   ```
   del key_name
   ```


6. 查询key

   ```
   keys pattern
   *  匹配任意数量的任意符号
   ?  匹配一个任意符号
   [] 匹配一个指定符号
   ```

   





## 2. 数据库通用操作

- 切换数据库

  ```
  select index
  ```

- 其他操作

  ```
  quit
  ping
  echo message
  ```

- 数据移动

  ```
  move key_name db
  ```

- 数据清除

  ```
  flushdb
  flushall
  dbsize
  ```

  





## 3. string 字符串

- 存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型
- 存储数据的格式：一个存储空间保存一个数据
- 存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用



**1. string基本操作**

- 设置指定 key 的值

  ```
  set key_name value
  ```

- 获取指定 key 的值

  ```
  get key_name
  ```

- 同时设置一个或多个 key-value 对

  ```
  mset key1 value1 key2 value2 ...keyN valueN
  ```

- 获取所有(一个或多个)给定 key 的值

  ```
  mget key1 key2 ...keyN
  ```

- 将给定 key 的值设为 value ，并返回 key 的旧值，key 不存在时，返回 nil  (用来更新)

  ```
  getset key_name value
  ```

- 返回 key 所储存的字符串值的长度

  ```
  stelen key_name
  ```

- 如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾

  ```
  append key_name value
  ```

**2. string数值增减操作**

​	**可以用于控制数据库表主键id，为数据库表主键提供生成策略，保障数据库表的主键唯一性**

- 将 key 中储存的数字值增一

  ```
  incr key_name
  ```

- 将 key 所储存的值加上给定的增量值

  ```
  incrby key_name increment
  ```

- 将 key 所储存的值加上给定的浮点增量值

  ```
  incrbyfloat key_name increment
  ```

- 将 key 中储存的数字值减一

  ```
  decr key_name
  ```

- key 所储存的值减去给定的减量值

  ```
  decrby key_name decrement
  ```

**3. string 时效性操作**

- 为指定的 key 设置值及其过期时间。如果 key 已经存在，setex 命令将会替换旧的值

  ```
  setex key_name seconds value
  ```

- 以毫秒为单位设置 key 的生存时间

  ```
  psetex key_name milliseconds value
  ```

**4. string其他操作**

- 返回 key 中字符串值的子字符

  ```
  getrange key_name start end
  ```

- 替换指定位置的内容)用指定的字符串覆盖给定 key 所储存的字符串值，覆盖的位置从偏移量 offset 开始

  ```
  setrange key_name offset value
  ```

- 只有在 key 不存在时设置 key 的值 (分布式锁中会经常使用)

  ```
  setnx key_name value
  ```

  

**string 类型数据操作的注意事项**

- 数据操作不成功的反馈与数据正常操作之间的差异

  ① 表示运行结果是否成功

  ​	 (integer) 0 → false 失败

  ​	 (integer) 1 → true 成功

  ② 表示运行结果值

   	(integer) 3 → 3  3个 

-  数据未获取到 :（nil）等同于null

- 数据最大存储量 :      512MB
-  数值计算最大范围 :  9223372036854775807



**String类型使用场景:**

- 计数器
- 统计多单位的数量
- 粉丝数
- 对象缓存存储





## 4. hash 哈希

新的存储需求：对一系列存储的数据进行编组，方便管理，典型应用存储对象信息

需要的存储结构：一个存储空间保存多个键值对数据

hash类型：底层使用哈希表结构实现数据存储



- 添加/修改

  ```
  Hset key_name field value
  Hmset key_name field1 value1 field2 value2 ...
  ```

- 获取数据

  ```
  Hget key_name filed
  Hmget key_name field1 field2 ...
  Hvals key_name (获取全部值)
  Hkeys key_name (获取全部字段)
  Hgetall key_name (获取全部键值对)
  ```

- 删除数据

  ```
  Hdel key_name field
  ```

- 获取哈希表字段数量

  ```
  Hlen key_name
  ```

- 获取哈希表中是否存在指定的字段

  ```
  Hexists key_name field
  ```

- 只有在字段 field 不存在时，设置哈希表字段的值

  ```
  Hsetnx key_name field value
  ```

- 设置指定字段的数值数据增加指定范围的值

  ```
  Hincrby key_name field increment
  Hincrbyfloat key_name filed increment
  ```

  

**hash 类型数据操作的注意事项**

- hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象。如果数据未获取到，

  对应的值为（nil）

- 每个 hash 可以存储 2 32 - 1 个键值对

- hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但hash设计初衷不是为了存

  储大量对象而设计的，切记不可滥用，更不可以将hash作为对象列表使用

- hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就很会低，有可能成为数据访问

  瓶颈





## 5. list 列表

数据存储需求：存储多个数据，并对数据进入存储空间的顺序进行区分

需要的存储结构：一个存储空间保存多个数据，且通过数据可以体现进入顺序

list类型：保存多个数据，底层使用双向链表存储结构实现



- 添加/修改数据

  ```
  Lpush key_name value1 ... valueN  ( 将一个值或多个值插入到列表头部(左) 列表不存在时创建 )
  Rpush key_name value1 ... valueN  ( 将一个值或多个值插入到列表尾部(右) 列表不存在时创建)
  Lpushx key_name value1 ... valueN (列表不存在时操作无效)
  Rpushx key_name value1 ... valueN (列表不存在时操作无效)
  ```

- 获取数据

  ```
  Lrange key_name start end  ( 获取列表指定范围内的元素)
  Lindex key_name index  	   ( 通过索引获取列表中的元素)
  ```

- 获取列表长度

  ```
  Llen key_name
  ```

- 获取并移除数据

  ```
  Lpop key_name ( 移出并获取列表的第一个元素(左))
  Rpop key_name ( 移出并获取列表的第一个元素(右))
  Rpoplpush source_key_name destination_key_name ( 移除列表的最后一个元素，并将该元素添加到另一个列表并返回)
  ```

- 规定时间内获取并移除数据

  ```
  blpop key_name [key2_name] timeout  ( 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。)
  brpop key_name [key2_name] timeout  ( 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。)
  brpoplpush sourcekey_name destinationkey_name timeout 
  ( 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
  ```

- 移除指定数据

  ```
  lrem key_name count value 
  (
  移除列表中与参数 VALUE 相等的元素，数量为 COUNT
  count > 0 : 从表头开始向表尾搜索
  count < 0 : 从表尾开始向表头搜索
  )
  ```

- 移除指定区间数据

  ```
  ltrim key_name start end
  ```

- 通过索引设置列表元素的值

  ```
  lset key_name index value
  ```

- 在列表的元素前或者后插入元素

  ```
  linsert key_name before/after existing_value new_value
  ```

  

**list 类型数据操作注意事项**

- list中保存的数据都是string类型的，数据总容量是有限的，最多2 32 - 1 个元素 (4294967295)。 

- list具有索引的概念，但是操作数据时通常以队列(lpush rpop)的形式进行入队出队操作，或以栈(lpush lpop)的形式进行入栈出栈操作

- 获取全部数据操作结束索引设置为-1 

- list可以对数据进行分页操作，通常第一页的信息来自于list，第2页及更多的信息通过数据库的形式加载





## 6. set 集合

案例:

微博,关注的人,粉丝

共同关注,共同爱好,二级好友





## 7. zset 有序集合

案例:

set 排序 存储成绩表  排行榜







