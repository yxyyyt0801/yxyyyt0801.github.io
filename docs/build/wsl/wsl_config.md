# 安装

WSL，即Windows Subsystem for Linux

```shell
# 安装
wsl --install -d Ubuntu-24.04

# 卸载版本
wsl --unregister Ubuntu-24.04

# 更新 WSL 内核
wsl --update

# 列出已安装的分发版
wsl --list --verbose

# 查看可用分发版
wsl --list --online

# 更新软件包列表并升级所有可升级的软件
sudo apt update && sudo apt upgrade -y
```



# 迁移

```shell
# 导出备份
wsl --export Ubuntu-24.04 "D:\data\wsl\Ubuntu-24.04.bak.tar"

# 清空旧目录，瞬间释放 C 盘空间
wsl --unregister Ubuntu-24.04

# 恢复备份
wsl --import Ubuntu-24.04 "D:\data\wsl\ubuntu" "D:\data\wsl\Ubuntu-24.04.bak.tar"  
```



# 换源

```shell
# 基于Ubuntu 24.04

# 备份
sudo cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bak

# 清空文件
sudo truncate -s 0 /etc/apt/sources.list.d/ubuntu.sources

# 写入阿里云镜像源
echo "Types: deb
URIs: https://mirrors.aliyun.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: https://mirrors.aliyun.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg" | sudo tee /etc/apt/sources.list.d/ubuntu.sources

# 更新索引
sudo apt update
```



# 基本操作

```shell
# 启动
wsl

# 退出
exit

# 关机
wsl --shutdown

# Windows 访问 Linux 文件
\\wsl$

# Linux 访问 Windows 文件
/mnt/c
```



# 安装软件

## Docker

```shell
# 安装 docker
curl -fsSL https://get.docker.com | sh

# 启动 docker
sudo service docker start

# 将当前用户加入 docker 组，避免每次输 sudo
sudo usermod -aG docker $USER

# 必须完全退出 Ubuntu 终端，然后重新打开
exit
```



## JDK

```shell
sudo apt install openjdk-8-jdk
```

