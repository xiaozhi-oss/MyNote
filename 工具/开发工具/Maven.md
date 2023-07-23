# 一、Maven概述和安装

## 1、概述

​	是一个工具，项目架构管理工具



**目前阶段**：自动导入jar包，解决导jar包的工作

**核心思想**：约定大于配置

- 有约束，不要去违反

Maven会规定好如何去编写我们的java代码，必须按照这个规范来；





## 2、安装

官网：https://maven.apache.org/

![image-20201229231729619](F:\Java学习\截图\image-20201229231729619.png)

下载文件后解压即可



### 目录结构

bin：可执行文件

boot：需要启动的一些选项，jar包

conf：配置文件

lib：依赖





### 配置环境变量

- M2_HOME			maven目录下的bin目录
- MAVEN_HOME     maven的目录
- 在系统的path中呢配置 %MAVEN_HOME%\bin

**说明**：为什么要创建两个，按照规范来

通过mvn -version来查看是否配置成功，一下情况就是成功的

![image-20201229233201823](F:\Java学习\截图\image-20201229233201823.png)





### 设置阿里云镜像

![image-20201229232147398](F:\Java学习\截图\image-20201229232147398.png)

其他的先不用配，我们配置mirror（镜像）标签里面的内容

**原因**：maven是国外的，访问慢，我们可以用镜像加速我们的下载

**建议使用阿里云镜像**

在mirrors标签下添加如下内容

```xml
  <mirrors>
	 <mirror>
　　　　<id>nexus-aliyun</id>
　　　　<mirrorOf>*,!jeecg,!jeecg-snapshots</mirrorOf>
　　　　<name>Nexus aliyun</name>
　　　　<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
　　</mirror>
  </mirrors>
```





### 创建本地仓库

在本地的仓库，远程仓库;

建立一个本地仓库

