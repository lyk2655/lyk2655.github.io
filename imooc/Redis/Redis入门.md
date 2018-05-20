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
 
### Redis 安装

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
 
 ### Jedis 入门
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
 
### Redis 数据结构
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
 - 每个哈希可以存储429496725个键值对
 
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

- 查看列表
- 两端弹出
- 获取列表元素个数
- 扩展命令


### Redis 通用命令
 
 
 
 
 
 
 
