# 简介

- 解决了两个问题
  - 构建
  - 依赖关系
  
- POM ( Project Object Model，项目对象模型 ) 

- 依赖范围 (Scope)

  - **compile**（默认）：编译、测试和运行时都可用
  - **provided**：编译和测试时可用，但运行时由 JDK 或容器提供
  - **runtime**：只在测试和运行时需要
  - **test**：仅在测试编译和执行阶段需要
  - **system**：类似于 provided，但需要显式指定 JAR 路径

- 依赖解析机制

  - 依赖调解

    当出现版本冲突时，Maven 使用以下规则解决：

    - 最近定义优先（在依赖树中路径最短的版本被选中）
    - 如果路径长度相同，则先声明的依赖优先

  - 依赖范围影响

    不同范围的依赖会影响传递性：

    - compile 范围的依赖会传递
    - **provided 和 test 范围的依赖不会传递**
    - runtime 范围的依赖会以 runtime 范围传递

- 可选依赖 (Optional Dependencies)

  **标记为 optional 的依赖不会传递**

- 仓库类型及优先级

  - **本地仓库**：位于用户主目录下的 `.m2/repository` 目录（缓存远程下载的依赖）

  - **远程仓库**：公司或组织搭建的仓库

    - 中央仓库：Maven 默认的公共仓库 https://repo.maven.apache.org/maven2/
    - 私服仓库：公司内部搭建（如Nexus）
    - 其他公共仓库：如阿里云、JCenter等

    ```xml
    <!--  maven 根目录下的 conf 文件夹中的 settings.xml 文件 -->
    <mirrors>
    	<mirror>
          <id>aliyunmaven</id>
          <mirrorOf>*</mirrorOf>
          <name>阿里云公共仓库</name>
          <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    </mirrors>
    
    <!-- pom.xml -->
    <repositories>
        <repository>
          <id>spring</id>
          <url>https://maven.aliyun.com/repository/spring</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
    </repositories>
    ```

    




# 常用命令

```shell
# 编译
mvn compile

# 运行测试
mvn test
# -P 指定 profile id
mvn test -Ptest

# 打包（生成jar）
mvn package
# 清理项目，打包但不运行测试
mvn clean package -DskipTests

# 安装到本地仓库（~/.m2/repository）
mvn install
# 跳过测试
mvn install -DskipTests
# 强制更新快照依赖
mvn clean install -U
# 调试构建问题
mvn -X clean install

# 部署到远程仓库（需配置distributionManagement）
mvn deploy

# 清理target目录
mvn clean

# 检查可用更新
mvn versions:display-dependency-updates

# 检查未使用的依赖
mvn dependency:analyze
```



# 插件

- maven-compiler-plugin
  - 编译 Java 源代码：将 `.java`文件编译为 `.class`文件
  - source、target   指定 Java 版本：配置源代码和目标字节码版本
  - annotationProcessorPaths   注解处理：支持编译时注解处理器
  - compilerArgs   编译器选项：传递自定义参数给 Java 编译器
  
- maven-resources-plugin 
  - nonFilteredFileExtensions   默认情况下，Maven 会对资源文件（如 `.properties`, `.xml`, `.txt`等）进行过滤，替换其中的 Maven 属性或项目属性。但某些文件（如二进制文件：图片、压缩包、已编译的类文件等）不应被处理，否则可能会损坏文件内容。`nonFilteredFileExtensions`允许你指定哪些文件扩展名应被排除在过滤过程之外。
  
- maven-deploy-plugin
  
  负责将项目构建产物（jar/war/pom 等）部署到远程仓库。它是执行 `mvn deploy`命令时默认调用的插件。
  
- apt-maven-plugin
  - generate-sources   在编译前自动运行注解处理器，生成额外的源代码（如 MapStruct、Lombok、JPA 元模型等），并将生成的源代码正确添加到项目的编译路径中。**推荐使用 maven-compiler-plugin 通过 `<annotationProcessorPaths>` 配置注解处理器**。
  
- spring-boot-maven-plugin
  - repackage   会在 Maven 的 `package`阶段之后执行，将标准的 Maven 构建输出（如普通的 JAR 文件）**重新打包**成可执行的 Spring Boot JAR（或 WAR）文件。默认会将原始的 Maven 构件重命名为 `*.jar.original`
  
- flatten-maven-plugin

  - 用于解决多模块项目在发布（deploy）或安装（install）时，因版本占位符（如 `${revision}`）未被解析而导致的依赖无法下载问题。本地、远程仓库的pom是去占位符的。

  - 扁平化pom，去除 `<parent>` 节点；代替所有占位符，如依赖的 ``
  
    ```xml
    <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>flatten-maven-plugin</artifactId>
                    <configuration>
                        <updatePomFile>true</updatePomFile>
                        <flattenMode>oss</flattenMode>
                    </configuration>
                    <executions>
                        <execution>
                            <id>flatten</id>
                            <phase>process-resources</phase>
                            <goals>
                                <goal>flatten</goal>
                            </goals>
                        </execution>
                        <execution>
                            <id>flatten.clean</id>
                            <phase>clean</phase>
                            <goals>
                                <goal>clean</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    </build>
    ```
    
    




# 最佳实践

- Snapshot 版本与 Release 版本 的选择
  - 协同开发时，A依赖B，B使用Snapshot 
    - 语义不符合，B也在开发过程中，要用Snapshot标识
    - 如果B用Release，频繁变更版本号，会导致版本号泛滥，同时，导致A需要频繁修改依赖版本
    - 如果B不用Snapshot，只用一个版本号的 Release 版本进行修改，即使B有更新，A 也不会频繁更新release的缓存（本地仓库）；如果用Snapshot版本，则A可以及时更新到最新版本（更新策略：**每天第一次构建**）
  - 正式环境不可使用Snapshot 版本。因为今天构建成功，明天更新了Snapshot 最新版本，可能会导致构建失败，导致系统不稳定