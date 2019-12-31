# 条件注解@Conditional

> 通过活动的profile，我们可以获得不同的Bean。Spring 4提供了一个更通用的基于条件的Bean的创建，即使用@Conditional注解。
>
> @Conditional根据满足某一个特定条件创建一个特定的Bean。比方说，当某一个jar包在一个类路径下的时候，自动配置一个或多个Bean；或者只有某个Bean被创建才会创建另外一个Bean。总的来说，就是根据特定条件来控制Bean的创建行为，这样我们可以利用这个特性进行一些自动的配置。
>
> 在Spring Boot中会用到大量的条件注解。

示例：以不同的操作系统作为条件，我们将通过实现Condition接口，并重写其matches方法来构造判断条件。若在Windows系统下运行程序，则输出列表命令为dir；若在Linux操作系统下运行程序，则输出列表命令为ls。

```java
//定义判断条件
//判断Windows的条件
package com.zhangs.learning.conditional;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class WinConditional implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return conditionContext.getEnvironment().
                getProperty("os.name").contains("Windows");
    }
}

//判断Linux的条件
package com.zhangs.learning.conditional;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class LinConditional implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return conditionContext.getEnvironment().
                getProperty("os.name").contains("Linux");
    }
}

//不同系统下Bean的类
//接口定义
package com.zhangs.learning.conditional;

public interface ListService {
    public String showListCmd();
}

//Windows系统下要创建的Bean类
package com.zhangs.learning.conditional;

public class WinListService implements ListService {
    @Override
    public String showListCmd() {
        return "dir";
    }
}

//Linux系统下要创建的Bean类
package com.zhangs.learning.conditional;

public class LinListService implements ListService {
    @Override
    public String showListCmd() {
        return "ls";
    }
}

//配置类
package com.zhangs.learning.conditional;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.zhangs.learning.conditional")
public class ConditionConfig {
    @Bean
    //通过@Conditional注解，符合Windows条件则实例化winListService。
    @Conditional(WinConditional.class)
    public WinListService winListService(){
        return new WinListService();
    }
    @Bean
    //通过@Conditional注解，符合Linux条件则实例化linListService。
    @Conditional(LinConditional.class)
    public LinListService linListService(){
        return new LinListService();
    }
}

//运行
package com.zhangs.learning.conditional;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context=new
                AnnotationConfigApplicationContext(ConditionConfig.class);
        ListService listService=context.getBean(ListService.class);
        System.out.println(listService.showListCmd());
        context.close();
    }
}
```

