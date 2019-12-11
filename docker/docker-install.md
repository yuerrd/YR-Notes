### Docker install

------

1. **安装依赖组件**

   ```shell
   sudo yum install -y yum-utils \ device-mapper-persistent-data \ lvm2
   ```

2. **设置稳定的仓库**

   ```shell
   sudo yum-config-manager \ --add-repo \ https://download.docker.com/linux/centos/docker-ce.repo
   ```

3. **修改阿里云镜像地址**

   ```shell
   sudo tee /etc/yum.repos.d/docker-ce.repo <<-'EOF'
   [docker-ce-stable]
   name=Docker CE Stable - $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/stable
   enabled=1
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-stable-debuginfo]
   name=Docker CE Stable - Debuginfo $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/stable
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-stable-source]
   name=Docker CE Stable - Sources
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/stable
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-edge]
   name=Docker CE Edge - $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/edge
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-edge-debuginfo]
   name=Docker CE Edge - Debuginfo $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/edge
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-edge-source]
   name=Docker CE Edge - Sources
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/edge
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-test]
   name=Docker CE Test - $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/test
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-test-debuginfo]
   name=Docker CE Test - Debuginfo $basearch
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/test
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   [docker-ce-test-source]
   name=Docker CE Test - Sources
   baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/test
   enabled=0
   gpgcheck=1
   gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
   EOF
   ```

4. **安装docker-ce**

   ```shell
   sudo yum makecache ; sudo yum install -y docker-ce
   ```

5. **启动docker服务**

   ```shell
   sudo systemctl start docker
   ```

6. **设置开机自启**

    ```shell
   sudo systemctl enable docker
    ```