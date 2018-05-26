## Redis 入门

### 1.1 课程介绍
- NoSql概述
- Resdis 概述
- Redis 安装使用
- Jedis 入门
- Redis 数据类型
- Keys 通用操作
- Redis 特性
- Redis 持久化

### 2.1 NoSql 概述
NoSql = Not Only Sql
非关系型数据库

为什么需要 NoSql： 
 - High performance 高并发读写
 - Huge Storage 海量数据的高效率存储和访问
 - High Scalability && High Availability 高可扩展和高可用性
 
NoSql 数据库四大分类
![NoSql数据库四大分类](https://github.com/lyk2655/lyk2655.github.io/raw/master/imooc/Redis/img/NoSql_4类数据库比较.png)

### Redis 概述
用C语言开发的开源的高性能的键值对数据库。

支持的键值数据类型：
 - 字符串类型
 - 列表类型
 - 有序集合类型
 - 散列类型
 - 集合类型
 
#### Redis 应用场景：
 - 缓存
 - 任务队列
 - 网站访问统计
 - 数据过期处理
 - 引用排行榜
 - 分布式集群架构中的session分离
 
### 3. Redis 安装

 - `sudo tar -zxvf redis-3.2.6.tar.gz`
 - `tar -zxvf redis-3.2.6.tar.gz`
 - ` sudo mkdir /usr/redis`
 - `sudo mv redis-3.2.6 /usr/redis/`
 - `cd /usr/redis/redis-3.2.6/`
 安装编译
 - `sudo make`
 - `sudo make install`
 - `sudo mkdir /etc/redis`
  直接运行 `redis-server` 是前端启动，终端不能关掉。
  修改redis.conf可以后台启动redis 服务器：
 - `sudo cp redis.conf /etc/redis/`
 - `sudo vi /etc/redis/redis.conf` daemonize no  => daemonize  yes
 - `cd /usr/local/bin/`
 启动:
 - `redis-server /etc/redis/redis.conf`
 关闭:
  - `redis-cli shutdown`
 测试联通：
 - `redis-cli -p 6379`
 - `ping` => PONG
 使用：
 ```
 127.0.0.1:6379>set name linyk3
 127.0.0.1:6379>get name
 127.0.0.1:6379>del name
 127.0.0.1:6379>keys *
 ```
 
 ### 4. Jedis 入门
 Jedis 是Redis 官方首选的Java客户端开发包
 在项目中引入jedis包
 ```
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>2.9.0</version>
</dependency>
```

Java 测试代码
```java
import org.junit.Test;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisTest {

	@Test
	/**
	 * 单实例测试
	 */
	public void demo1() {
		// 1.设置IP，端口
		Jedis jedis = new Jedis("127.0.0.1", 6379);
		// 2.保存数据
		jedis.set("name", "imooc");
		// 3.获取数据
		String value = jedis.get("name");
		System.out.println(value);
		// 4.释放资源
		jedis.close();
	}

	@Test
	/**
	 * 连接池测试
	 */
	public void demo2() {
		// 获取连接池配置对象
		JedisPoolConfig config = new JedisPoolConfig();
		//　设置最大连接数
		config.setMaxTotal(30);
		// 设置最大空闲连接数
		config.setMaxIdle(10);
		// 获取连接池
		JedisPool jedisPool = new JedisPool(config,"127.0.0.1",6379);
		// 获得核心对象
		Jedis jedis = null;
		try {
			//通过连接池获取连接
			jedis = jedisPool.getResource();
			// 保存数据
			jedis.set("pool", "imooc");
			// 获取数据
			String value = jedis.get("name");
			System.out.println(value);
			System.out.println(jedis.keys("*"));
		} catch(Exception e) {
			e.getSuppressed();
		}finally {
			// 关闭连接，释放资源
			if(jedis != null) {
				jedis.close();
			}
			// 关闭连接池
			if (jedisPool != null) {
				jedisPool.close();
			}
		}
	}
}
```
 
### 5. Redis 数据结构
五种数据类型：
 1. 字符串 String
 2. 哈希 hash
 3. 字符串列表 list
 4. 字符串集合 set
 5. 有序字符串集合 sorted set
 
Key 定义注意点：
 - 不要过长
 - 不要过短
 - 统一的命名规范
 
 ##### 字符串 String
  - 二进制安全，存入和获取的数据相同
  - value 最多可以容纳的数据长度是512M

常用命令
 - 赋值 
 `127.0.0.1:6379> set key value`
 - 取值
`127.0.0.1:6379> get key`
`127.0.0.1:6379> getset key newvalue` 
 - 删除
`127.0.0.1:6379> del key`
 - 数值增减
 `127.0.0.1:6379> incr num`
 `127.0.0.1:6379> decr num`
 incr命令将value自增1，如果value不存在，默认初始化为1，如果不能转为整型，会报错。
 decr命令将value自减1，如果value不存在，默认初始化为0，如果不能转为整型，会报错。
 - 扩展命令
 `127.0.0.1:6379>incrby key 5`
 `127.0.0.1:6379>decrby key 5`
 `127.0.0.1:6379>append key value2` 直接在字符串后面追加value2
 

#### Hash 类型
 - String Key 和 String Value 的map容器
 - 每个哈希可以存储 429496725 个键值对
 
常用命令
- 赋值
`127.0.0.1:6379>hset key coll value`
`127.0.0.1:6379>hset myhash username linyk3`
`127.0.0.1:6379>hset myhash age 18`
`127.0.0.1:6379>hset myhash2 username jerry age 17`
- 取值
`127.0.0.1:6379>hget myhash username`
`127.0.0.1:6379>hmget myhash username age`
`127.0.0.1:6379>hgetall myhash`
- 删除
`127.0.0.1:6379>hdel myhash2 username age`
- 增加数字
`127.0.0.1:6379>hincrby myhash2 age 8`
- 其他命令
`127.0.0.1:6379>hexists myhash`
`127.0.0.1:6379>hlen myhash`
`127.0.0.1:6379>hkeys myhash`
`127.0.0.1:6379>hval myhash`

#### List存储
- ArrayList 使用数组方式（读取效率高，增删效率低）
- LinkedList 使用双向链表方式（读取效率低，增删效率高）

常用命令 
- 两端添加
`127.0.0.1:6379>lpush mylist a b c`
`127.0.0.1:6379>rpush mylist d e f`

- 查看列表
`127.0.0.1:6379>lrange mylist 0 5`
`127.0.0.1:6379>lrange mylist 0 -2`
- 两端弹出
`127.0.0.1:6379>lpop mylist`
`127.0.0.1:6379>rpop mylist`
- 获取列表元素个数
`127.0.0.1:6379>llen mylist`
- 扩展命令
`127.0.0.1:6379>lpushx mylist m   //mylist 存在时，在左边插入m。 mylist 不存在，不插入`
`127.0.0.1:6379>rpushx mylist n`
`127.0.0.1:6379>lrem mylist `
`127.0.0.1:6379>lrange mylist 0 -1` //
`127.0.0.1:6379>linsert mylist after b 11` //在list的元素b之前插入11
`127.0.0.1:6379>rpoplpush mylist1 mylist2` //从mylist1右边pop出来，push进去mylist2

应用场景：消息队列

#### Set 存储
 - Set 在集合中不允许出现重复的元素。
 - Set 包含最大的元素个数：429496725
 
常用命令
`127.0.0.1:6379>sadd myset a b c`
`127.0.0.1:6379>srem myset 1`
`127.0.0.1:6379>smembers myset`  //查看set元素
`127.0.0.1:6379>scard myset` //查询set元素数量
`127.0.0.1:6379>sismembers myset a` //判断a是否set中元素
`127.0.0.1:6379>sdiff myset1 myset2` //差集 myset1 - myset2
`127.0.0.1:6379>sinter myset1 myset2` //交集 
`127.0.0.1:6379>sunion myset1 myset2` //并集
`127.0.0.1:6379>srandmember myset` //随机返回myset中的某元素
`127.0.0.1:6379>sdiffstore myset1 myset2 myset3` //myset1和myset2的差集的结果存储在myset3里
`127.0.0.1:6379>sinterstore myset1 myset2 myset3` //myset1和myset2的交集并把结果放到myset3里

应用场景：
1. 跟踪一些唯一数据，例如客户IP 唯一存储来判断是否注册
2. 用于维护数据对象之间的关联关系，例如购买A产品的客户set1， 和购买B产品的客户set2，那么同时购买A和B的客户就是set1和set2的并集。

#### Sorted-Set 存储
Sorted-Set 每个元素都有一个分数，按分数排列，位置有序。


常用命令：
`127.0.0.1:6379>zadd mysort 70 A 80 B  90 C` //在mysort里存储A元素（分数是70），B元素(分数80)，C元素(分数90)
`127.0.0.1:6379>zadd mysort 50 A` //A元素存在，修改分数为50. 若不存在，直接插入
`127.0.0.1:6379>zscore mysort A` //查看A元素的分数
`127.0.0.1:6379>zcard mysort`  //查看mysort的元素个数
`127.0.0.1:6379>zrem mysort A` //删除mysort中A元素
`127.0.0.1:6379>zrange mysort 0 -1` //范围查找元素
`127.0.0.1:6379>zrange mysort 0 -1 withscores` //范围查找元素以及分数(从小到大排序)
`127.0.0.1:6379>zrevrange mysort 0 -1 withscores` //范围查找元素以及分数(从大到小排序)
`127.0.0.1:6379>zremrangebyrank mysort 0 4` //按范围查找并将他们删除
`127.0.0.1:6379>zremrangebyscores mysort 80 100`//按分数范围查找并将他们删除
`127.0.0.1:6379>zrangebyscore mysort 0 100 withscores limit 0 2` //按分数查找0-100的，并将他们分数显示出来，且只显示0-2共两个
`127.0.0.1:6379>zcount mysort 80 90` //查看mysort中80分 和 90分的数量

应用场景：游戏排名，微博话题排名，大型在线游戏积分排行榜， 构建索引数据




### 6. Keys的通用操作

#### 通用命令
`127.0.0.1:6379>keys *` //查看所有的key
`127.0.0.1:6379>del key1 key2` //删除key1， key2
`127.0.0.1:6379>exists key1` //查看key1是否存在
`127.0.0.1:6379>get key1` //获取key1对应的value1
`127.0.0.1:6379>rename key1 key2`//将key1重命名为key2
`127.0.0.1:6379>expire key1 1000` //设置key1过期，时间是1000秒
`127.0.0.1:6379>ttl key1` //查看key1的到期时间，没有设置返回-1
`127.0.0.1:6379>type key1` //查看key1的类型，例如list，hash等

### 7. Redis 特性
1. 可以有多个数据库。一个redis实例可以提供0-15号，总共16个数据库。 
`127.0.0.1:6379>select 1` //可以选择连接1号数据库
`127.0.0.1:6379>move myset 0` //前面选择了1数据库，将1号数据库中的myset移动到0数据库


Redis 支持事务机制。所有的命令串行化，在执行命令时，不接受其他客户端的命令，从而所有命令原子化执行。支持的命令:
`127.0.0.1:6379>multi` //开启事务 
`127.0.0.1:6379>exec`  //提交
`127.0.0.1:6379>discard` //回滚


### 8. Redis 持久服务器 ### 8. Redis 持久化
 Redis 数据存在内存。 持久化： 内存->硬盘
 持久化方式：
 1. RDB方式： 在指定的时间间隔内将内存数据快照写入到硬盘
 2. AOF方式： 在日志中记录数据库的每一个操作。
 3. 禁止持久化: 仅仅用作缓存
 4. 同时使用RDB和AOF
 
#### RDB方式持久化
优势：
 - 备份，恢复
 - 性能最大化
 - 启动效率高

缺点: 
 - 定时持久化前的故障数据可能会丢失
 - 子进程来来fork写入到硬盘，当数据量很大的时候，服务器压力很大。
 
```
/etc/redis/redis.conf
save 900 1     //每900秒至少有1个可以发生变化，就写入到硬盘
save 300 10
save 60 10000
dbfilename dump.rdb  //保存文件名
dir ./               //路径
```

#### AOF方式持久化
优势：
 - 更高的数据安全性。
 - 日志写入方式为append，故障时不影响前面的数据。开机后可以恢复数据
 - 重写切换
 - 日志文件记录所有的修改操作，可以用来完成数据重建。
 
 
劣势：
 - 相同数量的数据集，内容容量相对较大；
 - 效率比较慢

```
/etc/redis/redis.conf
appendonly no   => yes 时才可以使用AOF
# appendfsync always  同步策略：总是同步
appendfsync everysec  同步策略：每一秒配置
# appendfsync no      同步策略：不同步
```


`127.0.0.1:6379>flushall` //清除数据库

### 总结
1. NoSql 
2. Redis 安装使用
3. Jedis 使用
4. 常用数据类型，重点String，Hash
5. 事务支持
6. 持久化

 
 
 

 
 
 
