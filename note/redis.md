## Redis

1. ### 安装

   ```
   ##下载文件
   wget https://github.com/redis/redis/archive/refs/tags/5.0.5.tar.gz
   tar xf 5.0.5.tar.gz
   
   ##安装
   yum install gcc
   make 
   src 生成 *.sh
   make PREFIX=/some/other/directory
   vi /etc/profile   PATH /some/other/directory

   ##环境变量
   export REDIS_HOME =/some/other/directory
   export PATH=$PATH:$REDIS
   source /etc/profile

   ##生成service服务
   cd utils
   ./install_server.sh
   
   ##服务启动脚本
   cd /etc/init.d 
   ```

   

2. ### 数据类型(type key)

   ```
   help @string
   help @list
   help @hash
   help @set
   help @sorted_set
   ```

   1. #### String

          object encoding key #embstr int raw
          set 
      - 字符串

      - 数值   

      - bitmap

        ```
        SETBIY            设置值
        BITCOUNT key 0,-1 统计值
        BITPOS            查询第一次出现1/0的值
        BITTOP            联合查询 and or xor not
        ```

   2. #### List

      - ##### 堆栈

        ```
         LPUSH k1  a b c  Link c--b--a
         LPOP  k1  c
         LRANG k1 0 ,-1
         -------------------------------
         RPUSH k2 a b c  link a--b--c
         RPOP  k2 c
        ```

      - ##### 队列

         ```
        LRANG k1 0 -1 
        LINSERT k1 after/before 元素 值
        ```

      - ##### 数组

        ```
        INDEX                 查询位置
        LSET                  设置位置
        LREM key count VALUE  移除
        ```

      - ##### 阻塞

        ```
        BLPOP
        BRPOP
        ```

   3. ##### Hash

   4. ##### Set

      - ##### 交集，并集 ,差集

        ```
          SADD key1 1 2 3 4
        	SADD key2 4 5 6 7
        	SINTER  key1 key2  #交集 4
        	SDIFF key1 key2    #差异   1 2 3
          SDIFF key2 key1    #4 5 6	7
          SUNION key1 key2   #1 2 3 4 5 6 7 
          SRANDMEMBER k1 5   #1 2 3 4 
          SRANDMEMBER k1 -5  #2 3 4 3 3 随机事件
          SPOP 取出一个
        ```
      
   5. ##### Sorted set

      - ##### 排序

        ```
        	ZADD key1 score1 x score2 xx 
        	ZSCORE key1            #分值
        	ZRANG key1             #从小到大
        	ZREVRANGE key          #从大到小
          ZINCRBY key1 score1 x  #修改分数
        ```
        
        
        
      - ##### 集合操作
      
        ```
                              权重         聚合函数
        ZUNIONSTORE key1 key2 WEIGHTS 1 0.5 SUM|MIN|MAX
        ```
      
      - #####  skip list
      
        ```
        1 - - - 5 - - - 9
        |               |
        1 - 3 - 5 - 7 - 9
        |   |   |   |   |
        1 2 3 4 5 6 7 8 9
        ```

3. ### 事务

   ```
   // WATCH  监视key
   // MULTI 开启事务
   // EXEC 执行事务
   MULTI 开启事务
   --------
   set k1 aa
   QUEUED
   --------
   set k2 bbb
   QUEUED
   --------
   exec 执行
   OK
   OK
   ```

   

4. ### 布隆过滤器模块

   模块地址： https://redis.io/modules

   布隆过滤器官网地址： https://github.com/RedisBloom/RedisBloom

   ```
   #redis.conft添加模块
   loadmodule /opt/redis5/RedisBloom-2.2.5/redisbloom.so #加载模块
   BF.ADD k1 test
   (integer) 1
   BF.EXISTS k1 test
   (integer) 0
   ```

   布谷鸟过滤器

5. #### 配置相关

   1. 过期策略

      ```
      1.被动是访问判定
      2.周期轮训判定（增量）
        2.1 过期时间的键集合
        2.2 每次从这个集合中随机检测20个键查看他们是否过期
        2.3 超过25%的集合中的键已经过期  继续检测过期集合
      ```

   2. 内存最大

      ```
      maxmemory  最大内存
      maxmemory-policy 回收策略
      LRU 最久没有使用的键
      LFU 频率最少的键
      策略：
      allkeys-lru     最久没有使用的键
      volatile-lru    过期时间的键集合中驱逐最久没有使用的键
      allkeys-random  所有key随机删除
      volatile-random 过期键的集合中随机驱逐
      volatile-ttl    驱逐马上就要过期的键
      volatile-lfu    过期时间的键中驱逐使用频率最少的键
      allkeys-lfu     所有键中驱逐使用频率最少的键
      ```

6. ### RDB（Save the DB on disk）

   1. #### Fork

      创建一个子进程 拷贝父类所有key的引用

      父进程的修改不会影响子进程 子进程的修改不会影响父进程

      copy on write 写时复制

   2. #### 触发规则

      save 手动触发

      bgsave： save <seconds> <changes>

   3. #### 存储文件

      var/lib/redis/6389/dump.rdb

7. #### AOF（写操作记录到文件）

   1. RDB和AOF可以同时开启

   2. 开启了AOF只会用AOF恢复

   3. 恢复数据优化

      ```
      触发合并优化 BGREWRITEAOF  
      4.0 以前 重写  合并重复的命令 
      4.0 以后 重写  先保存rdb格式数据，在写增量的数据aof文件
      
      auto-aof-rewrite-percentage 100%  
      auto-aof-rewrite-min-size 64mb    #达到64MB重写 记录上次BGREWRITEAOF后的大小
      ```

   4. 配置

      ```
      appendonly yes #开启AOF文件
      触发规则 写的buffer 触发flush
      #appendfsync always  每次
      appendfsync everysec  每秒
      #appendfsync no      IO buffer 满了
      ```

8. ####  集群搭建

   ```
    ###主从
    REPLICAOF ip port                        #设置从节点命令
    replicaof <masterip> <masterport>        #配置
    REPLICAOF no one                         #自己变成主 
    redis-server ./sentinel.conf --sentinel  #启动sentinel 
   ```

   sentinel 配置文件详情：https://redis.io/topics/sentinel

   ```
   集群
   redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
   --cluster-replicas 1
   
   ###配置
   https://redis.io/topics/cluster-tutorial
   cluster-enabled yes
   ```

   

9. #### 常见问题

   1. 击穿：高并发 key过期了 并发访问数据库

      ```
      key 过期
      LRU
      LFU 
      setnx() -> 锁
      1. 发现key null
      2. setnx 锁 过期时间
      3. 只有获取锁的去访问DB
      
      ```

   2. 穿透： 数据库根本不存在的的数据

      ```
      1. 客户端 使用布隆过滤器
      2. redis 使用布隆过滤器模板
      3. 布谷鸟过滤器
      ```

   3. 雪崩： 大量的key同时失效 大量的请求访问DB

      ```
      1. 随机过期时间
      2. 使用击穿方案
      3. sentinel 资源db限流
      ```

   4. 分布式锁

      ```
      setnx 过期时间
      redssion
      建议用 zk锁
      ```

10. #### 客户端

   ```
   lettuce 
   jedis
   redssion
   spring-data-redis
   ```
