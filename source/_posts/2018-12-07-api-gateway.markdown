---
layout: post
title: "API Gateway"
date: 2018-12-07 09:39:31 +0800
comments: true
categories: 
---
> 有一种寂寞，身边添一个可谈的人，一条知心的狗，或许就可以消减。有一种寂寞，茫茫天地之间余舟一芥的无边无际无着落，人只能各自孤独面对，素颜修行。 --龙应台 《目送》
<!-- more -->
## Intent
Aggregate calls to micro services in a single location: the API Gateway. The user makes a single call to the API Gateway, and the API Gateway then calls each relevant micro service.

## Applicability
you're also using the micro services pattern and need a single point of aggregation for your micro service calls.