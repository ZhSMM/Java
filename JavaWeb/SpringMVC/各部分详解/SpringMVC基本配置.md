# Spring MVC基本配置

## MVC Java编程配置

> 要启用MVC Java编程配置，需要在Java配置类上增加@EnableWebMvc注解，代码如下：
>
> ```java
> @Configuration
> @EnableWebMvc
> public class webConfig{}
> ```
>
> 要启用XML命名空间，则需要在DispatcherServlet上下文环境中（如果没有定义DispatcherServlet上下文，那么就在根上下文中）添加一个`mvc:annnotation-driven`元素：
>
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>     xmlns:mvc="http://www.springframework.org/schema/mvc"
>     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>     xsi:schemaLocation="
>         http://www.springframework.org/schema/beans
>         http://www.springframework.org/schema/beans/spring-beans.xsd
>         http://www.springframework.org/schema/mvc
>         http://www.springframework.org/schema/mvc/spring-mvc.xsd">
>     
> 	<mvc:annotation-driven/>
> </beans>
> ```
>
> 上面的简单的声明代码，就已经默认注册了一个 RequestMappingHandlerMapping  、一个 RequestMappingHandlerAdapter  ，以及一个 ExceptionHandlerExceptionResolver  ，以支持对使用了 @RequestMapping  、 @ExceptionHandler  及其他注解的控制器方法的请求处理。

`<mvc:annotation-driver>`注册可用的HTTP消息转换器的完整列表：

1. 转换字节数组的 `ByteArrayHttpMessageConverter`；
2. 转换字符串的 `StringHttpMessageConverter` ;
3. `ResourceHttpMessageConverter`  ： `org.springframework.core.io.Resource ` 与所有媒体类型之间的互相转换;
4. `SourceHttpMessageConverter ` ：从（到） `javax.xml.transform.Source ` 的转换;
5. `FormHttpMessageConverte`r  ：数据与`MultiValueMap<String, String>`  之间的互相转换;
6. `Jaxb2RootElementHttpMessageConverter ` ：Java对象与XML之间的互相转换——该转换器
   在classpath路径下有JAXB2依赖并且没有Jackson 2 XML扩展时被注册;
7. `MappingJackson2HttpMessageConverter`  ：从（到）JSON的转换——该转换器在classpath
   下有Jackson 2依赖时被注册;
8. `MappingJackson2XmlHttpMessageConverter`  ：从（到）XML的转换——该转换器在
   classpath下有Jackson 2 XML扩展时被注册;
9. `AtomFeedHttpMessageConverter ` ：Atom源的转换——该转换器在classpath路径下有Rome
   时被注册;
10. `RssChannelHttpMessageConverter`  ：RSS源的转换——该转换器在classpath路径下有
    Rome时被注册。

> Jackson JSON和XML转换器是通过` Jackson2ObjectMapperBuilder ` 创建的 `ObjectMapper ` 实例创建的，目的在于提供更好的默认配置；该builder会使用以下的默认属性对Jackson进行配置：
> 1. 禁用 DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES
>
> 2. 禁用 MapperFeature.DEFAULT_VIEW_INCLUSION
>
>   同时，如果检测到在classpath路径下存在这些模块，该builder也会自动地注册它们：
>
> 3. jackson-datatype-jdk7: 支持Java 7的一些类型，例如 java.nio.file.Path
>
> 4. jackson-datatype-joda: 支持Joda-Time类型
>
> 5. jackson-datatype-jsr310: 支持Java 8的Date & Time API类型
>
> 6. jackson-datatype-jdk8: 支持Java 8其他的一些类型，比如 Optional  等

## 默认配置的定制

> 在MVC Java编程配置方式下，如果你想对默认配置进行定制，你可以自己实现 WebMvcConfigurer  接口，要么继承 WebMvcConfigurerAdapter  类并覆写你需要定制的方法：
>
> ```java
> @Configuration
> @EnableWebMvc
> public class WebConfig extends WebMvcConfigurerAdapter {
> // Override configuration methods...
> }
> ```

## 静态资源映射

> 程序的静态文件（js、css、图片）等需要直接访问，这时我们可以使MyMvcConfig继承、`WebMvcConfigurerAdapter`或者实现WebMvcConfigurer接口，在配置里重写`addResourceHandlers`方法来实现。

示例：

```java
1. 添加静态资源
在src/main/resources下建立assets/js目录，并复制一个jQuery.js放置在此目录下；
2. 配置代码

