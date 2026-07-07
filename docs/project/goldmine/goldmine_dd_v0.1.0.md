# 需求分析

## IAM

Identity and Access Management（身份与访问管理）

### 模型

#### 公共

User（用户）

- 自然人基础身份信息（姓名、手机号、状态）
- 买家、商户、平台

Account（账户）

- 存储登录凭证（用户名/密码、OpenID）
- 一个 User 包含多个 Account（PC密码登录、App扫码、微信UnionID）



#### 平台

Platform_Org（部门）

Platform_Position（岗位）

platform_admin（平台管理员）

- 一个管理员属于一个User
- 无 Tenant 限制

platform_admin_Org_Position（任职）

Platform_Permission（平台权限）

- 父子关系
- 菜单、按钮

platform_role（平台角色）

platform_Role_Permission（角色权限映射）

platform_admin_Role（平台管理员角色映射）

- 内置角色
  - PLAT_SUPER_ADMIN 平台超管（全权限）
  - PLAT_OPS 运维（系统配置）
  - PLAT_CS 客服（商家/买家协助）
  - PLAT_AUDIT 审计（只读 + 日志）

Platform_Position_Default_Role（岗位默认角色）



#### 租户

Tenant（商户）

Tenant_Shop（店铺）

- ==业务经营单元（算钱、算库存）==
- 一个 Tenant 包含多个Shop

Tenant_Department（部门）

- ==行政管理单元，管人、管汇报==
- 父子关系
- 一个 Tenant 拥有一个顶层根部门（Root Department），其下可延伸多级子部门

Tenant_Position（岗位）

- ==HR 视角：编制、职级==
- 一个 Tenant 包含多个 Position，按 Tenant 隔离
- level 职级（用于汇报/审批）、category（岗位分类：M/P/S）、data_scope（全部、本部门、本人）

Tenant_Staff（员工）

- 不直接归属部门，通过 Tenant_Staff_Department_Position 任职表关联部门与岗位，按 Tenant 隔离
- 一个Staff属于一个User

Tenant_Staff_Department_Position（任职）

- 多对多
- 一人多部门是常态（矩阵型组织）

Tenant_Permission（权限）

Tenant_Role（角色）

Tenant_Role_Permission（角色权限映射）

Tenant_Staff_Role（员工角色映射）

Tenant_Staff_Shop（员工店铺映射）

Tenant_Position_Default_Role（岗位默认角色）

- 岗位和角色弱关联



#### 买家

Buyer（买家档案表）

- 一个Buyer属于一个User

Buyer_Tenant（商家会员映射）

- 会员等级，成长体系

Buyer_Role（买家角色表）

- 角色用于业务规则（如折扣、限购、黑白名单）而非系统菜单控制。系统菜单权限仅存在于 Platform 和 Tenant 体系中
- 角色：NORMAL / VIP（折扣、限购数量、专属商品） / BLACKLIST（禁止下单、禁止登录） / INTERNAL_TEST（隐藏价格、查看成本价）

Buyer_Buyer_Role（买家-角色关系）





# 项目模块

- goldmine-framework （root，版本聚合，插件定义）
  - goldmine-framework-parent （parent，构建定义）
    - goldmine-framework-common（公共定义）
    - goldmine-framework-data（数据仓库）
    - goldmine-framework-mvc（WEB）
  - goldmine-framework-dependencies（bom，外部引用）
- IAM
  - goldmine-framework-parent
    - goldmine-framework-data
    - goldmine-framework-api（服务依赖）
    - goldmine-framework-core（服务实现）
    - goldmine-framework-web（外部调用）
  - goldmine-framework-dependencies
- 
- domain 领域服务
  - mapper
    - XxxMapper
    - query
    - entity
    - XxxMapper.xml
  - api 系统内服务接口（本地调用、RPC调用）
    - XxxServcie
    - request
    - dto
  - service 服务实现
    - XxxServcieImpl
  - web 系统外服务接口
    - form
    - vo