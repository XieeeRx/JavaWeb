# IOC
### IOC是什么？
IOC，**控制反转**，Inverse of Control，是一种**编程原则**，它的设计和架构可以**实现组件间的解耦**，核心思想是**将控制权转移出去**。  
- 编程原则：不是某种落地技术，是一种理论。
- 组件间的解耦：
  - 什么是解耦？就是**解除耦合**，**“耦合”就是代码模块之间相互依赖的程度**。高度耦合就是关系紧密，关系越紧密，双方（或多方）之间的影响就更深，受桎梏的越多。
  - 为什么要解耦？我们一般追求**低耦合**，所以要解耦，即解除对象间的依赖关系，**减少对象间的互相影响**，增强它们的独立性。
- 如何将控制权转移出去：将原有的对象间的**主动依赖改为被动接受型依赖**。

### 怎么使用IOC？
##### 例子如下(Java)：
```java{.line-numbers}
private DemoDao dao = new DemoDaoImpl();

private DemoDao dao = (DemoDao) BeanFactory.getBean("demoDao");
```
上面的就是**强依赖**，下面就是用IOC（理论）实现的**弱依赖**。怎么理解？第一句的写法是**主动声明**<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">DemoDao</text>的实现类，在编译时就必须保证<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">DemoDaoImpl</text>的存在。第二句的写法**没有指定实现的类**，是叫<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">BeanFactory</text>小弟去帮我们找一个<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">name</text>为<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">demoDao</text>的对象，只有到运行期反射创建时才知道<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">DemoDaoImpl</text>是否存在。  

用第二句的方式，就不是我们自己去声明了。可以理解是<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">BeanFactory</text>去用获取对象的方式。即我们将控制权交给别人，这就叫**控制反转（Inverse of Control, IOC）**。而<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">BeanFactory</text>根据指定的<text style="color:yellow;background-color:blue;font-style:italic;font-weight:bold">beanName</text>去获取和创建对象的过程，就可以称作：**依赖查找（Dependency Lookup, DL）**。

### IOC的实现方式有哪些？
一种是**依赖查找**，另一种是**依赖注入**。
- 依赖查找：根据类型查找，创建的Bean是不带属性的。
- 依赖注入：创建的Bean是带属性的。  

【面试题】依赖查找与依赖注入的对比
- 依赖查找（Dependency Lookup）
- 依赖注入（Dependency Lookup）
- **作用目标不同**
  - 依赖查找的作用目标可以是方法体内，也可以是方法体外
  - 依赖注入的作用目标通常是类成员
- **实现方式不同**
  - 依赖查找通常是主动使用上下文搜索
  - 依赖注入通常借助一个上下文被动的接收

### 注解驱动的IOC容器与XML驱动的IOC容器有什么不同？
**1.配置方式：**
 - 注解驱动的IOC容器：使用`@Component`、`@Service`、`@Repository`等注解，以及用`@Autowired`用于自动装配，通过在类或方法上添加注解来声明组件与之的关系，不需要单独配置一个XML文件。
 - XML驱动的IOC容器：使用单独的XML文档做配置文件，需明确定义组件、属性、依赖关系等。
  
**2.可读性：**
 - 注解驱动的IOC容器：使用注解的代码可读性更高，与组件的依赖关系直接可见，因此维护性强。
 - XML驱动的IOC容器：使用外部化配置，更灵活，但不直观，降低可读性。

**3.编译检查：**
 - 注解驱动的IOC容器：基于Java代码，编译时进行类型检查，提供更好的类型安全性。
 - XML驱动的IOC容器：XML配置是运行时解析的，因此配置错误只有在运行时才会发现。