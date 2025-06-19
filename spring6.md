[toc]
# Spring 6
## 概述
### Spring 简介
Spring 是一款主流的 Java EE 轻量级开源框架 ，Spring 由 "spring之父" Rod Johnson 提出并创立，其目的是用于简化 Java 企业级应用的开发难度和开发周期。Spring 的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何 Java 应用都可以从 Spring 中受益。Spring 框架除了自己提供功能外，还提供整合其他技术和框架的能力。

Spring 自诞生以来备受青睐，一直被广大开发人员作为 Java 企业级应用程序开发的首选。时至今日，Spring 俨然成为了 Java EE 代名词，成为了构建 Java EE 应用的事实标准。

### Spring Framework
Spring 框架是一个分层的、面向切面的 Java 应用程序的一站式轻量级解决方案，它是 Spring 技术栈的核心和基础，是为了解决企业级应用开发的复杂性而创建的。

Spring 有两个最核心模块: **IoC** 和 **AOP**。

**loC：Inverse of Control** 的简写，译为 “控制反转”，指把创建对象过程交给 Spring 进行管理
**AOP：Aspect Oriented Programming** 的简写，译为 “面向切面编程"。AOP 用来封装多个类的公共行为，将那些与业务无关，却为业务模块所共同调用的逻辑封装起来，减少系统的重复代码，降低模块间的耦合度。另外AOP 还解决一些系统层面上的问题，比如日志、事务、权限等。

### Spring 特点
- 非侵入式：使用 Spring Framework 开发应用程序时，Spring对应用程序本身的结构影响非常小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会破坏原有结构，反而能将组件结构进一步简化。这就使得基于 Spring Framework 开发应用程序时结构清晰、简洁优雅。
- 控制反转：IOC--Inversion of Control，翻转资源获取方向。把自己创建资源、向环境索取资源变成环境将资源准备好，我们享受资源注入。
- 面向切面编程：AOP--Aspect Oriented Programming，在不修改源代码的基础上增强代码功能。
- 容器：Spring IOC 是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发效率。
- 组件化：Spring实现了使用简单的组件配置组合成一个复杂的应用。在 Spring 中可以使用 XML 和 Java 注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭建超大型复杂应用系统。
- 一站式：在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库。而且 Spring 旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在 Spring Framework 的基础上全部使用 Spring 来实现。

## 容器：IoC
IoC 是 Inversion of Control 的简写，译为 “控制反转"，它不是一门技术，而是一种**设计思想**，是一个重要的面向对象编程法则，能够指导我们如何设计出松耦合、更优良的程序。

Spring 通过 **IoC 容器**来管理所有 Java 对象的实例化和初始化，控制对象与对象之间的依赖关系。我们将由 IoC 容器管理的 Java 对象称为 Spring Bean，它与使用关键字 new 创建的 Java 对象没有任何区别。

IoC 容器是 Spring 框架中最重要的核心组件之一，它贯穿了 Spring 从诞生到成长的整个过程。
### IoC 容器
#### 控制反转（IoC）
控制反转是一种思想，控制反转是为了降低程序耦合度，提高程序扩展力。

控制反转，反转的是什么？
1. 将对象的创建权利交出去，交给第三方容器负责。
2. 将对象和对象之间关系的维护权交出去，交给第三方容器负责。

控制反转这种思想如何实现呢？
DI（Dependency Injection）：依赖注入
#### 依赖注入
DI（Dependency Injection）依赖注入，依赖注入实现了控制反转的思想。指 Spring 创建对象的过程中，将对象依赖属性通过配置进行注入。

依赖注入常见的实现方式包括两种：set注入和构造注入。

Spring 的 IoC 容器就是 IoC 思想的一个落地的产品实现。IoC 容器中管理的组件也叫做 bean。在创建 bean 之前，首先需要创建 IoC 容器。Spring 提供了 IoC 容器的两种实现方式：BeanFactory 是 IoC 容器的基本实现，是 Spring 内部使用的接口。面向 Spring 本身，不提供给开发人员使用；ApplicationContext，BeanFactory 的子接口，提供了更多高级特性。面向 Spring 的使用者，几乎所有场合都使用 ApplicationContext 而不是底层的 BeanFactory。

