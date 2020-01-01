# Spring MVC4.x

## 一、Spring MVC基础

> SSH指：Struts1.x + Spring +Hibernate
>
> S2SH指：Struts 2.x+Spring+Hibernate

### 一、概述

MVC：Model+View+Controller（数据模型+视图+控制器）

三层架构：Presentation tier+Application tier+Data tier（展现层+应用层+数据访问层）

- 三层架构是整个应用的架构，是由Spring框架负责管理的。一般项目结构中都有Service层、DAO层，这两个反馈在应用层和数据访问层。

关系：MVC只存在三层架构的展现层

Spring MVC：

- M：数据模型，包含数据的对象，在Spring MVC中，有一个专门的类叫Model，用来和V进行数据交互，传值；
- V：视图页面，包含JSP、freeMarker、Velocity、Thymeleaf、Tile等；
- C：控制器（Spring MVC的注解@Controller的类）。

### 二、Spring MVC项目快速搭建

> Spring MVC提供了一个DispatcherServlet来开发Web应用。在Servlet 2.5及以下的时候只要在web.xml下配置<servlet>元素即可。
>
> Servlet 3.0+无web.xml的配置方式，在Spring MVC里实现WebApplicationInitializer接口便可实现等同于web.xml的配置。

示例：

1. 构建Maven项目: [构建spring MVC需要的maven配置](./构建SpringMVC项目的maven配置.md)

2. 日志配置

   ```java
   //配置在src/main/resources目录下，新建logback.xml用来配置日志，内容如下：
   <?xml version="1.0" encoding="UTF-8"?> 
   <configuration scan="true" scanPeriod="1 seconds"> 
       <contextListener class="ch.qos.logback.classic.jul.LevelChangePropagator"> 
           <resetJUL>true</resetJUL> 
       </contextListener> 
     
       <jmxConfigurator/> 
       <appender name="console" class="ch.qos.logback.core.ConsoleAppender"> 
           <encoder> 
               <pattern>logbak: %d{HH:mm:ss.SSS} %logger{36} - %msg%n</pattern> 
           </encoder> 
       </appender>
       <!-- 将org.springframework.web包下的类的日志级别设置为DEBUG，我们开发Spring MVC经常出现和参数类型相关的4XX错误，设置此项我们会看到更详细的错误信息。--> 
         <logger name="org.springframework.web" level="DEBUG"/>      
       <root level="info"> 
           <appender-ref ref="console"/> 
       </root> 
     </configuration> 
   ```
   
3. 演示页面

   ```java
   //在src/main/resources(Spring Boot页面放置方式)下建立views目录，
   // 并在此目录下新建index.jsp，内容如下：
   <%@ page language="java" contentType="text/html; charset=UTF-8" 
       pageEncoding="UTF-8"%> 
   <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"> 
   <html> 
   <head> 
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
   <title>Insert title here</title> 
   </head> 
   <body> 
       <pre> 
           Welcome to Spring MVC world 
       </pre> 
   </body> 
   </html> 
   ```

4. Spring MVC配置

   ```java
   /*
   *  此处配置了JSP的ViewResolver，用来映射路径和实际页面的位置，@EnableWebMVC注解用来开启一些默认
   * 配置，如一些ViewResolver或MessageContext等
   *   Spring MVC中的ViewResolver，这是Spring MVC视图(JSP下就是html)渲染的核心机制；
   *Spring MVC里有一个接口叫做ViewResolver（我们的ViewResolver都实现该接口），实现这个接
   *口要重写方法resolveViewName（），这个方法的返回值是接口View，而View的职责就是使用model、
   *request、response对象，并将渲染的视图（不一定是html，可能是json、xml、pdf）返回给浏览器。
   *    
   *  路径前缀配置为/WEB-INF/classes/views/，跟开发目录不一致是因为运行时代码会将我们的页面自动
   *编译到/WEB-INF/classes/views/下。
   */
   
   package com.zhangs.springmvc;
   
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.web.servlet.config.annotation.EnableWebMvc;
   import org.springframework.web.servlet.view.InternalResourceViewResolver;
   import org.springframework.web.servlet.view.JstlView;
   
   @Configuration
   @EnableWebMvc
   @ComponentScan("com.zhangs.springmvc")
   public class MyMvcConfig {
       @Bean
       public InternalResourceViewResolver viewResolver(){
           InternalResourceViewResolver viewResolver=new
                   InternalResourceViewResolver();
           viewResolver.setPrefix("/WEB_INF/classes/views/");
           viewResolver.setSuffix(".jsp");
           viewResolver.setViewClass(JstlView.class);
           return viewResolver;
       }
   }
   ```

5. Web配置

   > ​        WebApplicationInitializer是Spring提供用来配置Servlet 3.0+配置的接口，从而实现了替代web.xml的位置。实现此接口将会自动被SpringServletContainerInitializer（用来启动Servlet3.0容器）获取到。

   ```java
   package com.zhangs.springmvc;
   
   import org.springframework.web.WebApplicationInitializer;
   import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
   import org.springframework.web.servlet.DispatcherServlet;
   
   import javax.servlet.ServletContext;
   import javax.servlet.ServletException;
   import javax.servlet.ServletRegistration.Dynamic;
   
   public class WebInitializer implements WebApplicationInitializer {
       @Override
       public void onStartup(ServletContext servletContext) throws ServletException {
           AnnotationConfigWebApplicationContext ctx=new
                   AnnotationConfigWebApplicationContext();
           ctx.register(MyMvcConfig.class);
           //新建WebApplicationContext，注册配置类，并将其和当前servletContext关联。
           ctx.setServletContext(servletContext);
   		
           //注册Spring MVC的DispatcherServlet。
           Dynamic servlet =servletContext.addServlet("dispatcher",new DispatcherServlet(ctx));
           servlet.addMapping("/");
           servlet.setLoadOnStartup(1);
       }
   }
   ```

6. 简单控制器

   ```java
   package com.zhangs.springmvc.web;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.ResponseBody;
   
   //利用@Controller注解声明是一个控制器
   @Controller
   public class HelloController {
       //利用@RequestMapping配置URL和方法之间的映射
       @RequestMapping("/index")
   
       //@ResponseBody直接返回值作为页面。
       public String Hello(){
           //通过上面ViewResolver的Bean配置，返回值为index，说明我们的页面放置的
           //  路径为/WEB-INF/classes/views/index.jsp。
           return "index";
       }
   }
   
   ```

7. 运行

   将程序部署到Tomcat中，启动Tomcat，访问http://localhost:8080/springmvc/index