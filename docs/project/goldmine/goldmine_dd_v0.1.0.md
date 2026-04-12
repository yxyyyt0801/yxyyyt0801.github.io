# 需求分析

## 用户中心

### 账户

### 角色

### 组织

### 权限



## 商品中心

### 商品

商品发布

- 



### 订单



# 项目模块

- common 公共定义
- service 公共服务，第三方服务封装
- domain 领域服务
  - mapper
    - XxxMapper
    - query
    - entity
    - XxxMapper.xml
  - api 系统内服务接口(本地调用、RPC调用)
    - XxxServcie
    - request
    - dto
  - service 服务实现
    - XxxServcieImpl
  - web 系统外服务接口
    - query
    - vo