ApplicationContext的主要实现类：
| 类型名                             | 简介                                                                 |
|----------------------------------|----------------------------------------------------------------------|
| ClassPathXmlApplicationContext   | 通过读取类路径下的 XML 格式的配置文件创建 IOC 容器对象               |
| FileSystemXmlApplicationContext  | 通过文件系统路径读取 XML 格式的配置文件创建 IOC 容器对象             |
| ConfigurableApplicationContext   | ApplicationContext 的子接口，包含一些扩展方法 refresh() 和 close()，让 ApplicationContext 具有启动、关闭和刷新上下文的能力。 |
| WebApplicationContext            | 专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对象，并将对象引入存入 ServletContext 域中。 |

#### 基于 XML 管理 Bean
##### 获取 Bean
```java
ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
// 根据 bean.xml 中的 id 获取对象
User user1 = (User) context.getBean("user");
// 根据类类型获取对象
User user2 = context.getBean(User.class);
// 根据 id 和类类型获取对象
User user3 = context.getBean("user", User.class);
```
当根据类型获取bean时，要求IOC容器中指定类型的bean有且只能有一个，否则会抛异常。
当 xml 中一共配置了两个类型一致的 Bean 时， 根据类型获取 Bean 会抛异常：
```xml
<bean id="user1" class="User"></bean>
<bean id="user2" class="User"></bean>
```

> 注意：
1.当根据类型获取bean时，要求IOC容器中指定类型的bean有且只能有一个。
2.如果类实现了接口，只有 Bean 唯一时接口类型可以获取 Bean 。

##### 依赖注入
###### setter 注入
第一步：实现类的 set 方法（注意也必须要有无参构造函数）；
```java
public void setName(String name) {
    this.name = name;
}

public void setAge(int age) {
    this.age = age;
}
```
第二步：配置 bean.xml 。
```xml
<bean id="user1" class="com.spring6.iocxml.User">
    <property name="age" value="17"></property>
    <property name="name" value="sophon"></property>
</bean>
```
###### 构造器注入
第一步：实现类的有参构造方法；
```java
public User(String name, int age) {
    System.out.println("带参构造调用...");
    this.age = age;
    this.name = name;
}
```
第二步：配置 bean.xml 。
```xml
<bean id="user2" class="com.spring6.iocxml.User">
    <constructor-arg name="name" value="nohpos"></constructor-arg>
    <constructor-arg name="age" value="19"></constructor-arg>
</bean>
```
> 注意：默认情况下，Spring 容器在初始化时会立即创建所有配置的单例（singleton）bean。并且这里的单例是指 id 唯一，而不是类唯一。

###### 特殊值处理
1.属性赋值为 null。
```xml
<property name="name">
    <null />
</property>
```
2.xml实体
```xml
<!-- 小于大于号在XML文档中用来定义标签的开始，不能随便使用 -->
<!-- 解决方案一：使用XML实体来代替。其中：&lt; 表示 '<' &gt; 表示 '>' -->
<property name="expression" value="a &lt; b"/>
```
3.CDATA节
```xml
<property name="expression">
    <!-- 解决方案二：使用CDATA节 -->
    <!-- CDATA中的C代表Character，是文本、字符的含义，CDATA就表示纯文本数据 -->
    <!-- XML解析器看到CDATA节就知道这里是纯文本，就不会当作XML标签或属性来解析 -->
    <!-- 所以CDATA节中写什么符号都随意 -->
    <value><![CDATA[a < b]]></value>
</property>
```

