### HBASE

##### 连接

```powershell
hbase shell
```

##### 查看所有表

```powershell
list
```

##### 查询

```powershell
# 获取一个id的所有数据
get 'member','rowKey'

# 获得一个id，一个列簇（一个列）中的所有数据
get 'member', 'rowKey', 'famil'

# 查询整表数据
scan 'member'

```

