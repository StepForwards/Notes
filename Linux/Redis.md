---
title: Redis
tags: linux
grammar_cjkRuby: true
---
## NoSQL

> NoSQL(Not Only Sql),是一项全新的数据库理念,和我们学过的MySQL和Orcal等这类的关系型数据库相对映,泛指非关系型数据库

**NoSQL优势**

> 在目前的互联网项目中,经常面临的3个问题,1.高并发2.高负载3.高可用

- 高并发,在同一个时间内应用服务器会收到大量的请求,同样这些请求也要去操作我们的数据库服务器,每次读写操作就要发生磁盘的IO,对于关系型数据库而言,已经无法无法承受了
- 高负载,因为有大量的用户访问门户网站,每个用户访问的以及点击都会产生各种数据,对于数据库而言需要保存大量的数据,那么在关系型数据库中在一张有几亿条记录的表中进行sql查询,效率是及其低的
- 高可用,随着与日俱增的访问量和用户量,当数据库已经达到上线的时候,就需要进行相应的扩展,对于关系型数据库而言,在进行数据的扩展和迁移的时候,往往需要停机维护NoSQL数据库的产生,就是为了解决上述难题

**NoSQL特点**

> NoSQL虽说种类繁多,但都具有如下特性,易扩展,高性能,高可用,灵活性数据模型

## Redis

> Redis是用c语言开发的一个开源的键值对数据库,官方提供的数据,对于50个并发,执行10万次请求,读的速度是110000次/s,写的速度的是81000次/s,它通过提供了多种键值类型满足多样化的需求,对于redis而言键的类型一般都为字符串,对应的值有5中类型
1.字符串类型(String)
2.散列类型(hash)
3.列表类型(list)
4.集合类型(set)
5.有序集合类型(sorted set)

**使用场景**
- 缓存(数据查询,新闻,商品信息)(使用最多)
- 任务队列(秒杀,抢购)
- 分布式集群的==session== 的分离

### 安装

> 官方推荐的是在Linux下进行安装的,同样也有windows下的安装版,只不过window下的版本不是官方版本,安装windows版本,解压双击redisserver.exe即可,推荐在linux下进行安装,以下内容就是在linux中redis因为Redis是c语言开发的,所以需要c语言的编译环境,先编译后再安装启动后默认端口号6379

- 安装redis编译的c环境, ==yum install gcc-c++==
- 在/opt/Software/ 目录中创建Redis 目录
- 上传redis-3.0.0.tar.gz 到Redis 目录
- 进入此目录解压文件==tar -xvf redis-3.0.0.tar.gz==
- 进入解压后的目录==cd redis-3.0.0==
- 执行编译命令==make==
- 在当前目录下执行安装命令, ==make== PREFIX=/opt/Software/Redis install ,会在Redis目录下生成一个bin目录

**前端启动**

> 这种方式启动redis后,会造成当前终端界面无法退出,如果需要命令操作,需要再开启一个命令窗口,输入相关命令

- 此时进入bin目录可以启动==redis ./redis-server== ,但是此时的命令行不能退出,如果退出了就意味着关闭了数据库
	- 对于上述情况如果想操作redis的话,需要再建立一个连接,进入到redis 中的bin 目录==cd   /opt/Software/Redis/bin/==
	- 启动客户端==./redis-cli -h ip地址 -p 端口号==,如果连接本机可以写为==./redis-cli==
	- 输入命令 ==set name aaa== 放入键值对, key 为name , 值为aaa
	- 输入命令 ==get name== ,就能得到aaa
	- 按键==ctrl+c== 退出当前客户端,或者==exit==

**后台启动**

> 对于前端启动,操作会很不方便,所以建议大家使用后台启动,即从后台启动后,不用再去打开新的窗口,进行相关操作

- 进入redis解压后的文件夹中执行命令==cd /opt/Software/Redis/redis-3.0.0==
- 将redis.conf 拷贝到bin 目录下==cp redis.conf ../bin/==
- 进入bin 目录,编辑文件redis.conf ,输入命令==vim redis.conf==
- 将redis.conf 文件中的==daemonize== 从==no== 修改成==yes== 表示后台启动
- 启动redisserver ==./redis-server redis.conf==
- 然后启动客户端==./redis-cli -h ip地址 -p 端口号==,如果连接本机可以写为==./redis-cli==
- 输入命令 ==get name== ,同样就能得到aaa ,说明服务已经启动
- 输入==crtl+c== 退出客户端
- 此时如果关闭服务器有两种方法
	- 1.强制关闭,查询==pid, ps -ef | grep redis== ,然后再强行==kill -9 pid==
	- 2.正常关闭,首先退出客户端,然后输入命令==./rediscli shutdown==

