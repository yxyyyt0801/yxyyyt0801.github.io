# 创建共用网络

创建共用网络，容器内网络通讯互相访问

```shell
docker network create goldmine-net
```



# MySQL

```shell
# 下载镜像
docker pull mysql:5.7

# 启动
docker run -p 3306:3306 --name mysql --network goldmine-net \
-v ~/data/mysql/log:/var/log/mysql \
-v ~/data/mysql/data:/var/lib/mysql \
-v ~/data/mysql/conf:/etc/mysql/conf.d \
-e MYSQL_ROOT_PASSWORD=root  \
-d mysql:5.7

# 进入容器
docker exec -it mysql /bin/bash

# 打开客户端
mysql -uroot -proot --default-character-set=utf8
```



# Redis

```shell
# 下载镜像
docker pull redis:7

# 启动
docker run -p 6379:6379 --name redis \
--network goldmine-net \
-v ~/data/redis/data:/data \
-d redis:7 redis-server --appendonly yes

# 进入容器，使用redis-cli命令连接redis服务
docker exec -it redis redis-cli
```



# Nginx

```shell
# 下载镜像
docker pull nginx:1.22

# 首次运行，为了获得配置
docker run -p 80:80 --name nginx \
--network goldmine-net \
-v ~/data/nginx/html:/usr/share/nginx/html \
-v ~/data/nginx/logs:/var/log/nginx  \
-d nginx:1.22

# 拷贝nginx配置到容器
docker container cp nginx:/etc/nginx ~/data/nginx/

# 修改配置名称
cd ~/data/nginx
mv nginx conf

# 删除首次运行的容器
docker stop nginx
docker rm nginx

# 正式启动，映射配置
docker run -p 80:80 --name nginx \
-v ~/data/nginx/html:/usr/share/nginx/html \
-v ~/data/nginx/logs:/var/log/nginx  \
-v ~/data/nginx/conf:/etc/nginx \
-d nginx:1.22
```



# RabbitMQ

```shell
# 下载镜像
docker pull rabbitmq:3.9-management

# 启动
docker run -p 5672:5672 -p 15672:15672 --name rabbitmq \
--network goldmine-net \
-v ~/data/rabbitmq/data:/var/lib/rabbitmq \
-d rabbitmq:3.9-management

# 开启虚拟机防火墙，使得主机可以访问 http://192.168.8.100:15672（guest/guest）
firewall-cmd --zone=public --add-port=15672/tcp --permanent
firewall-cmd --reload
```



# ELK

## Elasticsearch

```shell
# 下载镜像
docker pull elasticsearch:7.17.3

# 修改虚拟机内存大小
sysctl -w vm.max_map_count=262144

# 修改数据目录权限
mkdir -p ~/data/elasticsearch/data
chmod 777 ~/data/elasticsearch/data/

# 启动
docker run -p 9200:9200 -p 9300:9300 --name elasticsearch \
--network goldmine-net \
-e "discovery.type=single-node" \
-e "cluster.name=elasticsearch" \
-e "ES_JAVA_OPTS=-Xms512m -Xmx1024m" \
-v ~/data/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v ~/data/elasticsearch/data:/usr/share/elasticsearch/data \
-d elasticsearch:7.17.3

# 安装中文分词器IKAnalyzer，注意下载与Elasticsearch对应的版本，下载地址 https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.17.3
# 下载完成后解压到Elasticsearch的~/data/elasticsearch/plugins目录下
mkdir -p ~/software/elasticsearch/elasticsearch-analysis-ik-7.17.3
scp D:\download\elasticsearch-analysis-ik-7.17.3.zip root@192.168.8.100:~/software/elasticsearch/
unzip elasticsearch-analysis-ik-7.17.3.zip -d elasticsearch-analysis-ik-7.17.3
mv elasticsearch-analysis-ik-7.17.3 ~/data/elasticsearch/plugins/

# 重启服务
docker restart elasticsearch

# 开启虚拟机防火墙，使得主机可以访问 http://192.168.8.100:9200
firewall-cmd --zone=public --add-port=9200/tcp --permanent
firewall-cmd --reload
```



## Logstash

