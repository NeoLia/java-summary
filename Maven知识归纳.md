[TOC]

# 1. settings.xml(settings)

## 1.1. 元素一览

- localRepository
- interactiveMode
- offline
- pluginGroups
- proxies
- servers
- mirrors
- profiles
- activeProfiles

# 1.2. localRepository

- 作用：设置本地仓库路径
- 默认值：${user.home}/.m2/repository

## 1.3. interactiveMode

- 作用：设置Maven是否需要和用户交互以获得输入
- 默认值：true

## 1.4. offline

- 作用：设置在Maven进行项目编译和部署等操作时是否允许Maven进行联网来下载所需要的信息
- 默认值：false

## 1.5. pluginGroups

- 作用：在pluginGroups元素下面可以定义一系列的pluginGroup元素。表示当通过plugin的前缀来解析plugin的时候到哪里寻找。pluginGroup元素指定的是plugin的groupId
- 默认值：org.apache.maven.plugins和 org.codehaus.mojo

## 1.6. proxies

- 作用：用来配置不同的代理
- 默认值：无

## 1.7. servers

- 作用：设置不同仓库服务器的安全认证等信息，如用户名、密码、私钥位置、私钥密码
- 默认值：无

## 1.8. mirrors

- 作用：设置远程仓库的镜像
- 默认值：无

## 1.9. profiles

- 作用：根据环境参数来调整配置文件
- 默认值：无

## 1.10. activeProfiles

- 作用：出发profiles的条件判断逻辑
- 默认值：无

# 2. pom.xml