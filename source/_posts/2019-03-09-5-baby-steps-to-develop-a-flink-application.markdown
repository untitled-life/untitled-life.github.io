---
layout: post
title: "开发Flink应用程序的5个起步步骤"
date: 2019-03-09 19:39:10 +0800
comments: true
categories: Flink
---
> 一路上，两个人都很忙碌。是这样的，妈妈必须做导游，给安安介绍这个世界，安安是新来的。而妈妈漏掉的东西，安安得指出来，提醒他。 --龙应台 《孩子你慢慢来》
<!-- more -->

原文地址：https://www.da-platform.com/blog/5-steps-flink-application-development

使Flink运转起来非常简单。在这篇文章中，我们将介绍5个起步步骤，指导您在本地环境搭建第一个可以运行的Flink应用程序。我们从讨论软件需求开始，并指出一些帮助您理解框架功能的培训资源。如果您喜欢从头开始，我们还将向您展示如何引导应用程序!这个快速概述将使您能够几乎不耗费任何时间的情况下启动Flink应用程序。

# 1. 软件需求

您可以在Linux、Mac OS X和Windows上开发和执行Flink应用程序。因为社区中的大多数开发人员都是在基于unix的设置中操作的，所以这个环境包含了最丰富的工具支持。首先，真正的需求是安装Java开发工具包(JDK) 8(或更高版本)——无论您是要使用Java还是Scala进行开发。虽然开发Flink应用程序并不是严格要求的，但我们建议您也在您的计算机上安装以下软件:

   - Apache Maven 3. x。在我们的介绍培训中，大多数示例都使用Maven实现构建自动化。此外，Flink还提供了Maven原型来引导新的Flink Maven项目。
   - 用于Java和/或Scala开发的IDE。特别是对于Scala，我们推荐使用IntelliJ，因为它对Maven和易于安装的Scala插件提供了开箱即用的支持。对于Java, Eclipse或Netbeans也能正常工作。
   
# 2. 为你准备的训练材料

如果您喜欢从头开始您自己的项目，那么您可以直接跳到下一个步骤。否则，data Artisans将提供一系列的培训练习，帮助您熟悉Flink的操作方式，并随着时间的推移使您轻松掌握时间和状态管理等更复杂的主题。这些练习(和可能的解决方案)可以在GitHub上找到，所以您可以轻松地克隆项目并使用Maven来构建它: 

```bash
git clone https://github.com/dataArtisans/flink-training-exercises.git
cd flink-training-exercises
mvn clean package
```  
这需要几分钟，具体时间取决于连接的速度——Maven将下载所有必需的依赖项。如果一切按计划进行，构建成功，那么您就走上了正确的轨道!

# 3.引导您自己的Flink项目

当然，从现有项目开始是使用Flink进行应用程序开发的最简单方法。但是如果您想在某个时候从头开始创建自己的项目呢?Flink提供Maven原型来为Java和Scala应用程序生成Maven项目。例如，要创建一个quickstart Java项目作为Flink应用程序的基础，请运行以下命令:

```bash
mvn archetype:generate \
-DarchetypeGroupId=org.apache.flink \
-DarchetypeArtifactId=flink-quickstart-java \
-DarchetypeVersion=1.7.0
```

上面的命令为Flink 1.7.0生成一个Maven项目，其中包含两个类:StreamingJob和BatchJob;它们分别为流和批处理Flink程序提供了基本框架。您可以调整参数以匹配您的版本和命名首选项!我们建议您将该项目导入您选择的IDE中，以便开发一个可运行的示例。如果您正在与灵感作斗争，您可以在[Flink文档](https://ci.apache.org/projects/flink/flink-docs-release-1.7/tutorials/datastream_api.html#writing-a-flink-program)中获得一些提示。

# 4. 运行和调试您的第一个Flink应用程序

尽管Flink是一个分布式数据处理系统，但在本地环境中使用您的机器更容易启动它。在典型的设置中，您会让master (JobManager)和workers (taskmanager)在不同的机器上作为独立的JVM进程运行;但是Flink还包括一种模式，允许您像多线程进程一样在相同的JVM中执行应用程序。这种模式允许您在IDE中轻松地开发、调试和执行Flink应用程序，就像任何其他Java或Scala项目一样。要启动应用程序，只需像往常一样运行main()方法!

![flink-application](/images/post/flink-application.png)

# 5. 监视您的Flink应用程序

如果您想知道正在运行的应用程序的底层发生了什么，那么您可以轻松地使用捆绑在Flink中的web接口来可视化和监视它。要为本地开发启用该接口，您需要向POM文件添加一个新的依赖项:

```xml
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-runtime-web_2.11</artifactId>
    <version>${flink.version}</version>
</dependency>
```

并显式创建具有所需配置的本地执行环境:

```java
import org.apache.flink.configuration.ConfigConstants;
...
Configuration config = new Configuration();
config.setBoolean(ConfigConstants.LOCAL_START_WEBSERVER, true);
StreamExecutionEnvironment env = StreamExecutionEnvironment.createLocalEnvironment(config);
...
```

在程序运行之后，Flink web接口现在应该可以在[本地环境](http://localhost:8081) 下使用，看起来有点像下面的图像。记住，更改POM文件后要更新Maven项目，如果您的IDE中没有启用自动导入——这可能会在您第一次尝试访问web界面时导致错误。

![flink-application-1](/images/post/flink-application-1.png)

![flink-application-2](/images/post/flink-application-2.png)

恭喜你!您刚刚在IDE中运行了第一个Flink应用程序。Flink文档中的教程更进一步，展示了如何设置本地Flink集群，并像向远程集群提交作业一样提交作业。

我们鼓励您浏览Flink文档以获得进一步的支持或更详细的信息。如果您在任何时候感到困惑或有任何问题:我们的社区在Stack Overflow上非常活跃，您也可以使用邮件列表联系开发人员。





  