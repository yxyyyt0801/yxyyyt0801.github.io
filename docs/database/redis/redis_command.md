# 启动客户端

```shell
# 启动redis客户端
redis-cli
```



# 常用命令

```shell
# 清空当前数据库中的所有 key
flushdb
# 清空所有数据库
flushall

# 清屏
clear

# 返回当前数据库的 key 的数量
dbsize

# 获取 Redis 服务器的各种信息和统计数值
info
 
# 同步保存数据到硬盘
save
# 在后台异步保存当前数据库的数据到磁盘
bgsave

# 异步保存数据到硬盘，并关闭服务器
shutdown [nosave] [save]

# 返回当前服务器时间
time

# 获取指定配置参数的值
config get parameter
# 修改 redis 配置参数，无需重启
config set parameter value
# 对启动 Redis 服务器时所指定的 redis.conf 配置文件进行改写
config rewrite

# 返回主从实例所属的角色
role

# 将当前服务器转变为指定服务器的从属服务器(slave server)
slaveof host port

# 输入密码
auth xxx

# 切换数据库；默认共16个，索引是 0 - 15
# 默认0号数据库，没有[]
# select 1 选择1号数据库，注意[]中的数字代表几号数据库 127.0.0.1:6379[1]
select index

# 键对应值的底层实现数据结构
object encoding key
# 键对应值的对象类型
type key

# 为key设置过期时间n，单位秒
expire test 20
# 为key设置过期时间n，单位毫秒
PEXPIRE test 20000
# 指定在某一个秒数时间戳过期
EXPIREAT n 1642431178
# 指定在某一个毫秒数时间戳过期
PEXPIREAT n 1642431178

# 移除过期时间
PERSIST s

# 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)
# -1 永不过期
# -2 已过期，无法get
ttl test 
# 以毫秒为单位
PTTL test
```



# 字符串 string

可表示三种类型：int、string、byte[] 

字符串类型是Redis中最为基础的数据存储类型，它在Redis中是**二进制**安全的，这便意味着该类型可以接受任何格式的数据，如JPEG图像数据或json对象描述信息等。在Redis中字符串类型的value最多可以容纳的数据长度是**512M**

```shell
# 设置
set test 1
# ex ttl 秒
# nx 不存在时才会设置
set test 2 ex 1 nx
# 只有key不存在时才会设置，否则没有影响
setnx test 2
# 同时设置多个key value
mset t1 1 t2 2
# 从指定位置开始替换value，其实为0
setrange test 0 abc

# 获取
get test
# 同时获取多个key
mget test  t1
# 可以查看所有key；* 代表任意个字符，? 代表任意一个字符（生产不可使用）
keys *
keys t?
# 原子操作，设置新值，返回原始值 
getset test hello
# 0代表起始位置，-1代表无穷大
getrange test 0 -1

# 删除key
del t1

# 存在key返回1，不存在返回0
exists test1

# 追加新值到原值的末尾
append test bye

# 查看对应value的长度
strlen test

# integer类型自增1，非integer异常 (error) ERR value is not an integer or out of range
incr t2
# integer类型增加x
incrby t2 2

# integer类型自减1
decr t2
# integer类型自减x
decrby t2 4
```



# 散列 hash

可表示：Map、Pojo Class 

Redis中的Hash类型可以看成具有String key 和String value的map容器。所以该类型非常适合于存储对象的信息。如Username、password和age。如果Hash中包含少量的字段，那么该类型的数据也将仅占用很少的磁盘空间。 Redis 中每个 hash 可以存储 2^32 - 1 键值对（40多亿）。

```shell
# 设置key是P1的键值对
HSET p1 name test
# 设置多个
HMSET p2 name dev age 100

# 获取指定key中某一个键的值
HGET p1 name
# 获取多个
HMGET p2 name age
# 全部获取
HGETALL p1
# 获取指定key的所有键
HKEYS p1
# 获取指定key的所有值
HVALS p1

# 删除指定key的键值对
HDEL p1 sex

# 为指定key中某一个键的值增加X
HINCRBY p1 age 20 

# 判断指定key的键是否存在
HEXISTS p1 name

# 获取指定key的键值对数量
HLEN p1
```



# 列表 list

可表示：LinkedList 

在Redis中，List类型是按照插入顺序排序的字符串**链表**。和数据结构中的普通链表一样，我们可以在其头部(Left)和尾部(Right)添加新的元素。可以存储 2^32 - 1 。