### 命令
- 显示所有key的命令==keys \*==
	- ==keys s?== 以s开头,长度为2
- 清空所有数据==flushall==
- ==del key1 key2== 删除key
- ==exists key== 1代表存在,0代表不存在
- ==rename key newName== 给当前key重命名
- ==expire key== 时间设置key的过期时间,单位为秒
- ==ttl key== 获取key,所剩余的超时时间,如果没有设置超时返回1,如果返回2表示超时不存在
- ==type key== 获取key的指定类型,返回为string list set hash 和zset

**String类型**

> 字符串类型是Redis中最为基础的数据存储类型，它在Redis中是二进制安全的，这 便意味着该类型可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。 在Redis中字符串类型的Value最多可以容纳的数据长度是512M

- 设置键值对 ==set key value==
- 设置多个键值对==mset key1 value1 age 125==
- 通过指定键获取值==get key==
	- 如果key不存在返回的值为==nil==
- 删除key== del key==
- 先获取该key的值，然后在设置该key的值. ==getset key value==
- ==incr key== 增加键对应的值让值+1,如果键不存在自动创建一个,默认是0,并加1
- ==decr key== 减少键对应的值让值1
- ==incrby key increment== 相当于i+=increment
- ==decrby key decrement== 相当于i-=decrement
- ==append key value== 如果该key存在,则在原有的value后追加该值；key不存在，则重新创建一个key/value

**FastJson**

> 可以使用FastJson进行对象和字符串之间的转换

``` xml
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>fastjson</artifactId>
<version>1.0.4</version>
</dependency>
```
- 对象转换成json字符串==String jsonString = JSON.toJSONString(group);==
- json字符串转换成对象==Group group2 =  JSON.parseObject(jsonString, Group.class);==

**hash类型**

> Redis中的Hashes类型可以看成具有String Key和StringValue的map容器。所 以该类型非常适合于存储值对象的信息。如Username、Password和Age等。如果 Hash中包含很少的字段，那么该类型的数据也将仅占用很少的磁盘空间。每一个Hash可以存储4294967295个键值对。

- 赋值
	- ==hset key field1 value1== 为指定的key设定field/value对（键值对）设置一个
	- ==hmset key field1 value1 field2 value2== :为指定的key设定field/value对（键值对）设置多个

- 取值
	- ==hget key field== :返回指定的key中的field的值
	- ==hmget key field1 field2 field3== :返回指定的key中的field的值,返回多个
	- ==hgetall key== :获取key中的所有filedvaule

- 删除
	- ==hdel key filed1 filed2== ,删除多个
	- ==del key== 删除整个key对应的内容

- 增加
- ==hincrby key field== increment 设置key中filed的值增加increment，如：age增加20
- ==hexists key field== 判断指定的key中的filed是否存在
- ==hlen key== 获取key所包含的field的数量
- ==hkeys key== 获得所有的key
- ==hvals key== 获得所有的value

**list类型**

> 在Redis中，List类型是按照插入顺序排序的字符串链表。和数据结构中的普通链表 一样，我们可以在其头部(left)和尾部(right)添加新的元素。在插入时，如果该键并不 存在，Redis将为该键创建一个新的链表。与此相反，如果链表中所有的元素均被移 除，那么该键也将会被从数据库中删除。List中可以包含的最大元素数量是 4294967295.从元素插入和删除的效率视角来看，如果我们是在链表的两头插入或删除元素，这将会是非常高效的操作，即使链表中已经存储了百万条记录，该操作也可以在常量时间内完成。然而需要说明的是，如果元素插入或删除操作是作用于链表中间，那将会是 非常低效的

- 两端添加
	- ==lpush key value1 value2...== 在指定的key所关联的list的头部插入所有的 values，如果该key不存在，该命令在插入的之前创建一个与该key关联的空链 表，之后再向该链表的头部插入数据。插入成功，返回元素的个数。
	- ==rpush key value1、value2…== 在该list的尾部添加元素
	- ==lpushx key value== 仅当参数中指定的key存在时（如果与key管理的list中没 有值时，则该key是不存在的）在指定的key所关联的list的头部插入value。
	- ==rpushx key value== 在该list的尾部添加元素
