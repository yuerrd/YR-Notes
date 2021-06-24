## Zookeeper

1. #### 节点信息

   - 持久节点

   - 临时节点（client session）

   - 序列节点

     ```
     reate -s /node1
     /node10000000001
     ```

   - 节点存储数据（1MB）

   - 角色

      ```
     Leader
     Follower 才能选举
     Observer
      ```

   - 信息

     ```
     cZxid =0X200000002  全局事务id  0X2 第几届leader
     mZxid =0X200000003  修改事务id   
     连接信息也会消耗 全局事务id
     ```

2. ### 安装

   ```
   wget https://downloads.apache.org/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz
   
   export ZOO_HOME=/opt/apache-zookeeper-3.7.0-bin
   export PATH=$PATH:${ZOO_HOME}/bin
   source /etc/profile
   
   #修改zoo.cfg
   server.x=IP地址:接受数据:选主投票
   server.1=localhost:2287:3387
   server.2=localhost:2288:3388
   server.3=localhost:2289:3389
   server.4=localhost:2290:3390:observer
   dataDir=/opt/zoo/data/node1
   
   echo 1 >/opt/zoo/data/node1/myid
   echo 2 >/opt/zoo/data/node2/myid
   echo 3 >/opt/zoo/data/node3/myid
   
   zkServer.sh status /opt/zoo/config/zoo1.cfg
   zkServer.sh status /opt/zoo/config/zoo2.cfg
   zkServer.sh status /opt/zoo/config/zoo3.cfg
   ```

   

3. #### 协议

   - paxos

   - ZAB

     zxid 最新

     myid 最大

   - watch

      ```
     create
     delete
     change
     children
      ```

4. ### 分布式锁

   ```
   lock0000000001
   lock0000000002  watch  lock0000000001
   lock0000000003  watch  lock0000000002
   lock0000000004  watch  lock0000000003
   lock0000000005  watch  lock0000000004
   ```