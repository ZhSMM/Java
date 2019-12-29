# 简单的通过properties文件加载属性的代码

```java
//1.Properities文件中键值对：键名=键值，均不需要加引号，且不用‘;’结束，用换行来分隔不同属性对。
//2."12.properties"文件只能放在类路径resource路径，且不能加‘/’。

InputStream read= Thread.currentThread().
            			 getContextClassLoader().
                         getResourceAsStream("12.properties");
Properties props=new Properties();
props.load(read);
System.out.println(props.getProperty("hh"));
```