- 查看列表
	- ==lrange key start end== 获取链表中从start到end的元素的值，start、end可 为负数，若为1则表示链表尾部的元素，2则表示倒数第二个，依次类推…
- 两端弹出
	- ==lpop key== 返回并弹出指定的key关联的链表中的第一个元素，即头部元素。
	- ==rpop key== 从尾部弹出元素
- ==llen key== 返回指定的key关联的链表中的元素的数量。
- ==linsert key before|after pivot value== 在pivot元素前或者后插入value这个 元素。如==linsert mylist before 2 zhangsan==

**set类型**

> 在Redis中，我们可以将Set类型看作为没有排序的字符集合，和List类型一样，我 们也可以在该类型的数据值上执行添加、删除或判断某一元素是否存在等操作。需要 说明的是，这些操作的时间是常量时间。Set可包含的最大元素数是4294967295.和List类型不同的是，Set集合中不允许出现重复的元素。和List类型相比，Set类 型在功能上还存在着一个非常重要的特性，即在服务器端完成多个Sets之间的聚合计 算操作，如unions、intersections和differences。由于这些操作均在服务端完成， 因此效率极高，而且也节省了大量的网络IO开销

- ==sadd key value1、value2…== 向set中添加数据，如果该key的值已有则不会 重复添加
- ==smembers key== 获取set中所有的成员
- ==srem key member1、member2…== 删除set中指定的成员
- ==sismember key member== 判断参数中指定的成员是否在该set中，1表示存 在，0表示不存在或者该key本身就不存在
- ==scard key== 获取set中成员的数量
- ==srandmember key== 随机返回set中的一个成员
- 集合相关
- sdiff key1 key2 返回key1与key2中相差的成员，而且与key的顺序有 关。即返回差集。如sdiff key1
key2 表示key1中哪些元素不在key2中
	- ==sdiffstore destination key1 key2== 将key1、key2相差的成员存储在 destination上
	- ==sinter key1 key2== 返回交集。
	- ==sinterstore destination key1 key2== 将返回的交集存储在destination上
	- ==sunion key1、key2== 返回并集。
	- ==sunionstore destination key1 key2== 将返回的并集存储在destination上

**sortedset类型**

> SortedSets和Sets类型极为相似，它们都是字符串的集合，都不允许重复的成员出 现在一个Set中。它们之间的主要差别是SortedSets中的每一个成员都会有一个分 数(score)与之关联，Redis正是通过分数来为集合中的成员进行从小到大的排序。然 而需要额外指出的是，尽管SortedSets中的成员必须是唯一的，但是分数(score) 却是可以重复的。在SortedSet中添加、删除或更新一个成员都是非常快速的操作，其时间复杂度为 集合中成员数量的对数。由于SortedSets中的成员在集合中的位置是有序的，因此即便是访问位于集合中部的成员也仍然是非常高效的。

- ==zadd key score1 member1 score2 member2 …== 将所有成员以及该成员的分数存放到sortedset中
- ==zscore key member== 返回指定成员的分数
- ==zcard key== 获取集合中的成员数量
- ==zrem key member[member…]== 移除集合中指定的成员，可以指定多个成员。
- ==zrange key start end [withscores]== 获取集合中脚标为startend的成员，[withscores]参数表明返回的成员包含其分数。
- ==zrevrange key start end [withscores]== 按照分数从大到小的顺序返回索引从startend的元素,[withscores]参数表明返回的成员包含其分数。
- 删除元素
	- ==zremrangebyrank key start end== 按照排名顺序删除从后向前的元素
	- ==remrangebyscore key min max==按照分数从小到达删除
- ==zrangebyscore key min max [withscores] [limit offset count]== 返回分数在[min,max]的成员并按照分数从低到高排序。[withscores]：显示分数；[limit offset count]：offset，表明从脚标为offset的元素开始并返回count个成员,例如==zrangebyscore mysort 60 100 withscores limit 0 2==
- ==zcount key min max== 获取分数在[min,max]之间的成员个数
- ==zincrby key increment member== 设置指定成员的增加的分数
- ==zrank key member== 返回成员在集合中排名,从小到大排序,索引从0开始