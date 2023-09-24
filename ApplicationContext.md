# ApplicationContext
### 什么是ApplicationContext？
`ApplicationContext`是一个接口，同时它也是`BeanFactory`的子接口。那么`BeanFactory`又是什么？`BeanFactory`提供了配置框架和基本功能。但`ApplicationContext`在此基础上添加了更多企业的特定功能。`ApplicationContext`是`BeanFactory`的超集，即功能更强大。
### ApplicationContext有什么用？
首先要知道 `org.springframework.beans` 和 `org.springframework.context` 包是`SpringFramework`的IOC容器的基础。`BeanFactory`接口提供了一种高级配置机制，能够管理任何类型的对象。`ApplicationContext`是`BeanFactory`的子接口，它增加了：
- 与 `SpringFramework` 的AOP功能轻松集成
- 消息资源处理（用于国际化）
- 事件发布
- 应用层特定的上下文，例如Web应用程序中使用的`WebApplicationContext`
### ApplicationContext和BeanFactory对比，哪个好？
<p style="font-weight:bold">那肯定是使用ApplicationContext。</p>

> <text style="font-size:15px">Spring官网推荐将GenericApplicationContext及其子类AnnotationApplicationContext作为自定义引导的常见实现。这些实现类是用于所有常见 目的的SpringFramework核心容器的主要入口点：加载配置文件，触发类路径扫描，编程式注册Bean定义和带注解的类，以及（从5.0版本开始）注册功能性Bean的定义。</text>

**【面试题】**
<p style="font-weight:1000">BeanFactory与ApplciationContext有什么区别？</p> 

1. `BeanFactory`接口提供了一个抽象的配置和对象管理机制
2. `ApplicationContext`是`BeanFactory`的子接口
3. `ApplicationContext`简化了AOP的整合、消息机制、事件机制，以及对Web环境的扩展，`BeanFactory`没有这些扩展
4. `ApplicationContext`主要扩展以下功能：
   - AOP的支持（`AnnotationwareAspectJAutoProxyCreator`作用于Bean的初始化后）
   - 配置元信息（`BeanDefinition`、`Environment`、注解等）
   - 资源管理（`Resource`抽象）
   - 事件驱动机制（`ApplicationEvent`、`ApplicationListener`）
   - 消息与国际化（`LocaleResolver`）
   - `Environment`抽象（`SpringFramework3.1`以后）
