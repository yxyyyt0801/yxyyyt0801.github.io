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
    - 行覆盖 （90%以上）
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



## CICD

**CI（持续集成）**：频繁地将代码合并到主干，并自动进行构建和测试，尽早发现集成错误。

**CD（持续部署/交付）**：自动将通过测试的代码部署到测试或生产环境。

### 主流工具

- 云端托管型（SaaS）

  适合大多数团队，无需维护服务器，开箱即用。

  - **GitHub Actions**：GitHub 原生集成，配置简单，社区生态丰富。

  - **GitLab CI/CD**：GitLab 原生集成，功能强大，适合私有化部署。

  - **Jenkins Cloud**：Jenkins 的云端版本，结合了传统 Jenkins 的灵活性和云端的便利性。

  - **CircleCI**：专注于速度和易用性，支持复杂的并行构建。

  - **Travis CI**：老牌 CI 服务，对开源项目友好。

- 自托管/本地部署型

  适合对数据安全、网络隔离有高要求的企业。

  - **Jenkins**：业界常青树，插件生态极其丰富，高度可定制。
  - **GitLab Runner**：配合 GitLab 使用，支持私有化部署。
  - **Drone CI**：基于容器，轻量级且配置简单。
  - **Tekton**：Kubernetes 原生的 CI/CD 框架，云原生首选。

- 现代云原生工具

  专为容器和微服务架构设计。

  - **Argo CD**：基于 GitOps 的持续部署工具，声明式配置。
  - **Flux CD**：另一款流行的 GitOps 工具。
  - **Spinnaker**：Netflix 开源，专注于多云部署。



### 如何选择

**个人/初创团队**：首选 **GitHub Actions** 或 **GitLab CI/CD**，成本低，上手快。

**传统企业/复杂环境**：**Jenkins** 依然是可靠的选择，插件多，能应对各种定制化需求。

**云原生/K8s 环境**：**Argo CD** + **Tekton** 或 **GitLab** 是黄金组合。



# 基础框架与应用工程搭建

## 基础框架工程构建

### 依赖设计

- 能不依赖尽量不要依赖

- `<optional>true</optional>`  服务提供方**整合**多个功能，为了编译通过；减少传递依赖；（一般用于共享模块，一个接口，多个实现，底层不同的依赖可以用optional修饰；以插件形式提供）
  - `<scope>provided</scope>` 类似compile，但不会传递依赖，由外部容器提供API并由外部容器驱动



### 依赖冲突

- 依赖仲裁，按路径短的原则
  - 不同jar，按依赖路径
  - 同一个jar，按pom中出现的顺序



### 依赖管理

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



### 基础框架版本策略

- 大多数企业选择snapshot模式（**同名但不唯一，每日更新，方便通知，且频繁更新修复问题**）
  - 优势：框架、基础设施
  - 风险：风险大，应用部署多版本，万一某个版本不稳定，回滚很复杂
  - 应用：安全包

- 少数企业选择release模式（**唯一且不可变**）
  - 优势：容器化
  - 风险：风险小，影响相对较小，**个人推荐**
  - 缺点：更新升级不灵活
  - 应用：中间件



### 三方库升级策略

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



### 基础框架发布流程

- 依赖于 CICD 环境
  - gitlab + jenkins
  - github workflows



## 业务工程模版定制

### 案例（samples）

- 基础设施
- 业务组件



### 业务组件 BOM 设计

- 命名 xxx-dependencies
- 继承或组合基础设施的 BOM
  - 基础设施依赖管理
    - 继承或组合开源BOM
- 主要聚焦于业务组件依赖
  - 大量业务组件API
- 缺点
  - 版本升级缺乏灵活度，臃肿
  - 通常采用 **snapshot 模式** 升级
    - 业务 BOM 保持 snapshot 模式，业务组件依赖使用 release 模式（推荐？）
    - 业务 BOM、业务组件 保持 snapshot 模式



### 方法论

- DDD

  - （Domain-Driven Design，领域驱动设计）将软件系统的核心逻辑与业务领域（Domain）紧密结合，通过建立统一的业务语言来指导设计和开发。

