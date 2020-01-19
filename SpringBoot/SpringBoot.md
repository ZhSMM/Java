# Spring Boot

## 一、基本配置

### 1. 入口类和@SpringBootApplication

> ​    Spring Boot 通常有一个名为*Application的入口类，入口类里有一个main方法，这个main方法其实就是一个标准的Java应用的入口方法。在main方法中使用SpringApplication.run（Ch522Application.class，args ），启动Spring Boot应用项目。
>
> ​    @SpringBootApplication是Spring Boot的核心注解，为组合注解，源码如下：
>
> ```java
> @Target(ElementType.TYPE) 
> @Retention(RetentionPolicy.RUNTIME) 
> @Documented 
> @Inherited 
> @Configuration 
> @EnableAutoConfiguration 
> @ComponentScan 
> public @interface SpringBootApplication { 
>     Class<?>[] exclude() default {}; 
>     String[] excludeName() default {}; 
> } 
> ```
>
> ​    @SpringBootApplication注解主要组合了@Configuration、@EnableAutoConfiguration、@ComponentScan；若不使用@SpringBootApplication注解，则可以在入口类上直接使用@Configuration、@EnableAutoConfiguration、@ComponentScan。
>
> ​    其中，@EnableAutoConfiguration让Spring Boot根据类路径中的jar包依赖为当前项目进行自动配置。
>
> ​    例如，添加了spring-boot-starter-web依赖，会自动添加Tomcat和Spring MVC的依赖，那么Spring Boot会对Tomcat和Spring MVC进行自动配置。
>
> ​    又如，添加了spring-boot-starter-data-jpa依赖，Spring Boot会自动进行JPA相关的配置。
>
> ​    Spring Boot会自动扫描@SpringBootApplication所在类的同级包以及下级包里的Bean（若为JPA项目还可以扫描标注@Entity的实体类）。建议入口类放置的位置在groupId+arctifactID组合的包名下。

### 2. 关闭特定的自动配置

> ​    通过上面的@SpringBootApplication的源码我们可以看出，关闭特定的自动配置应该使用@SpringBootApplication注解的exclude参数，例如：
>
> `@SpringBootApplication(exclude ={DataSourceAutoConfiguration.class}) `

### 3.定制Banner

