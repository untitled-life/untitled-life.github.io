---
layout: post
title: "Spring Boot Return Data With JSON Format"
date: 2018-12-04 14:04:56 +0800
comments: true
categories: SpringBoot JSON
---
时间是一只藏在黑暗中的温柔的手，在你一出神一恍惚之间，物走星移。 --龙应台 《目送》
<!-- more -->
## 方案一(全局配置)
在application.properties文件加入如下配置
```properties
spring.jackson.date-format=yyyy-MM-dd HH:mm
spring.jackson.time-zone=GMT+8
```
在pom中添加如下依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
    <version>{version}</version>
</dependency>
```

## 方案二(局部注解配置)
```java
// 返回格式化的字符串
@JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm:ss")
// 接收格式化的字符串，转为Date类型对象
@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
```
在pom中添加如下依赖
```xml
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-joda</artifactId>
    <version>{version}</version>
</dependency>
```
