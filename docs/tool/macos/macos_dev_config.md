# 环境配置

## Homebrew

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew -v
```



## Git

```shell
brew install git
```



## ohmyzsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```



## JDK

```shell
# jdk21
brew install --cask temurin@21

# 查看安装位置
# /Library/Java/JavaVirtualMachines/temurin-21.jdk/Contents/Home
/usr/libexec/java_home -v 21

# 设置环境变量
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 21)' >> ~/.zshrc
echo 'export PATH="$JAVA_HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```



## Maven

```shell
# 忽略依赖
brew install --ignore-dependencies maven

# 全局配置 /usr/local/Cellar/maven/3.9.16/libexec/conf

# 用户级配置
mkdir -p ~/.m2
nano ~/.m2/settings.xml

<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.2.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0 https://maven.apache.org/xsd/settings-1.2.0.xsd">

  <!-- 本地仓库路径（可选，默认 ~/.m2/repository） -->
  <localRepository>${user.home}/.m2/repository</localRepository>

  <!-- 镜像：阿里云加速 -->
  <mirrors>
    <mirror>
      <id>aliyun</id>
      <mirrorOf>*</mirrorOf>
      <name>Aliyun Maven</name>
      <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
  </mirrors>

  <!-- JDK 编译版本（可选但推荐） -->
  <profiles>
    <profile>
      <id>jdk-21</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <maven.compiler.release>21</maven.compiler.release>
      </properties>
    </profile>
  </profiles>

</settings>

# 保存退出：Ctrl+O → Enter → Ctrl+X

# 验证
mvn help:effective-settings | head -30
```

