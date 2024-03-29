1.解压部署Maven核心程序
	①检查JAVA_HOME环境变量
		C:\Windows\System32>echo %JAVA_HOME%
		D:\DevInstall\jdk1.7.0_07
	②解压Maven的核心程序
		将apache-maven-3.2.2-bin.zip解压到一个非中文无空格的目录下。例如：D:\DevInstall\apache-maven-3.2.2
	③配置环境变量
		M2_HOME D:\DevInstall\apache-maven-3.2.2
		path	D:\DevInstall\apache-maven-3.2.2\bin
	④查看Maven版本信息验证安装是否正确
		C:\Windows\System32>mvn -v
		Apache Maven 3.2.2 (45f7c06d68e745d05611f7fd14efb6594181933e; 2014-06-17T21:51:42+08:00)
		Maven home: D:\DevInstall\apache-maven-3.2.2\bin\..
		Java version: 1.7.0_07, vendor: Oracle Corporation
		Java home: D:\DevInstall\jdk1.7.0_07\jre
		Default locale: zh_CN, platform encoding: GBK
		OS name: "windows 7", version: "6.1", arch: "amd64", family: "windows"
2.修改本地仓库
	①默认本地仓库位置：~\.m2\repository
		~表示当前用户的家目录，例如：C:\Users\[你当前登录系统的用户名]
	②指定本地仓库位置的配置信息文件：apache-maven-3.2.2\conf\settings.xml
	③在根标签settings下添加如下内容：

`<localRepository>[本地仓库路径，也就是RepMaven.zip的解压目录]</localRepository> `
3.第一个Maven工程
	①目录结构
		Hello
		|---src
		|---|---main
		|---|---|---java
		|---|---|---resources
		|---|---test
		|---|---|---java
		|---|---|---resources
		|---pom.xml

​	②POM文件内容

```xml
<?xml version="1.0" ?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.maven</groupId>
    <artifactId>Hello</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <name>Hello</name>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

​	③编写主程序代码
​		在src/main/java/com/atguigu/maven目录下新建文件Hello.java，内容如下

```java
package com.atguigu.maven;
public class Hello {
    public String sayHello(String name){
        return "Hello "+name+"!";
    }
}
```

​	

​	④编写测试代码
​		在/src/test/java/com/atguigu/maven目录下新建测试文件HelloTest.java

```java
package com.atguigu.maven;	
import org.junit.Test;
import static junit.framework.Assert.*;
public class HelloTest {
    @Test
    public void testHello(){
        Hello hello = new Hello();
        String results = hello.sayHello("litingwei");
        assertEquals("Hello litingwei!",results);	
    }
}
```

​	⑤运行几个基本的Maven命令
​		mvn compile	编译
​		mvn clean	清理
​		mvn test	测试
​		mvn package	打包
​		※注意：运行Maven命令时一定要进入pom.xml文件所在的目录！
4.第二个Maven工程
​	①工程名：HelloFriend
​	②目录结构与第一个Maven工程相同
​	③POM文件

```xml
<?xml version="1.0" ?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>HelloFriend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>HelloFriend</name>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.0</version>
            <scope>test</scope>
        </dependency>		

        <dependency>
            <groupId>com.atguigu.maven</groupId>
            <artifactId>Hello</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>

    </dependencies>

</project>
```

​	


​	④主程序：在src/main/java/com/atguigu/maven目录下新建文件HelloFriend.java

```java
package com.atguigu.maven;	
import com.atguigu.maven.Hello;
public class HelloFriend {
    public String sayHelloToFriend(String name){
        Hello hello = new Hello();
        String str = hello.sayHello(name)+" I am "+this.getMyName();
        System.out.println(str);
        return str;
    }
    public String getMyName(){
        return "John";
    }
}
```

​	⑤测试程序：在/src/test/java/com/atguigu/maven目录下新建测试文件HelloFriendTest.java

```java
package com.atguigu.maven;	
import static junit.framework.Assert.assertEquals;
import org.junit.Test;
import com.atguigu.maven.Hello;
public class HelloFriendTest {
    @Test
    public void testHelloFriend(){
        HelloFriend helloFriend = new HelloFriend();
        String results = helloFriend.sayHelloToFriend("litingwei");
        assertEquals("Hello litingwei! I am John",results);	
    }
}

```

⑥运行Maven命令

mvn install	安装

5.第三个Maven工程
	①设置通过Maven创建的工程的JDK版本——一劳永逸
		[1]打开settings.xml文件
		[2]找到profiles标签
		[3]加入如下配置

```xml
<profile>
    <id>jdk-1.7</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.7</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
        <maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
    </properties>
