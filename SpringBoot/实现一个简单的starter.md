# 实现一个简单的starter

1. 新建starter的maven项目；

2. 修改pom.xml:

   ```java
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"> 
     <modelVersion>4.0.0</modelVersion> 
     
     <groupId>com.wisely</groupId> 
     <artifactId>spring-boot-starter-hello</artifactId> 
     <version>0.0.1-SNAPSHOT</version> 
     <packaging>jar</packaging> 
     
     <name>spring-boot-starter-hello</name> 
     <url>http://maven.apache.org</url> 
     
     <properties> 
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 
     </properties> 
     
     <dependencies> 
       <dependency> 
           <groupId>org.springframework.boot</groupId> 
   ```

   