## Linux

1. ### 计算机组成

   - 门电路

   ​     非门:   $$Y=\overline{A}$$ 
   
   ​     与门：$$ Y=A *B$$ 
   
   ​     异或门：$$A \bigoplus B = (\overline{A} *B)+ (A *\overline {B})$$ 

   - 寄存器
     1. D触发器

        input(输入端) clock(时钟) output(输出端)

2. ###  命令

   ```shell
   lsof
   pcstat #缓存使用情况
   ```
   
3. ### 句柄

   ##### 局部文件句柄限制

   查看

   ```powershell
   ulimit -n
   ulimit -n 102400
   ```

   修改

   ```powershell
   sudo vim /etc/security/limits.conf
   
   #   实际的限制
   *   hard nofile 102400
   #   警告的限制
   *   soft nofile 102400 
   ```

   ##### 全局文件句柄限制

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

   ##### TCP释放时间，减小，调低time_wait状态端口等待时间

   ```powershell
   # 调低端口释放后的等待时间，默认为60s，修改为15~30s
   sysctl -w net.ipv4.tcp_fin_timeout=30
   # 修改tcp/ip协议配置，
   # 通过配置/proc/sys/net/ipv4/tcp_tw_resue, 默认为0，修改为1，释放TIME_WAIT端口给新连接使用
   sysctl -w net.ipv4.tcp_timestamps=1
   # 修改tcp/ip协议配置，快速回收socket资源，默认为0，修改为1
   sysctl -w net.ipv4.tcp_tw_recycle=1
   # 修改参数：
   $ vi /etc/sysctl.conf
   # -----意味着10000~65000端口可用
   net.ipv4.ip_local_port_range = 10000     65000     
   ```

   ##### 安装iptalbes会使用nf_conntrack模块跟踪连接

   跟踪的连接超过这个最大值，就会导致连接失败

   ```powershell
   # 查看最大值
   # wc -l /proc/net/nf_conntrack
   # cat /proc/sys/net/nf_conntrack_max
   1024000
   # vi /etc/sysctl.conf
   net.nf_conntrack_max = 2000500
   # or
   sysctl -w net.nf_conntrack_max=2000500
   ```

4. ### 进程 、线程 、纤程

