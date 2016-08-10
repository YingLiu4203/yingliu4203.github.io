---
layout: post
title: 简单看板开发流程
categories:
- Process
tags:
- Kanban
---

## 1. 需要简化
看板的使用有一个学习和适应过程，多层看板流程的想法比较理想，缺点是层次带来了复杂性。在启动阶段我们希望流程管理和流程工具尽可能简单。我们跳过比较正式复杂的看板系统比如JIRA，其中的一个主要考量就是希望看板工具简单、好用。采用Trello这个极简的看板系统可以达到开发流程可视化的主要目的。同时在开始阶段，我们只采用一层看板，看板把每个人近期几个月内的工作放在一个标准化的简单流程里面。

## 2. 简单看板流程
为了简化看板使用，所有的看板都有如下几个状态：New, Planned, WIP (work in Progress), RFR (Ready For Review) and Done.
所有Planned的工作都有时间估算而且不应该改变。所有进入WIP卡片都有责任人和截止日期，而且截止日期不应该改变。所有的WIP卡片都有三个时间域：计划时间－实际工作时间－估计剩余时间。 比如一个计划16小时完成的卡片，工作了8个小时后，发现还需要16小时，那么时间为 16h-8h-16h. 工作中的WIP的后二个时间域应该每天更新。

一个新的工作或服务开始的任务需求分析，需求文档应该列出所有的功能性和非功能性需求。只有需求分析完成并且审核后才进入设计阶段。设计应该列出所有采用的技术栈和子系统分解。需求和设计阶段产生的文档, Requirement.md和Design.md放在项目的Docuemnts目录，在实施中会持续改进和细化。

设计结束应该有很多子系统或功能模块。按照优先级会有需要的时间估计并放入Planned列表。在放入WIP列表时会分配给具体人员并有完成日期。每天的工作中需要更新实际花费时间并保持需求、设计和代码的一致性。最初的估计时间不能改变作为以后估算的参考。所有的工作在审核后进入完成（Done）列表并视情况转储（Archive）。

(Revised on 2016-07-26)