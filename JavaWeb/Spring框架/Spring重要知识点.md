# Spring基础

## 一、Spring演变

### 配置方式的变化

1. xml配置，将配置文件写成xxx.xml

2. 注解配置：在Spring 2.x时代，随着JDK 1.5带来的注解支持，Spring提供了声明Bean的注解（如@Component、@Service），大大减少了配置量。应用的基本配置（如数据库配置）用xml，业务配置用注解。

3. Java配置：从Spring 3.x到现在，Spring提供了Java配置的能力，使用Java配置可以让你更理解你配置的Bean。

### 概述

> Spring框架是一个轻量级的企业级开发的一站式解决方案。所谓解决方案就是可以基于Spring解决Java EE开发的所有问题。Spring框架主要提供了IoC容器、AOP、数据访问、Web开发、消息、测试等相关技术的支持。
>
> Spring使用简单的POJO（Plain Old Java  Object，即无任何限制的普通Java对象）来进行企业级开发。每一个被Spring管理的Java对象都称之为Bean；而Spring提供了一个IoC容器用来初始化对象，解决对象间的依赖管理和对象的使用。

1. Spring模块

<img src="C:\Users\Administrator\Desktop\zhangs\JavaWeb\imges\Spring模块.jpg" alt="Spring模块" />

Spring是模块化的，上图中每一个最小单元，Spring都至少有一个对应的jar包。

- 核心容器（Core Container）
  - Spring-Core：核心工具类，Spring其他模块大量使用Spring-Core；
  - Spring-Beans：Spring定义Bean的支持；
  - Spring-Context：运行时Spring容器；
  - Spring-Context-Support：Spring容器对第三方包的集成支持；
  - Spring-Expression：使用表达式语言在运行时查询和操作对象。
- AOP
  - Spring-AOP：基于代理的AOP支持；
  - Spring-Aspects：基于AspectJ的AOP支持。
- 消息（Messaging）
  - Spring-Messaging：对消息架构和协议的支持。
- Web
  - Spring-Web：提供基础的Web集成的功能，在Web项目中提供Spring的容器；
  - Spring-Webmvc：提供基于Servlet的Spring MVC；
  - Spring-WebSocket：提供WebSocket功能；
  - Spring-Webmvc-Portlet：提供Portlet环境支持。
- 数据访问/集成（Data Access/Integration）
  - Spring-JDBC：提供以JDBC访问数据库的支持；
  - Spring-TX：提供编程式和声明式的事务支持；
  - Spring-ORM：提供对对象/关系映射技术的支持；
  - Spring-OXM：提供对对象/xml映射技术的支持；
  - Spring-JMS：提供对JMS的支持。

2. Spring生态

   - Spring Boot：使用默认开发配置来实现快速开发。
   - Spring XD：用来简化大数据应用开发。
   - Spring Cloud：为分布式系统开发提供工具集。
   - Spring Data：对主流的关系型和NoSQL数据库的支持。
   - Spring Integration：通过消息机制对企业集成模式（EIP）的支持。
   - Spring Batch：简化及优化大量数据的批处理操作。
   - Spring Security：通过认证和授权保护应用。
   - Spring HATEOAS：基于HATEOAS原则简化REST服务开发。
   - Spring Social：与社交网络API（如Facebook、新浪微博等）的集成。
   - Spring AMQP：对基于AMQP的消息的支持。
   - Spring Mobile：提供对手机设备检测的功能，给不同的设备返回不同的页面的支持。
   - Spring for Android：主要提供在Android上消费RESTful API的功能。
   - Spring Web Flow：基于Spring MVC提供基于向导流程式的Web应用开发。
   - Spring Web Services：提供了基于协议有限的SOAP/Web服务。
   - Spring LDAP：简化使用LDAP开发。
   - Spring Session：提供一个API及实现来管理用户会话信息。

3. 项目搭建

   使用Maven、Gradle等构建工具。

### Spring基础配置

Spring框架本身有四大原则：

1. 使用POJO进行轻量级和最小侵入式开发。
2. 通过依赖注入和基于接口编程实现松耦合。
3. 通过AOP和默认习惯进行声明式编程。
4. 使用AOP和模板（template）减少模式化代码。

Spring所有功能的设计和实现都是基于此四大原则的。

#### 依赖注入

> 控制翻转（Inversion of Control-IOC）和依赖注入（dependency injection-DI）在Spring环境下是等同的概念，控制翻转是通过依赖注入实现的。所谓依赖注入指的是容器负责创建对象和维护对象间的依赖关系，而不是通过对象本身负责自己的创建和解决自己的依赖。
>
> 依赖注入的主要目的是为了解耦，体现了一种“组合”的理念。如果你希望你的类具备某项功能的时候，是继承自一个具有此功能的父类好呢？还是组合另外一个具有这个功能的类好呢？答案是不言而喻的，继承一个父类，子类将与父类耦合，组合另外一个类则使耦合度大大降低。
>
> Spring IoC容器（ApplicationContext）负责创建Bean，并通过容器将功能类Bean注入到你需要的Bean中。Spring提供使用xml、注解、Java配置、groovy配置实现Bean的创建和注入。
>
> 无论是xml配置、注解配置还是Java配置，都被称为配置元数据，所谓元数据即描述数据的数据。元数据本身不具备任何可执行的能力，只能通过外界代码来对这些元数据行解析后进行一些有意义操作。Spring容器解析这些配置元数据进行Bean初始化、配置和管理依赖。

声明Bean的注解：

- @Component组件，没有明确的角色。
- @Service在业务逻辑层（service层）使用。
- @Repository在数据访问层（dao层）使用。
- @Controller在展现层（MVC→Spring MVC）使用。

注入Bean的注解，一般情况下通用:

- @Autowired：Spring提供的注解。
- @Inject：JSR-330提供的注解。
- @Resource：JSR-250提供的注解。

@Autowired、@Inject、@Resource可注解在set方法上或者属性上，注解在属性上使代码更少、层次更清晰。