- TDD

  - （Test-Driven Development，测试驱动开发）先写测试，再写代码，然后重构，遵循严格的“红灯-绿灯-重构”循环。
    - 红灯肯定不通过、绿灯不管实现尽快通过、重构最小化实现业务需求

- BDD

  - （Behavior-Driven Development，行为驱动开发）是一种**敏捷软件开发方法**，它强调**从用户行为视角**来定义需求、编写测试和开发代码。

    简单来说，它把TDD（测试驱动开发）的“技术语言”升级成了**“业务语言”**，让开发人员、测试人员和产品经理能用同一套“剧本”沟通。




### 标准化项目结构

- API（API工程，定义API接口）
  - 接口（interfaces）
    - RPC通讯
      - Spring cloud OpenFeign 接口
        - spring-webmvc **可选依赖**
        - Lombok **可选依赖**
        - spring-cloud-starter-openfeign **可选依赖**
        - @FeignClient("${user-serice.name}")  使用占位符方式，易于后续服务拆分
          - **拆分服务接口升级**
            - 服务方：老接口 @Deprecated，当没有流量时，即可下线
            - 服务方：新接口，和老接口并存，同时保证url兼容
            - 调用方：1、新老接口并存；2、逐步下线老接口服务；3、切换新接口
      - Apache Dubbo RPC 接口
        - 依赖 dubbo-common
        - @DubboService
    - 业务接口
      - **通过元注解支持，注意依赖注解，而非API**（可编译通过，运行时可以没有，没有的话只会不识别！）
  - 模型（model）
    - RPC 通讯模型
    - 业务模型
    - 框架模型
  - 常量（constants）
  - 枚举（enums）
  - 注解（annotation）
  
- data（数据存储工程）

  - 类型
    - SQL
      - JDBC
        - Spring jdbc
        - mybatis
        - jpa

    - NoSQL
      - redis
      - mongo
      - es

  - 技术栈
    - Spring data

  - 目录
    - repository（接口）
      - extends CrudRepository（实现透明化，sql、nosql）Spring-jpa

- core

  - 技术栈
    - spring-boot-starter

- web（Web主项目工程）
  - 目录结构
    - XxxApplication
    - controller（实现 API 接口）
    - exception
  - 引导类 - main class
  - 构建 - Spring Boot  Maven Plugin（fat jar）
    - spirng-boot-maven-plugin
  - 技术栈
    - spirng-boot-starter-web

### 重构

  - api <- data <- core <- web

  - api 部署到**公共仓库**用于外部引用，其他是内部实现

    - web、data、core 模块 跳过部署

      - maven-deploy-plugin

        ```xml
        <configuration>
            <skip>true</skip>
        </configuration>
        ```

  - 统一版本管理

    - 创建 dependencies 模块，对外的bom，只导入 api 模块

      ```xml
      <dependencyManangement>
      	<!-- 导入api模块 -->
          <!-- 使用 dependencies 模块版本 -->
          <version>${verison}</version>
      </dependencyManangement>
      ```

    - 内部模块，parent模块定义；其他模块替换revision

      ```xml
      <properties>
      	<revision>...</revision>>
      </properties>
      
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>flatten-maven-plugin</artifactId>
        <version>${maven_flatten_version}</version>
        <configuration>
          <updatePomFile>true</updatePomFile>
          <flattenMode>oss</flattenMode>
        </configuration>
        <executions>
          <execution>
            <id>flatten</id>
            <goals>
              <goal>flatten</goal>
            </goals>
            <phase>process-resources</phase>
          </execution>
          <execution>
            <id>flatten.clean</id>
            <goals>
              <goal>clean</goal>
            </goals>
            <phase>clean</phase>
          </execution>
        </executions>
      </plugin>
      ```

  - 如何查看springboot和Springcloud对应版本兼容性

    - spring-io/start.spring.io -> start-site -> resources/application.yml

  - 通过Spring boot 插件打 fat jar

    - spring-boot-maven-plugins 添加 repackage

  - 通过Spring boot 插件排除间接依赖

    - spring-boot-maven-plugins 添加 exclude，**打包**的时候删除




### 业务工程脚手架

