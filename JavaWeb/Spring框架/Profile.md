# Profile

> Profile为不同环境下使用不同的配置提供了支持（开发环境下的配置和生产环境下的配置肯定是不同的，比如数据库配置）
>
> 1. 通过设定Environment的ActiveProfiles来设定当前context需要使用的配置环境。在开发中使用@Profile注解类或者方法，达到在不同情况下选择实例化不同的Bean；
> 2. 通过设定jvm的spring.profiles.active参数来设置配置环境；
> 3. Web项目设置在Servlet的context parameter中；

1. 设置方式

   ```java
   //Servlet 2.5及以下
   <servlet>
   	<servlet-name>dispatcher</servlet-name>
   	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class> 
       <init-param> 
           <param-name>spring.profiles.active</param-name> 
           <param-value>production</param-value> 
       </init-param> 
   </servlet> 
   
   Servlet 3.0及以上：
   
   public class WebInit implements WebApplicationInitializer { 
       @Override 
       public void onStartup(ServletContext container) throws ServletException { 
           container.setInitParameter("spring.profiles.default", "dev"); 
       } 
   } 
   ```

2. 演示

   ```java
   //创建Bean
   public class DemoBean1 {
       private String content;
       public DemoBean1(String content){
           this.content=content;
       }
   
       public String getContent() {
           return content;
       }
   
       public void setContent(String content) {
           this.content = content;
       }
   }
   
   //Profile配置
   @Configuration
   public class ProfileConfig {
       @Bean
       @Profile("dev")  //根据Profile的值实例化Bean
       public DemoBean1 demoBean1(){
           return new DemoBean1("from development profile");
       }
       @Bean
       @Profile("pro")  
       public DemoBean1 demoBean2(){
           return new DemoBean1("from production profile");
       }
   }
   
   //演示
   public class Main {
       public static void main(String[] args) {
           AnnotationConfigApplicationContext context=new
                   AnnotationConfigApplicationContext();
           //设置profile值为prod
           context.getEnvironment().setActiveProfiles("dev"); 
           //注册Bean配置类
           context.register(ProfileConfig.class);
           //刷新容器
           context.refresh();
   
           DemoBean1 demoBean1=context.getBean(DemoBean1.class);
           System.out.println(demoBean1.getContent());
   
           context.close();
       }
   }
   
   //结果
   from development profile
   
   ```

   