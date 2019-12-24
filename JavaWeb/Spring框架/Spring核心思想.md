# Spring核心思想

### IOC（Inversion Of Control）控制反转

> IOC是一种设计思想，将原本在程序中手动创建对象的控制权交给Spring框架来管理。IOC容器是Spring用来实现IOC的载体，实际上就是一个Map（key,value)，Map中存放的是各种对象。
>
> IOC容器就像一个工厂，当需要创建对象时，只需要配置好配置文件/注解就可以，而不需要考虑对象的创建过程，增加项目可维护性。

### AOP（Aspect-Oriented Programming）面向切面编程

> AOP：将与业务无关，却为业务模块所共同调用的逻辑或责任（如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，提高可维护性和可扩展性。使用AOP后可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，可以简化代码量，提高系统的扩展性。
>
> Spring AOP是基于动态代码的，如果要代理的对象实现了某个接口，那么spring aop就会使用JDK动态代理去创建代理对象；而对于没有实现接口的对象，就无法使用JDK动态代理，转而使用CGlib动态代理生成一个被代理对象的子类来作为代理。

### Spring AOP与AspectJ AOP区别

spring aop：运行时增强，基于代理（Proxying）；

Aspect J AOP：基于字节码操作（Bytecode Manipulation），在编译时增强，功能更强大。