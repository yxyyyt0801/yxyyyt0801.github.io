# 配置

settings.xml

```xml
<!-- 设置本地仓库位置 -->
<localRepository>D:\data\.m2\repository</localRepository>

<!-- 阿里云仓库，加快下载依赖 -->
<mirrors>
	<mirror>
	  <id>aliyunmaven</id>
	  <mirrorOf>*</mirrorOf>
	  <name>阿里云公共仓库</name>
	  <url>https://maven.aliyun.com/repository/public</url>
	</mirror>
</mirrors>
```



# 配置环境变量

## Maven

设置|系统|关于|高级系统设置|高级|环境变量|系统变量

- MAVEN_HOME    D:\install\apache-maven-3.6.3
- Path    %MAVEN_HOME%\bin



## Jdk

- JAVA_HOME    D:\install\jdk\jdk8u492-b09
- Path    %JAVA_HOME%\bin



# 验证

```shell
# 配置完成后，需要重启控制台
mvn -v
```



# 问题

- [ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.1:compile (default-compile) on project goldmine-framework-common: Compilation failure
  [ERROR] No compiler is provided in this environment. Perhaps you are running on a JRE rather than a JDK?

  配置jdk环境变量，之后控制台重启。