- Spring 技术栈
  - https://start.spring.io
  - https://start.aliyun.com
- Java EE 体系
  - https://code.quarkus.io



# RESTAPI设计

## REST API 服务端设计

### 服务端API模型设计

- 定义统一的REST请求和响应API模型；
- 媒体格式（内容）：JSON

- API请求模型设计

  - 模型对象 T

  - API请求模型：ApiRequest、Request、RequestVO、T 等


- API响应模型设计
  - 模型对象 T，code，message


- API业务代码设计
  - 模拟 HTTP status code（参考 HttpStatus）
    - int code（int 必须有值）
    - String message（支持国际化）




### 服务端API校验设计

依赖：validation-api、spring-webmvc

- Spring WebMVC 校验
  - 推荐使用 Bean Validation 扩展
    - Bean Validation 是一个 Java 标准规范，用于通过注解对数据模型（Bean）进行验证。它的实现最常见的是 Hibernate Validator。
  - 不推荐使用 Spring Web MVC 自定义扩展，和Spring mvc 强耦合
    - 扩展 HandlerMethodArgumentResolver 
    - BindingResult 
    - @CurrentUser



### 服务端API异常处理

推荐使用：@RestControllerAdvice 全局处理异常

RequestResponseBodyMethodProcessor ： 正常 @RestController 处理方法参数和方法返回对象



**Spring Web MVC 核心流程**

1. DispatcherServlet 处理 HTTP 请求（符合 Servlet Mapping 规范）
2. 通过 HTTP 请求（匹配条件）寻找 Handler
   1. 逻辑实现：通过多个 HandlerMapping（根据优先级排序）去寻找最匹配的 HandlerExecutionChain 对象
   2. HandlerExecutionChain：由一个 Handler（主要 HanderMethod） + N 个 HanderInterceptor
      1. 匹配条件：请求 URI、请求头，请求参数等
      2. Handler： 最常见的对象为 HandlerMethod
      3. HandlerMethod：最常见的场景是 @Controller  或 @RestController Bean 定义的 @RequestMapping 方法
      4. HandlerInterceptor：Handler 拦截器
3. 通过 Handler 找到合适的 HandlerAdapter 对象
   1. 最常见的场景：@RequestMapping 场景，Handler -> HanderMethod
4. 执行 HandlerAdapter 方法，将 Handler 作为参数对象，执行结果适配 ModelAndView
   1. 方法执行：执行业务逻辑，返回 OOP 对象（可能是：@Controller 中模板路径地址，或者 @RestController POJO 对象）
      1. 如果是  @RestController 处理的话，ModelAndView 的 View 不会被渲染，在处理过程中就已经被写入到了 HTTP Response
      2. 如果是 @Controller 的话，会执行 View 渲染
5. 将 ModelAndView 转化成 HTTP Response Message



### 服务端API POJO 通讯

实现接口HandlerMethodReturnValueHandler包装返回POPO，统一适配响应结果，隐形包装

然后通过配置类，添加到RequestMappingHandlerAdapter（注意放到HandlerMethodReturnValueHandler列表的最前边）



### 服务端API幂等性

常规实现：Redis 判断请求 Token 是否在 Redis 存在，如果存在的话，其他请求被拦截

通用实现：继承OncePerRequestFilter，HttpSession 与 Redis 打通，利用 Spring Session，HttpSession#setAttribute 它底层利用 Redis Hash

- **一句话解释**：`OncePerRequestFilter`的 **“Once”** 是为了解决 **Servlet 容器中因请求转发导致过滤器重复执行的问题**，它通过请求属性标记机制，确保在单个 HTTP 请求的完整生命周期内，过滤器的核心逻辑只运行一次。

  **最佳实践**：在 Spring 应用中，**几乎所有的自定义过滤器都应该继承 `OncePerRequestFilter`**，而不是直接实现 `Filter`接口，除非你明确需要处理多次执行的情况。这是 Spring Security、Spring Session 等官方组件的标准做法。



### 服务端多版本API实现

基于 Spring WebMVC 实现多版本 API 并行，实现 API 版本平滑升级。

