# Java自定义注解

> Java注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。包含在`java.lang.annotation`包中。

## 一、元注解

> 元注解是指注解的注解。包括@Retention、@Target、@Document、@Inherited四种。

### 1、 @Retention：定义注解的保留策略

```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Retention {
	RetentionPolicy value();
}
```

> @Retention指示注释类型的注释要保留多久。默认@Retention(RetentionPolicy.CLASS) 。

@Retention(RetentionPolicy.SOURCE)  //注解仅存在于源码中，在class字节码文件中不包含

@Retention(RetentionPolicy.CLASS) //默认的保留策略，注解会在class字节码文件中存在，但运行时VM无法获得

@Retention(RetentionPolicy.RUNTIME)  //注解会在class字节码文件中存在，在运行时可以通过反射获取到

### 2、@Target：限定注解

> @Target指示注释类型所适用的程序元素的种类。如果注释类型声明中不存在Target元注释，则声明的类型可以用在任意程序元素上。如果存在这样的元注释，则编译器强制实施指定的使用限制。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.ANNOTATION_TYPE)
public @interface Target {
    ElementType[] value();
}
```

@Target(ElementType.ANNOTATION_TYPE)：此元注释指示该声明类型是元注释类型，只能用在注释类型声明上。

|   ElementType   |              指示类型              |
| :-------------: | :--------------------------------: |
| ANNOTATION_TYPE | 注释类型声明，该注解只能用于注释上 |
|   CONSTRUCTOR   |            构造方法声明            |
|      FIELD      |      字段声明（包含枚举常量）      |
| LOCAL_VARIABLE  |            局部变量声明            |
|     METHOD      |              方法声明              |
|     PACKAGE     |               包声明               |
|    PARAMETER    |              参数声明              |
|      TYPE       | 类、接口（包括注释类型）或枚举声明 |

### 3、@Document：注解信息是否包含在javadoc文档中

> @Document注解标记的元素，Javadoc工具会将此注解标记元素的注解信息包含在javadoc中。默认注解信息不包含在文档中。

例：

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface Book{    
	String author() default "Zhang song";    
	String name() default "Good Book";
}

//使用
@Book(name="Very Good Book",author = "zhang song")
public class Test {
    private String author;
    private String name;
}

//编译：
打开cmd 输入javadoc命令：
javadoc -d D:\doc org.springmorning.demo.javabase.annotation.meta -encoding utf-8 -charset utf-8

说明：
-d D:\doc 表示：doc文件输入目录为D盘的doc文件夹；
org.springmorning.demo.javabase.annotation.meta 表示此包中所有类需要生成java doc html文件；
-encoding utf-8 表示：java代码采用的是utf-8字符编码编写的；
-charset utf-8 表示：java doc html文件为utf-8字符编码。
```

### 4.@Inherited：在注解中使用继承

> 默认情况下注解不会继承到子类中，可以在自定义注解时加上`java.lang.annotation.Inherited` 注解声明继承。
>
> 指示注释类型被自动继承。如果注释类型声明中存在Inherited元注释，并且用户在某一类声明中查询该注释类型，同时该类声明中没有此类型的注释，则将在该类的超类中自动查询该注释类型。此过程会重复进行，直到找到此类型的注释或达到了该类层次结构的顶层（Object）为止。如果没有超类具有该类型的注释，则查询将指示当前类没有这样的注释。
>
> 注意：如果使用注释类型注释类以外的任何事物，此注释类型都是无效的。还要注意，此元注释仅促成从超类继承注释；对已实现接口的注释无效。

```java
//源码
@Documented
@Retention(Value=RUNTIME)
@Target(Value=ANNOTATION_TYPE)
public @interface Inherited{}    
```

## 二、自定义注解

1. Annotation的工作原理

   JDK5.0后提供了注解功能，允许开发者定义和使用自己的注解类型。该功能由一个定义注解类型的语法和描述一个注解声明的语法，读取注解的API，一个使用注解修饰的class文件和一个注解处理工具组成。

   Annotation并不直接影响代码的语义，但可以被视为程序的工具或类库，能对正在运行的程序语义有影响。Annotation可以从源文件、class文件或在运行时通过反射读取。

2. @Override注解

   表示子类要重写父类的对应方法

3. @Deprecated

   表示方法不建议使用

4. @SuppressWarnings

   抑制警告，一般要注释在最里层的元素上

5. 自定义注解，通过@interface，自动继承java.lang.annotation.Annotation接口，由编译器完成其他细节。在定义注解时，不能继承其他的注解或接口。

```java
//定义简单注解
public @interface SimpleAnnotation{
    
}
public class Test{   //使用注解
    @SimpleAnnotation
    public void print(){
        
    }
}

//定义带变量的注解
public @interface SimpleAnnotation{
    String value1() default "默认值";
}
public class Test{   //使用注解
    @SimpleAnnotation(value1="abc") //使用注解时，属性名为value时，赋值时可以不指定名称
    public void print(){            //而直接写上属性值；不为value的变量均须以
        							//name=value的方式赋值
    }
}

//多变量使用枚举
enum MyEnum{
    SUN,
    WED
}
public @interface SimpleAnnotation{
    String value1() default "默认值";
    MyEnum value2() default MyEnum.SUN;
}
public class Test{   //使用注解
    @SimpleAnnotation(value1="abc",value2=MyEnum.WED)
    public void print(){           
        							
    }
}

//数组变量
public @interface SimpleAnnotation{
    String[] value1() default "默认值";
}
public class Test{   //使用注解
    @SimpleAnnotation(value1={"abc","hjgg"})
    public void print(){           
        							
    }
}
```

6. 使用反射读取RUNTIME保留策略的Annotation信息

```java
java.lang.refect
interface AnnotatedElement
所有实现该接口的类：AccessibleObject、Class、Constructor、Field、Method、Package

表示目前正在此VM中运行的一个已注释元素。该接口允许通过反射读取注释，且该接口返回的注释都是不可变且可序列化的。调用者可以修改已赋值数组枚举成员的访问器返回的数组；这不会对其他调用者产生任何影响。
    
boolean isAnnotationPresent(Class<? extends Annotation> annotationClass):
如果指定注释类型在此元素上，返回true，或者返回false
annotationClass：对应注释类型的Class对象

<T extends Annotation> T getAnnotation(Class<T> annotationClass):
如果存在该元素的指定类型的注释，则返回这些注释，否则返回null

Annotation[] getDeclaredAnnotation():返回直接存在于此元素上的注释，忽略继承的注释。

//例子
//自定义注解
@Target({ElementType.TYPE,ElementType.FIELD,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Book{
    String author() default "Zhang song";
    String name() default "Good Book";
}
//使用注解
public class Test {
    private String author;
    private String name;
    @Book(name="Very Good Book",author = "zhang song")
    public  void print(){
        System.out.println("dhjg");
    }
}
//反射获取注解
public class Main {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        Test test=new Test();
        //获取需要处理的方法的Method实例
        Method method=Test.class.getMethod("print",new Class[]{});
        if (method.isAnnotationPresent(Book.class)){ //判断该方法是否包含Book注解
            Book book=method.getAnnotation(Book.class); //获取该方法的Book注解实例
            method.invoke(test,new Object[]{});   //执行该方法
            String value1=book.author();  
            String value2=book.name();
            System.out.println(value1+value2);
        }
        Annotation[] annotations=method.getAnnotations();
        for (Annotation annotation:annotations) {
            System.out.println(annotation);
        }
    }
}
```





