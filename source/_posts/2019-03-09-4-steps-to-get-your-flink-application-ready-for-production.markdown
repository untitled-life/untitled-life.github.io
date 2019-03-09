---
layout: post
title: "4 steps to get your Flink application ready for production"
date: 2019-03-09 16:12:19 +0800
comments: true
categories: Flink
---
> 独裁，专制，腐败，不是哪一个主义制度所独有，但是东欧革命狂潮就应该给所有的专制政权，不管它是否什么主义，一个冰冷的警告，暴力，不能持久。 --龙应台 《百年思索》
<!-- more -->
原文地址：https://www.da-platform.com/blog/4-steps-flink-application-production-ready

本文将描述使Flink应用程序投入生产的必要配置步骤。在下面的部分中，我们概述了重要的配置参数，而这些参数是工程领导、DevOps和数据工程师将Flink作业引入生产阶段之前需要仔细考虑的。Apache Flink为大多数配置选项提供了开箱即用的默认设置，在许多情况下，这是POC阶段(概念验证)或探索不同api和Flink抽象的良好起点。

然而，将Flink应用程序引入生产环境需要额外的配置，这些配置能够有效地扩展和再次扩展您的应用程序，并使其可用于生产，并与不同的系统需求、Flink版本和连接器兼容，以用于未来的迭代和潜在的升级。

下面，我们将收集一些配置要点，以便在将Flink应用程序投入生产之前进行审查:
![FFT-4-steps-to-get-your-app-production-ready.png](/images/post/FFT-4-steps-to-get-your-app-production-ready.png)

## 1. 明确定义Flink运算符的最大并行度
Flink的键控状态被组织在所谓的键组中，然后这些键组被分发到您的Flink操作符的并行实例中。这是要分发的最小原子单元，因此也会影响Flink应用程序的可伸缩性。每个操作符的键组数对于每个作业只选择一次:手动或默认情况下。默认值将给出大致的操作并行度* 1.5，下界为128，上界为32768。它可以通过setMaxParallelism(int maxParallelism)手动定义每个作业和/或每个操作符。

任何进入生产环境的Flink作业都应该指定最大的并行度。但是，这个值的决定应该经过仔细考虑，因为此时，一旦设置了最大并行度，就不能在稍后的阶段更新它。更改最大并行度的Flink作业只能使用全新的状态从头开始。在更改最大并行度时，从以前的检查点或保存点进行恢复不可用。

建议将最大并行度设置为足以满足应用程序未来对可伸缩性和可用性的需求，同时又相对较低，以避免影响应用程序的总体性能。这是由于这样一个事实:在具有最高并行度的情况下，Flink为其重新伸缩的能力维护某些元数据，这可能会增加Flink应用程序的总体状态大小。

Flink文档提供了关于如何[使用检查点配置使用大状态的应用程序](https://ci.apache.org/projects/flink/flink-docs-stable/ops/state/large_state_tuning.html)的额外信息和指导。

## 2. 为Flink运算符分配唯一的用户id (uuid)
对于有状态Flink应用程序，建议为所有运算符分配惟一的用户id (uuid)。这是必要的，因为一些内置Flink运算符(比如windows)是有状态的，而其他运算符可能是无状态的，这使得很难知道哪些内置运算符实际上是有状态的，哪些不是。

Flink运算符的uuid可以使用uid(String uid)方法分配。运算符uuid允许Apache Flink有效地将运算符状态从保存点映射到适当的运算符，这是保存点在Flink应用程序中正常工作所必需的元素。

## 3.全面考虑Flink应用程序的状态后端
在投入生产之前，开发人员和工程领导应该仔细考虑他们的Flink应用程序的状态后端类型，因为Apache Flink目前不支持状态后端互操作性。这使得有必要从保存点恢复状态，用于最初获取保存点的相同状态后端。

在之前的一篇[博客](https://data-artisans.com/blog/stateful-stream-processing-apache-flink-state-backends)文章中介绍了Apache Flink中目前支持的3种状态后端之间的差异。

对于生产用例，强烈建议使用RocksDB状态后端，因为这是目前唯一一种支持大状态和异步操作(如快照)的状态后端，这些操作允许在不停止Flink操作的情况下编写快照。另一方面，使用RocksDB状态后端可能会带来性能折衷，因为所有状态访问和检索都需要序列化(和反序列化)才能跨越JNI边界，这可能会影响应用程序的吞吐量(与内存状态后端相比)。

## 4. 使作业管理器具有高可用性(HA)
高可用性(HA)配置确保Flink应用程序中的JobManager组件的潜在故障能够自动恢复，从而最大限度地减少停机时间。JobManager的主要职责是协调Flink部署，比如调度和适当的资源分配。

默认情况下，Flink为每个Flink集群设置一个JobManager实例。这将创建一个单点故障点(SPOF):如果JobManager崩溃，则无法提交任何新程序，并且正在运行的程序会失败。因此，强烈建议为生产环境[配置高可用性HA](https://ci.apache.org/projects/flink/flink-docs-stable/ops/config.html#high-availability-ha)。


以上4个步骤遵循社区设置的最佳实践，这些实践允许Flink应用程序在维护状态的同时任意扩展，处理更大容量的数据流和状态，并增加它们的可用性保证——生产用例的特定需求。Apache Flink文档中的[部署和操作](https://ci.apache.org/projects/flink/flink-docs-stable/ops/)部分为稳定的Flink操作提供了额外的指导和支持。我们强烈建议在将应用程序转移到生产环境之前，遵循上述步骤并仔细阅读文档。