> ​    Spring Boot启动时会有一个默认启动图案，我们可以在src/main/resource下新建banner.txt文件来进行自定义设置；字符生成可以通过[http://patorjk.com/software/taag](http://patorjk.com/software/taag) 网站。
>
> ​    关闭banner有两种方法：
>
> 1. main里的内容修改为：
>
>    ```java
>    SpringApplication app = new SpringApplication(Ch522Application.class); 
>    app.setShowBanner(false); 
>    app.run(args); 
>    ```
>
> 2. 或使用fluent API修改为：
>
>    ```java
>    new SpringApplicationBuilder(Ch522Application.class) 
>                    .showBanner(false) 
>                    .run(args); 
>    ```

### 4.Spring Boot 配置文件

> ​	Spring Boot使用一个全局的配置文件application.properties或application.yml，放置在src/main/resources目录或者类路径的/config下。
>
> ​	Spring Boot不仅支持常规的properties配置文件，还支持yaml语言的配置文件。yaml是以数据为中心的语言，在配置数据的时候具有面向对象的特征。
>
> ​	Spring Boot的全局配置文件的作用是对一些默认配置的配置值进行修改。

1. 示例

   将Tomcat的默认端口号8080修改为9090，并将默认的访问路径“/”修改为“/helloboot”。

   可以在application.properties中添加：
   
   ```java
   server.port=9090 
   server.servlet.context-path=/helloboot
   ```
   
   或者在application.yml中添加：
   
   ```java
   server: 
     port: 9090 
     //contextPath、context-path、contextPath或者CONTEXT_PATH形式其实是通用的
     servlet:
       contextPath: /helloboot
   ```
   
2. 使用xml配置：

   Spring Boot提倡零配置，即无xml配置，但在特殊要求必须使用xml配置时，可以通过Spring提供的@ImportResource来加载xml配置，如：

   `@ImportResource({"classpath:some-context.xml","classpath:another-context.xml"}) `

3. 外部配置

   Spring Boot允许使用properties文件、yaml文件或者使用命令行参数作为外部配置。

   - 命令行参数配置：Spring Boot可以是基于jar包运行的，打成jar包的程序可以直接通过下面命令运行，`java –jar xx.jar ` ，可以通过以下命令修改Tomcat端口号：`java –jar xx.jar --server.port=9090 `
   - 常规属性配置：在常规Spring环境下，注入properties文件里的值的方式，通过@PropertySource指明properties文件的位置，然后通过@Value注入值。在Spring Boot里，我们只需在application.properties定义属性，直接使用@Value("${键值}")注入即可。

   - 类型安全的配置（基于properties）：上例中使用@Value注入每个配置在实际项目中会显得格外麻烦，因为我们的配置通常会是许多个，若使用上例的方式则要使用@Value注入很多次。  Spring Boot还提供了基于类型安全的配置方式，通过@ConfigurationProperties将properties属性和一个Bean及其属性关联，从而实现类型安全的配置。示例：

     ```java
     //在application.properties上添加：
     //或新建properties文件,需要在@ConfigurationProperties属性locations里指定properties的位置，且需在入口类上配置
     author.name=lll
     author.age=22
     
     //类型安全的Bean
     import org.springframework.boot.context.properties.ConfigurationProperties; 
       
     @Component 
     //通过@ConfigurationProperties加载properties文件内的配置，通过prefix属性指定properties的配置的前缀，通过location指定properties文件的位置
     //如@ConfigurationProperties(prefix = "author",locations = {"classpath:config/author.properties"}) 
     @ConfigurationProperties(prefix = "author") 
     public class AuthorSettings { 
         private String name; 
         private Long age; 
         set/get 方法
     }
     
     //检验
     import org.springframework.beans.factory.annotation.Autowired; 
     import org.springframework.boot.SpringApplication; 
     import org.springframework.boot.autoconfigure.SpringBootApplication; 
     import org.springframework.web.bind.annotation.RequestMapping; 
     import org.springframework.web.bind.annotation.RestController; 
       
     import com.wisely.ch6_2_3.config.AuthorSettings; 
     @RestController 
     @SpringBootApplication 
     public class Ch623Application { 
         @Autowired  //可以自动注入
         private AuthorSettings authorSettings;
         
         @RequestMapping("/") 
         public String index(){ 
             return "author name is "+ authorSettings.getName()+" and author age is "+authorSettings.getAge(); 
         } 
       
         public static void main(String[] args) { 
             SpringApplication.run(Ch623Application.class, args); 
         } 
     } 
     ```

4. 日志配置

   Spring Boot支持Java Util  Logging、Log4J、Log4J2和Logback作为日志框架，无论使用哪种日志框架，Spring  Boot已为当前使用日志框架的控制台输出及文件输出做好了配置。

   默认情况下，Spring Boot使用Logback作为日志框架。

   ```java
   //配置日志级别，格式为logging.level.包名=级别
   logging.level.org.springframework.web=DEBUG
   
   //配置日志文件路径
   logging.file.path=D:/mylog/log.log
   ```

5. Profile配置

   Profile是Spring用来针对不同的环境对不同的配置提供支持的，全局Profile配置使用application-{profile}.properties（如application-prod.properties）。

   通过在application.properties中设置spring.profiles.active=prod来指定活动的Profile。

   简单演示，生产（prod）和开发（dev）环境，生产环境端口号80，开发环境下端口为8888；

   ```java
   //配置文件
   //application-prod.properties
   server.port=80
   //application-dev.properties
   server.port=8888
   //在application.properties中添加
   spring.profiles.active=dev
   ```

### 5、Spring Boot运行原理

> Spring Boot关于自动配置的源码在spring-boot-autoconfigure-x.jar内,想要知道做了哪些配置，有如下两种方法：
>
> 1. 运行时增加--debug参数:`java -jar xx.jar --debug`
> 2. 在application.properties中设置属性：`debug=true`
>
> 运行原理：
>
> 组合注解@SpringBootApplication注解中核心功能是有@EnableAutoConfiguration提供的，@EnableAutoConfiguration源码如下：
>
> ```java
> //@Import注解导入的配置功能，EnableAutoConfigurationImportSelector使用SpringFactoriesLoader.loadFactoryNames方法来扫描具有META-INF/spring.factories文件的jar包，而我们的spring-boot-autoconfigure-1.3.0.x.jar里就有一个spring.factories文件，此文件中声明了有哪些自动配置。
> 
> @Target(ElementType.TYPE) 
> @Retention(RetentionPolicy.RUNTIME) 
> @Documented 
> @Inherited 
> @Import({ EnableAutoConfigurationImportSelector.class, 
>         AutoConfigurationPackages.Registrar.class }) 
> public @interface EnableAutoConfiguration { 
>     Class<?>[] exclude() default {}; 
>     String[] excludeName() default {}; 
> } 
> ```
>
> 核心注解：
>
> 打开上面任意一个AutoConfiguration文件，一般都有下面的条件注解，在spring-boot-autoconfigure-1.3.0.x.jar的org.springframwork.boot.autoconfigure.condition包下，条件注解如下。
>
> - @ConditionalOnBean：当容器里有指定的Bean的条件下。
>
> - @ConditionalOnClass：当类路径下有指定的类的条件下。
>
> - @ConditionalOnExpression：基于SpEL表达式作为判断条件。
> - @ConditionalOnJava：基于JVM版本作为判断条件。
> - @ConditionalOnJndi：在JNDI存在的条件下查找指定的位置。
> - @ConditionalOnMissingBean：当容器里没有指定Bean的情况下。
> - @ConditionalOnMissingClass：当类路径下没有指定的类的条件下。
> - @ConditionalOnNotWebApplication：当前项目不是Web项目的条件下。
> - @ConditionalOnProperty：指定的属性是否有指定的值。
> - @ConditionalOnResource：类路径是否有指定的值。
> - @ConditionalOnSingleCandidate：当指定Bean在容器中只有一个，或者虽然有多个但是指定首选的Bean。
> - @ConditionalOnWebApplication：当前项目是Web项目的条件下。
> - 这些注解都是组合了@Conditional元注解，只是使用了不同的条件（Condition）。

分析@ConditionalOnWebApplication注解：

```java
//@ConditionalOnWebApplication源码
package org.springframework.boot.autoconfigure.condition;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.context.annotation.Conditional;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
//此注解使用的条件是OnWebApplication类
@Conditional({OnWebApplicationCondition.class})
public @interface ConditionalOnWebApplication {
    ConditionalOnWebApplication.Type type() default ConditionalOnWebApplication.Type.ANY;

    public static enum Type {
        ANY,
        SERVLET,
        REACTIVE;

        private Type() {
        }
    }
}

//OnWebApplicationCondition.class源码
class OnWebApplicationCondition extends FilteringSpringBootCondition {
    ....
    private ConditionOutcome isWebApplication(ConditionContext context, AnnotatedTypeMetadata metadata, boolean required) {
        switch(this.deduceType(metadata)) {
        case SERVLET:
            return this.isServletWebApplication(context);
        case REACTIVE:
            return this.isReactiveWebApplication(context);
        default:
            return this.isAnyWebApplication(context, required);
        }
    }
    private ConditionOutcome isAnyWebApplication(ConditionContext context, boolean required) {
        Builder message = ConditionMessage.forCondition(ConditionalOnWebApplication.class, new Object[]{required ? "(required)" : ""});
        ConditionOutcome servletOutcome = this.isServletWebApplication(context);
        if (servletOutcome.isMatch() && required) {
            return new ConditionOutcome(servletOutcome.isMatch(), message.because(servletOutcome.getMessage()));
        } else {
            ConditionOutcome reactiveOutcome = this.isReactiveWebApplication(context);
            return reactiveOutcome.isMatch() && required ? new ConditionOutcome(reactiveOutcome.isMatch(), message.because(reactiveOutcome.getMessage())) : new ConditionOutcome(servletOutcome.isMatch() || reactiveOutcome.isMatch(), message.because(servletOutcome.getMessage()).append("and").append(reactiveOutcome.getMessage()));
        }
    }
    .....
    
}

```

自动配置功能实现（简单的Spring Boot内置的自动配置功能：http的编码配置分析）：

```java
// 在常规项目中配置http编码的时候是在web.xml里配置一个filter，如：
<filter> 
  <filter-name>encodingFilter</filter-name> 
  <filter-class>
  	org.springframework.web.filter.CharacterEncodingFilter 
  </filter-class> 
  <init-param> 
   <param-name>encoding</param-name> 
   <param-value>UTF-8</param-value> 
  </init-param> 
  <init-param> 
   <param-name>forceEncoding</param-name> 
   <param-value>true</param-value> 
  </init-param> 
</filter> 

//自动配置要满足两个条件：
// 1.能配置CharacterEncodingFilter这个Bean；
// 2.能配置encoding和forceEncoding这两个参数；

// 配置参数
// 类型安全的配置，可以在application.properties中直接设置

//在application.properties配置的时候前缀是spring.http.encoding
@ConfigurationProperties(prefix = "spring.http.encoding")
public class HttpEncodingProperties { 
  
    //默认编码方式为UTF-8，若修改可使用spring.http.encoding.charset=编码
    public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8"); 
  
    private Charset charset = DEFAULT_CHARSET; 
  
    //设置forceEncoding，默认为true，若修改可使用spring.http.encoding.force=false。
    private boolean force = true;  
  
    public Charset getCharset() { 
        return this.charset; 
    } 
  
    public void setCharset(Charset charset) { 
        this.charset = charset; 
    } 
  
    public boolean isForce() { 
        return this.force; 
    } 
    public void setForce(boolean force) { 
        this.force = force; 
    } 
  
} 

//配置Bean
//调用上述配置，并根据条件配置CharacterEncodingFilter的Bean

@Configuration 
//开启属性注入，通过@EnableConfigurationProperties声明，使用@Autowired注入；
@EnableConfigurationProperties(HttpEncodingProperties.class) 
//当CharacterEncodingFilter在类路径的条件下；
@ConditionalOnClass(CharacterEncodingFilter.class) 
//当设置spring.http.encoding=enabled的情况下，如果没有设置则默认为true，即条件符合；
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true) 
public class HttpEncodingAutoConfiguration { 
    @Autowired 
    private HttpEncodingProperties httpEncodingProperties; //３ 
  
    //像使用Java配置的方式配置CharacterEncodingFilter这个Bean；
    @Bean 
    //当容器中没有这个Bean的时候新建Bean。
    @ConditionalOnMissingBean(CharacterEncodingFilter.class) 
    public CharacterEncodingFilter characterEncodingFilter() { 
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter(); 
        filter.setEncoding(this.httpEncodingProperties.getCharset().name()); 
        filter.setForceEncoding(this.httpEncodingProperties.isForce()); 
        return filter; 
    } 
  
} 
```

