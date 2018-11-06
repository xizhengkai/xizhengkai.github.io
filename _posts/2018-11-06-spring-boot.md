---
title: spring-boot搭建
description: 2小时学会spring boot
categories:
 - Java，网页
tags:
---

> 此教程跟随慕课网[2小时学会Spring Boot](https://www.imooc.com/learn/767)

<!-- more -->

## 环境搭建

### java环境

使用的IDE是idea，Java版本是1.8.0_192，然后配置环境变量

### 安装apache maven

* 下载maven
  访问maven的下载页面：<http://maven.apache.org/download.html>，其中包含针对不同平台的各种版本的maven下载文件。我在Windows上安装，下载的是3.3.9版本的二进制代码，下载地址如下：http://apache.fayea.com/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.zip

* 配置环境变量

  右键“计算机”，选择“属性”，之后点击“高级系统设置”，点击“环境变量”，来设置环境变量，有以下系统变量需要配置：

  新建系统变量 MAVEN_HOME 变量值：`C:\maven\apache-maven-3.3.9`

  编辑系统变量 Path 添加变量值： ;%MAVEN_HOME%\bin

* 测试安装
  在cmd中输入mvn -v 输出版本号

* 换源

  国内访问maven源很慢，因此我们需要将maven源改为阿里镜像，使用`mvn -X`查找其中的mvn使用的settings文件，我们将默认的`settings.xml`文件拷贝至用户目录下的`.m2`文件夹，然后在镜像这一部分中加入：

  ```xml
  <mirrors>
      <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>        
      </mirror>
  </mirrors>
  ```

### 创建项目

通过idea创建项目，选择`new->project->Spring Initializr`修改好域名和项目名称（在这项目名称改为girl），选择java版本，点击下一步，选择web，设置project存储路径。

## 创建spring boot应用，hellword

```java
package com.xzk.girl;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping(value = "/hello", method = RequestMethod.GET)
    public String say(){
        return "Hello Spring Boot!";
    }
}
```

然后运行，在网页中输入：

```http
http://127.0.0.1:8080/hello
```

即可显示

也可在cmd端，cd到C:\SprintBoot\girl，输入命令：

```sh
mvn spring-boot:run
```

还可以执行

```sh
mvn install
cd target
java -jar girl-0.0.1-SNAPSHOR.jar
```