###### 为对象类型属性赋值
1.外部 Bean
配置 Birthday 类型的 bean：
```xml
<bean name="birthday" class="com.spring6.iocxml.Birthday">
    <property name="year" value="2025"></property>
    <property name="month" value="12"></property>
    <property name="day" value="31"></property>
</bean>
```
引用外部对象：
```xml
<bean name="user" class="com.spring6.iocxml.User">
    <property name="name" value="zhangsan"></property>
    <property name="age" value="0"></property>
    <property name="birthday" ref="birthday"></property>
    <!--还可以修改类属性-->
    <property name="birthday.year" value="2000"></property>
</bean>
```
2.内部 Bean
```xml
<bean name="user" class="com.spring6.iocxml.User">
    <property name="name" value="zhangsan"></property>
    <property name="age" value="0"></property>
    <property name="birthday">
        <!-- 在一个 Bean 中再声明一个 Bean 就是内部 Bean -->
        <!-- 内部 Bean 只能用于给属性赋值，不能在外部通过 IOC 容器获取，因此可以省略 id 属性 -->
        <bean class="com.spring6.iocxml.Birthday">
            <property name="year" value="2025"></property>
            <property name="month" value="12"></property>
            <property name="day" value="31"></property>
        </bean>
    </property>
</bean>
```
###### 为数组类型赋值
```xml
<property name="hobbies">
    <array>
        <value>抽烟</value>
        <value>喝酒</value>
        <value>烫头</value>
    </array>
</property>
```
###### 为集合类型赋值
1.为List集合类型属性赋值
```xml
<!--  预先准备好集合内部类型的 Bean（注意：也可以内部 Bean）
<bean name="addr1" class="com.spring6.iocxml.Addr">
    <property name="province" value="陕西"></property>
    <property name="city" value="西安"></property>
</bean>

<bean name="addr2" class="com.spring6.iocxml.Addr">
    <property name="province" value="黑龙江"></property>
    <property name="city" value="哈尔滨"></property>
</bean>
 -->
<property name="addrList">
    <list>
        <ref bean="addr1"></ref>
        <ref bean="addr2"></ref>
    </list>
</property>
```
> 注意：若为 Set 集合类型属性赋值，只需要将其中的 list 标签改为 set 标签即可。

2.为Map集合类型属性赋值
```xml
<property name="postAddress">
    <map>
        <entry>
            <key>
                <value>京东</value>
            </key>
            <ref bean="addr1"></ref>
        </entry>
        <entry>
            <key>
                <value>拼多多</value>
            </key>
            <ref bean="addr2"></ref>
        </entry>
    </map>
</property>
```
3.引用集合类型的bean
```xml
<!--  预先准备好集合内部类型的 Bean（注意：也可以内部 Bean）
<bean name="addr1" class="com.spring6.iocxml.Addr">
    <property name="province" value="陕西"></property>
    <property name="city" value="西安"></property>
</bean>

<bean name="addr2" class="com.spring6.iocxml.Addr">
    <property name="province" value="黑龙江"></property>
    <property name="city" value="哈尔滨"></property>
</bean>
 -->

<util:list id="hobbiesList">
    <value>抽烟</value>
    <value>喝酒</value>
    <value>烫头</value>
</util:list>

<util:map id="addrMap">
    <entry>
        <key>
            <value>京东</value>
        </key>
        <ref bean="addr1"></ref>
    </entry>
    <entry>
        <key>
            <value>拼多多</value>
        </key>
        <ref bean="addr2"></ref>
    </entry>
</util:map>

<bean name="user" class="com.spring6.iocxml.User">
    <property name="name" value="zhangsan"></property>
    <property name="age" value="18"></property>
    <property name="hobbies" ref="hobbiesList"></property>
    <property name="postAddress" ref="addrMap"></property>
</bean>
```
> 注意：使用 util:list、util:map 标签必须引入相应的命名空间。
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>        xmlns:util="http://www.springframework.org/schema/util"
>        xsi:schemaLocation="http://www.springframework.org/schema/util
>        http://www.springframework.org/schema/util/spring-util.xsd
>        http://www.springframework.org/schema/beans
>        http://www.springframework.org/schema/beans/spring-beans.xsd">
> ```

###### p 命名空间
> 引入 p 命名空间
> ```xml
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>        xmlns:util="http://www.springframework.org/schema/util"
>        xmlns:p="http://www.springframework.org/schema/p"
>        xsi:schemaLocation="
>         http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
>         http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
> ```
引入p命名空间后，可以通过以下方式为bean的各个属性赋值：
```xml
<bean name="user" class="com.spring6.iocxml.User"
    p:name="zhangsan" p:age="18" p:birthday-ref="birthday" p:hobbies="hobbiesList" p:postAddress-ref="addrMap">
</bean>
```

###### 引入外部属性文件
1.加入依赖
```xml
 <!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.30</version>
</dependency>

