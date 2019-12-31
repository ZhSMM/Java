# 事件（Application Event）

> Spring的事件（Application Event）为Bean与Bean之间的消息通信提供了支持。当一个Bean处理完一个任务之后，希望另外一个Bean知道并能做相应的处理，这时我们就需要让另外一个Bean监听当前Bean所发送的事件。
>
> Spring的事件需要遵循如下流程：
>
> 1. 自定义事件，集成ApplicationEvent。
>
> 2. 定义事件监听器，实现ApplicationListener。
>
> 3. 使用容器发布事件。

示例：

```java
//自定义事件
public class DemoEvent extends ApplicationEvent {
    private static final long serialVersionUID = 1L;
    private String msg;

    public DemoEvent(Object source,String msg) {
        super(source);
        this.msg=msg;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}

//事件监听器
@Component
//实现ApplicationListener接口，并指定监听的事件类型
public class DemoListener implements ApplicationListener<DemoEvent> {
    //使用onApplicationEvent方法对消息进行处理
    public void onApplicationEvent(DemoEvent event) {

        String msg=event.getMsg();
        System.out.println("我（bean-demoListener）接收到了bean-demoEvent的消息"+msg);
    }
}
//事件发布类
@Component
public class DemoPublisher {
    @Autowired
    //注入ApplicationContext用来发布事件
    ApplicationContext applicationContext;
    public void publish(String msg){
        //使用ApplicationContext的publishEvent方法发布事件
        applicationContext.publishEvent(new DemoEvent(this,msg));
    }
}

//配置
@Configuration
@ComponentScan("com.zhangs.learning.event")
public class EventConfig {
}
//运行
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context=new
                AnnotationConfigApplicationContext(EventConfig.class);
        DemoPublisher demoPublisher=context.getBean(DemoPublisher.class);
        demoPublisher.publish("hello application event");
        context.close();
    }
}

```

