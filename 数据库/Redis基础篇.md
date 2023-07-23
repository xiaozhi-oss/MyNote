# redis的入门

## 1、简介

**认识NoSql**

![image-20220430140723120](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430140723120.png)



**认识Redis**

Redis诞生于2009年全称是Remote Dictionary Server，远程词典服务器，是一个基于内存的键值型NoSQL数据库。
特征：

-   键值（key-value）型，value支持多种不同数据结构，功能丰富
-   单线程，每个命令具备原子性
-   低延迟，速度快（基于内存、IO多路复用、良好的编码）。
-   支持数据持久化
-   支持主从集群、分片集群
-   支持多语言客户端

**官方网站**：https://redis.io/

**中文网**：https://www.redis.net.cn/

==**本次使用的是redis7**==



## 2、redis的安装

官网下载对应的包，然后上传到linux的opt目录下



### ①	准备工作

安装C语言编译环境

```sh
yum install -y centos-release-scl scl-utils-build
yum install -y devtoolset-8-toolchain
scl enable devtoolset-8 bash

# 安装完之后，测试gcc版本 
gcc --version
```



### ②	安装

```sh
# 1 进入到opt目录中解压redis
tar -zxvf redis-6.2.1.tar.gz
# 2 cd进入到redis文件夹中
cd redis-6.2.1
# 3 进行编译和安装
make && make install
```



### ③	启动

**前台启动（不推荐）**

在/usr/local/bin中使用redis-server

**缺陷**：命令行窗口关闭了，服务就停止了

![image-20210918161735390](E:\学习笔记\img\20210918161736.png)



**指定配置启动（推荐）**

```sh
# 1 拷贝一份redis.conf到其他目录
cp  /opt/redis-3.2.5/redis.conf  /etc/redis.conf

# 2 修改/etc/redis.conf文件
vim /etc/redis.conf
# 2.1 找到后台启动设置daemonize no改成yes

# 3 然后在/usr/local/bin目录中启动
[root@xiaozhi bin]# redis-server /etc/redis.conf

# 使用客户端来测试是否开启成功
[root@xiaozhi bin]# redis-cli
127.0.0.1:6379> ping
PONG

# 多个端口可以：redis-cli -p6379
```



**开机自启**

我们也可以通过配置来实现开机自启。

首先，新建一个系统服务文件：

```sh
vim /etc/systemd/system/redis.service
```

内容如下：

```sh
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /etc/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```



然后重载系统服务：

```sh
systemctl daemon-reload
```



现在，我们可以用下面这组命令来操作redis了：

```sh
# 启动
systemctl start redis
# 停止
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
```



执行下面的命令，可以让redis开机自启：

```sh
systemctl enable redis
```





### ④	redis关闭

**单实例关闭**

```sh
[root@xiaozhi bin]# redis-cli shutdown

# 也可以进入终端再关
127.0.0.1:6379> shutdown
not connected> 
```



**多实例关闭**

指定端口关闭：**redis-cli -p 6379 shutdown**



### ⑤卸载

首先停掉redis

然后删除`/usr/local/bin`下所有的redis文件

```sh
rm -rf /usr/local/bin/redis-*
```





## 3	redis相关知识

端口 6379的来源Merz在手机上的位置

串行  vs  多线程+锁（**memcached**） vs  单线程+多路IO复用(**Redis**)

![image-20210918164438734](E:\学习笔记\img\20210918164439.png)





# Redis配置文件

Redis的配置文件 Redis.conf



## 1、Units 单位

配置大小单位,开头定义了一些基本的度量单位，只支持bytes，不支持bit大小写不敏感

![image-20210919110345825](E:\学习笔记\img\20210919110347.png)



## 2、INCLUDES包含  ##

![image-20210919110521595](E:\学习笔记\img\20210919110522.png)

类似jsp中的include，多实例的情况可以把公用的配置文件提取出来



## 3、NETWORK  ##

### ①bind

默认情况bind=127.0.0.1只能接受本机的访问请求

不写的情况下，无限制接受任何ip地址的访问

生产环境肯定要写你应用服务器的地址；服务器是需要远程访问的，所以需要将其注释掉

**如果开启了protected-mode，那么在没有设定bind ip且没有设密码的情况下，Redis只允许接受本机的响应**

![image-20210919110800152](E:\学习笔记\img\20210919110801.png)

保存配置，停止服务，重启启动查看进程，不再是本机访问了

![image-20210919111012110](E:\学习笔记\img\20210919111013.png)



### ②protected-mode

将本机访问保护模式设置no

![image-20210919111109521](E:\学习笔记\img\20210919111110.png)



### ③port

端口号，默认 6379



### ④tcp-backlog

设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。

注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果



### ⑤timeout

一个空闲的客户端维持多少秒会关闭，0表示关闭该功能。即永不关闭。

![image-20210919111224490](E:\学习笔记\img\20210919111225.png)



### ⑥tcp-leepalive

对访问客户端的一种心跳检测，每个n秒检测一次。

单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60 



## 4、GENERAL 通用配置  ##

### ①daemonize

是否为后台进程，设置为yes

守护进程，后台启动



### ②pidfile

存放pid文件的位置，每个实例会产生一个不同的pid文件

![image-20210919111612233](E:\学习笔记\img\20210919111613.png)



### ③loglevel

指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为***\*notice\****

四个级别根据使用阶段来选择，生产环境选择notice 或者warning



### ④logfile

日志文件名称

![image-20210919111828901](E:\学习笔记\img\20210919111830.png)



### ⑤databases

设定库的数量 默认16，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id



## 5、SECURITY 安全  ##

**设置密码**

![image-20210919112133189](E:\学习笔记\img\20210919112134.png)

访问密码的查看、设置和取消

在命令中设置密码，只是临时的。重启redis服务器，密码就还原了。

永久设置，需要再配置文件中进行设置。

![image-20210919112142868](E:\学习笔记\img\20210919112144.png)



## 6、LIMITS限制  ##

### ①maxclients

设置redis同时可以与多少个客户端进行连接。

默认情况下为10000个客户端。

如果达到了此限制，redis则会拒绝新的连接请求，并且向这些连接请求方发出“max number of clients reached”以作回应。



### ②maxmemory

Ø 建议***\*必须设置\****，否则，将内存占满，造成服务器宕机

Ø 设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过**maxmemory-policy**来指定。

Ø 如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。

Ø 但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。



### ③maxmemory-policy

-   **volatile-lru**：使用LRU算法移除key，只对设置了过期时间的键；（最近最少使用）
-   **allkeys-lru**：在所有集合key中，使用LRU算法移除key
-   **volatile-random**：在过期集合中移除随机的key，只对设置了过期时间的键
-   **allkeys-random**：在所有集合key中，移除随机的key
-   **volatile-ttl**：移除那些TTL值最小的key，即那些最近要过期的key
-   **noeviction**：不进行移除。针对写操作，只是返回错误信息

![image-20210919112603398](E:\学习笔记\img\20210919112604.png)



### ④maxmemory-samples

-   设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。
-   一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小。





## 7、SNAPSHOTTING

```sh
################################ SNAPSHOTTING  ################################
# 配置保存的规则
save <seconds> <changes> [ <seconds> <changes> ...]
# 文件名
dbfilename dump.rdb
# 文件保存位置
dir /
# 默认是yes
# 如果配置成no，表示你不在乎数据一致性或者有其他的手段发现和控制这种不一致，那么快照写入失败时，也能确保redis继续接收新的请求
stop-writes-on-bgsave-error yes
# 默认yes
# 对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。
# 如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能
rdbcompression yes
# 默认yes
# 在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，
# 如果希望获取到最大的性能提升，可以关闭此功能，建议开启
rdbchecksum yes
# 在没有持久性的情况下删除复制中使用的RDB文件启用。默认情况下no，此选项是禁用的。
rdb-del-sync-files no
```





# reids数据结构

## 1、Redis 数据结构介绍 

十大数据结构

![image-20230509170057512](E:\学习笔记\img\img01\image-20230509170057512.png)



## 2、通用命令 

通用指令是部分数据类型的，都可以使用的指令，常见的有：

-   KEYS：查看符合模板的所有key，例如keys * 是查看所有的key
-   DEL：删除一个指定的key
-   unlink key：非阻塞删除（异步删除）
-   EXISTS：判断key是否存在
-   EXPIRE：给一个key设置有效期，有效期到期时该key会被自动删除
-   TTL：查看一个KEY的剩余有效期
-   help：可以查看对应命令的使用
-   Type：查看对应值的存储类型
-   select db：选择数据库，类似于mysql的`use db`
-   move key db：移动对应key到指定数据库
-   dbsize：查看当前数据库key的数量
-   flushdb：清空当前库
-   flushall：通杀全部库

通过help [command] 可以查看一个命令的具体用法，例如：

![image-20220430144429579](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430144429579.png)

==**注意**：命令是不区分大小写的，但是KEY区分大小写==



## String(字符串)

### ①介绍

String类型，也就是字符串类型，是Redis中最简单的存储类型。
其value是字符串，不过根据字符串的格式不同，又可以分为3类：

-   string：普通字符串
-   int：整数类型，可以做自增、自减操作
-   float：浮点类型，可以做自增、自减操作

**注意**：不管是哪种格式，底层都是字节数组形式存储，只不过是编码方式不同。字符串类型的<span style="color: red">最大空间不能超过512m</span>.

![image-20220430144715775](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430144715775.png)



### ②String常见命令

-   SET：添加或者修改已经存在的一个String类型的键值对
-   GET：根据key获取String类型的value
-   MSET：批量添加多个String类型的键值对
-   MGET：根据多个key获取多个String类型的value
-   INCR：让一个整型的key自增1
-   INCRBY:让一个整型的key自增并指定步长，例如：incrby num 2 让num值自增2
-   INCRBYFLOAT：让一个浮点类型的数字自增并指定步长
-   SETNX：添加一个String类型的键值对，前提是这个key不存在，否则不执行
-   SETEX：添加一个String类型的键值对，并且指定有效期





### ③区分不同的key

![image-20220430155948287](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430155948287.png)









## Hash (哈希表)

![image-20220430160456429](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430160456429.png)

**Hash的常见命令有**：

-   HSET key field value：添加或者修改hash类型key的field的值
-   HGET key field：获取一个hash类型key的field的值
-   HMSET：批量添加多个hash类型key的field的值
-   HMGET：批量获取多个hash类型key的field的值
-   HGETALL：获取一个hash类型的key中的所有的field和value
-   HKEYS：获取一个hash类型的key中的所有的field
-   HVALS：获取一个hash类型的key中的所有的value
-   HINCRBY:让一个hash类型key的字段值自增并指定步长
-   HSETNX：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行

![image-20220430161255404](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430161255404.png)





## List (列表)

Redis中的List类型与Java中的LinkedList类似，可以看做是一个双向链表结构。既可以支持正向检索和也可以支持反向检索。
特征也与LinkedList类似：

-   有序
-   元素可以重复
-   插入和删除快
-   查询速度一般

常用来存储一个有序数据，例如：朋友圈点赞列表，评论列表等。



**List的常见命令有**：

-   LPUSH key  element ... ：向列表左侧插入一个或多个元素
-   LPOP key：移除并返回列表左侧的第一个元素，没有则返回nil
-   RPUSH key  element ... ：向列表右侧插入一个或多个元素
-   RPOP key：移除并返回列表右侧的第一个元素
-   LRANGE key star end：返回一段角标范围内的所有元素
-   BLPOP和BRPOP：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil（阻塞模式）



**思考**：

-   如何利用List结构模拟一个栈?

    入口和出口在同一边

-   如何利用List结构模拟一个队列?

    入口和出口在不同边

-   如何利用List结构模拟一个阻塞队列?

    -   入口和出口在不同边
    -   出队时采用BLPOP或BRPOP





## Set (集合)

Redis的Set结构与Java中的HashSet类似，可以看做是一个value为null的HashMap。因为也是一个hash表，因此具备与HashSet类似的特征：

-   无序
-   元素不可重复
-   查找快
-   支持交集、并集、差集等功能



**Set常见命令**

Set的常见命令有：

-   SADD key member ... ：向set中添加一个或多个元素
-   SREM key member ... : 移除set中的指定元素
-   SCARD key： 返回set中元素的个数
-   SISMEMBER key member：判断一个元素是否存在于set中
-   SMEMBERS：获取set中的所有元素
-   SINTER key1 key2 ... ：求key1与key2的交集
-   SDIFF key1 key2 ... ：求key1与key2的差集
-   SUNION key1 key2 ..：求key1和key2的并集



**练习**

将下列数据用Redis的Set集合来存储：

```sh
# 张三的好友有：李四、王五、赵六
127.0.0.1:6379> Sadd zhangsan lisi wangwu zhaoliu
# 李四的好友有：王五、麻子、二狗
127.0.0.1:6379> Sadd lisi wangwu mazi ergou
```

利用Set的命令实现下列功能：

```sh
#  计算张三的好友有几人
127.0.0.1:6379> Scard zhangsan
#  计算张三和李四有哪些共同好友
127.0.0.1:6379> Sinter zhangsan lisi
#   查询哪些人是张三的好友却不是李四的好友
127.0.0.1:6379> SDIFF zhangsan lisi
1) "lisi"
2) "zhaoliu"
#   查询张三和李四的好友总共有哪些人
127.0.0.1:6379> Sunion zhangsan lisi
1) "zhaoliu"
2) "wangwu"
3) "lisi"
4) "mazi"
5) "ergou"
#   判断李四是否是张三的好友
127.0.0.1:6379> SISMEMBER lisi "zhangsan"
(integer) 0
#   判断张三是否是李四的好友
127.0.0.1:6379> SISMEMBER zhangsan "lisi"
(integer) 1
#   将李四从张三的好友列表中移除
127.0.0.1:6379> SREM zhangsan lisi
```





## ZSet(有序集合)

Redis的SortedSet是一个可排序的set集合，与Java中的TreeSet有些类似，但底层数据结构却差别很大。SortedSet中的每一个元素都带有一个score属性，可以基于score属性对元素排序，底层的实现是一个跳表（SkipList）加 hash表。
SortedSet具备下列特性：

-   可排序
-   元素不重复
-   查询速度快
-   因为SortedSet的可排序特性，经常被用来实现排行榜这样的功能



**SortedSet类型的常见命令**

1.  ZADD key score member：添加一个或多个元素到sorted set ，如果已经存在则更新其score值
2.  ZREM key member：删除sorted set中的一个指定元素
3.  ZSCORE key member : 获取sorted set中的指定元素的score值
4.  ZRANK key member：获取sorted set 中的指定元素的排名
5.  ZCARD key：获取sorted set中的元素个数
6.  ZCOUNT key min max：统计score值在给定范围内的所有元素的个数
7.  ZINCRBY key increment member：让sorted set中的指定元素自增，步长为指定的increment值
8.  ZRANGE key min max：按照score排序后，获取指定排名范围内的元素
9.  ZRANGEBYSCORE key min max：按照score排序后，获取指定score范围内的元素
10.  ZDIFF、ZINTER、ZUNION：求差集、交集、并集

**注意**：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可



**练习**：

将班级的下列学生得分存入Redis的SortedSet中：
Jack 85, Lucy 89, Rose 82, Tom 95, Jerry 78, Amy 92, Miles 76

```sh
127.0.0.1:6379> ZADD score 85 "Jack" 89 "Lucy" 82 "Rose" 95 "Tom" 78 "Jerry" 92 "Amy" 76 "Milles"
```

并实现下列功能：