<!-- 数据源 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.15</version>
</dependency>
```
2.创建外部属性文件 jdbc.properties
```properties
jdbc.user=root
jdbc.password=1234
jdbc.url=jdbc:mysql://localhost:3306/spring?serverTimezone=UTC
jdbc.driver=com.mysql.cj.jdbc.Driver
```
3.引入 context 命名空间，引入外部属性文件，配置 bean 。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClassName" value="${jdbc.driver}"></property>
    </bean>

</beans>
```
##### Bean 的作用域
在 Spring 中可以通过配置 Bean 标签的 scope 属性来指定 bean 的作用域范围，各取值含义参加下表：
| 取值         | 含义      | 创建对象的时机         |
|--------------|-----------|-----------------|
| singleton（默认） | 在IOC容器中，这个bean的对象始终为单实例 | IOC容器初始化时     |
| prototype    | 这个bean在IOC容器中有多个实例       | 获取bean时         |
如果是在WebApplicationContext环境下还会有另外几个作用域（但不常用）：
| 取值    | 含义          |
|---------|---------------------|
| request | 在一个请求范围内有效     |
| session | 在一个会话范围内有效     |

##### Bean 的生命周期
具体生命周期过程
1. bean 对象创建（调用无参构造器）。
2. 给 bean 对象设置属性。
3. bean 的后置处理器（初始化之前）。
4. bean 对象初始化（需在配置 bean 时指定初始化方法）。
5. bean 的后置处理器（初始化之后）。
6. bean 对象就绪可以使用。
7. bean 对象销毁（需在配置 bean 时指定销毁方法）。
8. IOC 容器关闭。

##### FactoryBean
> 区分：BeanFactory: Spring 实现 IoC 容器的顶层接口。

FactoryBean 是 Spring 提供的一种整合第三方框架的常用机制。和普通的 bean 不同，配置一个 FactoryBean 类型的 bean，在获取 bean 的时候得到的并不是 class 属性中配置的这个类的对象，而是 getObject() 方法的返回值。通过这种机制，Spring 可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。

将来整合 Mybatis 时，Spring 就是通过 FactoryBean 机制来帮我们创建 SqlSessionFactory 对象的。

第一步：创建类 UserFactoryBean
```java
public class UserFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        return new User();
    }

    @Override
    public Class<?> getObjectType() {
        return User.class;
    }
}
```
第二步：配置 bean
```xml
<!--如果我们想要获取 FactoryBean 本身，可以在 id 前加上 & 符号-->
<bean name="userFactoryBean" class="com.spring6.iocxml.UserFactoryBean"></bean>
```
第三步：测试
```java
@Test
public void test() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    // getBean 得到的是 User 对象，而非 userFactoryBean 对象
    User userFactoryBean = (User)context.getBean("userFactoryBean");
    System.out.println(userFactoryBean);
}
```
##### 基于 xml 的自动装配
自动装配：根据指定的策略，在 IOC 容器中匹配某一个 bean，自动为指定的 bean 中所依赖的类类型或接口类型属性赋值。

使用 bean 标签的 autowire 属性设置自动装配效果。
自动装配方式：byType
```xml
<bean id="userController" class="com.spring6.auto.controller.UserController" autowire="byType">
</bean>
<bean id="userService" class="com.spring6.auto.service.impl.UserServiceImpl" autowire="byType">
</bean>
<bean id="userDao" class="com.spring6.auto.dao.impl.UserDaoImpl">
</bean>
```
byType：根据类型匹配 IOC 容器中的某个兼容类型的 bean，为属性自动赋值
若在 IOC 中，没有任何一个兼容类型的 bean 能够为属性赋值，则该属性不装配，即值为默认值 null。
若在 IOC 中，有多个兼容类型的 bean 能够为属性赋值，则抛出异常 NoUniqueBeanDefinitionException。

自动装配方式：byName
```xml
<bean id="userController" class="com.spring6.auto.controller.UserController" autowire="byName">
</bean>
<bean id="userService" class="com.spring6.auto.service.impl.UserServiceImpl" autowire="byName">
</bean>
<bean id="userDao" class="com.spring6.auto.dao.impl.UserDaoImpl">
</bean>
```
byName：将自动装配的属性的属性名，作为 bean 的 id 在 IOC 容器中匹配相对应的 bean 进行赋值。

#### 基于注解管理 Bean
从 Java 5 开始，Java 增加了对注解（Annotation）的支持，它是代码中的一种特殊标记，可以在编译、类加载和运行时被读取，执行相应的处理。开发人员可以通过注解在不改变原有代码和逻辑的情况下，在源代码中嵌入补充信息。

