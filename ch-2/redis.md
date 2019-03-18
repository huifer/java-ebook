# redis

## 数据类型

- 字符串类型 ：String
- 散列类型：hash
- 列表类型：List
- 集合类型：Set
- 有序集合类型：SortedSet

### String类型

- 赋值
  - **set** key value
- 取值
  - **get** key
- 取值并且赋值
  - **gettest** key value
- 自增
  - value 整数类型 ，才能使用
  - **incr** key
- 尾部追加
  - **append** key value
- 获取字符串长度
  - **strlen** key

### hash类型

 - 赋值
    - **hset** key field value
 - 取值
    - **hget** key field
 - 删除field
    - **hdel** key field

### list类型

- 列表左侧增加
  - **lpush** key value
- 列表右侧增加
  - **rpush** key value
- 查看
  - **lrange** key start stop
- 左侧弹出
  - **LPOP** key
- 右侧弹出
  - **RPOP** key
- 长度
  - **llen** key
- 保留指定切片
  - **ltrim** key start stop

### set 类型

- 增加
  - **sadd** key member
- 删除
  - **srem** key member
- 获取所有
  - **smembers** key

### sortedSet类型

 - 增加
    - **zadd** key score member
- 删除
  - **zrem** key member
- 元素值排序
  - 从小到大
    - **zrange** key start stop
  - 从大到小
    - **zrevrange** key start stop
- 元素值
  - **zscore** key member
- 删除
  - **zrem** key member
- 获取排名
  - 从小到大
    - **zrank** key member
  - 从大到小
    - **zrevrank** key member

![1552911641675](assets/1552911641675.png)

## 应用场景

- 内存数据库
  - 登录信息
  - 访问信息
- 缓存服务器
  - 广告数据
- 任务队列（MQ）
  - 抢购
  - 12306
- 分布式集群架构session问题
- 网站访问统计
- 发布订阅的消息模式

## 命令行

- redis-server：启动redis服务（常用）
- redis-server ：启动redis服务（常用）
- redis-cli ：进入redis命令客户端
- redis-benchmark： 性能测试的工具
- redis-check-aof ： aof文件进行检查的工具
- redis-check-dump ：  rdb文件进行检查的工具
- redis-sentinel ：  启动哨兵监控服务

##  可视化

[RDM](https://redisdesktop.com/)

[rdm.dmg](https://github.com/onewe/RedisDesktopManager-Mac/releases)

## 客户端

- jedis

## 事务

- **不支持回滚**

### 命令行

- MULTI
  - 标记事务开始，将后续命令放入队列，使用exec执行
- EXEC
  - 在一个事务中执行所有先前放入队列的命令
- DISCARD
  - 清楚所有现在一个事务中放入队列
- WATCH
  - 当莫格事务要按照条件执行使用watch key 
  - 用来实现 **乐观锁**
- UNWATCH
  - 清楚所有先前为一个事务监控的兼







## 分布式锁

### 注意

 - 互斥性
    - 任意时刻只有一个客户端持有锁
 - 同一性
    - 加锁&解锁必须是一个客户端
 - 避免死锁
    - 一个客户端在锁的期限内崩溃没有解锁，其他客户端也需要能够加锁



### set方式

- set key value [EX seconds] [PX MILLISECONDS] [NX|XX]
  - EX seconds 指定到期时间单位秒
  - PX MILLISECONDS只能够到期时间 毫秒
  - NX 仅在键不存在时设置
  - XX 只有键存在才设置 

```java
public class RedisUtil {

    public static void main(String[] args) {
        poolConnect();

    }

    /**
     * redis 单例连接
     */
    public static void singleConnect() {
        Jedis jedis1 = new Jedis("localhost", 6379);
        String set = jedis1.set("testkey", "testvalue");
        System.out.println(set);
        String s1 = jedis1.get("testkey");
        System.out.println(s1);
        jedis1.close();

    }

    /**
     * jedis 连接池
     */
    public static void poolConnect() {
        JedisPool jedisPool = new JedisPool();
        Jedis resource = jedisPool.getResource();

        String s1 = resource.get("testkey");
        System.out.println(s1);
        resource.close();
        jedisPool.close();

    }

    private static JedisPool pool;


    static {
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMinIdle(10);

        pool = new JedisPool(config, "localhost", 6379);


    }

    public static Jedis getJedis() {
        return pool.getResource();
    }

    /**
     * 获取锁 (set)
     *
     * @param lockKey   锁key
     * @param requestId requestid
     * @param timeout   超时
     * @return
     */
    public static synchronized boolean getLockSet(String lockKey, String requestId, Integer timeout) {

        Jedis jedis = getJedis();

        String result = jedis.set(lockKey, requestId, "NX", "EX", timeout);
        if (result.equals("OK")) {
            return true;
        }
        return false;
    }

    /**
     * getLockSetNx
     *
     * @param lockKey
     * @param requestId
     * @param timeout
     * @return
     */
    public static synchronized boolean getLockSetNx(String lockKey, String requestId, Integer timeout) {

        Jedis jedis = getJedis();
        Long setnxL = jedis.setnx(lockKey, requestId);
        if (setnxL == 1L) {
            // 设置有效期
            jedis.expire(lockKey, timeout);
            return true;
        }
        return false;
    }


    /**
     * 释放锁
     * @param lockKey
     * @param requestId
     * @param timeout
     */
    public static void releaseLock(String lockKey, String requestId, Integer timeout) {
        Jedis jedis = getJedis();
        if (requestId.equals(jedis.get(lockKey))) {
            jedis.del(lockKey);
        }

    }

}

```

---

## 持久化

### rdb

- 默认持久话方式
- 通过快照的方式，进行快照需要条件
  - 自定义快照规则
  - 执行save 、bgsave、flushall
  - 主从复制操作

#### 自定义快照

- redis.conf

save <seconds> <change>

save 900 1 : 15分钟内至少一个key修改 进行快照

多个条件 **或关系**



### AOF

- AOF 开启后修改数据就有IO操作

- 修改成yes

  

### 