```java
@PostMapping(value = "/user/register", produces = "application/json;v=3") // V3
Boolean registerUser(@RequestBody @Validated @Valid User user) throws UserException;

@PostMapping(value = "/user/register", produces = "application/json;v=3.1") // V3.1
default Boolean registerUserV31(@RequestBody @Validated @Valid User user) throws UserException {
	// default 方法确保源码兼容（代码兼容）
    // v3.1 的实现有个别 v3
    return false;
}
```



## REST API 客户端设计

### 客户端 API 校验

基于 Spring RestTemplate、Spring WebClient  以及 Spring Cloud Open Feign 整合 Bean Validation，实现 REST API 客户端校验



#### Spring RestTemplate 面向资源

JAX-RS WebClient
HTTP 资源 -> POJO
面向资源 -> 面向对象

使用场景

- 普通 Java EE 场景，面向资源处理，以面向对象编程 
- Spring Cloud RestTemplate 在负载均衡提升

```java
RestTemplate restTemplate = ...;

restTemplate.getForObject("http://user-service/users",List.class);
```



反向场景

Spring WebMVC
核心 HTTP 消息转化器 - HttpMessageConverter



**扩展点**

**HttpMessageConverter**

用于 HTTP Message 序列化和反序列化



**ClientHttpRequestFactory** 无状态

偏向于底层 HTTP Client 通讯
代表实现：

- 拦截（Delegating、包装、装饰器） - InterceptingClientHttpRequestFactory，依赖底层  ClientHttpRequestFactory 以及 ClientHttpRequestInterceptor
  - Interceptor1 start
    - Interceptor2 start
      - Interceptor3 start
        - Interceptor4 start
          - Implementation		
        - Interceptor4 end
      - Interceptor3 end
    - Interceptor2 end	
  - Interceptor1 end
- 底层
  - JDK HttpURLConnection - SimpleClientHttpRequestFactory（默认）
  - Apache HttpComponents HttpClient - HttpComponentsClientHttpRequestFactory
  - OkHttp3 - OkHttp3ClientHttpRequestFactory



**ClientHttpRequestInterceptor**

HTTP Client 请求执行拦截



基本模式（client端）

- HTTP 请求：HTTP 请求头、请求主体
- HTTP 请求处理
  - 序列化：POJO  -> HTTP Message
  - 前置处理：请求传输前
  - 请求传输：HTTP Client 实现
- HTTP 响应处理
  - 反序列化：HTTP Message -> POJO
  - 后置处理：客户端返回结果前



性能优化

序列化/反序列化优化

基于 HttpMessageConverter 优化：

- 底层优化，比如使用 FastJSON 或者其他实现
- 减少 REST POJO 对象反序列化选项，比如设定一个或两个 HttpMessageConverter  实现，FastJsonHttpMessageConverter



HTTP Client 底层实现优化

- HTTP Components
- OkHttp3



相关议题

Spring Template 类模式（命令模式）

XXXTemplate 通常实现 XXXOperations、



#### Spring WebClient

#### Spring Cloud Open Feign









### 客户端 API  异常处理

基于 Spring RestTemplate、Spring WebClient  以及 Spring Cloud Open Feign 实现统一异常处理，并预留国际化文案扩展



### 客户端 API  POJO 通讯

基于 Spring Cloud Open Feign 隐形包装 POJO 成为 API 模型，实现接口编程友好性目的



### 客户端多版本 API 调用

基于 Java 接口实现多版本 API 调用，达到 API 平滑升级的目的



# 站点国际化设计

## 站点国际化设计

### 国际化基础

简介 JDK 和 Spring 国际化的同异，分析 Spring WebMVC 国际化实现



### 易用性设计

提供易用国际化 API，替代 JDK 和 Spring 国际化 API 



### 可配置设计

实现国际化文案配置化，优雅地处理字符编码问题



### 高性能设计

提供高性能国际化文案，提升国际化文案读取性能，以及解决传统 JDK 以及 Spring 文案格式化性能瓶颈



### 热部署设计

支持国际化文案热部署，实时获取内容变更，实现线程安全



## 站点国际化整合

### 服务端 REST API  国际化整合

无缝整合国际化 API 与 REST API 模型，实现应用程序零修改