```sh
# 删除Tom同学
127.0.0.1:6379> ZREM score "Tom"
# 获取Amy同学的分数
127.0.0.1:6379> ZSCORE score "Amy"
# 获取Rose同学的排名，从0开始的
127.0.0.1:6379> ZREVRANK score "Rose"
# 查询80分以下有几个学生
127.0.0.1:6379> ZCOUNT score 0 80
# 给Amy同学加2分
127.0.0.1:6379> ZINCRBY score 2 "Amy"
# 查出成绩前3名的同学
127.0.0.1:6379> ZREVRANGE score 0 2
# 查出成绩80分以下的所有同学
127.0.0.1:6379> ZREVRANGEBYSCORE score 80 0
```





## GEO(地理空间)

### 8.1 介绍

移动互联网时代LBS应用越来越多，交友软件中附近的小姐姐、外卖软件中附近的美食店铺、高德地图附近的核酸检查点等等，那这种附近各种形形色色的XXX地址位置选择是如何实现的？

地球上的地理位置是使用二维的经纬度表示，经度范围 (-180, 180]，纬度范围 (-90, 90]，只要我们确定一个点的经纬度就可以名取得他在地球的位置。

例如滴滴打车，最直观的操作就是实时记录更新各个车的位置，然后当我们要找车时，在数据库中查找距离我们(坐标x0,y0)附近r公里范围内部的车辆

**使用关系型数据库解决？**

```sql
select taxi from position where x0-r < x < x0 + r and y0-r < y < y0+r
```

但是这样会有什么问题呢？

1.查询性能问题，如果并发高，数据量大这种查询是要搞垮数据库的

2.这个查询的是一个矩形访问，而不是以我为中心r公里为半径的圆形访问。

3.精准度的问题，我们知道地球不是平面坐标系，而是一个圆球，这种矩形计算在长距离计算时会有很大误差



### 8.2 常用命令

-   GEOADD key longitude latitude member [longitude latitude member ...\]添加一个或多个地理空间位置到sorted set

-   GEOHASH key member [member ...\] 返回一个标准的地理空间的Geohash字符串

    GEOHASH它会将地理空间使用base32进行编码，这样我们就可以通过这个字符串作为唯一标识

-   GEOPOS key member [member ...\] 返回地理空间的经纬度

-   GEODIST key member1 member2 [unit\] 返回两个地理空间之间的距离

-   GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD\] [WITHDIST] [WITHHASH] [COUNT count] 查询指定半径内所有的地理空间元素的集合。

    -   WITHDIST: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。
    -   WITHCOORD: 将位置元素的经度和维度也一并返回。
    -   WITHHASH: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大
    -   COUNT 限定返回的记录数。
    -   ASC | DESC 正序或者反序

-   GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD\] [WITHDIST] [WITHHASH] [COUNT count]查询指定半径内匹配到的最大距离的一个地理空间元素。



### 8.3 实现案例

-   计算两点之间的距离

    ```sh
    127.0.0.1:6379> GEOADD city 116.403963 39.915119 "天安门" 116.403414 39.924091 "故宫" 116.024067 40.362639 "长城"
    (integer) 3
    127.0.0.1:6379> GEOPOS city 天安门 故宫 长城
    1) 1) "116.40396326780319214"
       2) "39.91511970338637383"
    2) 1) "116.40341609716415405"
       2) "39.92409008156928252"
    3) 1) "116.02406591176986694"
       2) "40.36263993239462167"
    # 最后面的是单位表示
    127.0.0.1:6379> GEODIST city 天安门 故宫 km
    "0.9988"
    127.0.0.1:6379> GEODIST city 天安门 故宫 m
    "998.8332"
    ```

-   查看10KM内的地点

    默认客户端中文会乱码，需要指定一下编码

    ```sh
    127.0.0.1:6379> GEORADIUS city 116.418017 39.914402 10 km withdist withcoord count 10 withhash desc
    1) 1) "\xe6\x95\x85\xe5\xae\xab"
       2) "1.6470"
       3) (integer) 4069885568908290
       4) 1) "116.40341609716415405"
          2) "39.92409008156928252"
    2) 1) "\xe5\xa4\xa9\xe5\xae\x89\xe9\x97\xa8"
       2) "1.2016"
       3) (integer) 4069885555089531
       4) 1) "116.40396326780319214"
          2) "39.91511970338637383"
    # 解决中文乱码
    [root@localhost ~]# redis-cli --raw
    
    # 查询北京王府井10km内的地点，
    127.0.0.1:6379> GEORADIUS city 116.418017 39.914402 10 km withdist withcoord count 10 withhash desc
    故宫
    1.6470
    4069885568908290
    116.40341609716415405
    39.92409008156928252
    天安门
    1.2016
    4069885555089531
    116.40396326780319214
    39.91511970338637383
    
    # 指定成员为中心查询
    127.0.0.1:6379> GEORADIUSBYMEMBER city 天安门 10 km withdist withcoord count 10 withhash asc
    天安门
    0.0000
    4069885555089531
    116.40396326780319214
    39.91511970338637383
    故宫
    0.9988
    4069885568908290
    116.40341609716415405
    39.92409008156928252
    ```

    





## HyperLogLog(基数统计)

### 9.1 介绍

基数：是一种数据集，去重后的真实个数

![image-20230504172715475](E:\学习笔记\img\img01\image-20230504172715475.png)

**可以做？**

-   统计某个网站的UV
-   统计某个文章的UV
-   ......



### 9.2 常用命令

-   PFADD key element [element...]：添加指定元素，元素是一个字符串
-   PFCOUNT key [key...]：返回给定的基数估算值，这个值会有0.8%的误差
-   PFMERGE deslkey sourcekey [sourcekey ...]：将多个HyperLogLog合并成一个HyperLogLog



### 9.3 案例实现

-   统计网站的访问人数

    ```sh
    127.0.0.1:6379> PFADD vis:ip 10.10.10.10 10.10.10.11 10.10.10.12 10.10.10.10
    (integer) 1
    127.0.0.1:6379> PFCOUNT vis:ip
    (integer) 3
    ```

-   统计单个用户的多篇文章的访问量

    ```sh
    127.0.0.1:6379> PFADD blog:2349523 10.10.10.10 10.10.10.11 10.10.10.12 10.10.10.10
    (integer) 1
    127.0.0.1:6379> PFADD blog:2349524 10.10.10.10 10.10.10.11 10.10.10.12 10.10.10.10 10.23.23.10 10.10.10.10
    (integer) 1
    127.0.0.1:6379> PFMERGE temp blog:2349523 blog:2349524
    OK
    127.0.0.1:6379> PFCOUNT temp
    (integer) 4
    127.0.0.1:6379>
    ```

    







## BitMap(位图)

### 10.1 介绍

由0和状态表示的二进制位的bit数组

![image-20230504165600764](E:\学习笔记\img\img01\image-20230504165600764.png)

说明：用String类型作为底层数据结构实现的一种统计二值状态的数据类型

位图本质是数组，它是基于String数据类型的按位的操作。该数组由多个二进制位组成，每个二进制位都对应一个偏移量(我们称之为一个索引)。

 Bitmap支持的最大位数是2^32位，它可以极大的节约存储空间，使用512M内存就可以存储多达<span style="color: red;">42.9亿的字节信息(2^32 = 4294967296)</span>



**能干嘛？**

可以用于状态统计，比如：

-   用户是否登录过
-   电影、广告是否被点击播放过
-   钉钉打卡上班，签到统计
-   ......



### 10.2 常用命令

-   setbit key offset value：根据偏移量设置值
-   getbit key offset：获取key对应偏移量的值
-   strlen：不是字符串长度而是占据几个字节，超过8位后自己按照8位一组一byte再扩容
-   bitcount：统计键里面含有1的有多少
-   bitop：对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。
    -   `BITOP AND destkey srckey1 srckey2 srckey3 ... srckeyN` ，对一个或多个 key 求逻辑并，并将结果保存到 destkey 。
    -   `BITOP OR destkey srckey1 srckey2 srckey3 ... srckeyN`，对一个或多个 key 求逻辑或，并将结果保存到 destkey 。
    -   `BITOP XOR destkey srckey1 srckey2 srckey3 ... srckeyN`，对一个或多个 key 求逻辑异或，并将结果保存到 destkey 。
    -   `BITOP NOT destkey srckey`，对给定 key 求逻辑非，并将结果保存到 destkey 。



### 10.3 案例实现

查询两天内签到的学生

```sh
127.0.0.1:6379> SETBIT 20220503 0 1
(integer) 0
127.0.0.1:6379> SETBIT 20220503 1 1
(integer) 0
127.0.0.1:6379> SETBIT 20220503 2 1
(integer) 0
127.0.0.1:6379> SETBIT 20220503 3 1
(integer) 0
127.0.0.1:6379> SETBIT 20220503 4 1
(integer) 0
127.0.0.1:6379> SETBIT 20220504 1 1
(integer) 0
127.0.0.1:6379> SETBIT 20220504 2 1
(integer) 0
127.0.0.1:6379> BITCOUNT 20220503
(integer) 5
127.0.0.1:6379> BITCOUNT 20220504
(integer) 2
127.0.0.1:6379> BITOP and res 20220503 20220504
(integer) 1
127.0.0.1:6379> BITCOUNT res
(integer) 2


```



查看某个学生一年的登录天数

```sh
127.0.0.1:6379> SETBIT 202006150060 1 1
(integer) 0
127.0.0.1:6379> SETBIT 202006150060 2 1
(integer) 0
127.0.0.1:6379> SETBIT 202006150060 345 1
(integer) 0
127.0.0.1:6379> BITCOUNT 202006150060
(integer) 3
```

还可以通过get命令获取它的ASCLL码值，通过这个来得到该学生的签到日历





## redis流(stream)

### 介绍

Redis Stream 是 Redis 5.0 版本新增加的数据结构。是Redis版的MQ

Redis Stream 主要用于消息队列（MQ，Message Queue），Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃。

简单来说发布订阅 (pub/sub) 可以分发消息，但无法记录历史消息。而Redis Stream可以记录消息，这样我们就可以知道消息是已读还是未读。

而 Redis Stream 提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失。

Redis Stream 的结构如下所示，它有一个消息链表，将所有加入的消息都串起来，每个消息都有一个唯一的 ID 和对应的内容：

![img](E:\学习笔记\img\img01\stream1.png)

每个 Stream 都有唯一的名称，它就是 Redis 的 key，在我们首次使用 xadd 指令追加消息时自动创建。

上图解析：

