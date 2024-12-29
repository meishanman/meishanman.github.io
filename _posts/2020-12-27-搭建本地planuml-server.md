# 搭建本地planuml-server

## 下载

``` shell
$ git clone https://github.com/plantuml/plantuml-server
```

## 配置mvn

``` 
$ sudo apt install maven
```

配置国内的maven服务器, 创建 ~/.m2/settings.xml，内容为:
``` 
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <mirrors>
        <!-- 阿里云仓库 -->
        <mirror>
          <id>alimaven</id>
          <mirrorOf>central</mirrorOf>
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
        </mirror>

        <!-- 中央仓库1 -->
        <mirror>
          <id>repo1</id>
          <mirrorOf>central</mirrorOf>
          <name>Human Readable Name for this Mirror.</name>
          <url>http://repo1.maven.org/maven2/</url>
        </mirror>

        <!-- 中央仓库2 -->
        <mirror>
          <id>repo2</id>
          <mirrorOf>central</mirrorOf>
          <name>Human Readable Name for this Mirror.</name>
          <url>http://repo2.maven.org/maven2/</url>
        </mirror>
  </mirrors>
</settings>
```

## 构建运行

``` 
$ cd plantuml-server
$ mvn jetty:run
```

访问地址为:http://127.0.0.1:8080

