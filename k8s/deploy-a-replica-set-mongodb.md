### docker-compose.yamldocker部署mongo副本集

#### 1.服务器地址

| 服务器IP      | 域名              | 节点      |
| :------------ | :---------------- | :-------- |
| 192.168.201.4 | mongodb-primary   | primary   |
| 192.168.201.5 | mongodb-secondary | secondary |
| 192.168.201.6 | mongodb-arbiter   | arbiter   |

#### 2.添加hosts

~~~
192.168.201.4 mongodb-primary
192.168.201.5 mongodb-secondary
192.168.201.6 mongodb-arbiter
~~~

#### 3.生成keyfile文件

~~~
sudo yum install openssl
sudo yum install openssl-devel
sudo openssl rand -base64 741 > mongodb-keyfile

# 修改权限
chmod 600 mongodb-keyfile
chown 999 mongodb-keyfile
~~~

#### 4.docker-compose

 [docker-compose.yaml](./docker-compose.yaml)

#### 5.启动容器

~~~shell
docker-compose up -d mongodb-primary
docker-compose up -d mongodb-secondary
docker-compose up -d mongodb-arbiter
~~~

#### 6.连接到副本集上并且安装配置

```
# 进入mongodb-primary容器
docker exec -it mongodb-primary bash

# 登陆mongod
mongo mongodb://admin:123456@mongodb-primary:27017

# 执行配置文件
rs.initiate( {
   _id : "rs0",
   members: [
      { _id: 0, host: "mongodb-primary:27017" ,priority:2},
      { _id: 1, host: "mongodb-secondary:27017",priority:1 },
      { _id: 2, host: "mongodb-arbiter:27017",arbiterOnly:true}
   ]
})

# 查看mongo节点信息
rs.status()
```