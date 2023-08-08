# Nacos2.2版本数据库适配插件

## 1. 插件概述

### 1.1 简介

基于 Nacos2.2.3 版本，对Nacos的数据库适配插件进行了改造，使得Nacos 2.2.3 可以适配Postgresql数据库。

说明：当前项目基于https://github.com/wuchubuzai2018/nacos-datasource-extend-plugins 进行改造，感谢原作者的开源分享。


### 2.2 插件工程结构说明

- nacos-datasource-plugin-ext 工程主目录
    - nacos-all-datasource-plugin-ext 工程计划可打包所有适配的数据库插件
    - nacos-datasource-plugin-ext-base 工程设计为数据库插件操作的适配抽象
    - nacos-postgresql-datasource-plugin-ext 工程可打包适配Postgresql的数据库插件
    
## 2. 下载和使用

IDEA导入时导入nacos-datasource-plugin-ext这个目录作为根目录即可。

### 2.1 插件引入

#### 2.1.1 源码方式引入

使用postgresql作为依赖引入到Nacos主分支源码中，使用Maven提前将当前工程Install到Maven仓库，然后在pom.xml中引入如下依赖：

```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-postgresql-datasource-plugin-ext</artifactId>
    <version>1.0.0</version>
</dependency>
```

或引入all模块。

#### 2.1.3 打包形式引入

在Nacos2.2.2的发布包环境下，下载当前插件项目源码，打包为jar包，将该文件放到nacos主目录下的plugins文件夹中，默认会使用Nacos的loader.path机制指定该插件的路径，打包插件可选择nacos-postgresql-datasource-plugin-ext打包即可。postgresql模块的打包默认会包含postgresql的jdbc驱动类和base依赖。

### 2.2 修改数据库配置

在application.yml文件中声明postgresql的配置信息：

```yaml
spring:
  datasource:
    platform: postgresql
db:
  url:
    0: jdbc:postgresql://127.0.0.1:5432/nacos?tcpKeepAlive=true&reWriteBatchedInserts=true&ApplicationName=nacos_java
  user: nacos
  password: nacos
  pool:
    config:
      driverClassName: org.postgresql.Driver
```

### 2.3 导入Postgresql的数据库脚本文件

导入nacos-postgresql的脚本文件，脚本文件在nacos-postgresql-datasource-plugin-ext/src/main/resources/schema文件夹下面.

上面操作完成后，启动Nacos即可。

## 3. 其他数据库插件开发

可参考nacos-postgresql-datasource-plugin-ext工程，新创建Maven项目，实现AbstractDatabaseDialect类，重写相关的分页操作逻辑与方法，并创建相应的mapper实现，减少了适配的成本。

目前对于Oracle、达梦数据库，仍然需要修改Nacos2.2的主分支代码，因为要兼容默认的命名空间ID为空的查询情况，社区官网未处理。

最近有时间，将进行Nacos主分支源码上关于Nacos的Oracle的兼容性适配，目前可以用Nacos2.1的改造代码进行处理。