```shell
# 在左侧插入元素
# 列表中value顺序为 3 2 1
LPUSH t1 1 2 3
# 在右侧插入元素
# 列表中value顺序为 1 2 3
RPUSH t1 1 2 3

# 弹出最左侧元素
LPOP t1
# 弹出最右侧元素
RPOP t1

# 获取范围元素，从最左侧开始编号，0为开始元素，后续元素序号递增1；如果需要获取所有元素可以使用 0 -1 作为范围查询条件
LRANGE t1 0 -1

# 链表长度
LLEN t1
```



# 集合 set

可表示：set，不重复的list 

在redis中，可以将Set类型看作是 **有序不重复** 的字符集合，和List类型一样，我们也可以在该类型的数值上执行添加、删除和判断某一元素是否存在等操作。Redis 中集合是通过哈希表实现的，这些操作的时间复杂度为O(1)，即常量时间内完成依次操作。 和List类型不同的是，Set集合中不允许出现重复的元素。 可以存储 2^32 - 1 。

```shell
# 向集合中插入元素，成功返回成功插入的个数，失败返回0
SADD s 1 2 3 7 10 5 4

# 查询所有元素
SMEMBERS s

# 删除集合中的指定元素
SREM s 1

# 随机返回元素，可以指定个数
SRANDMEMBER s 2

# 判断元素是否在集合中存在;存在，返回1；否则，返回0
SISMEMBER s 1

# 随机返回元素并删除
SPOP s 1

sadd s1 1 2 3
sadd s2 2 3 5
# 求多个集合的交集
SINTER s1 s2
# 求多个集合的并集
SUNION s1 s2
# 求多个集合的差集
SDIFF s1 s2
```



# 有序集合 zset

sortedset和set极为相似，他们都是字符串的集合，都不允许重复的成员出现在一个 set中。他们之间的主要差别是sortedset中每一个成员都会有一个分数与之关联。redis正是通过分数来为集合的成员进行从小到大的排序。sortedset中分数是可以重复的。 

```shell
zadd ss 99 a 98 b

# 范围查询，按score从小到大排序，withscores携带权重
ZRANGE ss 0 -1 withscores
# 按score从大到小排序
ZREVRANGE ss 0 -1 withscores

# 获取集合中某一个成员的score
ZSCORE ss a

# 获取集合中成员数量 
ZCARD ss

# 移除集合中指定的成员，可以指定多个成员 
ZREM ss a b

# 按照排名范围删除元素（从小到大排序，第一个元素的索引是0）
ZREMRANGEBYRANK ss 3 3
# 安装score范围删除元素
ZREMRANGEBYSCORE ss 44 99
```



# 位图 Bitmap

Bitmap不属于Redis的基本数据类型，而是基于String类型进行的位操作。而Redis中字符串的最大长度是 512M，所以 BitMap 的 offset 值也是有上限的，其最大值是 2^32 - 1。

```shell
# SETBIT key offset value
# 设置比特位
SETBIT 2021:user_id:10001 100 1
SETBIT 2021:user_id:10001 101 1

# BITCOUNT key [start end]
# 统计比特值为1的数量
BITCOUNT 2021:user_id:10001

# 0110 0001 
SET mykey "a"
# BITPOS key bit [start [end]]
# 返回字符串中，从左到右，第一个比特值为bit（0或1）的偏移量
bitpos mykey 1	# 1
bitpos mykey 0	# 0

# BITOP operation destkey key [key ...]
# 对多个字符串进行位操作，并将结果保存到destkey中；operation 可以是 AND、OR、XOR 或者 NOT
SETBIT 20210308 1 1
SETBIT 20210308 2 1
SETBIT 20210309 1 1

# 20210308~20210309都活跃的人数
BITOP and desk1 20210308 20210309 # 1
# 20210308~20210309总活跃的人数
BITOP or desk2 20210308 20210309 # 2
```



# 基数统计 HyperLogLog

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

- 基数指的是一个可重复集合中不重复元素的个数。 

```shell
# 添加指定元素到 HyperLogLog 中
PFADD runoobkey "redis"
PFADD runoobkey "mongodb"
PFADD runoobkey "mysql"
# 返回给定 HyperLogLog 的基数估算值
PFCOUNT runoobkey	# 3

PFADD runoobkey "mysql"
PFCOUNT runoobkey	# 3
```

