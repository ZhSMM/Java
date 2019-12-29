# Class.forName与ClassLoader

Class.forName()和ClassLoader都能对类进行加载。

ClassLoader是遵循双亲委派模型最终调用启动类加载器的类加载器，实现的功能是“通过一个类的全限定名来获取此类的二进制字节流”，获取到二进制流后放到JVM中。

Class.forName(）实际也是调用了ClassLoader来实现的。Class.forName()源码：

```java
@CallerSensitive
public static Class<?> forName(String className)
    throws ClassNotFoundException{
    Class<?> caller=Reflection.getCallerClass();
    return forName0(className,true,ClassLoader.getClassLoader(caller),caller);
}
```

Class.forName()最后调用的是forName0这个方法，在这个方法中的第二个参数被设为了true，代表进行了类初始化（类初始化会执行类中的静态代码块，以及对静态变量的赋值等操作）。

也可以调用Class.forName(String name,boolean initialize,ClassLoder loader)来选择在加载类时是否进行类初始化。

例:

```java
public class Test {
    static {
        System.out.println("静态代码块执行");
    }
    private static String str=Test.getStr();
    public static String getStr(){
        System.out.println("静态变量赋值");
        return "赋值";
    }
}

//测试
public class Try {
    public static void main(String[] args) 
        throws ClassNotFoundException {
        Class.forName("com.zhangs.classforNameTest.Test");
        System.out.println("---类加载器内容---");
        ClassLoader.getSystemClassLoader().loadClass("com.zhangs.classforNameTest.Test");
    }
}
//结果
静态代码块执行
静态变量赋值
---类加载器内容---

```

```java
Class clazz=Class.forName("全路径");   //会对类进行初始化：比如执行静态代码块等
Clazz clazz=ClassLoader.getSystemClassLoader().loadClass("全路径");  //只加载类，不初始化

clazz.newInstance():实例化
Method[] methods=clazz.getMethods();
methods[].invoke(clazz.newInstance);

Class.class:获取类字面量，编译时确定
Object.getClass():获取运行时的类实例。
```



```java

```

