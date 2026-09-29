- 镜像启动失败

  ```shell
  # 执行命令获取错误日志（LogPath）分析
  docker container inspect mysql
  ```

- 解决macOS无法访问docker容器服务的问题
  
  ```shell
  # MacOS 12.7.6 使用 Docker 版本 4.41.2
  https://desktop.docker.com/mac/main/amd64/191736/Docker.dmg
  ```
  
- 无法下载镜像，Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)；

  - Linux
  
    ```shell
    # 超时异常
    curl -v https://registry-1.docker.io/v2/
    
    # 配置加速源
    mkdir -p /etc/docker
    vi /etc/docker/daemon.json
    {
    	"registry-mirrors":[
    		"https://docker.m.daocloud.io",
    		"https://iicvml23.mirror.aliyuncs.com"
    	]
    }
    
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    # 检查配置
    docker info
    ```
  
  - macOS
  
    ```shell
    # Settings > Docker Engine > 增加
    
    	"registry-mirrors":[
    		"https://docker.m.daocloud.io",
    		"https://iicvml23.mirror.aliyuncs.com"
    	]
    ```
  
    