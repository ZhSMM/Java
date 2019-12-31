# Bean初始化和销毁

> 在实际开发时，经常会遇到Bean在使用之前和使用之后必要的操作，Spring对Bean的生命周期操作提供了支持。在使用Java配置和注解配置下提供如下两种方式：
>
> 1. Java配置方式：使用@Bean的initMethod和destroyMethod（相当于xml配置的init-method和destory-method）；
> 2. 注解方式：利用JSR-250的@PostConstruct和@PreDestroy。

1. 增加JSR250支持

   ```java
   <!-- https://mvnrepository.com/artifact/javax.annotation/jsr250-api -->
   <dependency>
       <groupId>javax.annotation</groupId>
       <artifactId>jsr250-api</artifactId>
       <version>1.0</version>
   </dependency>
   ```

2. 使用@Bean形式的Bean

   ```java
   public class DemoBean {
       public void init(){
           System.out.println("Bean的init");
       }
       public DemoBean(){
           System.out.println("Bean初始化");
       }
       public void destroy(){
           System.out.println("Bean销毁");
       }
   }
   ```

3. 使用JSR250形式的Bean

   ```java
   public class DemoJ250 {
       @PostConstruct    //在构造函数执行完之后执行
       public void init(){
           System.out.println("初始化之后使用之前");
       }
       public DemoJ250(){
           System.out.println("初始化250");
       }
       @PreDestroy     ///在Bean销毁之前执行
       public void destroy(){
           System.out.println("销毁之前");
       }
   }
   ```

4. 配置类

   ```java
   @Configuration
   public class DemoConfig1 {
       @Bean(initMethod = "init",destroyMethod = "destroy")
       DemoBean demoBean(){
           return new DemoBean();
       }
       @Bean
       DemoJ250 demoJ250(){
           return new DemoJ250();
       }
   }
   
   ```

5. 运行

   ```java
   public class Main {
       public static void main(String[] args) {
           AnnotationConfigApplicationContext context=new
                   AnnotationConfigApplicationContext(DemoConfig1.class);
           DemoBean demo=context.getBean(DemoBean.class);
           DemoJ250 demoJ250=context.getBean(DemoJ250.class);
           context.close();
       }
   }
   ```

6. 结果

   ```java
   Bean初始化
   Bean的init
   初始化250
   初始化之后使用之前
   销毁之前
   Bean销毁
   ```

   



