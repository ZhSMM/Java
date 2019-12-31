# Spring Aware

> Spring的依赖注入的最大亮点就是你所有的Bean对Spring容器的存在是没有意识的。即你可以将你的容器替换成别的容器，如Google Guice，这时Bean之间的耦合度很低。
>
> 但是在实际项目中，你不可避免的要用到Spring容器本身的功能资源，这时你的Bean必须要意识到Spring容器的存在，才能调用Spring所提供的资源，这就是所谓的Spring  Aware。其实Spring Aware本来就是Spring设计用来框架内部使用的，若使用了Spring  Aware，你的Bean将会和Spring框架耦合。
>
> Spring提供的Aware接口:
>
> ![Aware 接口](..\imges\Aware接口.jpg)
>
> Spring Aware的目的是为了让Bean获得Spring容器的服务。因为ApplicationContext接口集成了MessageSource接口、ApplicationEventPublisher接口和ResourceLoader接口，所以Bean继承ApplicationContextAware可以获得Spring容器的所有服务，但原则上我们还是用到什么接口，就实现什么接口。

实例：

```java
//准备，在资源目录下新建一个test.txt,内容随意

//Spring Aware演示Bean
package com.zhangs.learning.aware;

import org.apache.commons.io.IOUtils;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.context.ResourceLoaderAware;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Service;

import java.io.IOException;
@Service
//实现BeanNameAware、ResourceLoaderAware接口，获得Bean名称和资源加载的服务。
public class AwareService implements BeanNameAware, ResourceLoaderAware {
    private String beanName;
    private ResourceLoader loader;
    @Override
    //实现BeanNameAware需重写setBeanName方法。
    public void setBeanName(String s) {
        this.beanName=s;
    }

    @Override
    //实现ResourceLoaderAware需重写setResourceLoader。
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.loader=resourceLoader;
    }

    public  void outputResult(){
        System.out.println("Bean的名称"+beanName);
        Resource resource =loader.getResource("classpath:12.txt");
        try{
            System.out.println(IOUtils.toString(resource.getInputStream()));
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}

//配置类
package com.zhangs.learning.aware;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.zhangs.learning.aware")
public class AwareConfig {
}

//测试运行
package com.zhangs.learning.aware;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context=new
                AnnotationConfigApplicationContext(AwareConfig.class);
        AwareService awareService=context.getBean(AwareService.class);
        awareService.outputResult();
        context.close();
    }
}
//结果
Bean的名称awareService
woshiyizhixiaoxiaoniao
feibugao

```

