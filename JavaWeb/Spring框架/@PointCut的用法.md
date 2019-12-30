# @Pointcut的用法

> 为了使切点服用，可以使用@Pointcut专门定义拦截规则，然后在建言@Before、@Around、@After中调用Pointcut的签名；

## 定义

1. 定义：

   ```java
   //Pointcut表达式
   @Pointcut("拦截规则")
   //Pointcut签名（signature）
   public void annotationPointcut(){}
   
   //调用
   @Before("annotationPointcut()")
   //等同于
   @Before("拦截规则")
   ```

   

2. Pointcut定义时，还可以使用` &&、||、！ `这三个运算

   ```java
   //Pointcut定义时，还可以使用&&、||、！这三个运算
   @Pointcut("execution(* com.zhangs.aop.MessageSender.*(..))")
   public void logSender(){}
   
   @Pointcut("execution(* com.zhangs.aop.MessageReceiver.*(..))")
   public void logReceiver(){}
   
   //将匹配MessageSender与MessageReceiver中任何方法
   @Pointcut("logSender() || logReceiver()") 
   public void logMessage(){}
   ```

   

3. 还可以将一些公用的Pointcut放到一个类中，以供整个应用程序使用

   ```java
   //定义
   public class Pointcuts{
       @Pointcut("execution(* com.zhangs.aop.MessageSender.*(..))")
       public void logMessager(){}
       
       @Pointcut("execution(* com.zhangs.aop.MessageReceiver.*(..))")
   	public void logReceiver(){}
   }
   //使用
   //指定完整的类名加上Pointcut签名就可以了
   @Aspect
   public class LogAdvice{
       @Before("com.zhangs.aop.Pointcuts.logMessager()")
       public void before(JointPoint joinPoint){
           System.out.println(joinPoint.getSignature().getName());
       }
   }
   
   //当基于XML Sechma实现Advice时，如果Pointcut需要被重用，可以使用<aop:pointcut></aop:pointcut>
   //来声明Pointcut，然后在需要使用这个Pointcut的地方，用pointcut-ref引用就行了，如：
   
   <aop:config>
   　　<aop:pointcut id="log" expression=
         "execution(* com.savage.simplespring.bean.MessageSender.*(..))"/>
   　　<aop:aspect id="logging" ref="logBeforeAdvice">
   　　　　<aop:before pointcut-ref="log" method="before"/>
   　　　　<aop:after-returning pointcut-ref="log" method="afterReturning"/>
   　　</aop:aspect>
   </aop:config>
```
   
   

## 拦截规则

拦截规则的写法有很多，常用的有execution等；

1. execution格式：

   ```java
   //modifiers-pattern?:修饰符匹配
   //ret-type-pattern:返回值匹配，可以用*表示为任何值和全路径的类名等
   //declaring-type-pattern?:类路径匹配
   //name-pattern:方法名匹配，可以指定方法名、*（全部方法）、set*(以set开头的所有方法)等
   //(param-pattern)：参数匹配，可以指定具体的参数类型，多参数之间用“,”隔开，
   //                参数也可以用*匹配任意类型的参数，(..)匹配0个或多个任意参数，
   //                (*,int)匹配第一个为任意参数，第二参数为int类型；
   //throws-pattern?:异常类型匹配
   注：参数带？为可选项。
   execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern)throws-pattern?)
       
   //示例
   execution(* *(..)) 全匹配
   execution(public * com. *(..)) 匹配com包下全部的共有方法
   ```

   

2. within格式

   > 通过类匹配模式串声明切点，within()函数定义的连接点是针对目标类而言的，而非针对运行期对象的类型而言，这一点和execution()是相同的。

   ```java
   //接口
   public interface print(){
       public void print();
   }
   //实现类
   public class LogPrint implements print {
       @Override
       public void print() {
       }
   }
   //使用
   with(com.test.aop.LogPrint):匹配目标类LogPrint的所有方法。 如果切点调整为within(com.test.aop.Print)，则LogPrint中的所有方法都不匹配。 而Print本身是接口，不可能实例化，所以within(com.test.aop.Print)的声明是无意义的。
   ```

   

3. @annotation(注解)