### 客户端 REST API 国际化整合

Spring Cloud Open Feign 整合



### 模板引擎整合

Spring Web 模板



### Bean Validation 整合

Bean Validation（Hibernate Validator）国际化整合



# Web服务器容错性设计

## 基于 Apache Tomcat 实现 Web 服务容错性

### Tomcat 线程模型

结合 Java AQS 和 线程池等基础，理解Tomcat 线程模型



### Tomcat 核心组件

理解 Tomcat 网络连接，协议处理等核心组件，掌握 Spring Boot 对其管控细节



### Tomcat 限流

利用 JMX 和 Tomcat API 实现全局 Web 服务限流



## 基于 Resilience4j 实现 Web 服务容错性

### Resilience4j 基础

掌握服务 CircuitBreaker、Bulkhead 以及 RateLimiter 等模块特性以及核心 API 使用



### Resilience4j Servlet 整合 

基于 Resilience4j API 实现 Servlet 熔断、限流等特性



### Resilience4j Spring WebMVC 整合

基于 Resilience4j API 实现 Spring WebMVC 熔断、限流等特性



### Resilience4j  Spring WebFlux 整合

基于 Resilience4j API 实现Spring WebFlux 熔断、限流等特性



# 服务容错性高阶设计

## Resilience4j  整合第三方框架

- Resilience4j Spring Cloud Open Feign 扩展：基于 Resilience4j 实现通用 Spring Cloud Open Feign 熔断、限流等功能
- Resilience4j  MyBatis 扩展：基于 Resilience4j 实现 MyBatis  熔断、限流等功能
- Resilience4j Redis 扩展：基于 Resilience4j  实现 Spring Redis 熔断、限流等功能



## 服务容错性动态变更设计

- Spring Cloud Config 动态变更：理解 Spring Cloud Config 与 Spring Boot @ConfigurationProperties Bean 动态绑定的关系
- 动态 Tomcat 组件更新：使用 Spring Cloud Config 实现动态 Tomcat 组件更新
- 动态 Resilience4j 组件更新：使用 Spring Cloud Config 实现动态 Resilience4j 组件更新



# 服务柔性负载均衡设计

## 基于监控指标的负载均衡实现

- 核心监控指标：掌握 CPU 使用率、系统负载（Load）、线程状态（Threading）、响应时间（RT）、QPS 以及 TPS 等核心指标
- Netflix Servo：理解 Netflix Servo 架构和监控指标组件
- 上报监控指标：基于 Spring Cloud 服务注册接口实现监控指标上报
- 负载均衡实现：基于 Netflix Servo 监控指标实现 Spring Cloud Netflix Ribbon（老版本）负载均衡策略



## 基于动态权重的负载均衡实现

- 动态权重算法：基于服务实例启动时间（uptime） 以及初始化权重，动态计算权重值
- 元数据上报：基于 Spring Cloud 服务注册接口实现服务实例启动时间（uptime）以及初始化权重数据上报
- 负载均衡实现：基于 动态权重算法 实现 Spring Cloud LoadBalancer（新版本）负载均衡策略



# 服务监控基础

## Micrometer 基础

- 指标核心概念：理解指标基本类型 - Timer, Counter, Gauge, DistributionSummary 等，以及指标 Tags
- Micrometer 核心 API：掌握 Timer, Counter, Gauge, DistributionSummary，MeterBinder，MeterRegistry 等 API 使用和底层原理
- Micrometer 内建 Binder：讨论 Micrometer 内建 Binder，包括 JVM 、Kafka、Logging、系统、Tomcat 等



## Micrometer 整合第三方框架

- Micrometer 适配 Netflix Ribbon 监控指标：适配 Ribbon 内部 Servo 监控指标到 Micrometer 方式
- Micrometer 整合 Redis Spring：Redis Spring API 监控指标注册到 MeterRegistry
- Micrometer 整合 MyBatis ：基于 MyBatis Plug-in 机制将监控指标注册到 MeterRegistry
- Micrometer 整合 JDBC：基于 JDBC 核心 API 将监控指标注册到 MeterRegistry



# 服务监控平台设计

## 基于 Pull 方式指标监控平台设计

