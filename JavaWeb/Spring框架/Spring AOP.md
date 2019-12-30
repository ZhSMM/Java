# Spring AOP

> AOP：面向切面编程，相对于OOP面向对象编程。
>
> Spring的AOP的存在目的是为了解耦。AOP可以让一组类共享相同的行为。在OOP中只能通过继承类和实现接口，来使代码的耦合度增强，且类继承只能为单继承，阻碍更多行为添加到一组类上，AOP弥补了OOP的不足。

Spring支持AspectJ的注解式切面编程。

1. 使用@Aspect声明是一个切面。
2. 使用@After、@Before、@Around定义建言（advice），可直接将拦截规则（切点）作为参数。
3. 其中@After、@Before、@Around参数的拦截规则为切点（PointCut），为了使切点复用，可使用@PointCut专门定义拦截规则，然后在@After、@Before、@Around的参数中调用。
4. 其中符合条件的每一个被拦截处为连接点（JoinPoint）。

基于注解拦截示例：

```java
//编写拦截规则的注释
//注解说明：注解本身是没有功能的，和xml一样。注解和xml均属于元数据，元数据指解释数据的数据，这就
//所谓配置。注解的功能来自使用这个注解的地方。
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Action {
    String name();
}
//编写使用注解的被拦截类
@Service
public class DemoAnnotation {
    @Action(name="使用注解的被拦截add操作")
    public void add(){}
}
//编写切面
@Aspect
@Component  //通过@Component让此切面成为Spring容器管理的Bean。
public class LogAspect {
    @Pointcut("@annotation(com.aop.Action)")  //通过@PointCut注解声明切点
    public void annotationPointCut(){}

    @After("annotationPointCut()")     //通过@After注解声明一个建言，并使用@PointCut定
    public void after(JoinPoint joinPoint){   //义的切点
        //通过反射可获得注解上的属性，然后做日志记录相关的操作，下面的相同。
        MethodSignature signature=(MethodSignature)joinPoint.getSignature();
        Method method=signature.getMethod();
        Action action=method.getAnnotation(Action.class);
        System.out.println("注解式拦截"+action.name()); 
    }
}
//配置类
@Configuration
@ComponentScan("com.aop")
@EnableAspectJAutoProxy
public class DemoConfig {
}
//测试
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context=new
                AnnotationConfigApplicationContext(DemoConfig.class);
        DemoAnnotation demoAnnotation=context.getBean(DemoAnnotation.class);
        demoAnnotation.add();
        context.close();
    }
}
```





