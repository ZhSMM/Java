# Spring MVC的常用注解

## 常用注解

1. @Controller

   >@Controller注解在类上，表明这个类是Spring MVC里的Controller，将其声明为Spring的一个Bean，Dispatcher Servlet会自动扫描注解了此注解的类，并将Web请求映射到注解了@RequestMapping的方法上。
   >
   >这里特别指出，在声明普通Bean的时候，使用@Component、@Service、@Repository和@Controller是等同的，因为@Service、@Repository、@Controller都组合了@Compoment元注解；但在Spring MVC声明控制器Bean的时候，只能使用@Controller。

2. @RequestMapping

   > @RequestMapping注解是用来映射Web请求（访问路径和参数）、处理类和方法的。@RequestMapping可注解在类或方法上。注解在方法上的@RequestMapping路径会继承注解在类上的路径，@RequestMapping支持Servlet的request和response作为参数，也支持对request和response的媒体类型进行配置。

3. @ResponseBody

   > @ResponseBody支持将返回值放在response体内，而不是返回一个页面。我们在很多基于Ajax的程序的时候，可以以此注解返回数据而不是页面；此注解可放置在返回值前或者方法上。

4. @RequestBody

   > @RequestBody允许request的参数在request体中，而不是在直接链接在地址后面。此注解放置在参数前。

5. @PathVariable

   > @PathVariable用来接收路径参数，如/news/001，可接收001作为参数，此注解放置在参数前。

6. @RestController

   > @RestController是一个组合注解，组合了@Controller和@ResponseBody，这就意味着当你只开发一个和页面交互数据的控制的时候，需要使用此注解。若没有此注解，要想实现上述功能，则需自己在代码中加@Controller和@ResponseBody两个注解。

## 示例

1. 传值类

   ```java
   //添加jackson及相关依赖，获得对象和json或xml之间的转换；
   
<!--对json和xml格式的支持 --> 
   <dependency> 
       <groupId>com.fasterxml.jackson.dataformat</groupId> 
       <artifactId>jackson-dataformat-xml</artifactId> 
       <version>2.10.0</version> 
   </dependency> 
           
   //实际项目中，主要支持json数据，没必要同时支持json和xml，由于JavaScript的广泛使用，json成了最推
   // 荐的格式，因此只需导入支持json的包，依赖如下（上面的依赖包含下面的依赖）：
   <dependency> 
       <groupId>com.fasterxml.jackson.core</groupId> 
       <artifactId>jackson-databind</artifactId> 
       <version>2.10.0</version> 
   </dependency> 
   
   //此类用来演示获取request对象参数和返回此对象到response：
   package com.wisely.highlight_springmvc4.domain; 
     
   public class DemoObj { 
       private Long id; 
       private String name; 
       
       public DemoObj() { // jackson对对象和json做转换时一定需要此空构造
           super(); 
       } 
       public DemoObj(Long id, String name) { 
           super(); 
           this.id = id; 
           this.name = name; 
       } 
       public Long getId() { 
           return id; 
       } 
       public void setId(Long id) { 
           this.id = id; 
       } 
       public String getName() { 
           return name; 
       } 
       public void setName(String name) { 
           this.name = name; 
       } 
   } 
   ```
   
2. 注解演示控制器

   ```java
   package com.wisely.highlight_springmvc4.web.ch4_3; 
   import javax.servlet.http.HttpServletRequest; 
     
   import org.springframework.stereotype.Controller; 
   import org.springframework.web.bind.annotation.PathVariable; 
   import org.springframework.web.bind.annotation.RequestMapping; 
   import org.springframework.web.bind.annotation.ResponseBody;
   
   import com.wisely.highlight_springmvc4.domain.DemoObj; 
     
   @Controller //@Controller注解声明此类是一个控制器
   @RequestMapping("/anno") //映射此类的访问路径是/anno
   public class DemoAnnoController { 
     
       //此方法未标注路径，因此使用类级别的路径/anno；
       //produces可定制返回的response的媒体类型和字符集，
       //或需返回值是json对象，则设置produces=“application/json；charset=UTF-8”
       @RequestMapping(produces = "text/plain;charset=UTF-8") 
       //演示可接受HttpServletRequest作为参数，
       //当然也可以接受HttpServletReponse作为参数。此处的@ReponseBody用在返回值前面。
       public @ResponseBody String index(HttpServletRequest request) { 
           return "url:" + request.getRequestURL() + " can access"; 
       } 
     
       //演示接受路径参数，并在方法参数前结合@PathVariable使用，访问路径为/anno/pathvar/xx
       @RequestMapping(value = "/pathvar/{str}", produces = "text/plain;charset=UTF-8")
       public @ResponseBody String demoPathVar(@PathVariable String str, 
               HttpServletRequest request) { 
           return "url:" + request.getRequestURL() + " can access,str: " + str; 
       } 
     
       //演示常规的request参数获取，访问路径为/anno/requestParam？id=1。
       @RequestMapping(value = "/requestParam", produces = "text/plain;charset=UTF-8") 
       public @ResponseBody String passRequestParam(Long id, 
               HttpServletRequest request) { 
           
           return "url:" + request.getRequestURL() + " can access,id: " + id; 
     
       } 
     
       //演示解释参数到对象，访问路径为/anno/obj？id=1&name=xx。
       @RequestMapping(value = "/obj", produces = "application/json;charset=UTF-8") 
       //@ReponseBody也可以用在方法上。
       @ResponseBody 
       public String passObj(DemoObj obj, HttpServletRequest request) { 
           
            return "url:" + request.getRequestURL() 
                       + " can access, obj id: " + obj.getId()+" obj name:" + obj.getName(); 
     
       } 
     
       //演示映射不同的路径到相同的方法，访问路径为/anno/name1或/anno/name2
       @RequestMapping(value = { "/name1", "/name2" }, produces = "text/plain;charset=UTF-8") 
       public @ResponseBody String remove(HttpServletRequest request) { 
           
           return "url:" + request.getRequestURL() + " can access"; 
       } 
     
   } 
   ```

3. @RestController演示

   ```java
   package com.wisely.highlight_springmvc4.web.ch4_3; 
     
   import org.springframework.web.bind.annotation.RequestMapping; 
   import org.springframework.web.bind.annotation.RestController; 
     
   import com.wisely.highlight_springmvc4.domain.DemoObj; 
     
   @RestController //使用@RestController，声明是控制器，并且返回数据时不需要@ResponseBody
   @RequestMapping("/rest") 
   public class DemoRestController { 
       
       //返回数据的媒体类型为json
       @RequestMapping(value = "/getjson",produces={"application/json;charset=UTF-8"}) 
       public DemoObj getjson (DemoObj obj){ 
           //直接返回对象，对象会自动转换成json。
           return new DemoObj(obj.getId()+1, obj.getName()+"yy"); 
       } 
       //返回数据的媒体类型为xml。
       @RequestMapping(value = "/getxml",produces={"application/xml;charset=UTF-8"}) 
       public DemoObj getxml(DemoObj obj){
           //直接返回对象，对象会自动转换为xml。
           return new DemoObj(obj.getId()+1, obj.getName()+"yy");
       } 
     
   } 
   ```

   

