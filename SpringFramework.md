#Spring[SpringFramework]
<!-- ###什么是SpringFramework
Spring是一种应用程序级别的基础架构支持，专注于企业应用程序的“脚手架”。**以IOC和AOP为内核**，具有非侵入、容器管理、组件化、**轻量级**、**一站式**等优良特性的开源容器框架，主要目的是为了解决企业级编程开发中的复杂性，**实现敏捷开发**的应用型框架。 -->
*Spring就是SpringFramework*

###什么是SpringFramework[高级版]
SpringFramework 是一个开源的、**松耦合的**、分层的、**可配置**的一站式企业级 Java 开发框架，它的核心是 **IOC 与 AOP** ，它可以更容易的构建出**企业级 Java 应用**，并且它可以根据应用开发的组件需要，整合对应的技术。
*解释如下（**面试会用到**）：*
- **松耦合**：此概念是为了描述IOC和AOP，面试被提问到可以用IOC的组件之间的解耦相关知识回答。
- **可配置**：是关于SpringBoot的。
- **IOC和AOP**：基础中的基础，面试中说了比没说好。
- **AOP**：切面编程可以将应用业务做统一或特定的功能增强，能实现应用业务与增强逻辑的解耦。
- **企业级Java应用**：注意对比“企业级Java开发”和“JavaEE开发”的区别：SpringFramework不仅能构建在Web项目，对于普通的JavaSE项目、GUI项目，也是可以用SpringFramework的。
- **容器与时间**：管理应用中使用的组件Bean、托管Bean的生命周期、事件与监听器的驱动机制。

###SpringFramework包含的模块
***面试会用到***
- beans、core、context、expression【核心包】
- AOP【切面程序】
- JDBC【整合jdbc】
- ORM【整合ORM框架】
- TX【事务控制】
- Web【Web层技术】
- Test【整合测试】
- <text style="font-size:20px">......</text>