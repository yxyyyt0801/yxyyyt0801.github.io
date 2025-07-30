# Windows 安装

## 安装 VMware

- 版本：VMware-workstation-full-16.2.1-18811642
- 序列号：ZF3R0-FHED2-M80TY-8QYGC-NPKYF
- 编辑 | 首选项
  - 工作区：虚拟机的默认位置
  - 内存：2048MB

- VMware Fusion 11 注册码 `7HYY8-Z8WWY-F1MAN-ECKNY-LUXYX` 
- VMware Fusion 12 注册码 `ZF3R0-FHED2-M80TY-8QYGC-NPKYF`



## 网络连接设置

- 安装完成后
  - **windows：查看网络连接**会新建VMnet1和VMnet8两个网络连接

- 编辑 | 虚拟网络编辑器 | 更改VMnet8设置
  - 子网IP：192.168.8.0
  - 子网掩码：255.255.255.0
  - NAT设置
    - 网关IP：192.168.8.2
  - DHCP设置
    - 起始：192.168.8.3
    - 结束：192.168.8.255



## 新建虚拟机

- 文件 | 新建虚拟机

  - 自定义（高级）

  - 稍后安装操作系统

  - Linux：CentOS 7 64 位
  - 虚拟机名称：dev

  - 处理器：2P2C
  - 内存：2048MB

  - 网络类型：NAT

  - 最大磁盘大小：40GB；将虚拟磁盘存储为单个文件



## 安装 CentOS

- 版本：CentOS-7-x86_64-DVD-1810
- 选择指定虚拟机 | 设置 
  - CD / DVD 使用ISO映像文件，选择：CentOS-7-x86_64-DVD-1810.iso
- 开启此虚拟机
  - install Centos 7
- 按提示向导安装
  - language：english
  - date & time：Asia/shanghai
  - Language support：添加中文，简体中文
  - software selection：Minimal install
  - installation destination：automatic configure  partitioning
  - network & hostname：ens33 | <font color=red>on</font>；Host name：dev
- 开始安装
  - root password：root（太短，双击确认即可）
- 重启



# Mac 安装

- 安装VMware Fusion 12

- 新建虚拟机，自定义，不指定镜像

- 设置虚拟机参数，处理器、内存、磁盘、CD/DVD（选择镜像 & 务必选择 连接CD/DVD 驱动器）

- 启动

- install Centos 7，按提示向导安装

- 重启

- 网络设置

  - VMware｜偏好设置｜网络

    - 新建网络连接 vmnet3
    - 允许该网络上的虚拟机连接到外部网络（使用 NAT）
    - 将 mac 主机连接到该网络（12 没有此项）
    - 子网 IP：192.168.8.0

  - 检查VMware 配置

    ```shell
    cd /Library/Preferences/VMware Fusion
    vim /Library/Preferences/VMware Fusion/networking
    # 子网 IP
    # VNET_3_HOSTONLY_SUBNET 192.168.8.0
    
    cd /Library/Preferences/VMware Fusion/vmnet3
    vi nat.conf
    # NAT gateway address
    ip = 192.168.8.1
    netmask = 255.255.255.0
    ```

  - 修改虚拟机网络适配器：虚拟机设置｜网络适配器

    - 选择vmnet3

  - 修改centos网卡配置

    ```shell
    vi /etc/sysconfig/network-scripts/ifcfg-ens33
    
    BOOTPROTO=static
    ONBOOT=yes	# 重要，重启网卡配置生效
    IPADDR=192.168.8.100
    NETMASK=255.255.255.0
    GATEWAY=192.168.8.1
    DNS1=114.114.114.114
    ```
    
  - 重启网络服务 service network restart
  
  - 修改虚拟机网络适配器：虚拟机设置
  
    - CD/DVD 取消连接CD/DVD
    - 启动磁盘：硬盘
