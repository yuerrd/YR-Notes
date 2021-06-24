### 安装命令

#### java

官网地址：https://www.oracle.com/technetwork/java/javase/downloads/index.html、

华为开源镜像站：https://repo.huaweicloud.com/java/jdk/

1. 下载jdk

   ```shell
   wget https://repo.huaweicloud.com/java/jdk/8u151-b12/jdk-8u151-linux-x64.tar.gz
   ```

2. 安装

   ```
   tar -zxvf jdk-8u151-linux-x64.tar.gz
   ```

3. 环境变量配置

   ```shell
   echo 'export JAVA_HOME=/opt/jdk1.8.0_151' >> /etc/profile
   echo 'export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar' >> /etc/profile
   echo 'export PATH=$PATH:${JAVA_HOME}/bin' >> /etc/profile
   source /etc/profile
   ```

#### maven

官方地址：http://maven.apache.org/download.cgi

1. 下载gz包

   ```
   wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
   ```

2. 解压

   ```
   tar -zxvf apache-maven-3.6.3-bin.tar.gz
   ```

3. 配置环境变量

   ```
   echo 'export M2_HOME=/opt/apache-maven-3.6.3' >> /etc/profile
   echo 'export PATH=$PATH:${M2_HOME}/bin' >> /etc/profile
   source /etc/profile
   ```

   