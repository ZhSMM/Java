# @Enable*注解的工作原理

> @EnableAspectJAutoProxy开启对AspectJ自动代理的支持。
>
> @EnableAsync开启异步方法的支持。
>
> @EnableScheduling开启计划任务的支持。
>
> @EnableWebMvc开启Web MVC的配置支持。
>
> @EnableConfigurationProperties开启对@ConfigurationProperties注解配置Bean的支持。
>
> @EnableJpaRepositories开启对Spring Data JPA Repository的支持。
>
> @EnableTransactionManagement开启注解式事务的支持。
>
> @EnableCaching开启注解式的缓存支持。
>
> 通过简单的@Enable*来开启一项功能的支持，从而避免自己配置大量的代码，大大降低使用难度。那么这个神奇的功能的实现原理是什么呢？我们一起来研究一下。
>
> 通过观察这些@Enable*注解的源码，我们发现所有的注解都有一个@Import注解，@Import是用来导入配置类的，这也就意味着这些自动开启的实现其实是导入了一些自动配置的Bean。这些导入的配置方式主要分为以下三种类型。

1. 直接导入配置类

   ```java
   @Target(ElementType.TYPE) 
   @Retention(RetentionPolicy.RUNTIME) 
   @Import(SchedulingConfiguration.class) 
   @Documented 
   public @interface EnableScheduling { 
   }
   
   //直接导入配置类SchedulingConfiguration，这个类注解了@Configuration，
   // 且注册了一个scheduledAnnotationProcessor的Bean，源码如下：
   @Configuration 
   @Role(BeanDefinition.ROLE_INFRASTRUCTURE) 
   public class SchedulingConfiguration { 
     
       @Bean(name = TaskManagementConfigUtils.SCHEDULED_ANNOTATION_PROCESSOR_BEAN_NAME) 
       @Role(BeanDefinition.ROLE_INFRASTRUCTURE) 
       public ScheduledAnnotationBeanPostProcessor scheduledAnnotationProcessor() { 
           return new ScheduledAnnotationBeanPostProcessor(); 
       } 
     
   } 
   ```

2. 依据条件选择配置类

   ```java
   @Target(ElementType.TYPE) 
   @Retention(RetentionPolicy.RUNTIME) 
   @Documented 
   @Import(AsyncConfigurationSelector.class) 
   public @interface EnableAsync { 
   Class<? extends Annotation> annotation() default Annotation.class; 
       boolean proxyTargetClass() default false; 
       AdviceMode mode() default AdviceMode.PROXY; 
       int order() default Ordered.LOWEST_PRECEDENCE; 
   } 
   
   //AsyncConfigurationSelector通过条件来选择需要导入的配置类，AsyncConfigurationSelector
   // 的根接口为ImportSelector，这个接口需重写selectImports方法，在此方法内进行事先条件判断。
   // 此例中，若adviceMode为PORXY，则返回ProxyAsyncConfiguration这个配置类；若activeMode为
   // ASPECTJ，则返回AspectJAsyncConfiguration配置类，源码如下：
   
   public class AsyncConfigurationSelector extends AdviceModeImportSelector<EnableAsync> { 
     
       private static final String ASYNC_EXECUTION_ASPECT_CONFIGURATION_CLASS_NAME = 
               "org.springframework.scheduling.aspectj.AspectJAsyncConfiguration"; 
     
       @Override 
       public String[] selectImports(AdviceMode adviceMode) { 
           switch (adviceMode) { 
               case PROXY: 
                   return new String[] { ProxyAsyncConfiguration.class.getName() }; 
               case ASPECTJ: 
                   return new String[] { ASYNC_EXECUTION_ASPECT_CONFIGURATION_CLASS_NAME }; 
               default: 
                   return null; 
           } 
       } 
   } 
   ```

3. 动态注册Bean

   ```java
   @Target(ElementType.TYPE) 
   @Retention(RetentionPolicy.RUNTIME) 
   @Documented 
   @Import(AspectJAutoProxyRegistrar.class) 
   public @interface EnableAspectJAutoProxy { 
       boolean proxyTargetClass() default false; 
   } 
   
   //AspectJAutoProxyRegistrar实现了ImportBeanDefinitionRegistrar接口，
   // ImportBean DefinitionRegistrar的作用是在运行时自动添加Bean到已有的配置类，通过重写方法：
   registerBeanDefinitions(AnnotationMetadata importingClassMetadata,                                          BeanDefinitionRegistry registry) 
   
   // 其中，AnnotationMetadata参数用来获得当前配置类上的注解；BeanDefinitionRegistry参数用来注册Bean。源码如下：
   
   class AspectJAutoProxyRegistrar implements ImportBeanDefinitionRegistrar { 
       @Override 
       public void registerBeanDefinitions( 
               AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) { 
     
           AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry); 
     
           AnnotationAttributes enableAJAutoProxy = 
                   AnnotationConfigUtils.attributesFor(importingClassMetadata, EnableAspectJAutoProxy.class); 
           if (enableAJAutoProxy.getBoolean("proxyTargetClass")) { 
               AopConfigUtils.forceAutoProxyCreatorToUseClassProxying(registry); 
           } 
       } 
   }
   ```

   