1.  创建一个目录

    ![image-20201229233845214](https://gitee.com/xiaozhi-oos/save-img/raw/upload-img/image-20201229233845214.png)

2.  修改settings.xml配置文件

    ![image-20201229234118887](https://gitee.com/xiaozhi-oos/save-img/raw/upload-img/image-20201229234118887.png)

**说明**：如果没有创建一个，那么maven就会使用默认的路径

![image-20201229234312669](F:\Java学习\截图\image-20201229234312669.png)

我们让它指向我们创建好的本地仓库的路径





# 二、在IDEA中使用Maven

## 1、创建maven项目

1、创建

![image-20201229235654594](F:\Java学习\截图\image-20201229235654594.png)

**说明**：创建一个MavenWeb项目，不选择模板就是创建一个空的Maven项目

2	选择路径

![image-20201230000241545](F:\Java学习\截图\image-20201230000241545.png)

![image-20201230000010486](F:\Java学习\截图\image-20201230000010486.png)

3	导入包

![image-20201230000514207](F:\Java学习\截图\image-20201230000514207.png)

4	等待项目初始化完毕

![image-20201230000639592](F:\Java学习\截图\image-20201230000639592.png)

5	看配置

IDEA项目创建成功之后，看一眼Maven配置

![image-20201230001138463](F:\Java学习\截图\image-20201230001138463.png)

改成我们自己的文件就可以了

![image-20201230001244210](F:\Java学习\截图\image-20201230001244210.png)

![image-20201230001415196](F:\Java学习\截图\image-20201230001415196.png)

这样就设置完成了





## 2、标记文件夹功能

**第一种方式**

![image-20201230004114624](F:\Java学习\截图\image-20201230004114624.png)



**第二种方式**

![image-20201230004616714](F:\Java学习\截图\image-20201230004616714.png)







## 3、配置tomcat服务器

![image-20201230141304280](F:\Java学习\截图\image-20201230141304280.png)





# 三、pom模式-Maven工程关系

Maven工县基fRoM (roject Ohject ModeD项目对象模型)模式实现的。在Maven中每个项目都相当于是-个对象,对象(项目)和对象(项目)之间是有关系的。关系包含了:依赖、继承、聚合,实现Maven项目可以更加方便的实现导jar包、拆分项目等效果。

我们可以通过package标签来指定一个模块为pom项目，pom项目也就是逻辑项目，可以说是用来控制版本的，通过继承这个pom项目可以使用对应的包，因为包的版本是在pom项目中统一管理的，我们需要变动版本的时候就可以很轻松的修改对应的版本，我们只需要改动property标签中的值就可以了。

![image-20210323220750269](F:\Java学习\截图\image-20210323220750269.png)



## 依赖的传递性

假如A依赖于B，B依赖于C，那么A就依赖C，



## 依赖基本配置

在pom.xml文件 根元素project下的 dependencies标签中，配置依赖信息，内可以包含多个 dependence元素，以声明多个依赖。每个依赖dependence标签都应该包含以下元素：

groupId, artifactId, version : 依赖的基本坐标， 对于任何一个依赖来说，基本坐标是最重要的， Maven根据坐标才能找到需要的依赖。
这3个属性，可以理解成一个3维的x-y-z坐标，可以通过3个值，来确定一个库中维一个依赖

gooupId 可以理解成一个java的包名(java包名中默认以公司域名倒写)，在对应的.m2仓库，它是一个可以有很多层的目录,以. 来进行目录分级

**artifactID** 可以理解是项目或模块的名称

**version** 表示不同的版本号

**type**： 依赖的类型，大部分情况下不需要声明。 默认值为jar

Scope： 依赖范围（compile,test,provided,runtime,system 五种状态）

**Optional**：标记依赖是否可选,true表示可选，依赖不传递，false表示不可选，依赖传递

**exclusions**： 用来排除传递性依赖 其中可配置多个exclusion标签，每个exclusion标签里面对应的有groupId, artifactId, version三项基本元素

版权声明：本文为CSDN博主「_大帅_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ted_cs/article/details/83865969





## 依赖范围

也就是在什么时候有效，比如你的依赖范围（scope）是test，那么只有在测试的时候才会引入，打包的时候不会将test范围的jar包打包

**compile**: 编译依赖范围。如果没有指定，就会默认使用该依赖范围。使用此依赖范围的Maven依赖，对于编译、测试、运行三种classpath都有效

**system**: 系统依赖范围。该依赖与编译、测试、运行三种classpath的关系，和provided依赖范围完全一致。但是，使用system范围依赖时必须通过systemPath元素显式地指定依赖文件的路径。由于此类依赖不是通过Maven仓库解析的，而且往往与本机系统绑定，可能造成构建的不可移植，因此应该谨慎使用。

**provided**: 已提供依赖范围。使用此依赖范围的Maven依赖，对于编译和测试classpath有效，但在运行时无效。典型的例子是servlet-api，编译和测试项目的时候需要该依赖，但在运行项目的时候，由于容器已经提供，就不需要Maven重复地引入一遍(如：servlet-api)。

**runtime**: 运行时依赖范围。使用此依赖范围的Maven依赖，对于测试和运行classpath有效，但在编译主代码时无效。典型的例子是JDBC驱动实现，项目主代码的编译只需要JDK提供的JDBC接口，只有在执行测试或者运行项目的时候才需要实现上述接口的具体JDBC驱动。

**test**: 测试依赖范围。使用此依赖范围的Maven依赖，只对于测试classpath有效，在编译主代码或者运行项目的使用时将无法使用此类依赖。典型的例子就是JUnit，它只有在编译测试代码及运行测试的时候才需要。



版权声明：本文为CSDN博主「_大帅_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ted_cs/article/details/83865969



## 工程关系

### 继承

**说明**：在一个父项目中可以创建多个子项目，这也方便我们已多个项目开发的形式开发程序，每个独立的模块都是一个项目，便于团队协作

1	创建一个pom工程

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xiaozhi</groupId>
    <artifactId>MavenTest</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>Subproject</module>
    </modules>
	<!--修改版本在这里修改就可以了-->
    <properties>
        <mysql-connector>5.1.47</mysql-connector>
        <mybatis>3.5.2</mybatis>
        <junit>4.12</junit>
    </properties>
    <dependencyManagement>
        <dependencies>
            <!--mysql驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector}</version>
            </dependency>
            <!--mybatis-->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis}</version>
            </dependency>
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

2	创建一个子工程继承父工程

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xiaozhi</groupId>
    <artifactId>MavenTest2</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>com.xiaozhi</groupId>
        <artifactId>MavenTest</artifactId>
        <version>1.0-SNAPSHOT</version>
        <!--引用的xml文件的位置-->
        <relativePath>../MavenTest/pom.xml</relativePath>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </dependency>
    </dependencies>
</project>
```

3	我们可以看到我么依赖包中就引入了对应的包

![image-20210323221744112](F:\Java学习\截图\image-20210323221744112.png)





### 聚合

每个功能模块都是一个项目，以项目的方式进行开发，便于协作和管理

![image-20210323222442161](F:\Java学习\截图\image-20210323222442161.png)







# 四、常见插件

## 1、编译器插件

在文件的下面添加

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```



## 2、资源拷贝插件

它会指定那个文件夹的那些文件会被放入到jar包中

```xml
<!--资源拷贝插件-->
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
    <resource>
        <directory>src/main/resources</directory>
        <includes>
            <include>**/*.xml</include>
            <include>**/*.properties</include>
        </includes>
    </resource>
</resources>
```



# 五、Maven常见命令

**install**		本地安装，包含编译，打包，安装本地仓库

**clean**		 清除已经编译信息

**compile**	 只编译，javac命令

**package**	打包。包含编译，打包两个功能



**install和package的区别**

package命令完成了项目编译，单元测试、打包功能，但是没有把大号的可执行jar包（war包或者其他形式的包）部署到本地maven仓库和远程maven私服仓库





# 六、多环境开发配置











# 七、私服



















