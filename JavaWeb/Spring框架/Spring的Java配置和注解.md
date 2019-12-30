# Spring的Java配置和注解

> Java配置是Spring 4.x推荐的配置方式，可以完全替代xml配置；Java配置也是Spring Boot推荐的配置方式。

Java配置通过@Configuration和@Bean来实现的：

- @Configuration声明当前类是一个配置类，相当于一个Spring配置的xml文件；
- @Bean注解在方法上，声明当前方法的返回值为一个Bean；

Java配置和注解配置的使用原则：

- 全局配置使用Java配置（如数据库相关配置、MVC相关配置）；
- 业务Bean的配置使用注解配置（@Service、@Component、@Repository、@Controlle）

简单示例：

```java
//功能类AService
public class AService {
    public String print(String str){
        return "Hello "+str+" !";
    }
}
//使用功能类的UserAService
public class UserAService {
    private AService aService;
    public UserAService(AService aService){
        this.aService=aService;
    }
    public String print(String str){
        return this.aService.print(str);
    }
}
//Java配置
@Configuration  //配置类，这里为@ComponentScan包扫描主要是所有的bean在此类中定义了。
public class AConfig {
    @Bean  //返回一个Bean，名称为方法名。
    public AService aService(){
        return new AService();
    }
    @Bean
    public UserAService userAService(){
        return new UserAService(aService());//注入Bean，直接调用aService()
    }
    //另外一种注入的方式，直接将AService作为参数给useAService（），这也是Spring容器提供的极好的功能。在Spring容器中，只要容器中存在某个Bean，就可以在另外一个Bean的声明方法的参数中写入。
    /*
    public UserAService userAService(AService aService){
        return new UserAService(aService);
    }
    */
    
}
//测试
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context=
                new AnnotationConfigApplicationContext(AConfig.class);
        UserAService userAService=context.getBean(UserAService.class);
        System.out.println(userAService.print("zhangs"));
        context.close();
    }
}


```

