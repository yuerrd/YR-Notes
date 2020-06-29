# 句柄

### 局部文件句柄限制

查看

```powershell
Ulimit -n
```

修改

```powershell
sudo vim /etc/security/limits.conf

#   实际的限制
*   hard nofile 1000000
#   警告的限制
*   soft nofile 1000000 
```

### 全局文件句柄限制

查看

```
cat /proc/sys/fs/file-max
```

临时修改

```powershell
echo 1000000 > /proc/sys/fs/file-max
```

修改

```powershell
suod vim /etc/sysctl.conf
fs.file-max=1000000
sudo sysctl -p
```

