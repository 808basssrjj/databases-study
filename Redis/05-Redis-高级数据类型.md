# 一. Bitmaps(位图)

**位图操作二进制来记录,非0即1**



**Bitmaps类型的基础操作**

- 获取指定key对应偏移量上的bit值

  **getbit** key offset

- 设置指定key对应偏移量上的bit值，value只能是1或0

  **setbit** key offset value

- 统计数量1的数量

  **bitcount**  key [start end]

- 对指定key按位进行交、并、非、异或操作，并将结果保存到destKey中 

  **bitop**  op destKey key1 [key2...]

  and：交

  or：并

  not：非

  xor：异或



统计用户信息,活跃,不活跃.登录,未登录,打卡   两个状态的,都可以用bitmaps



# 二. Hyperloglog(基数统计)

**基数是不重复的数 ,可以接受误差**



**HyperLogLog类型的基本操作**

- 添加数据

  **pfadd** *key element [element ...]*

- 统计数据

  **pfcount** *key [key ...]*

- 合并数据

  **pfmerge** *destkey sourcekey [sourcekey...]*



**注意**

- 用于进行基数统计，不是集合，不保存数据，只记录数量而不是具体数据

- 核心是基数估算算法，最终数值存在一定误差

- 误差范围：基数估计的结果是一个带有 0.81% 标准错误的近似值

- 耗空间极小，每个hyperloglog key占用了12K的内存用于标记基数

- pfadd命令不是一次性分配12K内存使用，会随着基数的增加内存逐渐增大

- Pfmerge命令合并后占用的存储空间为12K，无论合并之前数据量多少





# 三. Geospatial (地理位置)

**geo底层原理是zset  可用zset命令操作geo**



**GEO类型的基本操作**

- 添加坐标点   ( 可以将一个或多个经度(longitude)、纬度(latitude)、位置名称(member)添加到指定的key 中)

  **geoadd** *key longitude latitude member [longitude latitude member ...]*

- 获取坐标点  ( 从给定的 key 里返回所有指定名称(member)的位置（经度和纬度），不存在的返回 nil)

  **geopos** *key member [member ...]*

- 计算坐标点距离  (返回两个给定位置之间的距离。)

  **geodist** *key member1 member2 [unit]*

- 根据坐标求范围内的数据  (以给定的经纬度为中心， 找出某一半径内的元素)

  **georadius** *key longitude latitude radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]*

- 根据点求范围内数据     (找出位于指定范围内的元素，中心点是由给定的位置元素决定)

  **georadiusbymember** *key member radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]*

- 获取指定点对应的坐标hash值  (返回一个或多个位置元素的 Geohash 表示,将返回11个字符的Geohash字符串)

  **geohash** *key member [member ...]*



应用于地理位置计算 :  朋友的定位, 附近的人, 打车的距离





