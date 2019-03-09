---
layout: post
title: "4 characteristics of Timers in Apache Flink to keep in mind"
date: 2019-03-09 16:23:32 +0800
comments: true
categories: Flink
---
> 虽然心中有爱，但是爱，冻结在经年累月的沉默里，好像藏着一个疼痛的伤口，没有纱布可绑。 --龙应台 《亲爱的安德烈》
<!-- more -->
原文地址：https://www.da-platform.com/blog/4-characteristics-of-timers-in-apache-flink

本文描述了在Apache Flink中使用计时器的一些基本概念和注意事项。开发人员可以使用Flink的ProcessFunction操作符注册自己的计时器，以便可以访问流应用程序的一些基本构建块，例如：

- 事件(流元素)
- 状态(容错、一致、仅在键控流上)
- 计时器(事件时间和处理时间，仅在键控流上)

有关Apache Flink ProcessFunction的更多信息，我们建议阅读[Apache Flink 1.7文档](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/process_function.html)以获得更多的说明和指导。

# Apache Flink中的计时器是什么?
计时器使Flink流应用程序具有响应性，并可以对处理和事件时间的更改做出适配。我们之前的[一篇文章](https://www.da-platform.com/blog/stream-processing-introduction-event-time-apache-flink)更详细地介绍了Apache Flink中时间的其他概念以及处理、摄入和事件时间之间的差异。当使用计时器处理事件流时，每当调用**processElement(…)**时，都会传递一个上下文对象，允许您访问元素的事件时间戳和TimerService。然后可以使用TimerService为将来的事件/处理时间瞬间注册回调。通过这样做，一旦到达计时器的特定时间瞬间，**onTimer(…)**方法将被调用。

**onTimer(…)**回调被调用的时间点首先取决于是使用处理时间还是事件时间来注册计时器。特别是:

- 当使用处理时间在Flink应用程序中注册计时器时，当机器的时钟时间达到计时器的时间戳时，将调用onTimer(…)方法。
- 当使用事件时间在Flink应用程序中注册计时器时，当操作符的水印达到或超过计时器的时间戳时，将调用onTimer(…)方法。

与**processElement(…)**方法类似，**onTimer(…)**回调中的状态访问也限定在当前键(其注册计时器的键)。

这里值得注意的是，onTimer(…)和processElement(…)的调用都是同步的，因此在**onTimer(…)**和**processElement(…)**方法中对状态的访问和修改是安全的。

# 牢记定时器的4个特点

在这一段中，我们将讨论Apache Flink中计时器的4个基本特性，在使用它们之前应该记住这些特性。这些是:

## 1. 定时器在KeyedStream上注册
由于计时器是按每个键注册和触发的，所以KeyedStream是Apache Flink中使用计时器的任何操作和函数的先决条件。

## 2.计时器会自动删除重复数据
TimerService会自动删除计时器的重复项，每个键和时间戳最多只能有一个计时器。这意味着，当多个计时器为同一个键或时间戳注册时，onTimer()方法将只调用一次。

## 3.定时器具有检查点特性
计时器由Flink进行检查点，就像任何其他托管状态一样。当Flink从检查点或保存点恢复作业时，每一个注册的计时器应该在恢复操作触发之前被启动。

## 4. 计时器可以被删除
从Flink 1.6开始，计时器可以暂停和删除。如果您使用的Apache Flink版本比Flink 1.5老，那么您可能会遇到一个糟糕的检查点性能，因为有许多计时器无法删除或停止。

您可以使用以下命令停止处理时间定时器:

```scala
long timestampOfTimerToStop = ...
ctx.timerService( ).deleteProcessingTimeTimer(timestampOfTimerToStop);
```

您还可以通过以下命令停止事件时间计时器:

```scala
long timestampOfTimerToStop = ...
ctx.timerService( ).deleteEventTimeTimer(timestampOfTimerToStop);
```

![Timers-in-flink](/images/post/FFT-Timers-in-flink.png)

这里值得一提的是，如果没有注册具有给定时间戳的计时器，则停止计时器没有效果。




