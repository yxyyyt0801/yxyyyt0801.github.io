- VMware Workstation 无法恢复错误: (vcpu-0) Exception 0xc0000005 (access violation) has occurred.

  ```shell
  # VMware Workstation的虚拟化与window10自带的Hyper-V服务发生冲突
  # 管理员运行Terminal执行命令后，重启系统
  bcdedit /set hypervisorlaunchtype off
  ```

- SSH远程连接虚拟机，发生错误 “Add correct host key in C:\\Users\\Rain/.ssh/known_hosts to get rid of this message”

  ```shell
  ssh-keygen -R 192.168.8.100
  ```

- Cannot find a valid baseurl for repo: base/7/x86_64

  ``` shell
  # 保证网络连接可用
  ping www.baidu.com -c 3
  
  # 更换镜像源
  vi /etc/yum.repos.d/CentOS-Base.repo
  # 注释 4 个 mirrorlist，取消注释 更换 baseurl
  baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
  baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
  baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
  baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
  
  # 修改 DNS
  vi /etc/resolv.conf
  8.8.8.8
  8.8.4.4
  ```

  