- Prometheus  Endpoint：讨论 Spring Boot Actautor Prometheus  Endpoint 与 Micrometer 适配细节
- Prometheus 平台搭建：Prometheus 使用 Spring Cloud 注册中心发现服务实例，并拉取应用 Metrics 数据
- Grafana  平台搭建：整合 Prometheus 数据源，构建 Java 应用监控指标图形化



## 基于 Push 方式指标监控平台设计

- Prometheus Pushgateway 搭建：搭建 Prometheus Pushgateway，为 Java 应用推送指标做准备
- Micrometer Prometheus 注册中心：掌握 Micrometer Prometheus 注册中心使用方法，了解基本底层实现
- Micrometer InfluxDB 注册中心：切换 Micrometer  InfluxDB 注册中心，了解两种时序数据库的差异
- 指标监控平台混合模式：掌握 Pull 和 Push 监控数据混搭模式



# 服务链路追踪设计

## 基于 Java 应用层追踪服务链路

- Spring Cloud Sleuth 引入：借助 Spring Cloud Sleuth 组件理解 Java 应用层服务链路追踪基本架构
- Spring Cloud Sleuth 核心 API：理解 Tracer、Span 等 API 基本使用方法
- Spring Cloud Sleuth 第三方整合：使用 Span API 整合第三方框架，如 MyBatis、Redis 等



## 基于 Java Instrument 追踪服务链路重构

- Java Instrument 机制：理解 Java Instrument 机制，并掌握字节码提升编程
- Web 服务链路重构：基于字节码提升工具 Spring Cloud Open Feign 以及 Spring WebMVC 
- 第三方服务链路重构：重构 Redis、JDBC 以及 MyBatis 服务链路实现



# 服务网关整合设计

## 服务网关稳定性设计

- 网关容错性设计：Spring Cloud Gateway 结合 Resilience4j 实现熔断和限流
- 网关柔性设计：Spring Cloud Gateway 整合柔性 LoadBalancer 实现



## 服务网关可观测性设计

- 网关监控设计：Spring Cloud Gateway 整合 Micrometer 实现指标监控
- 网关链路跟踪设计：Spring Cloud Gateway 整合 Spring Cloud Slueth 实现链路跟踪



# 服务性能优化

## Spring Web 性能优化

-  Spring AOP 优化：集合 Spring Web 场景使用静态代理替换 Spring AOP 代理对象
-  Spring Web 组件优化：优化非必需 Web 组件，减少计算时间和内存开销
-  Spring Web 缓存优化：Spring WebMVC REST Request Body 和 Response Body 对象缓存优化，减少重复序列化和反序列化计算
-  Spring Web REST 序列化/反序列化：提升 REST 序列化/反序列化性能，减少不必要的计算



## Spring Cloud 性能优化

- @RefreshScope 优化：替换 @RefreshScope 实现，减少 Spring 应用上下文停顿的风险
- Spring Cloud OpenFeign 优化：提升 REST 序列化/反序列化性能，提高 HTTP 传输效率，减少负载均衡计算消耗
- Spring Cloud 配置优化：失效 Bootstrap 应用上下文，优化配置读取实现，避免 System Properties 并发锁阻塞等问题



# 工程脚手架定制

## Spring 脚手架运用、架构与定制

- Spring 脚手架搭建：在本地和测试环境搭建 Spring 脚手架，并了解 CI/CD 环境中的注意事项
- Spring 脚手架架构：了解 Spring Start 与 Spring Initialzr 之间的关系，掌握 Spring Initialzr  各个模块的职责以及它们之间的联系
- Spring 脚手架定制：根据基础框架和业务组件的 BOM 以及依赖信息，定制它们的模块



## Spring 脚手架原理、实现与扩展

- Spring 脚手架原理：理解 Spring Initialzr 工程构建的实现原理，包括：项目元信息、Project 子应用上下文以及 Web Endpoints
- Spring 脚手架实现：理解 Spring Initialzr 底层实现，包括：Maven 构建系统，代码生成原理以及元信息处理
- Spring 脚手架扩展：根据项目依赖组件实现多模块和动态 Maven 业务模板工程





