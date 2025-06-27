# 安装

## MAC 升级NodeJS

```shell
# 清除nodejs的cache
sudo npm cache clean -f

# 使用npm安装n模块
sudo npm install -g n

# node所有版本
npm view node versions

# 升级到最新版本
# sudo n stable 升级到稳定版本
# sudo n xx.xx  升级到具体版本号
sudo n latest 

# 查看版本
node -v 

# 升级、降级版本
sudo n 16.20.2
```



## Windows 安装NodeJS

下载网站 https://nodejs.org/zh-cn/download

安装 / 卸载 node-v16.20.2-x64.msi



## 配置镜像源

```shell
# 原镜像
# npm config set registry https://registry.npmjs.org/

# 永久修改
# 设置淘宝镜像
npm config set registry https://registry.npmmirror.com

npm config get registry
```
