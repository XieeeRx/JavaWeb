# IOC
### IOC是什么？
IOC，**控制反转**，Inverse of Control，是一种**编程原则**，它的设计和架构可以**实现组件间的解耦**，核心思想是**将控制权转移出去**。  
- 编程原则：不是某种落地技术，是一种理论。
- 组件间的解耦：
  - 什么是解耦？就是**解除耦合**，**“耦合”就是代码模块之间相互依赖的程度**。高度耦合就是关系紧密，关系越紧密，双方（或多方）之间的影响就更深，受桎梏的越多。
  - 为什么要解耦？我们一般追求**低耦合**，所以要解耦，即解除对象间的依赖关系，**减少对象间的互相影响**，增强它们的独立性。
- 如何将控制权转移出去：将原有的对象间的**主动依赖改为被动接受型依赖**。

### 怎么使用IOC？
**例子如下(Java)：**
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

### 详解-依赖注入
正如我们前面所强调的，依赖注入（Dependency Injection, DI）是一种软件**设计模式**。用于管理和处理**组件之间的关系**。
*核心思想*：将依赖项注入到需要它们的对象中，而不是在对象内部直接创建这些依赖项，有助于**降低**组件之间的**耦合度**。主要通过以下方式实现：  
**1. 属性[setter]注入：**
- **一种是基于xml的setter注入方式：**
```Java {.line-numbers}
<bean id="person" class="com.linkedbear.spring.basic_di.a_quickstart_set.bean.Person">
    <property name="name" value="test-person-byset"/>
    <property name="age" value="18"/>
</bean>
```
- **一种是注解形式的setter注入方式：**
```Java {.line-numbers}
@Bean
public Person person() {
    Person person = new Person();
    person.setName("test-person-anno-byset");
    person.setAge(18);
    return person;
}
```
**2. 构造器注入：**
当一些bean本身没有无参构造器时如下：
```Java {.line-numbers}
public Person(){

}
```
这时我们就要使用构造器注入进行修改：
```Java {.line-numbers}
public Person(String name, Integer age){
  this.name = name;
  this.age = age;
}
```
以上的情况会使原来的`bean`标签创建失效，提示没有默认的构造方法，所以我们采用xml方式的构造器注入和注解式的构造器属性注入。
- **xml方式的构造器注入：**
```Java {.line-numbers}
<bean id="person" class="com.linkedbear.spring.basic_di.b_constructor.bean.Person">
    <constructor-arg index="0" value="test-person-byconstructor"/>
    <constructor-arg index="1" value="18"/>
</bean>
```
顾名思义，在bean标签内声明一个子标签constructor-arg，指构造器参数，可指定构造器中属性并进行属性注入。
- **注解式构造器属性注入：**
```Java {.line-numbers}
@Bean
public Person person() {
    return new Person("test-person-anno-byconstructor", 18);
}
```
注解驱动的bean注册中，也是直接使用编程式赋值即可。  
**3.注解式属性注入：**
- **@Component下的属性注解：**
```Java {.line-numbers}
@Component
public class Black {
    @Value("black-value-anno")
    private String name;
    @Value("0")
    private Integer order;
    
    @Override
    public String toString() {
        return "Black{" + "name='" + name + '\'' + ", order=" + order + '}';
    }
}
```
我们在新建类，并声明`name`和`order`后，直接在要注入的字段上标注`@Value`注解，最后通过组件扫描到IOC容器即可。
- **外部配置文件引入-@PropertySource:**
如上，新建一个新的类，声明`name`和`order`后，并进行注解：
```Java {.line-numbers}
 @Component
public class Red {
    
    @Value("${red.name}")
    private String name;
    
    @Value("${red.order}")
    private Integer order;
    
    @Override
    public String toString() {
        return "Red{" + "name='" + name + '\'' + ", order=" + order + '}';
    }
}
```
同时在resources目录下新建一个此类的properties
```{.line-numbers}
red.name=red-value-byproperties
red.order=1
```
然后在执行类中将`@PropertySource`和组件扫描`ComponentScan`用上即可。
```Java {.line-numbers}
@Configuration
// 顺便加上包扫描
@ComponentScan("com.linkedbear.spring.basic_di.c_value_spel.bean")
@PropertySource("classpath:basic_di/value/red.properties")
public class InjectValueConfiguration {
    
}
```
作为一个**properties文件**，它加载到 SpringFramework 的 IOC 容器后，会**转换成 Map 的形式**来保存这些配置，而 SpringFramework 中本身在初始化时就有一些配置项，这些配置项也都放在这个 Map 中。**占位符的取值**就是从这些配置项中取。
- **如何使用SpEL表达式？**
如果咱要在属性注入时，使用一些特殊的数值（如一个 Bean 需要依赖另一个 Bean 的某个属性，或者需要**动态处理**一个特定的属性值），这种情况 ${} 的占位符方式就办不了了（占位符只能取配置项），需要一种更强大的表达方式来满足这种需求，这种表达方式就是 **SpEL 表达式**。
SpEL 的语法统一用 #{} 表示，花括号内部编写表达式语言。
```Java {.line-numbers}
@Component
public class Blue {
    
    @Value("#{'blue-value-byspel'}")
    private String name;
    
    @Value("#{2}")
    private Integer order;
```
还能这样用：
```Java {.line-numbers}
@Component
public class Green {
    
    @Value("#{'copy of ' + blue.name}")
    private String name;
    
    @Value("#{blue.order + 1}")
    private Integer order;
```
xml中的使用方法：
```Java {.line-numbers}
<bean class="com.linkedbear.spring.basic_di.c_value_spel.bean.Green">
    <property name="name" value="#{'copy of ' + blue.name}"/>
    <property name="order" value="#{blue.order + 1}"/>
</bean>
```