Spring 从 2.5 版本开始提供了对注解技术的全面支持，我们可以使用注解来实现自动装配，简化 Spring 的 XML 配置。

Spring 通过注解实现自动装配的步骤如下：
1. 引入依赖。
2. 开启组件扫描。
3. 使用注解定义 Bean 。
4. 依赖注入。

##### 组件扫描
Spring 默认不使用注解装配 Bean，因此我们需要在 Spring 的 XML 配置中，通过 context:component-scan 元素开启 Spring Beans的自动扫描功能。开启此功能后，Spring 会自动从扫描指定的包（base-package 属性设置）及其子包下的所有类，如果类上使用了 @Component 注解，就将该类装配到容器中。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启组件扫描功能-->
    <context:component-scan base-package="com.atguigu.spring6"></context:component-scan>
</beans>
```
> 注意：在使用 context:component-scan 元素开启自动扫描功能前，首先需要在 XML 配置的一级标签 中添加 context 相关的约束。

**扫描方式一：全包扫描**
```xml
<context:component-scan base-package="com.spring6">
</context:component-scan>
```

**扫描方式二**
```xml
<context:component-scan base-package="com.spring6">
    <!-- context:exclude-filter标签：指定排除规则 -->
    <!-- 
 		type：设置排除或包含的依据
		type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
		type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
	-->
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--<context:exclude-filter type="assignable" expression="com.spring6.controller.UserController"/>-->
</context:component-scan>
```

**扫描方式三**
```xml
<context:component-scan base-package="com" use-default-filters="false">
    <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
    <!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
    <!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
    <!-- 
 		type：设置排除或包含的依据
		type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
		type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
	-->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	<!--<context:include-filter type="assignable" expression="com.spring6.controller.UserController"/>-->
</context:component-scan>
```
##### 使用注解定义 Bean
Spring 提供了以下多个注解，这些注解可以直接标注在 Java 类上，将它们定义成 Spring Bean。
| 注释         | 说明                            |
|--------------|---------------------------------------------------------------------|
| @Component   | 该注解用于描述 Spring 中的 Bean，它是一个泛化的概念，仅仅表示容器中的一个组件（Bean），并且可以作用在应用的任何层次，例如 Service 层、Dao 层等。使用时只需将该注解标注在相应类上即可。 |
| @Repository  | 该注解用于将数据访问层（Dao 层）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。               |
| @Service     | 该注解通常作用在业务层（Service 层），用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
| @Controller  | 该注解通常作用在控制层（如 SpringMVC 的 Controller），用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
例如：
```java
// 不设置注解 value 默认为类名首字母小写
@Component
public class User {
}
/*
测试代码（注意要提前配置组件扫描）
@Test
public void test() {
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    User user = (User)context.getBean("user");
    System.out.println(user);
}
*/
```

##### @Autowired 注入
单独使用 @Autowired 注解，默认根据类型装配（默认 byType）。且不需要提供 set 方法。

**属性注入**示例：
```java
@Controller
public class UserController {
    // 自动装配 userService
    @Autowired
    private UserService userService;

    public void addUser() {
        System.out.println("UserController addUser ...");
        userService.addUserService();
    }
}

/*
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;

    @Override
    public void addUserService() {
        System.out.println("UserServiceImpl addUserService ...");
        userDao.add();
    }
}
*/

/*
@Repository
public class UserDaoImpl implements UserDao {
    @Override
    public void add() {
        System.out.println("UserDaoImpl add...");
    }
}
*/
```
除了直接属性注入以外，还可以 set 注入、构造方法注入、形参上注入等，当组件类中只有一个有参构造函数时 @Autowired 注解可以省略。

如果一个接口拥有多个实现类，例如 UserDaoImpl 和 UserDaoRedisImpl 都实现了 UserDao 接口则会报错，因为 @Autowired 注解默认根据类型装配。此时可以搭配 @Qualifier 注解实现根据名称匹配（byName）。
```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    @Qualifier(value = "userDaoRedisImpl")
    private UserDao userDao;

    @Override
    public void addUserService() {
        System.out.println("UserServiceImpl addUserService ...");
        userDao.add();
    }
}
```

