```shell
# 下载镜像
docker pull logstash:7.17.3

# 创建目录和文件logstash.conf
mkdir ~/data/logstash
touch logstash.conf

# 拷贝内容到logstash.conf
input {
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 4560
    codec => json_lines
    type => "debug"
  }
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 4561
    codec => json_lines
    type => "error"
  }
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 4562
    codec => json_lines
    type => "business"
  }
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 4563
    codec => json_lines
    type => "record"
  }
}
filter{
  if [type] == "record" {
    mutate {
      remove_field => "port"
      remove_field => "host"
      remove_field => "@version"
    }
    json {
      source => "message"
      remove_field => ["message"]
    }
  }
}
output {
  elasticsearch {
    hosts => "es:9200"
    index => "xmall-%{type}-%{+YYYY.MM.dd}"
  }
}

# 启动
# --link 连接容器elasticsearch，可以互相通信
docker run --name logstash -p 4560:4560 -p 4561:4561 -p 4562:4562 -p 4563:4563 \
--network goldmine-net \
--link elasticsearch:es \
-v ~/data/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf \
-d logstash:7.17.3

# 进入容器
docker exec -it logstash /bin/bash

# 在容器内安装json_lines插件
logstash-plugin install logstash-codec-json_lines
```



## Kibana

```shell
# 下载镜像
docker pull kibana:7.17.3

# 启动
docker run --name kibana -p 5601:5601 \
--network goldmine-net \
--link elasticsearch:es \
-e "elasticsearch.hosts=http://es:9200" \
-d kibana:7.17.3

# 开启虚拟机防火墙，使得主机可以访问 http://192.168.8.100:5601
firewall-cmd --zone=public --add-port=5601/tcp --permanent
firewall-cmd --reload
```



# MongoDB

```shell
# 下载镜像
docker pull mongo:4

# 启动
docker run -p 27017:27017 --name mongo \
--network goldmine-net \
-v ~/data/mongo/db:/data/db \
-d mongo:4
```



# MinIO

```shell
# 下载镜像
docker pull minio/minio

# 启动
docker run -p 9090:9000 -p 9001:9001 --name minio \
--network goldmine-net \
-v ~/data/minio/data:/data \
-e MINIO_ROOT_USER=minioadmin \
-e MINIO_ROOT_PASSWORD=minioadmin \
-d minio/minio server /data --console-address ":9001"

# 主机可以访问 http://192.168.8.100:9090
```



# Nexus

```shell
# 下载镜像
docker pull sonatype/nexus3

# 创建目录
mkdir -p ~/data/nexus/data
chmod 777 -R ~/data/nexus/data

# 启动
docker run -d --name nexus3 -p 8081:8081 --restart always \
--network goldmine-net \
-v ~/data/nexus/data:/nexus-data \
sonatype/nexus3

# 查看日志，出现 "Started Sonatype Nexus OSS 3.64.0-04" 启动成功；主机可以访问 http://192.168.8.100:8081
docker logs -f nexus3

# 首次登录查看并在管理界面修改密码（admin/密文）
cat ~/data/nexus/data/admin.password
```



# Nacos

## 创建数据库

```mysql
-- 创建数据库（必须用utf8mb4编码）
CREATE DATABASE nacos_config CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 下载Nacos官方SQL文件（注意必须版本一致，否则数据库结构不一致会导致系统错误）
wget https://github.com/alibaba/nacos/blob/2.0.3/distribution/conf/nacos-mysql.sql

-- 导入到MySQL
mysql -u root -proot nacos_config < mysql-schema.sql
```



## 创建docker镜像

- 注意 `MYSQL_SERVICE_HOST=mysql` 如果mysql也是一个镜像容器的话，要配置在一个网络中 `docker network create goldmine-net`，同时两个容器都要声明 `--network goldmine-net` 加入到同一个 network 中，否则无法互相访问

```shell
# 下载镜像
docker pull nacos/nacos-server:2.0.3

# 启动
#   -e MYSQL_SERVICE_HOST=mysql 使用容器名
docker run -d \
  --name nacos-standalone \
  --network goldmine-net \
  -p 8848:8848 \
  -p 9848:9848 \
  --restart=always \
  -e MODE=standalone \
  -e SPRING_DATASOURCE_PLATFORM=mysql \
  -e MYSQL_SERVICE_HOST=mysql \
  -e MYSQL_SERVICE_PORT=3306 \
  -e MYSQL_SERVICE_USER=root \
  -e MYSQL_SERVICE_PASSWORD=root \
  -e MYSQL_SERVICE_DB_NAME=nacos_config \
  -e MYSQL_SERVICE_DB_PARAM="characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&allowPublicKeyRetrieval=true" \
  -e JVM_XMS=512m \
  -e JVM_XMX=512m \
  -v ~/data/nacos/logs:/home/nacos/logs \
  nacos/nacos-server:2.0.3
  
# 访问
http://localhost:8848/nacos（ nacos / nacos ）
```

