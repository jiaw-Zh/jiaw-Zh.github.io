---
title: "基于架构的测试"
date: 2022-03-12T21:52:04+08:00
categories: ['测试理论']
tags: ['2022','架构']
---

## 架构测试的基本原则

对于测试人员来讲，学习架构知识应该有自己独特的视角

基本只要做到**清楚原理、了解在被测系统中的部署架构**，从测试的角度能够调用必要的接口就可以了

在实际应用中可以遵循 **“由广度到深度”** 和 **“自上而下”** 两个基本原则

“由广度到深度”中的 **“广度”** 是指在平时工作以外的时间中，应该多注重全领域架构知识的积累，此时那些系统性地介绍架构知识的书籍就可以给你最大程度的帮助了

“由广度到深度”的 **“深度”** 是指，对于架构中某一领域的特定知识在项目中要实际使用的时候，必须要刨根问底，通过实际的测试来加深对架构知识细节的理解

**“自上而下”** 是指，在实际测试项目中，当需要设计涉及架构的测试用例和场景的时候，千万不要直接基于“点”来设计测试，而是应该：首先通过全局阅读理解上层架构设计；然后，在理解了架构设计的初衷和希望达成目的的基础上，再向下设计测试场景和用例

## 高性能架构设计

### 缓存

- 对于前端的测试场景，需要分别考虑缓存命中和缓存不命中情况下的页面加载时间。
- 基于缓存过期测试策略的设计，需要考虑到必须要重新获取数据的测试场景。
- 需要针对可能存在的缓存“脏数据”，进行有针对性的测试。缓存“脏数据”，是指数据库中的数据已经更新，但是缓存中的数据还没来得及更新的场景。
- 需要针对可能的缓存穿透进行必要的测试。缓存穿透，是指访问的数据并不存在，所以这部分数据永远不会有被缓存的机会，因此此类请求会一直重复访问数据库。
- 系统冷启动后，在缓存预热阶段的数据库访问压力是否会超过数据库实际可以承载的压力。
- 对于分布式缓存集群来说，由于各集群使用的缓存算法不同，那么如果要在缓存集群中增加更多节点进行扩容的话，扩容对原本已经缓存数据的影响也会不同。所以，我们需要针对缓存集群扩容的场景，进行必要的测试和性能评估。

### 集群
- 集群容量扩展。也就是说，集群中加入新的节点后，是否会对原有的 session 产生影响。
- 对于无状态应用，是否可以实现灵活的实效转移。
- 对于基于 session 的有状态应用，需要根据不同的 session 机制验证会话是否可以正常保持，即保证同一 session 始终都有同一个确定的节点在处理。
- 当集群中的一个或者多个节点宕机时，对在线用户的影响是否符合设计预期。
- 对于无状态应用来说，系统吞吐量是否能够随着集群中节点的数量呈线性增长。
- 负载均衡算法的实际效果，是否符合预期。
- 高并发场景下，集群能够承载的最大容量。


## 可伸缩性架构设计

### 可伸缩性缓存
- 针对缓存集群中新增节点的测试，验证其对原有缓存的影响是否足够小；
- 验证系统冷启动完成后，缓存中还没有任何数据的时候，如果此时网站负载较大，数据库是否可以承受这样的压力；
- 需要验证各种情况下，缓存数据和数据库数据的一致性；
- 验证是否已经对潜在的缓存穿透攻击进行了处理，因为如果有人刻意利用这个漏洞来发起海量请求的话，就有可能会拖垮数据库。

### 可伸缩性数据库

- 正确读取到刚写入数据的延迟时间；
- 在数据库架构发生改变，或者同样的架构数据库参数发生了改变时，数据库基准性能是否会发生明显的变化；
- 压力测试过程中，数据库服务器的各项监控指标是否符合预期；
- 数据库在线扩容过程中对业务的影响程度；
- 数据库集群中，某个节点由于硬件故障对业务的影响程度。


## 可扩展性架构设计

> 提升网站可扩展性性的核心，就是降低系统各个模块和组件之间的耦合

### 微服务架构

- 基于消费者契约的 API 测试，核心思想是：只测试那些真正被实际使用到的 API 调用，如果没有被使用到的，就不去测试。
- 各服务之间使用 Mock Service 解耦

### 事件驱动架构与消息队列
- 从构建测试数据的角度来看，为了以解耦的方式测试系统的各个模块，我们就需要在消息队列中构造测试数据。这也是为什么很多互联网的自动化测试框架中都会集成有消息队列写入工具的主要原因。
- 从测试验证的角度来看，我们不仅需要验证模块的行为，还要验证模块在消息队列中的输出是否符合预期。为此，互联网的自动化测试框架中也都会集成消息队列的读取工具。
- 从测试设计的角度来看，我们需要考虑消息队列满、消息队列扩容等情况下系统功能是否符合设计预期。
- 除此之外，我们还需要考虑，某台消息队列服务器宕机的情况下，丢失消息的可恢复性以及新的消息不会继续发往宕机的服务器等等。



参考：[《软件测试 52 讲》-茹炳晟](https://time.geekbang.org/column/article/42373?cid=100009601)