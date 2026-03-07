# 前言

## 测试

### 测试类型

- 单元测试
  - Mock
  - API 测试
  - 目标：测试覆盖
    - 模块覆盖
    - 类覆盖
    - 方法覆盖
    - 行覆盖
- 性能测试
- 集成测试
  - 嵌入式测试（脱离环境，单元隔离，单元部署）
    - Spring jdbc：@EnableEmbeddedDatabase
    - Spring redis：@EnableEmbeddedRedisServer
    - Spring kafka：@EmbeddedKafka
  

### 单元测试

- 进入一家公司接旧项目，通过单元测试熟悉系统功能



## Java 企业应用

- 基础设施
  - spring
  - Java EE container（Tomcat、Jetty）
  - MQ
  - Cache（Redis）
  - RPC（Dubbo、OpenFeign、gRPC）
  - Search（ES）
  - Big Data（Hive、HBase）
  - Task、Job、Scheduler
- 业务共享模块
  - 部署
    - 近端（客户端依赖）
    - 远端（云端API调用）
  - 分类
    - 交易
    - 会员
    - 物流
    - 风控
- 业务应用
  - 聚焦某个业务领域



# 基础框架与应用工程搭建

## 基础框架工程构建

**Maven构建**

- 依赖设计

  - 能不依赖尽量不要依赖

  - `<optional>true</optional>`  服务提供方**整合**多个功能，为了编译通过；减少传递依赖；（一般用于共享模块，一个接口，多个实现，底层不同的依赖可以用optional修饰；以插件形式提供）
    - `<scope>provided</scope>` 类似compile，但不会传递依赖，由外部容器提供API并由外部容器驱动

- 依赖冲突

  - 依赖仲裁，按路径短的原则
    - 不同jar，按依赖路径
    - 同一个jar，按pom中出现的顺序

- 依赖管理

  - 统一在BOM中管理版本 DependencyManagement（也是pom文件）
    - 了解不同版本的兼容情况
  - 基础或共享依赖模块，尽量向下兼容低版本的第三方Artifact
  - 基础或共享依赖模块，要兼容可选依赖Artifact
    - 实现层面动态判断（ClassLoading尽量避免出现在对象级别）
    - 减少StackTrace大小，ZGC
  - 基础或共享依赖模块，尽可能依赖出现概率大的第三方Artifact
  - 依赖某个或少数API时，尽可能**复制**这些API实现，而非依赖整个第三方Artifact（哈哈，和我原来想的不一样）；避免依赖过多！
    - dubbo复制netty
    - spring复制asm
    - shard插件
  
- 基础框架版本策略

  - 大多数企业选择snapshot模式（**同名但不唯一，每日更新，方便通知，且频繁更新修复问题**）
    - 优势：框架、基础设施
    - 风险：风险大，应用部署多版本，万一某个版本不稳定，回滚很复杂
    - 应用：安全包

  - 少数企业选择release模式（**唯一且不可变**）
    - 优势：容器化
    - 风险：风险小，影响相对较小，**个人推荐**
    - 缺点：更新升级不灵活
    - 应用：中间件

- 三方库升级策略

  **优雅升级**三方库，springboot、springcloud

  - 基础设施 ，采用不同的`<profile></profile>` 回归测试，通过 `mvn clean verify -P regression` 激活测试用例
  - 应用，需要QA介入回归
  - 依赖参考
    - spring cloud
      - spring boot dependencies
        - spring cloud build dependencies

      - spring cloud build -> spring cloud build dependencies
        - spring cloud consul

    - 建议
      - dependencies 对外 聚合（如 spring boot 和 spring cloud）
      - parent 对内 继承（如 spring cloud consul 和 spring cloud build）

- 基础框架发布流程

  - 依赖于 CICD 环境
    - gitlab + jenkins
    - github workflows



## 业务工程模版定制

**案例（samples）**

- 基础设施
- 业务组件



**方法论**

- DDD
- TDD
- BDD



**标准化项目结构**

- API（API工程，定义API接口）
  - 接口（interfaces）
    - RPC通讯
      - Spring cloud OpenFeign 接口
        - spring-webmvc 可选依赖
        - spring-cloud-starter-openfeign 可选依赖
        - @FeignClient("${user-serice.name}")  使用占位符方式，易于后续服务拆分
          - **拆分服务接口升级**
            - 服务方：老接口 @Deprecated，当没有流量时，即可下线
            - 服务方：新接口，和老接口并存，同时保证url兼容
            - 调用方：1、新老接口并存；2、逐步下线老接口服务；3、切换新接口
      - Apache Dubbo RPC 接口
    - 业务接口
  - 模型（model）
    - RPC 通讯模型
    - 业务模型
    - 框架模型
  - 常量（constants）
  - 枚举（enums）
  - 注解（annotation）
- data（数据存储工程）
- core（biz）
- web（Web主项目工程）
  - 引导类 - main class
  - 构建 - Spring Boot  Maven Plugin



**业务工程脚手架**

- Spring 技术栈
  - https://start.spring.io
  - https://start.aliyun.com
- Java EE 体系
  - https://code.quarkus.io



# RESTAPI设计



# 站点国际化设计



# Web服务器容错性设计



# 服务容错性高阶设计



# 服务柔性负载均衡设计



# 服务监控平台设计



# 服务链路追踪设计



# 服务网关整合设计



# 服务性能优化



# 工程脚手架定制