</profile>
```



​	②工程坐标

```xml
groupId：com.atguigu.maven
ArtifactId：MakeFriends
Package：com.atguigu.maven
```



​	③在src/main/java中新建类com.atguigu.maven.MakeFriends

```java
public String makeFriends(String name){
    HelloFriend friend = new HelloFriend();
    friend.sayHelloToFriend("litingwei");
    String str = "Hey,"+friend.getMyName()+" make a friend please.";
    System.out.println(str);
    return str;
}
```

​	④在src/test/java中新建类com.atguigu.maven.MakeFriendsTest

```java
package com.atguigu.maven;
import static junit.framework.Assert.assertEquals;
import org.junit.Test;
public class MakeFriendsTest {
    @Test
    public void testMakeFriends(){		
        MakeFriends makeFriend = new MakeFriends();
        String str = makeFriend.makeFriends("litingwei");
        assertEquals("Hey,John make a friend please.",str);
    }
}
```



​	⑤添加依赖信息

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.9</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>HelloFriend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```

​	⑥在Eclipse环境下执行Maven命令：右击pom.xml选择run as 中的命令执行即可
6.测试依赖的范围对传递性的影响
​	①在Hello中添加对spring-core的依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.0.0.RELEASE</version>
    <scope>compile</scope>
</dependency>
```

​	②在HelloFriend中查看spring-core是否被加入了运行时环境
​	③将Hello中对spring-core的依赖范围修改为test，再到HelloFriend中检查
​	④将Hello中对spring-core的依赖范围修改为provided，再到HelloFriend中检查
​	⑤结论：非compile范围的依赖不能传递，必须在有需要的工程中单独加入
7.测试依赖原则
​	①路径最短者优先
​		[1]在Hello中依赖log4j-1.2.17

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



[2]在HelloFriend中依赖log4j-1.2.14

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.14</version>
</dependency>
```


[3]查看MakeFriends中自动引入的log4j是哪个版本

②路径相同时先声明者优先
	[1]创建OurFriends工程，依赖log4j-1.2.17
	[2]让MakeFriends依赖OurFriends
	[3]测试MakeFriends中，HelloFriend和OurFriends依赖的先后顺序和引入的log4j版本之间的关系



8.创建Web工程
	①ServletAPI依赖

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>
</dependency>
```

​	②JSPAPI依赖

```xml
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.1.3-b06</version>
    <scope>provided</scope>
</dependency>
```

9.Web工程自动部署

```xml
<build>
    <finalName>AtguiguWeb</finalName>
    <plugins>
        <plugin>
            <groupId>org.codehaus.cargo</groupId>
            <artifactId>cargo-maven2-plugin</artifactId>
            <version>1.2.3</version>
            <configuration>
                <container>
                    <containerId>tomcat6x</containerId>
                    <home>D:\DevInstall\apache-tomcat-6.0.39</home>
                </container>
                <configuration>
                    <type>existing</type>
                    <home>D:\DevInstall\apache-tomcat-6.0.39</home>
                    <!-- 如果Tomcat端口为默认值8080则不必设置该属性 -->
                    <properties>
                        <cargo.servlet.port>8989</cargo.servlet.port>
                    </properties>
                </configuration>
            </configuration>
            <executions>  
                <execution>  
                    <id>cargo-run</id>  
                    <phase>install</phase>  
                    <goals>  
                        <goal>run</goal>  
                    </goals>  
                </execution>  
            </executions>
        </plugin>
    </plugins>
</build>
```

10.继承
	①创建Parent工程，打包方式为pom
	②收集所有非compile范围的依赖信息，使用dependencyManagement标签统一管理

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

​	③在各个子工程中引用父工程

```xml
<parent>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>Parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!-- 以当前文件为基准查找父工程中pom.xml文件的相对路径 -->
    <relativePath>../Parent/pom.xml</relativePath>
</parent>
```



④删除子工程中的重复信息
	groupId
	artifactid
⑤在子工程中找到被父工程管理的依赖信息，删除版本号部分
⑥在父工程中统一修改已管理的依赖信息的版本号，看是否能够控制所有子工程

11.聚合
	在总的聚合工程中加入如下信息

```xml
<modules>
    <module>../Hello</module>
    <module>../HelloFriend</module>
    <module>../MakeFriends</module>
</modules>
```

