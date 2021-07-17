## Mysql

1. ####  mycli 安装centos7

   ```
    python3 -m pip install --upgrade pip
    pip3 install mycli
   ```

    [安装地址](https://www.mycli.net/install)
 Note：Python 3.6+ 
   
2.  #### 功能

   - 连接器： 连接用户的连接
   - 分析器： 词法分析，语法分析
   - 优化器： 优化SLQ语句，规定执行流程
   - 执行器 ：SQL语句的执行

3. #### 调优

   - show profiling
   - Performance Schema
   - 尽量避免null
   - 选择最小的数据类型
   
4. #### 数据类型

   - [整型类型](https://dev.mysql.com/doc/refman/5.7/en/integer-types.html) 

     |   类型    | 空间 | 有符号的最小值 | 有符号的最大值 | 无符号最小值 | 无符号最大值 |
     | :-------: | :--: | :------------: | :------------: | :----------: | :----------: |
     |  TINYINT  |  8   |      -128      |      127       |      0       |     255      |
     | SMALLINT  |  16  |     -32768     |     32767      |      0       |    65535     |
     | MEDIUMINT |  24  |    -8388608    |    8388607     |      0       |   16777215   |
     |    INT    |  32  |  -2147483648   |   2147483647   |      0       |  4294967295  |
     |  BIGINT   |  64  |     -2 ^63     |    2 ^63-1     |      0       |    2^64-1    |

   - [字符串](https://dev.mysql.com/doc/refman/5.7/en/char.html)

     | Value        | `CHAR(4)` | Storage Required | `VARCHAR(4)` | Storage Required |
     | :----------- | :-------- | :--------------- | :----------- | :--------------- |
     | `''`         | `'  '`    | 4 bytes          | `''`         | 1 byte           |
     | `'ab'`       | `'ab '`   | 4 bytes          | `'ab'`       | 3 bytes          |
     | `'abcd'`     | `'abcd'`  | 4 bytes          | `'abcd'`     | 5 bytes          |
     | `'abcdefgh'` | `'abcd'`  | 4 bytes          | `'abcd'`     | 5 bytes          |
     
     varchar 会用1个字节或2个字节长度的前缀数据  不超过255 一个字节使用1个长度，超过255 字节使用2个字节长度
     
     char 最大值255 varchar最大值65535
     
   - [时间类型](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-types.html)
   
     | Data Type                                                    | “Zero” Value            | rang                                      | Storage Required |      |
     | :----------------------------------------------------------- | :---------------------- | ----------------------------------------- | ---------------- | ---- |
     | [`DATE`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) | `'0000-00-00'`          | 1000-01-01 ~ 9999-12-31                   | 3bytes           |      |
     | [`TIME`](https://dev.mysql.com/doc/refman/5.7/en/time.html)  | `'00:00:00'`            |                                           |                  |      |
     | [`DATETIME`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) | `'0000-00-00 00:00:00'` | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 | 8bytes           | 毫秒 |
     | [`TIMESTAMP`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) | `'0000-00-00 00:00:00'` | 1970-01-01 00:00:01 ~ 2038-01-19 03:14:07 | 4bytes           | 秒   |
     | [`YEAR`](https://dev.mysql.com/doc/refman/5.7/en/year.html)  | `0000`                  |                                           |                  |      |
     datetime：  占用空间大，损失日期类函数 与时区无关，字符串
   
     timestamp： 采用整型存储，依赖时区，
   
     date： 可以使用日期类函数，字符串 
   
5. #### 存储引擎

   |          |        MyISAM        |           InnoDB           |
   | :------: | :------------------: | :------------------------: |
   | 支持版本 |       5.5之前        |          5.5之后           |
   |   事务   |          N           |             Y              |
   |   表锁   |          Y           |             Y              |
   |   行锁   |          N           |             Y              |
   |   外键   |          N           |             Y              |
   |  count   |   保存有表的总行数   |     没有保存表的总行数     |
   | CURD操作 |     大量的SELECT     | 大量的DELETE,INSERT,UPDATE |
   |  B+tree  | 叶子节点存放数据索引 |      叶子节点存放数据      |

   

6. #### [EXPLAIN](https://dev.mysql.com/doc/refman/5.7/en/explain-output.html)

   ```
   +----+--+------+------------+------+---------------+--------+---------+--------+------+----------+--------+
   | id |select_type|table|partitions | type | possible_keys | key | key_len | ref    | rows | filtered | Extra  |
   +----+----+-------+------------+------+--    -------+--------+---------+--------+------+----------+--------+
   | 1  | SIMPLE | staff | <null>| ALL  | <null>     | <null> | <null>  | <null> | 2    | 100.0    | <null> |
   +----+---+-------+----------+------+---------------+--------+---------+--------+------+----------+--------+
   ```

   type优先级： 

   <u>system</u> **>** <u>cons</u>t **>** <u>eq_ref</u> **>** <u>ref</u> **>** <u>fulltext</u> **>** <u>ref_or_null</u>**>**<u>index_merge</u>**>**<u>unique_subquery</u>**>**<u>index_sumquery</u>**>**<u>range</u>**>**<u>index</u>**>**alll

7. #### 索引

   - 回表

     1. 普通索引叶子节点存放的是主键id
     2. 查到主键id再查主键id数的数据

   - 索引覆盖  

     ```mysql
     select id from user where name= 1
     ```

   - 最左匹配

     ```
     select * from user where age=? and name =?
     select * from user whre
     ```

   - 索引下推 组合索引

     多个条件 返回数据集合，返回后判断，返回前判断
     
     

8. #### 数据导入

   ```
   source  /xxx/xxx.sql
   ```

9. #### 优化

   - 查询执行时间

     ```sql
     set profiling=1;
     show profiles;
     #最后一次查询时间
     show status like '%last_query_const%'
     ```

   - order by 排序

     ```sql
     # 没超过使用单次排序， 超过是有两次排序
     show variables like '%max_length_for_sort_data%';
     ```

     1. 使用索引排序

     2. 排序都是 aes or desc

     3. 第一列使用范围查询不能在使用索引排序

     4. 两次传输排序

        - 一次只查询排序字段

        - 二次查询排序完的数据

     5. 单次传输排序

        - 读取数据的所有列并排序

     6. 

   - 类型转换会产生全表索引 

      ```sql
     select name,age,phone from user wehre phone =143XXXX8423
     select name,age,phone from user wehre phone ='143XXXX8423'
     ```

   - 建立索引优化

     1. 区分不大的列不建索引

     2. 使用区分度规划索引 80%

         ```sql
        select count(destinct("列名")/count(*)) from table;
        ```

     3. 一张表索引数量不要超过5个

     4. join 不超过3张表

   - 查询优化

     1. 系统性能
     2. 不建议使用select * 
     3. 等值传播（术语） 多张表where加相同的查询条件写一个条件就可以

   - join优化

     ```sql
     # 默认256k
     show variables like '%join_buffer_size%';
     # optimizer_switch block_nested_loop 设置为on 默认开启
     show variables like '%optimizer_switch%';
     ```

   - Limit 优化

     ```sql
     select * from user limit 10000,10;
     select * from user a join(select id from user limit 10000,10) b on a.id = b.id;
     ```

   - 

10. #### [分区](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)

  - ##### 分区类型

    1. 范围划分

       ```sql
       CREATE TABLE employees (
           id INT NOT NULL,
           fname VARCHAR(30),
           lname VARCHAR(30),
           hired DATE NOT NULL DEFAULT '1970-01-01',
           separated DATE NOT NULL DEFAULT '9999-12-31',
           job_code INT NOT NULL,
           store_id INT NOT NULL
       )
       PARTITION BY RANGE (store_id) (
           PARTITION p0 VALUES LESS THAN (6),
           PARTITION p1 VALUES LESS THAN (11),
           PARTITION p2 VALUES LESS THAN (16),
           PARTITION p3 VALUES LESS THAN (21)
       );
       ```

    2. 列表分区

       ```sql
       CREATE TABLE employees (
           id INT NOT NULL,
           fname VARCHAR(30),
           lname VARCHAR(30),
           hired DATE NOT NULL DEFAULT '1970-01-01',
           separated DATE NOT NULL DEFAULT '9999-12-31',
           job_code INT,
           store_id INT
       )
       PARTITION BY LIST(store_id) (
           PARTITION pNorth VALUES IN (3,5,6,9,17),
           PARTITION pEast VALUES IN (1,2,10,11,19,20),
           PARTITION pWest VALUES IN (4,12,13,14,18),
           PARTITION pCentral VALUES IN (7,8,15,16)
       );
       ```

    3. 列分区

    4. hash分区

       ```sql
       CREATE TABLE employees (
           id INT NOT NULL,
           fname VARCHAR(30),
           lname VARCHAR(30),
           hired DATE NOT NULL DEFAULT '1970-01-01',
           separated DATE NOT NULL DEFAULT '9999-12-31',
           job_code INT,
           store_id INT
       )
       PARTITION BY HASH(store_id)
       PARTITIONS 4;
       ```

    5. key分区

       ```sql
       CREATE TABLE tm1 (
           s1 CHAR(32) PRIMARY KEY
       )
       PARTITION BY KEY(s1)
       PARTITIONS 10;
       
       ```

    6. 子分区

  - ##### 管理

    1. 使用分区选择来获取此信息

       ```sql
       mysql> SELECT * FROM tr PARTITION (p2);
       +------+-------------+------------+
       | id   | name        | purchased  |
       +------+-------------+------------+
       |    2 | alarm clock | 1997-11-05 |
       |   10 | lava lamp   | 1998-12-25 |
       +------+-------------+------------+
       2 rows in set (0.00 sec)
       ```

    2. 删除分区

       ```sql
       ALTER TABLE tr DROP PARTITION p2;
       ```

    3. 添加分区

       ```sql
       mysql> ALTER TABLE members
            >     ADD PARTITION (
            >     PARTITION n VALUES LESS THAN (1970));
       ```

    4. 

11. #### 配置

   1. 跳过认证插件

      ```sql
      # 配置 vim /etc/my.cnf
      --skip-grant-tables[={OFF|ON}]
      类型	布尔值
      默认值	OFF
      ```

   2. 连接

      ```sql
      # 查看最大连接数
      show variables like '%max_connections%';
      +-----------------+-------+
      | Variable_name   | Value |
      +-----------------+-------+
      | max_connections | 151   |
      +-----------------+-------+
      set max_connections=3000
      # 限制用户的连接数
      show variables like '%max_user_connections%';
      +----------------------+-------+
      | Variable_name        | Value |
      +----------------------+-------+
      | max_user_connections | 0     |
      +----------------------+-------+
      set global max_user_connections = 10;
      # 等待队列
      show variables like '%back_log%';
      +---------------+-------+
      | Variable_name | Value |
      +---------------+-------+
      | back_log      | 80    |
      +---------------+-------+
      
      # 配置 vim /etc/my.cnf
      # max_connections = 3000
      # max_user_connections=10
      # back_log=80
      
      ```

   3. 查询语句

      ```sql
      # 查看执行的信息
       show processlist
      ```

   4. [variables](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)

      ```sql
      # log_bin
      show variables like '%log_bin%';
      +---------------------------------+--------------------------------+
      | Variable_name                   | Value                          |
      +---------------------------------+--------------------------------+
      | log_bin                         | ON                             |
      | log_bin_basename                | /var/lib/mysql/mysql-bin       |
      | log_bin_index                   | /var/lib/mysql/mysql-bin.index |
      | log_bin_trust_function_creators | OFF                            |
      | log_bin_use_v1_row_events       | OFF                            |
      | sql_log_bin                     | ON                             |
      +---------------------------------+--------------------------------+
      # vim /etc/my.cnf
      log-bin=mysql-bin
      server-id=1
      # 二进制日志格式
      binlog_format=ROW
      # 指定而精致日志的数据库
      binlog-do-db=db_name
      # 忽略的db数据库
      binlog-ignore-db=db_name
      
      # 查询日志
      show variables like '%general_log%';
      +------------------+-----------------------------------+
      | Variable_name    | Value                             |
      +------------------+-----------------------------------+
      | general_log      | OFF                               |
      | general_log_file | /var/lib/mysql/VM-21-8-centos.log |
      +------------------+-----------------------------------+
      # 慢查询
      show variables like '%slow_query_log%';
      +---------------------+----------------------------------------+
      | Variable_name       | Value                                  |
      +---------------------+----------------------------------------+
      | slow_query_log      | OFF                                    |
      | slow_query_log_file | /var/lib/mysql/VM-21-8-centos-slow.log |
      +---------------------+----------------------------------------+
      # 慢查询时间
      show variables like '%query_time%';
      +-----------------+-----------+
      | Variable_name   | Value     |
      +-----------------+-----------+
      | long_query_time | 10.000000 |
      +-----------------+-----------+
      # 查询缓存
      show variables like '%query_cach%';
      +------------------------------+---------+
      | Variable_name                | Value   |
      +------------------------------+---------+
      | have_query_cache             | YES     |
      | query_cache_limit            | 1048576 |
      | query_cache_min_res_unit     | 4096    |
      | query_cache_size             | 1048576 |
      | query_cache_type             | OFF     |
      | query_cache_wlock_invalidate | OFF     |
      +------------------------------+---------+
      # 缓存状态查询
      show status like '%Qcache%';
      +-------------------------+---------+
      | Variable_name           | Value   |
      +-------------------------+---------+
      | Qcache_free_blocks      | 1       |
      | Qcache_free_memory      | 1031832 |
      | Qcache_hits             | 0       |
      | Qcache_inserts          | 0       |
      | Qcache_lowmem_prunes    | 0       |
      | Qcache_not_cached       | 12      |
      | Qcache_queries_in_cache | 0       |
      | Qcache_total_blocks     | 1       |
      +-------------------------+---------+
      # 排序缓存
      show variables  like '%sort_buffer_size%';
      +-------------------------+---------+
      | Variable_name           | Value   |
      +-------------------------+---------+
      | innodb_sort_buffer_size | 1048576 |
      | myisam_sort_buffer_size | 8388608 |
      | sort_buffer_size        | 262144  |
      +-------------------------+---------+
      ```

   

12. #### 锁

   - MySAM  表锁

     1. 共享读锁
     2. 独占血锁

     ```
     lock table user read;
     lock table user write;
     unlock table;
     # 状态查询
     show status like "%table_locks%"
     +-----------------------+-------+
     | Variable_name         | Value |
     +-----------------------+-------+
     | Table_locks_immediate | 122   |
     | Table_locks_waited    | 0     |
     +-----------------------+-------+
     ```

   - InnoDB

     ```sql
     autocommit=1 # 立即生效，自动提交
     autocommit=0 # 不生效，commit命提交
     ```

     

     1. 共享锁

        ```sql
        select * from user where id =1 lock in share mode;
        ```

     2. 排它锁

        ```sql
        select * from user where id =1 for update;
        ```



