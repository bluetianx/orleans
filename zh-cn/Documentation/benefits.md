---
layout: page
title: Main Benefits
---

# 好处

Orleans的主要好处是：**开发人员生产力**，即使对于非专业程序员也是如此，**默认情况下透明的可伸缩性**不需要程序员的特别努力。我们在下面对这两个好处进行了扩展。

## 开发人员生产力

Orleans编程模型通过提供以下关键抽象、保证和系统服务，提高了专家和非专家程序员的生产率。

-   **熟悉的面向对象编程(oop)范式**

grains是用异步方法实现声明的.net grain接口的.net类。因此，在程序员看来，grains是可以直接调用其方法的远程对象。通过将方法调用转换为消息、将它们路由到正确的端点、调用目标Grain的方法以及以完全透明的方式处理失败和角落情况，这为程序员提供了熟悉的oop范例。

-   **grains 单线程执行**

运行时保证一个Grain一次不会在多个线程上执行。再加上与其他Grain的隔离，程序员永远不会在Grain级别面临并发，也不需要使用锁或其他同步机制来控制对共享数据的访问。仅此特性就使得分布式应用程序的开发对于非专家程序员来说是容易的。

-   **透明激活**

运行时仅当有要处理的消息时才激活Grains。这将创建对应用程序代码可见并由其控制的grain的引用的概念与对应用程序透明的内存中grain的物理激活的概念完全分离。在许多方面，这类似于虚拟内存，因为它决定何时“换出页”(停用)或“置入页”(激活)一个grains；应用程序可以不间断地访问逻辑创建的grains的全部“内存空间”，无论它们是否在任何特定时间点位于物理内存中。透明激活通过在硬件资源池中存储和迁移Grain实现动态、自适应的负载平衡。这个特性是对传统的actor模型的一个重大改进，其中actor生命周期是由应用程序管理的。

-   **位置透明度**

程序员用来调用grain方法或传递给其他组件的grain引用(代理对象)只包含grain的逻辑标识。Grains逻辑标识到其物理位置的转换和相应的消息路由是由orleans运行时透明完成的。应用程序代码与grain进行通信，而不注意它们的物理位置，这可能会随着时间的推移而改变，原因是失败或资源管理，或者是grain在调用时被停用。

-   **与持久存储的透明集成**

orleans允许声明性地将grain的内存状态映射到持久化存储。它同步更新，透明地确保调用方仅在成功更新持久状态后才能接收结果。扩展和/或定制现有持久存储提供程序集是直截了当的。

-   **异常的自动传播**

运行时使用异步和分布式try/catch的语义自动将未处理的错误传播到调用链上。因此，错误不会在应用程序中丢失。

这允许程序员将错误处理逻辑放在适当的位置，而无需在每个级别手动传播错误的繁琐工作。

### 默认情况下透明的可伸缩性

orleans编程模型旨在引导程序员在几个数量级上成功地扩展应用程序或服务。这是通过结合经验证的最佳实践和模式以及通过提供较低级别系统功能的有效实现来实现的。以下是实现可伸缩性和性能的一些关键因素：

-   **应用状态的隐式细Grain划分**

通过使用Grain作为直接可寻址的实体，程序员隐式地分解了应用程序的整体状态。虽然Orleans编程模型没有规定Grains应该有多大或多小，但在大多数情况下，拥有相对较大数量的Grains(数百万或更多)是有意义的，每个Grains代表应用程序的自然实体，如用户帐户或采购订单。由于grains是独立可寻址的，而物理位置是由运行时抽象出来的，因此Orleans在以透明和通用的方式平衡负载和处理热点方面具有极大的灵活性，而不需要应用程序开发人员的任何想法。

-   **适应性资源管理**

Grains与其他Grains相互作用时，不会假设它们的位置。由于这种位置透明性，运行时可以动态地管理和调整可用硬件资源的分配。运行时通过针对加载和通信模式在计算集群中对Grain的存储和迁移做出细Grain的决策来实现这一点，而不会使传入的请求失败。通过创建特定Grain的多个副本，运行时可以在不更改应用程序代码的情况下提高Grain的吞吐量。

-   **多路通信**

Orleans的Grains有逻辑端点，它们之间的消息传递通过一组固定的全对所有物理连接(TCP套接字)进行多路复用。这允许运行时以较低的每粒操作系统开销托管数百万个可寻址实体。此外，Grains的激活和停用不会产生注册/注销物理端点(如TCP端口或HTTP URL)或甚至关闭TCP连接的成本。

-   **高效调度**

运行时计划在每个物理处理器内核一个线程的自定义线程池中执行大量单线程Grain。使用以非阻塞、基于连续性的风格(Orleans编程模型的要求)编写的Grain代码，应用程序代码以非常高效的“协作”多线程方式运行，没有争用。这使得系统能够达到高吞吐量，并以非常高的CPU利用率(高达90%+)运行，具有极大的稳定性。系统中grains数量的增长和负载的增加不会导致额外的线程或其他操作系统原语，这一事实有助于单个节点和整个系统的可伸缩性。

-   **显式异步**

orleans编程模型使分布式应用程序的异步特性变得明确，并指导程序员编写非阻塞异步代码。结合异步消息传递和高效的调度，这使得无需显式地使用多线程就可以实现很大程度的分布式并行性和总吞吐量。