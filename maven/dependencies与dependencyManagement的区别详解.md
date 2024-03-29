

> 在上一个项目中遇到一些jar包冲突的问题，之后还有很多人分不清楚dependencies与dependencyManagement的区别，本篇文章将这些区别总结下来。

## 1、DepencyManagement

​		当我们的项目模块很多的时候，我们使用Maven管理项目非常方便，帮助我们管理构建、文档、报告、依赖、scms、发布、分发的方法。可以方便的编译代码、进行依赖管理、管理二进制库等等。

​		由于我们的模块很多，所以我们又抽象了一层，抽出一个`itoo-base-parent `来管理子项目的公共的依赖。为了项目的正确运行，必须让所有的子项目使用依赖项的统一版本，必须确保应用的各个项目的依赖项和版本一致，才能保证测试的和发布的是相同的结果。

​		在我们项目顶层的POM文件中，我们会看到`dependencyManagement `元素。通过它元素来管理jar包的版本，让子项目中引用一个依赖而不用显示的列出版本号。Maven会沿着父子层次向上走，直到找到一个拥有`dependencyManagement `元素的项目，然后它就会使用在这个`dependencyManagement `元素中指定的版本号。

pom继承关系图如下：

![image-20210601170645126](https://gitee.com/zzursy/blog-image/raw/master/img/20210601170645.png)

**Itoo-base-parent(pom.xml)**

```xml
<dependencyManagement> 

    <dependencies> 
        <dependency> 
            <groupId>org.eclipse.persistence</groupId> 
            <artifactId>org.eclipse.persistence.jpa</artifactId> 
            <version>${org.eclipse.persistence.jpa.version}</version> 
            <scope>provided</scope> 
        </dependency> 

        <dependency> 
            <groupId>javax</groupId> 
            <artifactId>javaee-api</artifactId> 
            <version>${javaee-api.version}</version> 
        </dependency> 
    </dependencies> 
</dependencyManagement>
```

**Itoo-base(pom.xml)**

```xml
<!--继承父类--> 
<parent> 
    <artifactId>itoo-base-parent</artifactId> 
    <groupId>com.tgb</groupId> 
    <version>0.0.1-SNAPSHOT</version> 
    <relativePath>../itoo-base-parent/pom.xml</relativePath> 
</parent> 

<modelVersion>4.0.0</modelVersion> 
<artifactId>itoo-base</artifactId> 
<packaging>ejb</packaging> 

<!--依赖关系--> 
<dependencies> 
    <dependency> 
        <groupId>javax</groupId> 
        <artifactId>javaee-api</artifactId> 
    </dependency> 

    <dependency> 
        <groupId>com.fasterxml.jackson.core</groupId> 
        <artifactId>jackson-annotations</artifactId> 
    </dependency> 

    <dependency> 
        <groupId>org.eclipse.persistence</groupId> 
        <artifactId>org.eclipse.persistence.jpa</artifactId> 
        <scope>provided</scope> 
    </dependency> 
</dependencies> 
</project>
```

这样做的好处：**统一管理项目的版本号，确保应用的各个项目的依赖和版本一致，才能保证测试的和发布的是相同的成果。**

因此，在顶层pom中定义共同的依赖关系。同时可以避免在每个使用的子项目中都声明一个版本号，这样想升级或者切换到另一个版本时，只需要在父类容器里更新，不需要任何一个子项目的修改；

如果某个子项目需要另外一个版本号时，只需要在dependencies中声明一个版本号即可。子类就会使用子类声明的版本号，不继承于父类版本号。



## 2、Dependencies

相对于dependencyManagement，所有声明在dependencies里的依赖都会自动引入，并默认被所有的子项目继承。



## 3、区别

- dependencies即使在子项目中不写该依赖项，那么子项目也会从父项目中继承该依赖项**（全部继承）**

- dependencyManagement里**只是声明依赖，并不实现引入**，因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的。只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom。另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

> <dependencies>中的jar直接加到项目中，管理的是依赖关系（如果有父pom、子pom，则子pom中只能被动接受父类的版本）；
>
> <dependencyManagement>主要管理版本，对于子类继承同一个父类是很有用的，集中管理依赖版本不添加依赖关系，对于其中定义的版本，子pom不一定要继承父pom所定义的版本。

## 4、Maven约定优于配置

 *它提出这一概念，为项目提供合理的默认行为，无需不必要的配置。提供了默认的目录*

![image-20210601171335831](https://gitee.com/zzursy/blog-image/raw/master/img/20210601171335.png)

 对于**Maven**约定优于配置的理解，一方面对于小型项目基本满足我们的需要基本不需要自己配置东西，使用**Maven**已经配置好的，快速上手，学习成本降低；另一方面，对于不满足我们需要的还可以自定义设置，体现了灵活性。配置大量减少了，随着项目变的越复杂，这种优势就越明显。





> 转载于：http://blog.csdn.net/liutengteng130/article/details/46991829