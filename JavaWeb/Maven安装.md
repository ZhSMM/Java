# Maven安装

## 安装

1. 下载Maven

   根据操作系统下载正确的Maven版本，并解压到任意目录。

   下载地址：[https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi)

2. 配置Maven

   创建环境变量： 变量名：M2_HOME   变量值：安装路径

   Path : %M2_HOME%\bin;

3. 测试安装：在控制台输入： `mvn-v`

## 使用

> Maven是基于项目对象模型的概念运作的，所以Maven的项目都有一个pom.xml用来管理项目的依赖以及项目的编译等功能。

1. dependencies元素

   > <dependencies></dependencies>，此元素包含多个项目依赖需要使用的<dependency></dependency>。

2. dependency元素

   > <dependency></dependency>内部通过groupId、artifactId以及version确定唯一的依赖，有人称这三个为坐标，代码如下。
   >
   > groupId：组织的唯一标识。
   >
   > artifactId：项目的唯一标识。
   >
   > version：项目的版本。

   ```Java
   <dependency> 
       <groupId>org.springframework</groupId> 
       <artifactId>spring-webmvc</artifactId> 
       <version>4.1.5.RELEASE</version> 
   </dependency> 
   ```

3. 变量定义

   > 变量定义：<properties></properties>可定义变量在dependency中引用，代码如下。

   ```java
   <properties>            
   <spring-framework.version>4.1.5.RELEASE</spring-framework.version> 
   </properties> 
   
   <dependency>
       <groupId>org.springframework</groupId> 
       <artifactId>spring-webmvc</artifactId> 
       <version>${spring-framework.version}</version> 
   </dependency> 
   ```

4. 编译插件

   > Maven提供了编译插件，可在编译插件中涉及Java的编译级别，代码如下。

   ```java
   <build> 
           <plugins> 
               <plugin> 
                   <groupId>org.apache.maven.plugins</groupId> 
                   <artifactId>maven-compiler-plugin</artifactId> 
                   <version>2.3.2</version> 
                   <configuration> 
                       <source>1.7</source> 
                       <target>1.7</target> 
                   </configuration> 
               </plugin> 
           </plugins> 
   </build> 
   ```

5. Maven运作方式

   > Maven会自动根据dependency中的依赖配置，直接通过互联网在Maven中心库下载相关依赖包到.m2目录下，.m2目录下是你本地Maven库。
   >
   > 如果你不知道你所依赖jar包的dependency怎么写的话，推荐到http://mvnrepository.com网站检索。
   >
   > 若Maven中心库中没有你需要的jar包（如Oracle），你需要通过下面的Maven命令打到本地Maven库后应用即可，如安装Oracle驱动到本地库：

   ```java
   mvn install:install-file -DgroupId=com.oracle "-DartifactId=ojdbc14" 
   "-Dversion=10.2.0.2.0" "-Dpackaging=jar" "-Dfile=D:\ojdbc14.jar" 
   ```

   