-   Consumer Group ：消费组，使用 XGROUP CREATE 命令创建，一个消费组有多个消费者(Consumer)。
-   last*delivered*id ：游标，每个消费组会有个游标 last*delivered*id，任意一个消费者读取了消息都会使游标 last*delivered*id 往前移动。==也就是说每条消息只能被组中的某一个消费者消费==
-   pending*ids ：消费者(Consumer)的状态变量，作用是维护消费者的未确认的 id。 pending*ids 记录了当前已经被客户端读取的消息，但是还没有 ack (Acknowledge character：确认字符）。







### 常用指令

消息队列相关命令：

-   XADD - 添加消息到末尾
-   XTRIM - 对流进行修剪，限制长度
-   XDEL - 删除消息
-   XLEN - 获取流包含的元素数量，即消息长度
-   XRANGE - 获取消息列表，会自动过滤已经删除的消息
-   XREVRANGE - 反向获取消息列表，ID 从大到小
-   XREAD - 以阻塞或非阻塞方式获取消息列表

消费者组相关命令：

-   XGROUP CREATE - 创建消费者组
-   XREADGROUP GROUP - 读取消费者组中的消息
-   XACK - 将消息标记为"已处理"
-   XGROUP SETID - 为消费者组设置新的最后递送消息ID
-   XGROUP DELCONSUMER - 删除消费者
-   XGROUP DESTROY - 删除消费者组
-   XPENDING - 显示待处理消息的相关信息
-   XCLAIM - 转移消息的归属权
-   XINFO - 查看流和消费者组的相关信息；
-   XINFO GROUPS - 打印消费者组的信息；
-   XINFO STREAM - 打印流信息



### 命令详解

#### XADD

使用 [XADD](https://redis.com.cn/commands/xadd.html) 向队列添加消息，如果指定的队列不存在，则创建一个队列，XADD 语法格式：

```
XADD key ID field value [field value ...]
```

-   key ：队列名称，如果不存在就创建
-   ID ：消息 id，我们使用 * 表示由 redis 生成，可以自定义，但是要自己保证递增性(不可重复)。
-   field value ： 记录。



**XADD实例**

```sh
redis> XADD mystream * name Sara surname OConnor
"1601372323627-0"
redis> XADD mystream * field1 value1 field2 value2 field3 value3
"1601372323627-1"
redis> XLEN mystream
(integer) 2
redis> XRANGE mystream - +
1) 1) "1601372323627-0"
  2) 1) "name"
   2) "Sara"
   3) "surname"
   4) "OConnor"
2) 1) "1601372323627-1"
  2) 1) "field1"
   2) "value1"
   3) "field2"
   4) "value2"
   5) "field3"
   6) "value3"
redis>
```



#### XTRIM

使用 [XTRIM](https://redis.com.cn/commands/xtrim.html) 对流进行修剪，限制长度， 语法格式：

```sh
XTRIM key MAXLEN|MINID [=|~] threshold [LIMIT count]
```

-   key ：队列名称
-   MAXLEN ：长度
-   MINID：最小ID
-   count ：数量



**XTRIM实例**

```sh
127.0.0.1:6379> XADD mystream * field1 A field2 B field3 C field4 D
"1601372434568-0"
127.0.0.1:6379> XTRIM mystream MAXLEN 2
(integer) 0
127.0.0.1:6379> XRANGE mystream - +
1) 1) "1601372434568-0"
  2) 1) "field1"
   2) "A"
   3) "field2"
   4) "B"
   5) "field3"
   6) "C"
   7) "field4"
   8) "D"
127.0.0.1:6379>

redis>
```



#### XDEL

使用 [XDEL](https://redis.com.cn/commands/xdel.html) 删除消息，语法格式：

```sh
XDEL key ID [ID ...]
```

-   key：队列名称
-   ID ：消息 ID



**XDEL实例**

```sh
127.0.0.1:6379> XADD mystream * a 1
1538561698944-0
127.0.0.1:6379> XADD mystream * b 2
1538561700640-0
127.0.0.1:6379> XADD mystream * c 3
1538561701744-0
127.0.0.1:6379> XDEL mystream 1538561700640-0
(integer) 1
127.0.0.1:6379> XRANGE mystream - +
1) 1) 1538561698944-0
   2) 1) "a"
      2) "1"
2) 1) 1538561701744-0
   2) 1) "c"
      2) "3"
```



#### XLEN

使用 [XLEN](https://redis.com.cn/commands/xlen.html) 获取流包含的元素数量，即消息长度，语法格式：

```sh
XLEN key
```

-   key：队列名称



**XLEN实例**

```sh
redis> XADD mystream * item 1
"1601372563177-0"
redis> XADD mystream * item 2
"1601372563178-0"
redis> XADD mystream * item 3
"1601372563178-1"
redis> XLEN mystream
(integer) 3
redis>
```



#### XRANGE

使用 XRANGE 获取消息列表，会自动过滤已经删除的消息 ，语法格式：

```sh
XRANGE key start end [COUNT count]
```

-   key ：队列名
-   start ：开始值， - 表示最小值
-   end ：结束值， + 表示最大值
-   count ：数量



**实例**

```sh
redis> XADD writers * name Virginia surname Woolf
"1601372577811-0"
redis> XADD writers * name Jane surname Austen
"1601372577811-1"
redis> XADD writers * name Toni surname Morrison
"1601372577811-2"
redis> XADD writers * name Agatha surname Christie
"1601372577812-0"
redis> XADD writers * name Ngozi surname Adichie
"1601372577812-1"
redis> XLEN writers
(integer) 5
redis> XRANGE writers - + COUNT 2
1) 1) "1601372577811-0"
   2) 1) "name"
   2) "Virginia"
   3) "surname"
   4) "Woolf"
2) 1) "1601372577811-1"
   2) 1) "name"
   2) "Jane"
   3) "surname"
   4) "Austen"
redis>
```



#### XREVRANGE

使用 [XREVRANGE](https://redis.com.cn/commands/xrevrange.html) 获取消息列表，会自动过滤已经删除的消息 ，语法格式：

```sh
XREVRANGE key end start [COUNT count]
```

-   key ：队列名
-   end ：结束值， + 表示最大值
-   start ：开始值， - 表示最小值
-   count ：数量



**XREVRANGE实例**

```sh
redis> XADD writers * name Virginia surname Woolf
"1601372731458-0"
redis> XADD writers * name Jane surname Austen
"1601372731459-0"
redis> XADD writers * name Toni surname Morrison
"1601372731459-1"
redis> XADD writers * name Agatha surname Christie
"1601372731459-2"
redis> XADD writers * name Ngozi surname Adichie
"1601372731459-3"
redis> XLEN writers
(integer) 5
redis> XREVRANGE writers + - COUNT 1
1) 1) "1601372731459-3"
   2) 1) "name"
   2) "Ngozi"
   3) "surname"
   4) "Adichie"
redis>
```



#### XREAD

使用 XREAD 以阻塞或非阻塞方式获取消息列表 ，语法格式：

```sh
XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...]
```

-   count ：数量
-   milliseconds ：可选，阻塞毫秒数，没有设置就是非阻塞模式
-   key ：队列名
-   id ：消息ID
    -   $ : $代表特殊ID，表示以当前Stream已经存储的最大的ID作为最后一个ID
    -   0-0：代表最小的ID开始获取队列中的消息，000/0/0-0都可以

阻塞它会等待新的消息添加进来直接读取返回，会等待到指定的毫秒数

非阻塞直接获取消息返回



**实例**

*# 从 Stream 头部读取两条消息*

```sh
####################   非阻塞  #######################
redis> XREAD COUNT 2 STREAMS mystream 0-0
1) 1) "mystream"
   2) 1) 1) "1683705561947-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
      2) 1) "1683705575519-0"
         2) 1) "k3"
            2) "v3"
            3) "k6"
            4) "v6"
            
127.0.0.1:6379> XREAD count 2 streams mystream $
# 此时队列中不存在最大ID ，所以返回的为空
(nil)

####################   阻塞  #######################
# 1 设置阻塞获取 - 会等待新消息进来
127.0.0.1:6379> XREAD count 2 block 20000 streams mystream $

# 2 打开第二个客户端添加消息
127.0.0.1:6379> XADD mystream *  k1 v2
"1683708163453-0"

# 3 此时获取到了添加到的消息
127.0.0.1:6379> XREAD count 2 block 20000 streams mystream $
1) 1) "mystream"
   2) 1) 1) "1683708163453-0"
         2) 1) "k1"
            2) "v2"
(3.73s
```



#### XGROUP CREATE

基于 Stream 实现的消息队列，如何保证消费者在发生故障或宕机再次重启后，仍然可以读取未处理完的消息？

-   Streams 会自动使用内部队列（也称为 PENDING List）留存消费组里每个消费者读取的消息保底措施，直到消费者使用 XACK 命令通知 Streams“消息已经处理完成”。
-   消费确认增加了消息的可靠性，一般在业务处理完成之后，需要执行 XACK 命令确认消息已经被消费完成

![image-20230510165831928](E:\学习笔记\img\img01\image-20230510165831928.png)

使用 XGROUP CREATE 创建消费者组，语法格式：

```sh
XGROUP CREATE key groupname id|$ [MKSTREAM] [ENTRIESREAD entries_read]
```

-   key ：队列名称，如果不存在就创建
-   groupname ：组名。
-   $ ： 表示从尾部开始消费，只接受新消息，当前 Stream 消息会全部忽略。

从头开始消费:

```shell
XGROUP CREATE mystream consumer-group-name 0-0  
```

从尾部开始消费:

```sh
XGROUP CREATE mystream consumer-group-name $
```



**案例**

```sh
127.0.0.1:6379> XGROUP CREATE mystream groupA $
OK
127.0.0.1:6379> XGROUP CREATE mystream groupB 0
OK
```



#### XREADGROUP GROUP

使用 XREADGROUP GROUP 读取消费组中的消息，语法格式：

```sh
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...]
```

-   group ：消费组名
-   consumer ：消费者名。
-   count ： 读取数量。
-   milliseconds ： 阻塞毫秒数。
-   key ： 队列名。
-   ID ： 消息 ID。

```sh
XREADGROUP GROUP consumer-group-name consumer-name COUNT 1 STREAMS mystream >
```



**案例**

```sh
127.0.0.1:6379> XREADGROUP group groupA consumer1 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1683705561947-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
      2) 1) "1683705575519-0"
         2) 1) "k3"
            2) "v3"
            3) "k6"
            4) "v6"
	 ......
# 每个组只能消费一次，第二次消费就为nil了
127.0.0.1:6379> XREADGROUP group groupA consumer1 streams mystream >
(nil)
# 同一个的消费者也是不能消费同一条消息
127.0.0.1:6379> XREADGROUP group groupA consumer2 streams mystream >
(nil)

# 换一个组就又可以消费了
127.0.0.1:6379> XREADGROUP group groupB consumer1 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1683705561947-0"
         2) 1) "k1"
            2) "v1"
            3) "k2"
            4) "v2"
   ......
```

**消费者的目的**：让组内的多个消费者共同分担读取消息，所以，我们通常会让每个消费者读取部分消息，从而实现消息读取负载在多个消费者间是均衡分布的



#### XGROUP DESTROY

删除消费组或组中的消费者

```sh
XGROUP DELCONSUMER key groupname consumername
```



#### XPENDING

使用 XPENDING 查看组中的 已读取但未确认 的消息，语法格式：

```sh
XPENDING key group [[IDLE min-idle-time] start end count [consumer]]
```

-   key ：队列名
-   group ：组名。
-   count ： 读取数量。
-   milliseconds ： 阻塞毫秒数。
-   key ： 。
-   ID ： 消息 ID。

```sh
XREADGROUP GROUP consumer-group-name consumer-name COUNT 1 STREAMS mystream >
```



**案例**

```sh
#==================   查看消费组消费的消息  ===================== 
127.0.0.1:6379> XPENDING mystream groupA
1) (integer) 8			# 消费条数
2) "1683705561947-0"	# 最小ID
3) "1683708163453-0"	# 最大ID
4) 1) 1) "consumer1"	# 那个组消费的
      2) "8"
127.0.0.1:6379> XPENDING mystream groupC
1) (integer) 6
2) "1683705561947-0"
3) "1683707565771-0"
4) 1) 1) "consumer1"
      2) "2"
   2) 1) "consumer2"
      2) "2"
   3) 1) "consumer3"
      2) "2"
      
#==================   查看消费组中某一个消费者消费的消息  =====================      
127.0.0.1:6379> XPENDING mystream groupC - + 10 consumer2
1) 1) "1683705641820-0"
   2) "consumer2"
   3) (integer) 268096
   4) (integer) 1
2) 1) "1683705646742-0"
   2) "consumer2"
   3) (integer) 268096
   4) (integer) 1
```





#### XACK

使用 XACK 向 已读取但未确认 的消息发送确认，语法格式：

```sh
XACK key group id [id ...]
```



**案例**

```sh
# 查看已读未确定的消息
127.0.0.1:6379> XPENDING mystream groupC - + 10 consumer1
1) 1) "1683705561947-0"
   2) "consumer1"
   3) (integer) 616938
   4) (integer) 1
2) 1) "1683705575519-0"
   2) "consumer1"
   3) (integer) 616938
   4) (integer) 1
   
# ACK签收消息
127.0.0.1:6379> XACK mystream groupC 1683705561947-0
(integer) 1

# 再次查看发现没有了签收的那条消息
127.0.0.1:6379> XPENDING mystream groupC - + 10 consumer1
1) 1) "1683705575519-0"
   2) "consumer1"
   3) (integer) 642287
   4) (integer) 1
```



#### XINFO

用户打印 Stream / Consumer / Group 的详细消息，格式如下：

```sh
XINFO stream mystream [FULL [COUNT count]]
XINFO CONSUMERS key groupname
XINFO GROUPS key
```





## bitfield(位域)（了解）

[BITFIELD](https://www.redis.com.cn/commands/bitfield.html) 命令可以将一个 Redis 字符串看作是一个由二进制位组成的数组， 并对这个数组中任意偏移进行访问 。 可以使用该命令对一个有符号的 5 位整型数的第 1234 位设置指定值，也可以对一个 31 位无符号整型数的第 4567 位进行取值。类似地，本命令可以对指定的整数进行自增和自减操作，可配置的上溢和下溢处理操作。

`BITFIELD`命令可以在一次调用中同时对多个位范围进行操作：它接受一系列待执行的操作作为参数，并返回一个数组，数组中的每个元素就是对应操作的执行结果。

例如，对位于 5 位有符号整数的偏移量 100 执行自增操作，并获取位于偏移量 0 上的 4 位长无符号整数：

```
> BITFIELD mykey INCRBY i5 100 1 GET u4 0
1) (integer) 1
2) (integer) 0
```

注意:

1.  使用 [GET](https://www.redis.com.cn/commands/get.html) 子命令对超出字符串当前范围的二进制位进行访问（包括键不存在的情况），超出部分的二进制位的值将被当做是 0。
2.  使用 [SET](https://www.redis.com.cn/commands/set.html) 或 [INCRBY](https://www.redis.com.cn/commands/incrby.html) 子命令对超出字符串当前范围的二进制位进行访问将导致字符串被扩大，被扩大的部分会使用值为 0 的二进制位进行填充。在对字符串进行扩展时，命令会根据字符串目前已有的最远端二进制位，计算出执行操作所需的最小长度。



### 语法

redis [BITFIELD](https://www.redis.com.cn/commands/bitfield.html) 命令基本语法如下：

```sh
redis 127.0.0.1:6379> BITFIELD key [GET type offset] [SET type offset value] [INCRBY type offset increment] [OVERFLOW WRAP|SAT|FAIL]
```



### 支持的子命令以及整数类型

支持的子命令：

-   **GET** `<type>` `<offset>` -- 返回指定的二进制位范围。
-   **SET** `<type>` `<offset>` `<value>` -- 对指定的二进制位范围进行设置，并返回它的旧值。
-   **INCRBY** `<type>` `<offset>` `<increment>` -- 对指定的二进制位范围执行加法操作，并返回它的旧值。可以通过向 increment 参数传入负值来实现相应的减法操作。

除了以上三个子命令之外，还有一个子命令，它可以改变之后执行的 [INCRBY](https://www.redis.com.cn/commands/incrby.html) 子命令在发生溢出情况时的行为:

-   **OVERFLOW** `[WRAP|SAT|FAIL]`

当被设置的二进制位范围值为整数时，用户可以在类型参数的前面添加 `i` 来表示有符号整数， `u` 来表示无符号整数。 比如说，我们可以使用 `u8` 来表示 8 位长的无符号整数，也可以使用 `i16` 来表示 16 位长的有符号整数。

命令最大支持 64 位长的有符号整数以及 63 位长的无符号整数，其中无符号整数的 63 位长度限制是由于 Redis 协议目前还无法返回 64 位长的无符号整数而导致的。



### 二进制位和位置偏移量

在二进制位范围命令中，用户有两种方法来设置偏移量：如果用户给定的是一个没有任何前缀的数字，那么这个数字指示的就是字符串以零为开始（zero-base）的偏移量。

如果用户给定的是一个带有 `#` 前缀的偏移量，那么命令将使用这个偏移量与被设置的数字类型的位长度相乘，从而计算出真正的偏移量。例如：There are two ways in order to specify offsets in the bitfield command.

```
BITFIELD mystring SET i8 #0 100 SET i8 #1 200
```

命令会把 mystring 里面，第一个 i8 长度的二进制位的值设置为100 并把第二个 i8 长度的二进制位的值设置为 200 。当我们把一个字符串键当成数组来使用，并且数组中储存的都是同等长度的整数时，使用 `#` 前缀可以让我们免去手动计算被设置二进制位所在位置的麻烦。



### 溢出控制

用户可以通过 `OVERFLOW` 以下三个参数来控制BITFIELD 命令在执行自增或者自减操作时上溢出和下溢出：

-   **WRAP**: 使用回绕（wrap around）方法处理有符号整数和无符号整数的溢出情况。对于无符号整数来说，回绕就像使用数值本身与能够被储存的最大无符号整数执行取模计算，这也是 C 语言的标准行为。对于有符号整数来说，上溢将导致数字重新从最小的负数开始计算，而下溢将导致数字重新从最大的正数开始计算。比如说，如果我们对一个值为 127 的 i8 整数执行加一操作，那么将得到结果 -128。
-   **SAT**: 使用饱和计算（saturation arithmetic）方法处理溢出，也即是说，下溢计算的结果为最小的整数值，而上溢计算的结果为最大的整数值。举个例子，如果我们对一个值为 120 的 i8 整数执行加 10 计算，那么命令的结果将为 i8 类型所能储存的最大整数值 127 。与此相反，如果一个针对 i8 值的计算造成了下溢，那么这个 i8 值将被设置为 -127 。
-   **FAIL**: 在这一模式下，命令将拒绝执行那些会导致上溢或者下溢情况出现的计算，并向用户返回空值表示计算未被执行。

需要注意的是， `OVERFLOW` 做作用于该命令之后，下一个 `OVERFLOW` 子命令之前的 [INCRBY](https://www.redis.com.cn/commands/incrby.html) 命令。

默认情况下, [INCRBY](https://www.redis.com.cn/commands/incrby.html) 使用 **WRAP** 参数。

```
> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
1) (integer) 1
2) (integer) 1
> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
1) (integer) 2
2) (integer) 2
> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
1) (integer) 3
2) (integer) 3
> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
1) (integer) 0
2) (integer) 3
```



### 返回值

命令的返回值是一个数组，数组中的每个元素对应一个被执行的子命令。需要注意的是，`OVERFLOW` 子命令本身并不产生任何回复。

例如下面的 `OVERFLOW FAIL` 返回 NULL 。

```
> BITFIELD mykey OVERFLOW FAIL incrby u2 102 1
1) (nil)
```



### 用途

BITFIELD 命令的作用在于它能够将很多小的整数储存到一个长度较大的位图中，又或者将一个非常庞大的键分割为多个较小的键来进行储存，从而非常高效地使用内存，使得 Redis 能够得到更多不同的应用 ——特别是在实时分析领域：BITFIELD 能够以指定的方式对计算溢出进行控制的能力，使得它可以被应用于这一领域。



### 性能考虑

[BITFIELD](https://www.redis.com.cn/commands/bitfield.html) 在一般情况下都是一个快速的命令，需要注意的是，访问一个长度较短的字符串的远端不存在的二进制位将引发一次内存分配操作，这一操作花费的时间可能会比命令访问已有的字符串花费的时间要长。



### 二进制位的顺序

[BITFIELD](https://www.redis.com.cn/commands/bitfield.html) 把位图第一个字节偏移量 0 上的二进制位看作是 most significant 位，以此类推。举个例子，如果我们对一个已经预先被全部设置为 0 的位图进行设置，将它在偏移量 7 的值设置为 5 位无符号整数值 23 （二进制位为 10111 ），那么命令将生产出以下这个位图表示：

```
+--------+--------+
|00000001|01110000|
+--------+--------+
```

当偏移量和整数长度与字节边界进行对齐时，BITFIELD 表示二进制位的方式跟大端表示法（big endian）一致，但是在没有对齐的情况下，理解这些二进制位是如何进行排列也是非常重要的。







# Redis持久化

## 1、概述

为什么需要持久化？

Redis的数据是存储在内存中的，内存断点即失，那么如果在没有持久化的情况下，再次重启Redis就会出现缓存为空的情况，那么这个时候缓存时失效的，所有的访问压力就会在MySQL数据库上就会导致MySQL宕掉，为了不让这种情况的发生，我们可以持久化Redis中的数据，宕机之后再次启动我们就可以直接将持久化的数据直接加载到Redis中，省去了重新写入的步骤，让缓存可以立刻生效，但是持久化还是会带来不同的问题？Redis中有两种方式来进行持久化，分别是RDB和AOF

-   RDB：快照的形式保存，恢复的时候读取快照文件进行恢复
-   AOF：每次操作都进行记录，恢复的时候重新执行命令进行恢复

![image-20230515144728613](E:\学习笔记\img\img01\image-20230515144728613.png)





## 2、RDB(Redis Database)

### 2.1 介绍

RDB（Redis Database）：实现类似照片记录效果的方式，就是把某一时刻的数据和状态以文件的形式写到磁盘上，也就是快照。这样一来即使故障宕机，快照文件也不会丢失，数据的可靠性也就得到了保证。这个快照文件就称为RDB文件(dump.rdb)，其中，RDB就是Redis DataBase的缩写。



### 2.2 基本使用

#### 2.2.1 自动触发

##### ①介绍

要使用RDB需要在配置中进行持久化配置，在Redis配置文件 `` 配置项中进行修改。配置的是自动触发，也就是在某段时间内保存了多少次数据就进行保存



##### ②配置

==要注意不同版本它们的配置文件可能不同，本次使用的版本是7.0.11，可以设置多个规则==

```sh
# 表示的是 在多少秒内修改多少次就保存一次
save <seconds> <changes> [ <seconds> <changes> ...]
```

-   是Reids6.2以下版本的配置文件

    ![image-20230515151627656](E:\学习笔记\img\img01\image-20230515151627656.png)

-   Reids6.2到Redis7.0的配置文件，可以配置多个规则

    ![image-20230515150305412](E:\学习笔记\img\img01\image-20230515150305412.png)

-   指定文件名

    ```sh
    dbfilename dump.rdb
    ```

-   指定文件保存的位置

    ```sh
    dir /opt/redis/redis-7.0.11/saveRDB
    ```



##### ③案例

**测试RDB策略是否成功**

-   设置规则

    ```sh
    # 设置保存频率，5秒钟内修改2次就保存一次
    save 5 2
    # 存放文件名
    dbfilename dump.rdb
    # 设置存放位置
    dir /opt/redis/redis-7.0.11/saveRDB/
    ```

-   重新启动Redis

-   进入Redis客户端，五秒内添加或修改两条数据

    ```sh
    127.0.0.1:6379> set k1 v1
    OK
    127.0.0.1:6379> set k2 v2
    OK
    127.0.0.1:6379> set k3 v3
    OK
    127.0.0.1:6379> set k4 v4
    OK
    127.0.0.1:6379> keys *
    1) "k1"
    2) "k4"
    3) "k3"
    4) "k2"
    ```

-   查看是否有生成文件

    ![image-20230515181151330](E:\学习笔记\img\img01\image-20230515181151330.png)

-   再次5秒内添加2条数据，查看文件大小的变化

    ![image-20230515181330329](E:\学习笔记\img\img01\image-20230515181330329.png)

    可以看到文件是有变化的，证明它执行了保存动作，nice~~~



**数据恢复测试**

将Redis停止，接着再次启动查看是否有数据，如果有那么证明数据恢复成功，因为RDB会自动找到我们保存的文件，然后将它读入到内存中完成恢复

```sh
[root@localhost saveRDB]# systemctl stop redis.service
[root@localhost saveRDB]# systemctl start redis.service
[root@localhost saveRDB]# redis-cli 
127.0.0.1:6379> keys *
1) "k2"
2) "k3"
3) "k4"
4) "k1"
127.0.0.1:6379>
```

恢复没问题~~~。



#### 2.2.2 手动触发

##### ① 介绍

有时候一些重要的数据我们不想等它执行到对应次数的时候才进行备份，想让它立刻进行备份，这个时候就需要我们手动进行备份



##### ②命令

-   Save：在主程序中执行会阻塞当前Redis服务器，直到它持久化完成，意味着所有的请求Redis此刻都不能进行处理，这在线上环境是非常危险的操作，所以线上是禁用的

-   BGSAVE（默认）：Redis会在后台异步进行持久化从中，不阻塞当前服务器，快照同时还可以响应客户端请求，该触发方式会fork一个进程由子进程复制持久化过程
    ![image-20230515182759845](E:\学习笔记\img\img01\image-20230515182759845.png)

    fork：在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，尽量避免膨胀。

-   LASTSAVE：获取最后一次执行快照的时间



##### ③案例

```sh
127.0.0.1:6379> set k5 v5
OK
127.0.0.1:6379> BGSAVE
Background saving started
```

![image-20230515182953251](E:\学习笔记\img\img01\image-20230515182953251.png)

完美哈哈~~





### 2.3 优势和劣势

**优势**

![image-20230515184606584](E:\学习笔记\img\img01\image-20230515184606584.png)

小总结：

-   适合大规模的数据恢复
-   按照业务进行定时备份
-   对数据完整性和一致性要求不高
-   RDB文件在内存中的加载速度要比AOF快得多



**劣势**

![image-20230515184744157](E:\学习笔记\img\img01\image-20230515184744157.png)

小总结：

-   在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失从当前至最近一次快照期间的数据，快照之间的数据丢失

-   内存数据的全量同步，如果数据量太大就会导致I/O严重影响服务器性能

-   RDB依赖于主进程的fork，在更大的数据集中，这可能会导致服务请求的瞬间延迟

    frok的时候内存中的数据被克隆一份，大致2倍的膨胀性，需要考虑



### 2.4如何检查修复rdb文件

```sh
[root@localhost bin]# pwd
/usr/local/bin
[root@localhost bin]# ll
total 21852
-rw-r--r--. 1 root root       92 Nov 11  2022 dump.rdb
-rwxr-xr-x. 1 root root      248 Nov 27 07:47 normalizer
-rwxr-xr-x. 1 root root     2363 Oct 17  2022 pcre-config
-rwxr-xr-x. 1 root root    97816 Oct 17  2022 pcregrep
-rwxr-xr-x. 1 root root   195008 Oct 17  2022 pcretest
-rwxr-xr-x. 1 root root  5205176 May 10 01:23 redis-benchmark
# 检查aof文件
lrwxrwxrwx. 1 root root       12 May 10 01:23 redis-check-aof -> redis-server
# 检查rdb文件
lrwxrwxrwx. 1 root root       12 May 10 01:23 redis-check-rdb -> redis-server
-rwxr-xr-x. 1 root root  5422592 May 10 01:23 redis-cli
lrwxrwxrwx. 1 root root       12 May 10 01:23 redis-sentinel -> redis-server
-rwxr-xr-x. 1 root root 11436968 May 10 01:23 redis-server
# 检验文件
[root@localhost bin]# redis-check-rdb /opt/redis/redis-7.0.11/saveRDB/dump.rdb 
[offset 0] Checking RDB file /opt/redis/redis-7.0.11/saveRDB/dump.rdb
[offset 27] AUX FIELD redis-ver = '7.0.11'
[offset 41] AUX FIELD redis-bits = '64'
[offset 53] AUX FIELD ctime = '1684146533'
[offset 68] AUX FIELD used-mem = '956016'
[offset 80] AUX FIELD aof-base = '0'
[offset 82] Selecting DB ID 0
[offset 129] Checksum OK
[offset 129] \o/ RDB looks OK! \o/
[info] 5 keys read
[info] 0 expires
[info] 0 already expired
```

如果出现了错误不能修复，只能找另外的方式进行修改了！！！



### 2.5 快照配置详解

```sh
################################ SNAPSHOTTING  ################################
# 配置保存的规则
save <seconds> <changes> [ <seconds> <changes> ...]
# 文件名
dbfilename dump.rdb
# 文件保存位置
dir /
# 默认是yes
# 如果配置成no，表示你不在乎数据一致性或者有其他的手段发现和控制这种不一致，那么快照写入失败时，也能确保redis继续接收新的请求
stop-writes-on-bgsave-error yes
# 默认yes
# 对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。
# 如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能
rdbcompression yes
# 默认yes
# 在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，
# 如果希望获取到最大的性能提升，可以关闭此功能，建议开启
rdbchecksum yes
# 在没有持久性的情况下删除复制中使用的RDB文件启用。默认情况下no，此选项是禁用的。
rdb-del-sync-files no
```



### 2.6 触发和禁用RDB快照

什么情况下会触发快照？

-   配置文件中默认的快照配置
-   手动执行 save / bgsave 命令
-   执行 flushdb / flushall 命令也会产生 dump.rdb 文件，但是里面是空的，毫无意义
-   执行shutdown 且没有设置开启AOF持久化
-   主从复制时，主节点自动触发



禁用RDB快照

-   命令方式：

    ```sh
    redis-cli config set save ""
    ```

-   配置方式：

    ```sh
    save ""
    ```

    ![image-20230517082445957](E:\学习笔记\img\img01\image-20230517082445957.png)













## 3、AOF

### 3.1 概述

AOF（Append Only file）是以日志的形式**记录每个写的操作**，只可以追加文件但不可以修改文件，Redis在重启的时候会将日志中的命令重新执行一遍以达到恢复数据的目的。

在默认情况下，redis是没有开启AOF的，开启AOF需要在配置文件中进行配置。

**AOF的持久化工作流程**

![image-20230517162938864](E:\学习笔记\img\img01\image-20230517162938864.png)

1.  客户端接收命令的输入
2.  Redis Server不会直接写入到AOF文件中，而是先将命令放入到AOF缓存区中进行保存，这是为了不频繁的去进行IO操作，到达一定量以后再写入磁盘
3.  AOF缓冲会根据AOF缓冲同步文件的三种策略将命令写入磁盘上的AOF文件
4.  由于AOF只需追加所以多次修改的内容会使文件膨胀，所以AOF到达一定的量就会进行命令的合并（又称AOF重写），从而起到减少恢复加载的时间和效率。
5.  Redis服务器重启的时候会从AOF文件中载入数据用来恢复

**写回策略**

![image-20230517165203381](E:\学习笔记\img\img01\image-20230517165203381.png)

| 配置项           | 功能                                                         |
| ---------------- | ------------------------------------------------------------ |
| Always           | 同步写回，每个命令执行完成立刻同步地将日志写回磁盘           |
| Everysec（默认） | 每秒协会，每个写命令执行完，只是先把日志写到AOF文件的内存缓冲区，每隔1秒把缓冲区中的内容写入磁盘 |
| No               | 操作系统控制的写回，每个命令执行完成，只是先把日志写到AOF文件的内容缓冲区，由操作系统决定何时将缓冲区内容写回磁盘 |

![image-20230517164413096](E:\学习笔记\img\img01\image-20230517164413096.png)



### 3.2 配置项

这里列举常用的配置项

```sh
# 开启AOF，默认是关闭
appendonly no

# 保存的aof的文件名
appendfilename "appendonly.aof"

# 在快照配置的dir配置项所在的文件下再创建一个文件夹，redis7和6的区别，因为reids7它的aof文件变成了三个（重大变化）
# appendonly.aof.1.base.rdb		base：基本文件，重写的时候会重写到这个文件中
# appendonly.aof.1.incr.aof		incr：追加文件，记录所有的写命令
# appendonly.aof.manifest	    manifest：清单文件
appenddirname "appendonlydir"

# 三种文件同步方式
appendfsync always
appendfsync everysec
appendfsync no

# aof重写期间是否同步，默认为no
no-appendfsync-on-rewrite no

# 重写触发配置、文件重写策略
# 当前的AOF文件的大小相较于上次文件的大小增长了100%
auto-aof-rewrite-percentage 100
# 当文件达到最小64mb时将文件进行合并重写
auto-aof-rewrite-min-size 64mb
```



### 3.3 案例

#### 3.3.1 正常恢复

1.  启动aof，修改配置文件

2.  新生成了dump.rdb和aof文件夹

    ```sh
    [root@localhost saveRDB]# ll
    total 4
    drwxr-xr-x. 2 root root 103 May 17 01:57 appendonlydir
    -rw-r--r--. 1 root root 108 May 17 02:29 dump.rdb
    [root@localhost appendonlydir]# cd appendonlydir
    [root@localhost appendonlydir]# ll
    total 12
    -rw-r--r--. 1 root root  89 May 17 01:57 appendonly.aof.1.base.rdb
    -rw-r--r--. 1 root root 0 May 17 02:29 appendonly.aof.1.incr.aof
    -rw-r--r--. 1 root root  88 May 17 01:57 appendonly.aof.manifest
    ```

    可以看到 incr文件刚创建是为0的，因为此时还没有数据执行命令

3.  启动redis，输入命令添加几条数据，再次查看aof文件夹变化

    ```sh
    [root@localhost appendonlydir]# ll                                                        
    total 12                                                                                  
    -rw-r--r--. 1 root root  89 May 17 01:57 appendonly.aof.1.base.rdb      
    # 可以看到文件大小变化了
    -rw-r--r--. 1 root root 110 May 17 02:29 appendonly.aof.1.incr.aof                        
    -rw-r--r--. 1 root root  88 May 17 01:57 appendonly.aof.manifest   
    ```

4.  查看 `incr`文件内容是怎样的结构

    ```sh
    [root@localhost appendonlydir]# cat appendonly.aof.1.incr.aof 
    *2
    $6
    SELECT
    $1
    0
    *3
    $3
    set
    $2
    k1
    $2
    v1
    *3
    $3
    set
    $2
    k2
    $2
    v2
    *3
    $3
    set
    $2
    k3
    $2
    v3
    ```

    可以看到命令被记录下来了

5.  停止redis并将对应文件夹下的 ` .rdb`文件删除，然后再次启动查看数据是否重新写入

    ```sh
    [root@localhost saveRDB]# redis-cli                                                       
    127.0.0.1:6379> keys *                                                                    
    1) "k2"                                                                                   
    2) "k3"                                                                                   
    3) "k1"  
    # 没问题，很nice~~~
    ```

    

#### 3.3.2 异常恢复

**问题**：redis执行持久化操作的时候意外down机了，此时数据出现不完整，该如何解决呢？

**解决**：使用 `/usr/local/bin`下的 `redis-check-aof --fix` 命令来修复

![image-20230517174058570](E:\学习笔记\img\img01\image-20230517174058570.png)

**模拟情景**

1.  停止redis服务器，将 `.incr` 文件的数据进行修改

    ![image-20230517174308912](E:\学习笔记\img\img01\image-20230517174308912.png)

2.  再次启动redis发现报错

    ![image-20230517174433684](E:\学习笔记\img\img01\image-20230517174433684.png)

3.  修复文件

    ```sh
    [root@localhost bin]# redis-check-aof --fix /opt/redis/redis-7.0.11/saveRDB/appendonlydir/appendonly.aof.1.incr.aof
    Start checking Old-Style AOF
    0x              6a: Expected \r\n, got: 3432
    AOF analyzed: filename=/opt/redis/redis-7.0.11/saveRDB/appendonlydir/appendonly.aof.1.incr.aof, size=136, ok_up_to=81, ok_up_to_line=26, diff=55
    This will shrink the AOF /opt/redis/redis-7.0.11/saveRDB/appendonlydir/appendonly.aof.1.incr.aof from 136 bytes, with 55 bytes, to 81 bytes
    Continue? [y/N]: y
    Successfully truncated AOF /opt/redis/redis-7.0.11/saveRDB/appendonlydir/appendonly.aof.1.incr.aof
    ```

    查看文件情况

    ![image-20230517174711947](E:\学习笔记\img\img01\image-20230517174711947.png)

    发现k3的值被删除了

4.  再次启动就启动成功了~~~

此案例中我们发现我们可以手动修改`.incr`文件，也就是说当我们错误使用`flushall`等删库命令的时候只需要将`.incr`文件中的命令删除一下即可

```sh
127.0.0.1:6379> keys *
1) "k2"
2) "k1"
127.0.0.1:6379> FLUSHDB
OK
```

停止redis服务器，接着找到`.incr`中的flushdb命令进行删除，再次重启就可以将数据进行恢复了



### 3.4 优势和劣势

优势：更好的保护数据不丢失，性能高，可做紧急恢复

劣势：

-   同样的数据量，占用空间比rdb的要大，恢复速度比rdb的要慢
-   aof运行效率要满于rdb，每秒同步策略效率较好，不同步频率和rdb相同



### 3.5 AOF重写机制

#### 3.5.1 概述

AOF的运行原理是将命令追加到 `.incr` 文件中，它不会进行替换，那么我们多次设置同一个值就会占有多次修改的空间，这是不必要的，所以AOF有一个重写机制来将这些重复修改的命令进行合并，我们称这个操作为重写机制

**重写机制原理**

1.  在重写开始前，redis会创建一个“重写子进程”，这个子进程会读取现有的AOF文件，并将其包含的指令进行分析压缩并写入到一个临时文件中。
2.  与此同时，主进程会将新接收到的写指令一边累积到内存缓冲区中，一边继续写入到原有的AOF文件中，这样做是保证原有的AOF文件的可用性，避免在重写过程中出现意外。
3.  当“重写子进程”完成重写工作后，它会给父进程发一个信号，父进程收到信号后就会将内存中缓存的写指令追加到新AOF文件中
4.  当追加结束后，redis就会用新AOF文件来代替旧AOF文件，之后再有新的写指令，就都会追加到新的AOF文件中
5.  重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似



#### 3.5.2 触发机制

**自动触发**

需要进行对应的配置，在redis中的默认配置

![image-20230517183701000](E:\学习笔记\img\img01\image-20230517183701000.png)

表示的是在 当文件大小比上次重写的文件大 百分值一百 倍后且文件大于 64M时就进行重写



**手动触发**

客户端向服务器发送 `bgrewriteaof` 命令来手动触发重写机制



#### 3.5.3 案例

需求说明：将配置项规则改为

```sh
# 关闭混合，设置为no
aof-use-rdb-preamble no

# 设置重写规则
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 1k
```

接着多次对一个值进行修改，当 `.incr` 文件达到对应大小是，看是否有触发重写机制

![image-20230517185209101](E:\学习笔记\img\img01\image-20230517185209101.png)

此时的`.incr`文件大小

![image-20230517184547722](E:\学习笔记\img\img01\image-20230517184547722.png)

接着疯狂添加数据

![image-20230517185949491](E:\学习笔记\img\img01\image-20230517185949491.png)

观察发现，当超过1k的数据就会进行文件的重写，因为只对一个数据进行修改，所以数据量时一样的

![image-20230517185827316](E:\学习笔记\img\img01\image-20230517185827316.png)

完美~~~~





## 4、RDB和AOF混合持久化

### 4.1 两种模式同时开启

**问题**：RDB和AOF是否可以共存，如果可以那么先后顺序是怎样的？

**回答**：是可以共存的，在两者都开启的情况下，重启时只会加载aof文件，不会加载rdb文件

![image-20230518145814549](E:\学习笔记\img\img01\image-20230518145814549.png)

同时开启两种持久化模式，Redis会优先加载AOF文件来做恢复，AOF的数据完整性是比RDB的要好的。既然AOF的数据完整性更好，为什么还要开启RDB呢，因为AOF文件在不断的变化，RDB是不变的更适合做备份。



### 4.2 混合模式（推荐）

混合模式就是RDB+AOF，RDB负责全量数据保存，AOF负责增量数据保存，当**重写策略被触发或者手动触发重写策略**的时候首先会将所有的数据进行全量保存，于此同时还会接收新的命令保存到AOF文件中，重写完成之后就会删除原有的AOF文件，生成新的AOF文件，这个时候的AOF文件包含两部分，**一部分是RDB格式，一部分是AOF模式**。

![image-20230518161554640](E:\学习笔记\img\img01\image-20230518161554640.png)

这样做的好处就是在RDB被触发的区间内也能进行数据持久化，down机之后也能保证数据的完整性，由于前面的数据都是使用RDB进行存储的，所以恢复的时间也有所优化。后面的数据是AOF进行保存的，所以也可以保持数据的完整性。推荐使用~~~

**开启混合模式**

```sh
aof-use-rdb-preamble yes
```







## 5、纯缓存模式Only

开启持久化模式会消耗一部分的性能，为了使 Redis 性能达到极致我们可以使用纯缓存模式，需要持久化的时候可以使用命令的方式进行持久化

-   关闭RDB

    ```sh
    save ""
    ```

-   关闭AOF

    ```sh
    appendonly no
    ```







# redis事物

## 1、概述

redis事物是一个单独的隔离操作，事物中的命令都会被序列化，按顺序执行。事物在执行的过程中，不会被其他客户端发来的命令打断

Redis事物的主要作用就是串联多个命令，防止命令之间插队



**事物的两个阶段**

![image-20220508100023529](E:\学习笔记\img\image-20220508100023529.png)

组队阶段：通过Multi命令开启组队，客户端发送过来的命令就会放到队列中，并不会执行

执行阶段：通过Exec命令执行队列中的命令，按顺序执行，如果在执行阶段之前使用了discard命令，事物结束





## 2、使用事物

-   multi：开启事物，进行组队
-   discard：撤销组队
-   exec：执行命令

![image-20220508101107026](E:\学习笔记\img\image-20220508101107026.png)



## 3、事物中出现异常

**情况一**：在组队阶段有异常，执行失败，所有命令不执行

![image-20220508101345926](E:\学习笔记\img\image-20220508101345926.png)



**情况二**：执行阶段有异常，有异常的那条数据执行失败，其他的成功执行

![image-20220508101600032](E:\学习笔记\img\image-20220508101600032.png)



**总结**

-   语法出现错误就会导致所有的操作一起失败
-   语法没有错误，但是它的使用不合法，那么在提交事务的时候就是不合法的那个执行失败，其他执行成功





## 4、事物冲突 - 锁

### ①悲观锁

**悲观锁**：每一次访问都会给对应的key上锁，直到访问结束，其他客户端才能获取到这个key的值

这种方式虽然保证了数据安全性，但是它损失了效率，Redis作为缓存时需要很快的响应，所以这种方式一般不采用

![image-20220508101919065](E:\学习笔记\img\image-20220508101919065.png)



### ②乐观锁(推荐使用)

**乐观锁**：通过版本号来解决冲突，在进行事物更新时会看一下版本号是否有发生改变，如果发生改变，就要获取最新版本的数据，然后对其进行操作

-   **watch key [key ...]命令**：在multi执行之前，先执行watch key命令可以监视一个或多个key，如果在事物执行之前这个（或这些）key被其他命令改动，那么事物也被打断
-   **UNWATCH**：取消监听，当我们发现有人修改了值我们可以手动取消监听

![image-20220508102911839](E:\学习笔记\img\image-20220508102911839.png)

![image-20220508102933297](E:\学习笔记\img\image-20220508102933297.png)

**说明**：客户端1将key进行修改exec执行，那么其他客户端得到的为null，多个客户端同时访问，只能有一个成功







## 5、事物的三大特性





## 6、秒杀案例 - 事物的使用

### ① ab工具模拟

安装

```sh
yum install -y httpd-tools
```



### ②代码实现





### ③ 超时和超卖问题





# Redis管道和发布订阅

## 管道

### 1、为什么需要管道？

Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。一个请求会遵循以下步骤：

1 客户端向服务端发送命令分四步(发送命令→命令排队→命令执行→返回结果)，并监听Socket返回，通常以阻塞模式等待服务端响应。

2 服务端处理命令，并将结果返回给客户端。

**上述两步称为：Round Trip Time(简称RTT,数据包往返于两端的时间)**

如果同时需要执行大量的命令，那么就要等待上一条命令应答后再执行，这中间不仅仅多了RTT（Round Time Trip），而且还频繁调用系统IO，发送网络请求，同时需要redis调用多次read()和write()系统方法，系统方法会将数据从用户态转移到内核态，这样就会对进程上下文有比较大的影响了，性能不太好，o(╥﹏╥)o

如果我们需要批量的执行多个命令，那么我们的RTT就会有多次，原生的 `mset`命令也可以批量进行设置，但是只能批量处理同一种类型，而管道时可以处理多种命令类型，批处理多个命令时得到优化。

**使用**

```sh
redis-cli --pie
```





### 2、案例

创建一个`.txt`文件，写入下面的内容

```txt
set k100 v100
set k200 v200
hset k300 name xiaohei
hset k300 age 20
hset k300 gender nan
```

批量执行命令

```sh
cat test.txt | redis-cli --pipe
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 5
```

查看命令是否已经执行

```sh
127.0.0.1:6379> keys *
1) "k300"
2) "k200"
5) "k100"
# nice~~~
```



### 3、注意事项

-   pipeline缓冲的指令只是会依次执行，不保证原子性，如果执行命令中发生异常，它不会停下，会继续执行后面的命令
-   使用 pipeline 组装的命令个数太多，不然数量过大客户端阻塞的时间可能过久，同时服务端此时也被迫回复一个队列的答复，占用很多的内存





## 发布订阅（了解）

### 1、介绍

它是一种消息通信模式，发送者(PUBLISH)发送消息，订阅者(SUBSCRIBE)接收消息，可以实现进程间的消息传递

它的工作模式就类似于我定于了微信的公众号，当公众号发布新的内容时就会推送内容给订阅了公众号的用户



### 2、常用命令

| 命令                                                         | 描述                               |
| :----------------------------------------------------------- | :--------------------------------- |
| [PSUBSCRIBE](https://redis.com.cn/commands/psubscribe.html)  | 订阅一个或多个符合给定模式的频道。 |
| [PUBSUB](https://redis.com.cn/commands/pubsub.html)          | 查看订阅与发布系统状态。           |
| [PUBLISH](https://redis.com.cn/commands/publish.html)        | 将信息发送到指定的频道。           |
| [PUNSUBSCRIBE](https://redis.com.cn/commands/punsubscribe.html) | 退订所有给定模式的频道。           |
| [SUBSCRIBE](https://redis.com.cn/commands/subscribe.html)    | 订阅给定的一个或多个频道的信息。   |
| [UNSUBSCRIBE](https://redis.com.cn/commands/unsubscribe.html) | 指退订给定的频道。                 |







# Redis复制(replica)

## 1、概述

Redis支持主从复制的高可用架构和故障转移，当主节点down掉的时候，从节点可以补上，所以这个模式提高了高可用性，不至于在主机down掉了之后就整个服务就不可用了。主从复制的模式还可以是主负责写入，从节点负责读操作，这样的话就可以分担服务的压力。

![image-20230519164225451](E:\学习笔记\img\img01\image-20230519164225451.png)

可以看到主节点会同步数据给从节点，从而达到复制的效果

**它可以**：

-   读写分离
-   容灾恢复
-   数据备份
-   水平扩容支撑高并发



## 2、使用

这里我们用 master 表示主节点，v表示从节点



### 2.1 配置

要使用主从复制只需要配置从库，主库是不用动的，配置从库来实现主从复制

```sh
# 指定master的IP和端口号
replicaof 主库IP 端口号

# 权限校验 
# master如果配置了密码登录，那么就需要从节点设置校验密码。否则master会拒绝 slave的 访问 
masterauth xxxxxx
```



### 2.2 基本操作命令

| 命令                    | 作用                                                        |
| ----------------------- | ----------------------------------------------------------- |
| info replication        | 查看复制节点的主从关系和配置信息                            |
| replicaof 主库IP 端口号 | 指定主库的ip和端口号（一般在配置文件中写入）                |
| slaveof 主库IP 端口号   | 每次与master 断开之后需要重新链接，除非配置进redis.conf文件 |
| slaveof no one          | 当前从库转为主库，自立山头                                  |





## 3、实战演练

**配置说明**：6379为主机，6380和6381为从机

![image-20230531171103999](E:\学习笔记\img\img01\image-20230531171103999.png)





### 3.1 一主二从

![image-20230531171103999](E:\学习笔记\img\img01\image-20230531171103999.png)



#### 3.1.1 配置文件指定

①复制一个新的redis.conf到`/myredis`目录下

②修改redis.conf的配置

```sh
# 开启daemonize yes 后台运行
# 注释掉bind 127.0.0.1. 
# 设置protected-mode为no
# 指定端口
# 指定当前工作目录，dir
dir /myredis
# log文件名字,logfile
logfile /myredis/redis.log
# requirepass
requirepass 111111
```

③复制配置文件到从机上并进行对应修改

```sh
# 修改端口号 -> 对应不同的redis定义对应不同的端口号
# # 指定master的IP和端口号
replicaof 192.168.10.10 6379

# master如果配置了密码登录，那么就需要从节点设置校验密码。否则master会拒绝 slave的 访问 
masterauth 111111
```

④测试 -> 先启动主机，再启动从机

⑤查看主从节点信息

-   日志文件

    -   主节点

        ![image-20230531165156839](E:\学习笔记\img\img01\image-20230531165156839.png)

    -   从节点

        ![image-20230531170055793](E:\学习笔记\img\img01\image-20230531170055793.png)

-   命令查看 -> 使用 `INFO replication` 进行查看

    -   主节点

        ![image-20230531164535854](E:\学习笔记\img\img01\image-20230531164535854.png)

    -   从节点

        ![image-20230531164608610](E:\学习笔记\img\img01\image-20230531164608610.png)

能看到对应的消息就表示成功开启了主从复制~~~~

⑥测试

在主节点上添加数据，查看从节点是否已经同步对应数据

```sh
# master节点
127.0.0.1:6379> mset k1 v1 k2 v2
OK
127.0.0.1:6379> keys *
1) "k2"
2) "k1"

# slave节点
127.0.0.1:6380> keys *
1) "k1"
2) "k2"

127.0.0.1:6381> keys *
1) "k1"
2) "k2"
```

没有问题，完美~~~



#### 3.1.2 主从节点问题

**从节点是否可以执行命令？**是不可以的

![image-20230531165441624](E:\学习笔记\img\img01\image-20230531165441624.png)



**从机切入点问题**

从节点down掉了，主机继续添加数据，那么从节点再次上线之后是否会同步数据呢？

是会的，首先会全量加载数据，接着就是增量同步数据

```sh
# 首先关闭 slave2
127.0.0.1:6381> SHUTDOWN

# master执行命令
127.0.0.1:6379> mset k4 v4 k5 v5
OK

# 再次启动slave2
127.0.0.1:6381> get k4
"v4"
127.0.0.1:6381> get k5
"v5"
```

结果发现，即使掉队了重新上线之后数据也是会同步过来的



**主机down掉之后从机会上位吗？**

```sh
# 关闭master
127.0.0.1:6379> SHUTDOWN

# 查看从节点信息
127.0.0.1:6380> info replication
role:slave
master_host:192.168.10.10
master_port:6379
master_link_status:down
......
```

可以发现从节点还是slave，而master显示已经down掉，所以从节点默认是不会在master节点down掉上位的



**主机shutdown后，重启后主从关系还在吗? 从机还能否顺利复制?** 

刚才关闭了master，此时再次启动查看状态信息

```sh
127.0.0.1:6380> info replication
role:slave
master_host:192.168.10.10
master_port:6379
master_link_status:up

# master添加数据
127.0.0.1:6379> set k6 v6
OK

# slave1查看数据
127.0.0.1:6380> get k6
"v6"
```

可以看到主从节点重新恢复了，从机也能顺利复制



**某台从机down后，master继续，从机重启后它能跟上大部队吗?**

是可以的



#### 3.1.3 命令手动指定

首先将从节点 slave1 和 slave2 设置的配置去掉

![image-20230531215637294](E:\学习笔记\img\img01\image-20230531215637294.png)

master密码得留着，进行注册的时候需要这个配置

接着再次启动查看节点信息

```sh
127.0.0.1:6380> info replication
role:master
connected_slaves:0
......
```

可以发现两个从节点都变成了master，因为我们没有进行配置。

接下来我们使用命令来让它们成为从节点

```sh
127.0.0.1:6380> SLAVEOF 192.168.10.10 6379
OK
127.0.0.1:6380> info replication
role:slave
master_host:192.168.10.10
master_port:6379
master_link_status:up
......
```

可以看到身份发生了变化，其他的从节点也是这样使用命令设置即可

**注意**：从机重启之后关系就不在了，需要再次命令建立关系





### 3.2 薪火相传

由于从节点的数量会给master同步数据的压力，所以我们需要由 slave 来同步给其他的 slave，从而减轻master同步数据的压力，这就是传说中的传纸条~~~

![image-20230531221108454](E:\学习笔记\img\img01\image-20230531221108454.png)

我们需要实现上图的关系连接，使用`slaveof 主机号 端口号` 来让 `slave2`改变门户，拜`slave1`为老大

```sh
# 此时我们是一主二从
127.0.0.1:6379> info replication
role:master
connected_slaves:2	# 可以看到有两个从节点

# 让slave2拜slave1为老大
127.0.0.1:6381> SLAVEOF 192.168.10.11 6380
OK
# 查看slave2信息
127.0.0.1:6381> INFO replication
role:slave
master_host:192.168.10.11	# 拜码头成功
master_port:6380


# 查看 slave1 的节点信息
127.0.0.1:6380> info replication
role:slave
master_host:192.168.10.10
master_port:6379
master_link_status:up
......
connected_slaves:1
slave0:ip=192.168.10.12,port=6381,state=online,offset=29568,lag=0
......
```

 可以看到 `slave1` 中又一个从节点，接着我们测试一下数据是否有进行同步

```sh
# master设置
127.0.0.1:6379> set test 11
OK

# slave1查看
127.0.0.1:6380> get test
"11"

# slave2查看
127.0.0.1:6381> get test
"11"
```

可以看到数据是已经进行同步了的，成功~~~





### 3.3 反客为主

让从节点重新变成主节点，执行下面命令或者删除复制的配置

```sh
# 方式一：注释或删除配置
# replicaof 192.168.10.10 6379

# 方式二：使用命令 -> SLAVEOF no one
127.0.0.1:6380> SLAVEOF no one
OK
127.0.0.1:6380> info replication
role:master
connected_slaves:1
......
```





## 4、工作原理和流程

1.  slave启动，同步初始

    slave启动成功连接master后会发送一个sync命令

2.  首次连接，全量复制

    主节点收到SYNC命令后，开始执行BGSAVE命令生成RDB文件并使用缓冲区记录所有接收到的写命令，接着从节点收到RDB文件后将它加载进内存，完成复制初始化

3.  心跳持续，持续通信

    ```sh
    # master发出PING包的周期，默认是10秒
    repl-ping-replica-period 10
    ```

4.  进入平稳期，增量复制

    主节点继续将接受到的命令同步给从节点

5.  从机下线，重连续传

    如果主节点重新启动，从节点将向主节点发送PSYNC命令，主节点将检查从节点的复制偏移量，并决定是使用部分重同步还是完全重同步的方式向从节点同步数据，类似于断点续传





## 5、复制的缺点

**复制延迟，信号衰减**

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

![image-20230531224952579](E:\学习笔记\img\img01\image-20230531224952579.png)



**master挂掉需要人工干预**

master挂掉之后从节点是不会自己上位的，所以当master挂掉之后就需要人工手动的将 slave 节点设置为 master，这个时间段整个服务是不可用的，人工手动修改也是比较麻烦的事情，所以redis有哨兵和集群两种方式来提高高可用性





# Redis哨兵(sentinel)

## 1、概述

主从复制在Master挂掉之后从节点只能在原地等候Master节点的回归，损失了系统的高可用，所以我们需要哨兵在Master挂掉了之后从新选一个新的Master来继续提供服务。

哨兵的作用就是无人值守，监控Master节点的运行，当Master出现问题的时候就需要哨兵来选出从节点成为新的Master

![image-20230601161419718](E:\学习笔记\img\img01\image-20230601161419718.png) 

**提供的功能**：

-   主从监控：监控Master是否正常运行
-   消息通知：哨兵可以将故障转移的结果发送给客户端
-   故障转移：Master异常，选举新的Master来提供服务
-   配置中心：客户端可以通过哨兵来获取当前Master的主机节点地址和端口号





## 2、使用

### 2.1 配置文件详解

sentinel和redis的配置文件是分开的

![image-20230601161634210](E:\学习笔记\img\img01\image-20230601161634210.png)



sentinel中和redis配置文件同款的配置项

```sh
# 保护模式
protected-mode no
# 端口号
port 26379
# 后台启动
daemonize no
# pid文件位置
pidfile /var/run/redis-sentinel.pid
# 日志文件
logfile ""
# 工作目录
dir /tmp
```



**监听Master的配置项**

```sh
sentinel monitor <master-name> <ip> <redis-port> <quorum>
# sentinel monitor mymaster 127.0.0.1 6379 2		默认配置
```

**`quorum`**配置项：表示最少有几个哨兵认可客观下线同意故障迁移的法定票数。由于网络原因会导致Master和其中某个哨兵的通信中断，这个时候这个哨兵就会认为Master已经挂掉了，但事实上Master并没有挂掉，所以就需要另外的哨兵来进行判断，看一下其他的哨兵是否还能接收到来自Master的心跳，如果超过指定的票数就会进行故障迁移，也就是选取新的Master上线。

**配置Master的连接密码**

```sh
sentinel auth-pass <master-name> <password>
```

**其他配置项**

```sh
sentinel down-after-milliseconds <master-name> <milliseconds>：
# 指定多少毫秒之后，主节点没有应答哨兵，此时哨兵主观上认为主节点下线

sentinel parallel-syncs <master-name> <nums>：
# 表示允许并行同步的slave个数，当Master挂了后，哨兵会选出新的Master，此时，剩余的slave会向新的master发起同步数据

sentinel failover-timeout <master-name> <milliseconds>：
# 故障转移的超时时间，进行故障转移时，如果超过设置的毫秒，表示故障转移失败

sentinel notification-script <master-name> <script-path> ：
# 配置当某一事件发生时所需要执行的脚本

sentinel client-reconfig-script <master-name> <script-path>：
# 客户端重新配置主节点参数脚本
```





### 2.2 实践案例

![image-20230601164016530](E:\学习笔记\img\img01\image-20230601164016530.png) 

**架构说明**：有三台redis形成主从复制的架构，由于资源有限，Sentinel集群放在同一台机器上

| 机器                                                     |
| -------------------------------------------------------- |
| 192.168.10.10 -> Master  sentinel1、sentinel2、sentinel3 |
| 192.168.10.11 -> slave                                   |
| 192.168.10.12 -> slave                                   |



#### 2.2.1 启动三台sentinel

**编写配置文件**

将配置文件复制到`/myredis`下，将下面的配置复制三份，对应的端口号要进行修改，分别是 26379、26380、26381

```sh
bind 0.0.0.0
daemonize yes
protected-mode no
port 26379
logfile "/myredis/sentinel26379.log"
pidfile /var/run/redis-sentinel26379.pid
dir /myredis
sentinel monitor mymaster 192.168.10.10 6379 2
sentinel auth-pass mymaster 111111
```

![image-20230601164910635](E:\学习笔记\img\img01\image-20230601164910635.png) 



**启动三台sentinel**

==首先启动三台redis==，再启动sentinel

![image-20230601221644324](E:\学习笔记\img\img01\image-20230601221644324.png)  

```sh
[root@xdz myredis]# redis-sentinel sentinel26379.conf --sentinel
[root@xdz myredis]# redis-sentinel sentinel26380.conf --sentinel
[root@xdz myredis]# redis-sentinel sentinel26381.conf --sentinel
```

![image-20230601165310892](E:\学习笔记\img\img01\image-20230601165310892.png) 

启动成功~~~



#### 2.2.2 鸠占鹊巢

将 master 关掉，接着查看两台从机的变化

 ![image-20230601223308880](E:\学习笔记\img\img01\image-20230601223308880.png)

![image-20230601223344891](E:\学习笔记\img\img01\image-20230601223344891.png) 

可以看到两台 slave 中的一台变成了master，完成了上位



#### 2.2.3 老master回归

我们将老master重新上线，看一下它会是什么情况

![image-20230601224439163](E:\学习笔记\img\img01\image-20230601224439163.png) 

可以看到老家伙回归之后只能乖乖的给新老大当小弟了哈哈



#### 2.2.4 查看配置文件变化

sentinel 会动态的修改配置文件，以前是master现在是从节点它的配置文件会发生变化吗？

**老master**

![image-20230601224733104](E:\学习笔记\img\img01\image-20230601224733104.png) 

发现文件的末尾添加上了这几行配置，也就是说老master再也不能成为master了，除非新的master挂掉了它可以竞争上位

**新master**

![image-20230601225001687](E:\学习笔记\img\img01\image-20230601225001687.png) 

reids的配置文件的末尾都被sentinel动态添加了配置



**sentinel配置文件**

![image-20230601225412952](E:\学习笔记\img\img01\image-20230601225412952.png) 

上面是 sentinel 动态添加到它的配置文件中的配置项，其中的 myid 是用来表示 sentinel 节点的唯一id



### 2.3 使用建议

-   哨兵节点的数量应为多个，哨兵本身应该集群，保证高可用
-   哨兵节点的数量应该是奇数
-   各个哨兵节点的配置应一致
-   如果哨兵节点部署在Docker等容器里面，尤其要注意端口的正确映射
-   哨兵集群+主从复制，并不能保证数据零丢失

所以大型企业大多数是**采用集群的方式**来保证数据安全性和高可用







## 3、故障切换和选举原理

### 3.1 SDown主观下线(Subjectively Down)

所谓主观下线（Subjectively Down， 简称 SDOWN）指的是单个Sentinel实例对服务器做出的下线判断，即单个sentinel认为某个服务下线（有可能是接收不到订阅，之间的网络不通等等原因）。主观下线就是说如果服务器在[sentinel down-after-milliseconds]给定的毫秒数之内没有回应PING命令或者返回一个错误消息， 那么这个Sentinel会主观的(单方面的)认为这个master不可以用了，o(╥﹏╥)o

![image-20230601230023619](E:\学习笔记\img\img01\image-20230601230023619.png) 

```sh
sentinel down-after-milliseconds <masterName> <timeout>
```

 表示master被当前sentinel实例认定为失效的间隔时间，这个配置其实就是进行主观下线的一个依据

master在多长时间内一直没有给Sentine返回有效信息，则认定该master主观下线。也就是说如果多久没联系上redis-servevr，认为这个redis-server进入到失效（SDOWN）状态。



### 3.2 ODwn客观下线(Objectively Down)

客观下线就是在主观下线之后发生的，当主管下线触发后就会进行商议，如果票数满足设置的值那么就会进行新的master选举

![image-20230601230134030](E:\学习笔记\img\img01\image-20230601230134030.png)

**masterName**是对某个master+slave组合的一个区分标识(一套sentinel可以监听多组master+slave这样的组合)

**quorum这个参数是进行客观下线的一个依据**，法定人数/法定票数

意思是至少有quorum个sentinel认为这个master有故障才会对这个master进行下线以及故障转移。因为有的时候，某个sentinel节点可能因为自身网络原因导致无法连接master，而此时master并没有出现故障，所以这就需要多个sentinel都一致认为该master有问题，才可以进行下一步操作，这就保证了公平性和高可用。



### 3.3 哨兵选取leader

当 master 被判断主观下线后，各个哨兵就开始商议要选举出一个leader，由这个leader来failover（故障迁移）

![image-20230601230513764](E:\学习笔记\img\img01\image-20230601230513764.png) 



#### 3.3.1 查看 sentinel 日志文件进行分析

-   26379

    ![image-20230601231329438](E:\学习笔记\img\img01\image-20230601231329438.png) 

-   26380

    ![image-20230601231546371](E:\学习笔记\img\img01\image-20230601231546371.png)

-   26381

    和80的一样都是给 79 投了一票



#### 3.3.2 哨兵 leader 如何选举出来呢？

**Raft算法 **

![image-20230601230632408](E:\学习笔记\img\img01\image-20230601230632408.png)

监视该主节点的所有哨兵都有可能被选为领导者，选举使用的算法是Raft算法；Raft算法的基本思路**是先到先得**：谁网络好谁就是有机会

即在一轮选举中，哨兵A向B发送成为领导者的申请，如果B没有同意过其他哨兵，则会同意A成为领导者



### 3.4 选取新master

选举出新的 leader 之后由它来选取新的master



#### 3.4.1 新主登基

某个 slave 被选举成为了 master，接着其他的 slave 要拜这个为老大

**选举当选的规则**

![image-20230601231936341](E:\学习笔记\img\img01\image-20230601231936341.png) 

1.  优先级 `replica-priority`最高的从节点（数字越小优先级越高）

    ![image-20230601232210537](E:\学习笔记\img\img01\image-20230601232210537.png) 

2.  复制偏移量位置 offset 最大的从节点

    可以理解为同步数据最多的那个从节点，因为由于网络的原因会导致多个从节点之间的数据量浮动的情况。

3.  最小的 Run ID 的从节点

    字典顺序，ASCLL码

选举出来的从节点会执行 `slaveif no one`命令成为新的master



#### 3.4.2 群臣俯首

当选出 master 节点之后，剩下的 slave 就要拜这个新的 master 为老大

哨兵 leader 发消息给其他的 slave 让它们拜新 master 为老大



#### 3.4.3 旧主拜服

当老的master从新上线，也要乖乖的认新的master为老大

哨兵 leader 会让原来的 master 降级为 slave 并恢复正常工作





# Redis集群(cluster)

## 1、概述

>   维基百科
>
>   **计算机集群**（英语：computer cluster）是一组松散或紧密连接在一起工作的[计算机](https://zh.wikipedia.org/wiki/電子計算機)。由于这些计算机协同工作，在许多方面它们可以被视为单个系统。与[网格计算机](https://zh.wikipedia.org/wiki/网格计算)不同，计算机集群将每个[节点](https://zh.wikipedia.org/w/index.php?title=节点_(计算机科学)&action=edit&redlink=1)设置为执行相同的任务，由软件控制和调度。



简单点来说就是多个服务器组成一个集群来提供服务，每个服务器分摊服务的压力，比如存储服务就是每个服务器都存一部分的数据，这样协同工作形成一个整体，水平扩展的能力可以使系统拥有很好的扩展性。



**是什么**

由于数据量过大，单个Master复制集难以承担，因此需要对多个复制集来过程一个集群提供服务， 其作用就是提供在多个Redis节点共享数据的程序集。每个Redis节点存有不同的数据，这样协同工作来形成一个大的集群系统。

![image-20230602162748314](E:\学习笔记\img\img01\image-20230602162748314.png) 

上图是官网的集群架构图，分别是三台Master和三台Slave，由它们组成三组主从复制的结构。



**能干嘛**

Redis集群支持多个Master，每个Master又可以挂载多个slave

-   读写分离
-   支持数据的高可用
-   支持海量数据的读写存储操作

由于Cluster自带Sentinel的故障转移机制，内置了高可用的支持，无需再去使用哨兵功能客户端与Redis的节点连接，不再需要连接集群中所有的节点，只需要任意连接集群中的一个可用节点即可槽位slot负责分配到各个物理服务节点，由对应的集群来负责维护节点、插槽和数据之间的关系

Redis集群不保证强一致性，在特定的情况下它可能会丢失一些被系统接收到的写入请求命令，也就是说Redis集群是AP而不是CP





## 2、集群算法

### 2.1 概述

>   **官网原话**：
>
>   **Key distribution model** 密钥分发模型
>
>   集群的密钥空间分为 16384 个插槽，有效地为 16384 个主节点的集群大小设置了上限（但是，建议的最大节点大小约为 ~ 1000 个节点）。
>
>   集群中的每个主节点处理 16384 哈希槽的子集。当没有正在进行的集群重新配置时（即哈希槽从一个节点移动到另一个节点），集群是稳定的。当集群稳定时，单个节点将提供单个哈希槽（但是，服务节点可以有一个或多个副本，这些副本将在网络拆分或故障的情况下替换它，并且可用于扩展读取操作，其中读取过时数据是可以接受的）。
>
>   用于将密钥映射到哈希槽的基本算法如下（有关此规则的哈希标记例外，请阅读下一段）：
>
>   ```sh
>   HASH_SLOT = CRC16(key) mod 16384
>   ```

**Redis集群的槽位slot**

Redis集群没有使用一致性Hash，而是引入了**哈希槽**的方式

Redis集群有 16348（2^14）个哈希槽，每个key通过 `CRC6` 校验后对 16384 取模来决定放置哪个槽位，集群的每个节点负责部分的hash槽

例如：当前集群有三个节点，那么槽位分配分别是 0-5460、5361-10922、10923-16383，通过计算 key 来获取它要放入的槽位置，接着由负责对应槽位的Redis节点来提供服务。

![image-20230602171820473](E:\学习笔记\img\img01\image-20230602171820473.png) 

**Redis集群的分片**

使用Redis集群时我们会将存储的数据分散到多台redis机器上，这称为分片。简言之，集群中的每个Redis实例都被认为是整个数据的一个分片。

为了找到给定key的分片，我们对key进行CRC16(key)算法处理并通过对总分片数量取模。然后，使用确定性哈希函数，这意味着给定的key将多次始终映射到同一个分片，我们可以推断将来读取特定key的位置。

**分片和槽位的组合优势**

方便扩容收缩和数据分派查找。这种方式可以让我们很容易的添加节点，只需要将其他节点的槽位分配一点给新增的节点即可，删除也是一样的，将对应的槽位还给对应节点。无论是添加节点还是删除节点都能保证集群可用。



### 2.2 slot槽位映射方案

#### 2.2.1 哈希取余分区

![image-20230602172905836](E:\学习笔记\img\img01\image-20230602172905836.png) 

通过计算key的哈希值摸上服务节点的数量来决定由那个节点来提供服务

**优点**：简单粗暴，只需要预估好数据规划好节点即可，

**缺点**：进行扩容和缩容比较麻烦，每次节点变动都要进行重新计算，也就是说如果由一台挂掉了就会导致节点数量发生变化，此时就需要冲洗洗牌。



#### 2.2.2 一致性哈希算法分区

**一致性Hash算法背景**

一致性哈希算法在1997年由麻省理工学院中提出的，设计目标是为了解决分布式缓存数据变动和映射问题，某个机器宕机了，分母数量改变了，自然取余数不OK了。它可以减少影响客户端到服务器端的映射关系

**三大步骤**

1.  算法构建一致性哈希环

    一致性哈希算法必然有个hash函数并按照算法产生hash值，这个算法的所有可能哈希值会构成一个全量集，这个集合可以成为一个hash空间[0,2^32-1]，这个是一个线性空间，但是在算法中，我们通过适当的逻辑控制将它首尾相连(0 = 2^32),这样让它逻辑上形成了一个环形空间。**实际上是一个数组，逻辑上是一个环形**

    ![image-20230602173538278](E:\学习笔记\img\img01\image-20230602173538278.png) 

2.  redis服务器IP节点映射

    将集群中各个IP节点映射到环上的某一个位置。将各个服务器使用Hash进行一个哈希，具体可以选择服务器的IP或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希环上的位置。假如4个节点NodeA、B、C、D，经过IP地址的哈希函数计算(hash(ip))，使用IP地址哈希后在环空间的位置如下：

    ![image-20230602173637615](E:\学习笔记\img\img01\image-20230602173637615.png) 

3.  key落到服务器的落建规则

    计算出key的hash值，确定key在环上的位置，接着按顺序针走，遇到的第一台Redis就是提供服务的节点。

    ![image-20230602174009045](E:\学习笔记\img\img01\image-20230602174009045.png)

**优点**：

-   一致性哈希算法的容错性

    假设Node C宕机，可以看到此时对象A、B、D不会受到影响。一般的，在一致性Hash算法中，如果一台服务器不可用，则受影响的数据仅仅是此服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它不会受到影响。简单说，就是C挂了，受到影响的只是B、C之间的数据且这些数据会转移到D进行存储。

    ![image-20230602174308093](E:\学习笔记\img\img01\image-20230602174308093.png) 

-   一致性哈希算法的扩展性

    数据量增加了，需要增加一台节点NodeX，X的位置在A和B之间，那收到影响的也就是A到X之间的数据，重新把A到X的数据录入到X上即可，不会导致hash取余全部数据重新洗牌。

    ![image-20230602174327449](E:\学习笔记\img\img01\image-20230602174327449.png) 



**缺点**：一致性哈希算法的数据倾斜问题

一致性Hash算法在服务**节点太少时**，容易因为节点分布不均匀而造成**数据倾斜**（被缓存的对象大部分集中缓存在某一台服务器上）问题，例如系统中只有两台服务器：

![image-20230602174412082](E:\学习笔记\img\img01\image-20230602174412082.png) 

可以看到A节点明显服务压力比B的要更大



#### 2.2.3 哈希槽分区(Redis使用)

解决均匀分配的问题，在数据和节点之间又加入了一层，把这层称为哈希槽（slot），用于管理数据和节点之间的关系，现在就相当于节点上放的是槽，槽里放的是数据。

![image-20230602174512983](E:\学习笔记\img\img01\image-20230602174512983.png) 

槽解决的是粒度问题，相当于把粒度变大了，这样便于数据移动。哈希解决的是映射问题，使用key的哈希值来计算所在的槽，便于数据分配

**多少个hash槽**

一个集群只能有16384个槽，编号0-16383（0-2^14-1）。这些槽会分配给集群中的所有主节点，分配策略没有要求。

集群会记录节点和槽的对应关系，解决了节点和槽的关系后，接下来就需要对key求哈希值，然后对16384取模，余数是几key就落入对应的槽里。HASH_SLOT = CRC16(key) mod 16384。以槽为单位移动数据，因为槽的数目是固定的，处理起来比较容易，这样数据移动问题就解决了。



### 2.3 面试题

**为什么Redis集群的最大槽数是 16384 个？**

Redis集群并没有使用一致性hash而是引入了哈希槽的概念。Redis 集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽，集群的每个节点负责一部分hash槽。但为什么哈希槽的数量是16384（2^14）个呢？

CRC16算法产生的hash值有16bit，该算法可以产生2^16=65536个值。换句话说值是分布在0~65535之间，有更大的65536不用为什么只用16384就够？作者在做mod运算的时候，为什么不mod65536，而选择mod16384？ HASH_SLOT = CRC16(key) mod 65536为什么没启用

作者回答：https://github.com/redis/redis/issues/2576

 ![image-20230602174822053](E:\学习笔记\img\img01\image-20230602174822053.png)

>   **作者回答**：
>
>   正常的心跳数据包带有节点的完整配置，可以用幂等方式用旧的节点替换旧节点，以便更新旧的配置。这意味着它们包含原始节点的插槽配置，该节点使用2k的空间和16k的插槽，但是会使用8k的空间（使用65k的插槽）。
>
>   同时，由于其他设计折衷，Redis集群不太可能扩展到1000个以上的主节点。
>
>   因此16k处于正确的范围内，以确保每个主机具有足够的插槽，最多可容纳1000个矩阵，但数量足够少，可以轻松地将插槽配置作为原始位图传播。请注意，在小型群集中，位图将难以压缩，因为当N较小时，位图将设置的slot / N位占设置位的很大百分比。

**说明**：

**(1)如果槽位为65536，发送心跳信息的消息头达8k，发送的心跳包过于庞大。**

在消息头中最占空间的是myslots[CLUSTER_SLOTS/8]。 当槽位为65536时，这块的大小是: 65536÷8÷1024=8kb 

在消息头中最占空间的是myslots[CLUSTER_SLOTS/8]。 当槽位为16384时，这块的大小是: 16384÷8÷1024=2kb 

因为每秒钟，redis节点需要发送一定数量的ping消息作为心跳包，如果槽位为65536，这个ping消息的消息头太大了，浪费带宽。

**(2)redis的集群主节点数量基本不可能超过1000个。**

集群节点越多，心跳包的消息体内携带的数据越多。如果节点过1000个，也会导致网络拥堵。因此redis作者不建议redis cluster节点数量超过1000个。 那么，对于节点数在1000以内的redis cluster集群，16384个槽位够用了。没有必要拓展到65536个。

**(3)槽位越小，节点少的情况下，压缩比高，容易传输**

Redis主节点的配置信息中它所负责的哈希槽是通过一张bitmap的形式来保存的，在传输过程中会对bitmap进行压缩，但是如果bitmap的填充率slots / N很高的话(N表示节点数)，bitmap的压缩率就很低。 如果节点数很少，而哈希槽数量很多的话，bitmap的压缩率就很低。 

**总的来说**：Redis是一个需要低延迟快熟反应的数据库，所以传输越快就越好







### 2.4 CRC16算法分析











## 3、集群搭建

![image-20230602231922464](E:\学习笔记\img\img01\image-20230602231922464.png) 

**架构说明**：

资源有限，利用三台机器来做这个实验，一台机器上运行两个实例，一共是六台，对应是三组一主一从，集群它会自己进行分配，不需要我们去指定，所以不用指定谁是 Master 谁是 Slave





### 3.1 集群搭建

#### 3.1.1 集群配置

创建一个新目录放置我们集群的文件

```sh
mkdir -p /myredis/cluster
```

三台机器对应的IP和启动的端口号

| 192.168.10.10 | 192.168.10.11 | 192.168.10.12 |
| ------------- | ------------- | ------------- |
| 6381          | 6383          | 6385          |
| 6382          | 6384          | 6386          |

在对应主机的 `/myredis/cluster` 目录下添加对应端口号的配置文件

```sh
bind 0.0.0.0
daemonize yes
protected-mode no
port 6381
logfile "/myredis/cluster/cluster6381.log"
pidfile /myredis/cluster6381.pid
dir /myredis/cluster
dbfilename dump6381.rdb
appendonly yes
appendfilename "appendonly6381.aof"
requirepass 111111
masterauth 111111
 
# 开启集群
cluster-enabled yes
# 集群的配置文件
cluster-config-file nodes-6381.conf
# 集群连接的超时时间
cluster-node-timeout 5000
```

==其他端口号的机器要修改对应的端口号！！！==



#### 3.1.2 启动集群

首先启动我们配置好的 6 台实例

```sh
redis-server redisCluster<port>.conf
```

通过 redis-cli命令为6台机器构建集群关系

```sh
# 注意这里要写真实地址，不能写 127.0.0.1
# ----cluster-replicas <num> 表示为每个Master创建 num 个slave节点
redis-cli -a 111111 --cluster create --cluster-replicas 1 192.168.10.10:6381 192.168.10.10:6382 192.168.10.11:6383 192.168.10.11:6384 192.168.10.12:6385 192.168.10.12:6386
```

![image-20230602234913048](E:\学习笔记\img\img01\image-20230602234913048.png) 

创建成功~~~



#### 3.1.3 查看集群状态

| 命令             | 作用呢           |
| ---------------- | ---------------- |
| info replication | 查看当前节点信息 |
| cluster info     |                  |
| cluster nodes    |                  |

连接进入6381 作为切入点，查看并检验集群状态

```sh
[root@xdz cluster]# redis-cli -a 111111 -p 6381
```

**查看集群信息**

```sh
127.0.0.1:6381> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
# 集群运行的节点数
cluster_known_nodes:6
# Master的数量为3台
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
```

**查看节点信息**

```sh
127.0.0.1:6381> cluster nodes
da1ccf965889a4874605ce89f489c89ceab3ef4f 192.168.10.11:6383@16383 master - 0 1685721310557 3 connected 5461-10922
6b4b0bcec3a687b29273d3f79abb834d4386f2cf 192.168.10.12:6386@16386 slave da1ccf965889a4874605ce89f489c89ceab3ef4f 0 1685721311600 3 connected
a6719abe0d0639d27dbdb48e267a6c0e0c15e691 192.168.10.10:6382@16382 slave e426860fd72c155c6ea02878b3ad4162b7200a8e 0 1685721311000 5 connected
e426860fd72c155c6ea02878b3ad4162b7200a8e 192.168.10.12:6385@16385 master - 0 1685721311000 5 connected 10923-16383
7a74adf8649cc0e0cb3255f8575cf199ec3660d1 192.168.10.11:6384@16384 slave 2c390e90f71a8085915fb47f64e7daca5d4bcae6 0 1685721311600 1 connected
2c390e90f71a8085915fb47f64e7daca5d4bcae6 192.168.10.10:6381@16381 myself,master - 0 1685721311000 1 connected 0-5460
```

我们可以通过节点信息知道Master节点和Slave节点分别是哪几个

-   master前面的就是我们的Master节点
-   slave 前面的是 从节点，后面的是它对应的主节点
-   myself 表示是当前的客户端
-   5461-10922、10923-16383、0-5460表示的是分配给对应节点的槽位

那么可以得到的信息如下表

| Master | Slave | 槽位        |
| ------ | ----- | ----------- |
| 6383   | 6386  | 5461-10922  |
| 6385   | 6382  | 10923-16383 |
| 6381   | 6384  | 0-5460      |



### 3.2 集群读写

**添加数据**

```sh
127.0.0.1:6381> set k1 v1
(error) MOVED 12706 192.168.10.12:6385
127.0.0.1:6381> set k2 v2
OK
```

设置 k2 没有报错，设置 k1 的时候报错了，这是为啥？

我们此时是在 6381 客户端下的，而 k1 计算出来的值不在 6381 节点映射的槽位，所以会出现这个错误

```sh
# 通过 CLUSTER KEYSLOT <key> 命令可以查看key对应的槽位值
127.0.0.1:6381> CLUSTER KEYSLOT k1
(integer) 12706
```

**如何解决？**

在连接客户端之前加`-c` 参数，它会帮我们重定向到对应的节点

```sh
redis-cli -a 111111 -p 6381 -c
```

此时再次添加数据

```sh
127.0.0.1:6381> set k1 v1                                                    
-> Redirected to slot [12706] located at 192.168.10.12:6385                  
OK
```

完美~~~



### 3.3 主从容错切换迁移

#### 3.3.1 Master挂掉

此时的集群信息

| Master | Slave | 槽位        |
| ------ | ----- | ----------- |
| 6383   | 6386  | 5461-10922  |
| 6385   | 6382  | 10923-16383 |
| 6381   | 6384  | 0-5460      |

我们将 `6383` 端口号关闭，看一下 `6386` 会不会上位

```sh
127.0.0.1:6383> SHUTDOWN
not connected>
```

接着查看集群信息

![image-20230604115141255](E:\学习笔记\img\img01\image-20230604115141255.png)

`6383`成功上位，让我们恭喜这位候补选手



#### 3.3.2 Master回归

那么 Master 回归它还是 Master 吗？

![image-20230604115430956](E:\学习笔记\img\img01\image-20230604115430956.png)

On No~~~，该死，回不去了，Master是以 Slave 的方式回归，老大哥变小弟了哈哈



#### 3.3.3 维持原有关系

能不能 Master 回归还是 Master 呢？是可以的，可以在对应机器上执行下面的命令来指定 Master

```sh
CLUSTER FAILOVER
```

恢复关系，之前我们的Master节点是 `6383`，所以登录到 `6383` 机器

```sh
127.0.0.1:6383> CLUSTER FAILOVER
OK
```

查看节点信息

![image-20230604115946371](E:\学习笔记\img\img01\image-20230604115946371.png)

我胡汉天又回来了哈哈~~~





### 3.4 主从扩容

#### 3.4.1 加入集群



**新增节点**

在 192.168.10.12 机器上添加两个节点 分别是 6387、6388

```sh
# 启动两台机器
[root@localhost cluster]# redis-server redisCluster6387.conf                              
[root@localhost cluster]# redis-server redisCluster6388.conf
```



**加入集群**

将 6387 加入到集群中，通过 6381 机器加入到集群中，6381 相当于是 6387 的领路人，带它加入集群大家庭中

```sh
[root@localhost cluster]# redis-cli -a 111111 --cluster add-node 192.168.10.12:6387 192.168.10.10:6381
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Adding node 192.168.10.12:6387 to cluster 192.168.10.10:6381
>>> Performing Cluster Check (using node 192.168.10.10:6381)
M: 2c390e90f71a8085915fb47f64e7daca5d4bcae6 192.168.10.10:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: da1ccf965889a4874605ce89f489c89ceab3ef4f 192.168.10.11:6383
   slots: (0 slots) slave
   replicates 6b4b0bcec3a687b29273d3f79abb834d4386f2cf
M: 6b4b0bcec3a687b29273d3f79abb834d4386f2cf 192.168.10.12:6386
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: a6719abe0d0639d27dbdb48e267a6c0e0c15e691 192.168.10.10:6382
   slots: (0 slots) slave
   replicates e426860fd72c155c6ea02878b3ad4162b7200a8e
M: e426860fd72c155c6ea02878b3ad4162b7200a8e 192.168.10.12:6385
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 7a74adf8649cc0e0cb3255f8575cf199ec3660d1 192.168.10.11:6384
   slots: (0 slots) slave
   replicates 2c390e90f71a8085915fb47f64e7daca5d4bcae6
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
>>> Getting functions from cluster
>>> Send FUNCTION LIST to 192.168.10.12:6387 to verify there is no functions in it
>>> Send FUNCTION RESTORE to 192.168.10.12:6387
>>> Send CLUSTER MEET to node 192.168.10.12:6387 to make it join the cluster.
[OK] New node added correctly.	# 表示加入成功
```



**检查集群情况**

```sh
[root@xdz cluster]# redis-cli -a 111111 --cluster check 192.168.10.10:6381
```

![image-20230604130620661](E:\学习笔记\img\img01\image-20230604130620661.png)

此时新加入的加点是还没有分配槽位的



#### 3.4.2 分派槽位

各个节点给新加入的加点分派部分槽位

```sh
[root@xdz cluster]# redis-cli -a 111111 --cluster reshard 192.168.10.10:6381
```

![image-20230604131304696](E:\学习笔记\img\img01\image-20230604131304696.png)

**检查集群情况**

![image-20230604131425822](E:\学习笔记\img\img01\image-20230604131425822.png)

可以看到新加入的节点也拥有自己的槽位了，它被分成了三部分，是集群之前的三台节点都分配一点所造成的，每台机器都分派出一部分。



#### 3.4.3 分配从节点

命令

```sh
redis-cli -a "密码" --cluster add-node 新节点ID:端口号 领路节点ID:端口号 --cluster-slave --cluster-master-id masterID 
```

为新增的 6387 节点添加 从节点

```sh
[root@xdz cluster]# redis-cli -a 111111 --cluster add-node 192.168.10.12:6388 192.168.10.1
2:6387 --cluster-slave --cluster-master-id 83c7f82b66f1ad1dca1647b3150d46437df0caa4   
```

![image-20230604132057078](E:\学习笔记\img\img01\image-20230604132057078.png) 

添加成功，再次检查集群状态

![image-20230604132201476](E:\学习笔记\img\img01\image-20230604132201476.png)  

成功~~~~





### 3.5 主从缩容

让  6387 和 6388 节点下线

首先需要获取下线节点的ID

![image-20230604173226887](E:\学习笔记\img\img01\image-20230604173226887.png)



**从集群中将 6388 节点删除**

```sh
[root@xdz cluster]# redis-cli -a 111111 --cluster  del-node 192.168.10.12:6388 d9261712bf10d40c127e2b8a573f658d315cba66
```

![image-20230604173434110](E:\学习笔记\img\img01\image-20230604173434110.png)

检查集群信息

![image-20230604173631993](E:\学习笔记\img\img01\image-20230604173631993.png) 

可以发现集群中还剩余 7 个节点，6388 成功被移除



**将 6387 的槽位全部转给 6381**

![image-20230604174218808](E:\学习笔记\img\img01\image-20230604174218808.png)

此时检查集群状态可以发现 6381 的槽位数是另外两个节点的两倍

![image-20230604174038569](E:\学习笔记\img\img01\image-20230604174038569.png)



**将 6387 从集群中移除**

```sh
[root@xdz cluster]# redis-cli -a 111111 --cluster del-node 192.168.10.12:6387 83c7f82b66f1ad1dca1647b3150d46437df0caa4
```

![image-20230604174455222](E:\学习笔记\img\img01\image-20230604174455222.png)

检查集群状态

![image-20230604174614578](E:\学习笔记\img\img01\image-20230604174614578.png)

自此，6387 和 6388 就成功被移除了~~~~





## 4、常用配置和命令

### 4.1 配置

![image-20230604175001804](E:\学习笔记\img\img01\image-20230604175001804.png)

```sh
cluster-require-full-coverage yes
```

默认是yes，集群是又多个节点组成，多个节点之间分摊服务的压力，如果其中的节点挂掉，就会导致提供的服务不完整，yes表示不完整的情况下Reids不提供服务，no表示继续提供服务，不过数据不完整，对于数据完整性的应用建议是yes，相反可以设置为no



### 4.2 Reids 命令

**查看槽位是否被占用**

```sh
 CLUSTER COUNTKEYSINSLOT slot
 # 返回0表示未占用，1表示已占用
```

```sh
127.0.0.1:6381> CLUSTER COUNTKEYSINSLOT 123
(integer) 0
```



**查看 key 对应的槽位**

```sh
CLUSTER KEYSLOT key
# 返回对应的槽位
```

```sh
127.0.0.1:6381> CLUSTER KEYSLOT k1
(integer) 12706
```



**节点成为 Master**

```sh
CLUSTER FAILOVER [FORCE|TAKEOVER]
# FIRCE 强制
# TAKEOVER 接管
```

```sh
127.0.0.1:6382> CLUSTER FAILOVER
OK
127.0.0.1:6382> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=192.168.10.12,port=6385,state=online,offset=37391,lag=0
......
```



**查看节点信息**

```
CLUSTER INFO
```

```sh
127.0.0.1:6381> CLUSTER INFO
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:12
cluster_my_epoch:11
cluster_stats_messages_ping_sent:57205
cluster_stats_messages_pong_sent:83070
cluster_stats_messages_fail_sent:5
cluster_stats_messages_auth-ack_sent:4
cluster_stats_messages_update_sent:2
cluster_stats_messages_sent:140286
cluster_stats_messages_ping_received:58488
cluster_stats_messages_pong_received:61302
cluster_stats_messages_meet_received:6
cluster_stats_messages_fail_received:2
cluster_stats_messages_auth-req_received:4
cluster_stats_messages_update_received:1
cluster_stats_messages_received:119803
total_cluster_links_buffer_limit_exceeded:0
```



**查看集群信息**

```
CLUSTER NODES
```

```sh
127.0.0.1:6381> CLUSTER NODES
da1ccf965889a4874605ce89f489c89ceab3ef4f 192.168.10.11:6383@16383 slave 6b4b0bcec3a687b29273d3f79abb834d4386f2cf 0 1685873121716 9 connected
6b4b0bcec3a687b29273d3f79abb834d4386f2cf 192.168.10.12:6386@16386 master - 0 1685873121611 9 connected 6827-10922
a6719abe0d0639d27dbdb48e267a6c0e0c15e691 192.168.10.10:6382@16382 master - 0 1685873120694 12 connected 12288-16383
e426860fd72c155c6ea02878b3ad4162b7200a8e 192.168.10.12:6385@16385 slave a6719abe0d0639d27dbdb48e267a6c0e0c15e691 0 1685873121000 12 connected
7a74adf8649cc0e0cb3255f8575cf199ec3660d1 192.168.10.11:6384@16384 slave 2c390e90f71a8085915fb47f64e7daca5d4bcae6 0 1685873121508 11 connected
2c390e90f71a8085915fb47f64e7daca5d4bcae6 192.168.10.10:6381@16381 myself,master - 0 1685873120000 11 connected 0-6826 10923-12287
```





### 4.3 redis-cli 命令

| 功能                      | 命令                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 客户端以集群方式访问      | redis-cli -a 密码 -p 端口号 **-c**                           |
| 添加节点到集群            | redis-cli  -a 密码 --cluster **add-node** 新节点IP:prot 引路人IP:prot |
| 从集群中移除节点          | redis-cli  -a 密码 --cluster **del-node** 新节点IP:prot 引路人IP:prot |
| 给 Master 添加 slave 节点 | redis-cli  -a 密码 --cluster **add-node** 新节点IP:prot 引路人IP:Port  **--cluster -slave --cluster-master-id master节点ID** |
| 重新分配节点              | redis-cli -a 密码 --cluster **reshard** IP:prot              |
| 检查集群状态              | redis-cli -a 密码 **check** 节点IP:port                      |





# Reids的Java客户端

![image-20220430172828283](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430172828283.png)





## 1、Jedis

Jedis官方地址：https://github.com/redis/jedis

Jedis的所有方法都是和命令一致的



### 1.1 基本使用

**代码实现**

创建maven工程，并引入依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>4.2.0</version>
</dependency>
<!--单元测试-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.7.0</version>
</dependency>
```



**测试**

```java
public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1 建立连接
        jedis = new Jedis("------", 6379);
        // 2 设置密码
        jedis.auth("------");
        // 3 选择库
        jedis.select(0);
    }

    @Test
    public void testString(){
        String result = jedis.set("name", "小智");
        System.out.println(result);
        String name = jedis.get("name");
        System.out.println(name);
    }

    @Test
    public void testHash(){
        jedis.hset("user:1", "name", "Jack");
        jedis.hset("user:1", "age", "11");

        // 获取值
        Map<String, String> map = jedis.hgetAll("user:1");
        System.out.println(map);
    }

    @AfterEach
    void tearDown() {
        if (jedis != null) {
            // 释放连接
            jedis.close();
        }
    }
}
```



### 1.2 Jedis连接池

创建JedisPool

```java
public class JedisConnectionFactory {

    private static final JedisPool jedisPool;

    static {
        // 配置连接池
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(8);
        poolConfig.setMaxIdle(8);
        poolConfig.setMaxWaitMillis(1000);
        // 创建连接池对象
        new JedisPool(poolConfig, "-------",
                6379, 1000, "-------");
    }

    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```

然后通过jedisPool获取连接

```java
@BeforeEach
void setUp() {
    jedis = JedisConnectionFactory.getJedis();
}
```





## 2、Spring Data Reids

### 2.1 简介

SpringData是Spring中数据操作的模块，包含对各种数据库的集成，其中对Redis的集成模块就叫做SpringDataRedis，官网地址：https://spring.io/projects/spring-data-redis

-   提供了对不同Redis客户端的整合（Lettuce和Jedis）
-   提供了RedisTemplate统一API来操作Redis
-   支持Redis的发布订阅模型
-   支持Redis哨兵和Redis集群
-   支持基于Lettuce的响应式编程
-   支持基于JDK、JSON、字符串、Spring对象的数据序列化及反序列化
-   支持基于Redis的JDKCollection实现

![image-20220430202129662](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430202129662.png)



### 2.2 连接单机使用

#### 2.2.1 配置使用

引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--连接池-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

yml配置

```yml
spring:
  application:
    name: redis_cluster

  redis:
    host: ip
    port: 6379
    password: 111111
    lettuce:
      pool:
          max-active: 8
          max-wait: 1ms
          max-idle: 8
          min-idle: 0
```



**测试**

```java
@Autowired
private RedisTemplate redisTemplate;

@Test
void contextLoads() {
    // 写入一条String数据
    redisTemplate.opsForValue().set("name", "haha");
    // 获取数据
    String name = (String) redisTemplate.opsForValue().get("name");
    System.out.println(name);
}
```





#### 2.2.2 的序列化方式

发现问题：我们对对应key的值进行覆盖的时候，会出现不是覆盖内容的情况

写入的内容

![image-20220430210441299](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430210441299.png)

存储到redis中的内容

![image-20220430210526626](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430210526626.png)

这是由于SpringDataRedis使用是JDK 的序列化机制，JDK在底层调用ObjectOutputStream将Object对象转成字节，所以显示出来的就是转换过的

![image-20230604212515739](E:\学习笔记\img\img01\image-20230604212515739.png)



**修改序列化方式**

创建一个RedisTemplate替换默认的

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        // 创建RedisTemplate对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂
        template.setConnectionFactory(connectionFactory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        // 设置key的序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        // 创建value的序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
        return template;
    }
}
```

最后测试结果

![image-20220430212708546](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430212708546.png)



#### 2.2.3 序列化一个对象

```java
@Test
public void test(){
    // 写入一个对象
    redisTemplate.opsForValue().set("user:1000", new User("小智", 19));
    // 获取对象
    User user = (User) redisTemplate.opsForValue().get("name:1000");
    System.out.println("user = " + user);
}
```

![image-20220430213127351](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20220430213127351.png)





### 2.3 连接集群

#### 2.3.1 配置连接

**修改 yml文件**

```yml
spring:
  application:
    name: redis_cluster

  ############## Redis集群 ###############
  redis:
    password: 111111
    # 添加集群信息
    cluster:
      max-redirects: 3
      nodes: 192.168.10.10:6381,192.168.10.10:6382,192.168.10.11:6383,192.168.10.11:6384,192.168.10.12:6385,192.168.10.12:6386
    lettuce:
      pool:
          max-active: 8
          max-wait: 1ms
          max-idle: 8
          min-idle: 0
```

**测试**

```java
@Test
public void testCluster(){
    redisTemplate.opsForValue().set("k1", "v1");
    String k1 = (String) redisTemplate.opsForValue().get("k1");
    System.out.println(k1);
}
```



#### 2.3.2 动态感应刷新

我们尝试让一台Redis节点down掉，接着 slave 会上位成为Master，此时我们尝试访问Redis集群会发生什么？

首先我们让 6381 节点 down 掉，它的 slave 会上位成为  Master，接着我们使用 Java 客户端访问集群