package com.wisely.highlight_springmvc4; 
  
import org.springframework.context.annotation.Bean; 
import org.springframework.context.annotation.ComponentScan; 
import org.springframework.context.annotation.Configuration; 
import org.springframework.web.servlet.config.annotation.EnableWebMvc; 
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry; 
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter; 
import org.springframework.web.servlet.view.InternalResourceViewResolver; 
import org.springframework.web.servlet.view.JstlView; 
  
@Configuration 
@EnableWebMvc//开启SpringMVC支持 
@ComponentScan("com.wisely.highlight_springmvc4")
//继承WebMvcConfigurerAdapter类，重写其方法对SpringMVC进行配置
public class MyMvcConfig extends WebMvcConfigurerAdapter{
  
    @Bean 
    public InternalResourceViewResolver viewResolver(){ 
        InternalResourceViewResolver viewResolver = 
                new InternalResourceViewResolver(); 
        viewResolver.setPrefix("/WEB-INF/classes/views/"); 
        viewResolver.setSuffix(".jsp"); 
        viewResolver.setViewClass(JstlView.class); 
        return viewResolver; 
    } 
    
    @Override 
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        //addResourceLocations指的是文件放置的目录；
        //addResourceHandler指的是对外暴露的访问路径；
        registry.addResourceHandler("/assets/**").
            addResourceLocations("classpath:/assets/");        
    }     
} 
```

## 拦截器配置

> 拦截器（Interceptor）实现对每一个请求处理前后进行相关的业务处理，类似于Servlet的Filter。
>
> 可让普通的Bean实现HanlderInterceptor接口或者继承HandlerInterceptorAdapter类来实现自定义拦截器。
>
> 通过重写WebMvcConfigurerAdapter的addInterceptors方法来注册自定义的拦截器，本节演示一个简单的拦截器的开发和配置，业务含义为计算每一次请求的处理时间。

```java
//示例拦截器

package com.wisely.highlight_springmvc4.interceptor; 
  
import javax.servlet.http.HttpServletRequest; 
import javax.servlet.http.HttpServletResponse; 
  
import org.springframework.web.servlet.ModelAndView; 
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter; 

//继承HandlerInterceptorAdapter类来实现拦截器
public class DemoInterceptor extends HandlerInterceptorAdapter { 
  
    @Override 
    //重写preHandle方法，在请求发生前执行。
    public boolean preHandle(HttpServletRequest request, 
            HttpServletResponse response, Object handler) throws Exception { 
        long startTime = System.currentTimeMillis(); 
        request.setAttribute("startTime", startTime); 
        return true; 
    } 
  
    @Override 
    //重写postHandle方法，在请求完成后执行。
    public void postHandle(HttpServletRequest request, //3 
            HttpServletResponse response, Object handler, 
            ModelAndView modelAndView) throws Exception { 
        		long startTime = (Long) request.
            					getAttribute("startTime"); 
            	request.removeAttribute("startTime"); 
        		long endTime = System.currentTimeMillis(); 
            	System.out.println("本次请求处理时间为:" + new Long(endTime - startTime)+"ms"); 
        		request.setAttribute("handlingTime", endTime - startTime); 
    }   
} 

//配置
    @Bean  //配置拦截器的Bean
    public DemoInterceptor demoInterceptor(){ 
        return new DemoInterceptor(); 
    } 

    @Override
	//重写addInterceptors方法，注册拦截器。
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(demoInterceptor()); 
    } 
```

