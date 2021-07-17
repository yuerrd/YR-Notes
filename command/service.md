### Python 简单命令

1. 启动一个简单服务

   ```
   python -m SimpleHTTPServer 18765
   ```
   
2. pm2启动一个服务

   ```
    pm2 start npm --name autopai-push -- run serve
   ```

### 查看CPU变高排查

1. 先执行top ，找到CPU占用比较高的进程

   ```
   top
   ```

2. jistack  进程ID  

   ```
   jstack 进程ID  >show.txt
   ```

3. 找到经常中cpu占用比较高的线程，线程Id转为16进制

   ```
   top  -p 进程ID - H  ## 查看进程中的线程情况
   ```

4. 通过线程ID查找线程情况

