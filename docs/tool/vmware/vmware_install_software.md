# 系统软件

## ifconfig

```shell
# ifconfig、netstat命令
sudo yum -y install net-tools
```



## ntpdate

```shell
# 时间同步
sudo yum -y install ntpdate
```



## wget

```shell
# wget
sudo yum -y install wget
```



## git

```shell
# git
sudo yum -y install git
```



## zip

```shell
yum install -y unzip zip
```



## SDKMAN

```shell
# 安装依赖
sudo yum install -y curl zip unzip git

# 安装sdkman
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# 初始化
echo 'export SDKMAN_DIR="$HOME/.sdkman"' >> ~/.bashrc
echo '[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"' >> ~/.bashrc

# 查看
sdk list java
 
# 安装 java 21
sdk install java 21.0.12.1-crac+1-zulu

# 仅当前终端（退出失效，调试用）
sdk use java 21.0.12.1-crac+1-zulu

# 设为用户全局默认（新终端生效）
sdk default java 21.0.12.1-crac+1-zulu

# 查看当前
sdk current java
```



# 开发软件

## Docker

```shell
# yum-utils
yum install -y yum-utils device-mapper-persistent-data lvm2

# 为yum源添加docker仓库位置
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 安装docker
yum install docker-ce

# 启动docker
systemctl start docker

# 验证
docker info

# 安装上传下载软件，执行docker cp指令
# 将test.sql文件拷贝到mysql容器的/目录下 
# docker cp /root/data/test/test.sql mysql:/
yum -y install lrzsz
```

