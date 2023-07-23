# 整合Thymeleaf

## 1、模板概述

模板引擎，我们其实大家听到很多，其实jsp就是一个模板引擎，还有用的比较多的freemarker，包括SpringBoot给我们推荐的Thymeleaf，模板引擎有非常多，但再多的模板引擎，他们的思想都是一样的，什么样一个思想呢我们来看一下这张图：

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7Idia351qHgmH2vbzurk1Pp6V42bcomyzTYY0q6ic7AB8lvciaoicxyalNYQYZgslIrIjdXWLFNcOxUmQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1&retryload=1)

模板引擎的作用就是我们来写一个页面模板，比如有些值呢，是动态的，我们写一些表达式。而这些值，从哪来呢，就是我们在后台封装一些数据。然后把这个模板和这个数据交给我们模板引擎，模板引擎按照我们这个数据帮你把这表达式解析、填充到我们指定的位置，然后把这个数据最终生成一个我们想要的内容给我们写出去，这就是我们这个模板引擎，不管是jsp还是其他模板引擎，都是这个思想。只不过呢，就是说不同模板引擎之间，他们可能这个语法有点不一样。其他的我就不介绍了，我主要来介绍一下SpringBoot给我们推荐的Thymeleaf模板引擎，这模板引擎呢，是一个高级语言的模板引擎，他的这个语法更简单。而且呢，功能更强大。

我们呢，就来看一下这个模板引擎，那既然要看这个模板引擎。首先，我们来看SpringBoot里边怎么用。



**Thymeleaf分析**

前面呢，我们已经引入了Thymeleaf，那这个要怎么使用呢？

我们首先得按照SpringBoot的自动配置原理看一下我们这个Thymeleaf的自动配置规则，在按照那个规则，我们进行使用。

我们去找一下Thymeleaf的自动配置类：ThymeleafProperties

```java
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
```

我们可以在其中看到默认的前缀和后缀！

我们只需要把我们的html页面放在类路径下的templates下，thymeleaf就可以帮我们自动渲染了。

使用thymeleaf什么都不需要配置，只需要将他放在指定的文件夹下即可！





## 2、使用

### 引入Thymeleaf

```xml
<!--thymeleaf-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```







## 3、Thymeleaf 语法

要学习语法，还是参考官网文档最为准确，我们找到对应的版本看一下；

Thymeleaf 官网：https://www.thymeleaf.org/ ， 简单看一下官网！我们去下载Thymeleaf的官方文档！

**我们做个最简单的练习 ：我们需要查出一些数据，在页面中展示**

1、修改测试请求，增加数据传输；

```java
@Controller
public class TestController {
    @RequestMapping("/t1")
    public String helloWorld(Model model){
        model.addAttribute("测试的数据");
        return "test";
    }
}
```

2、我们要使用thymeleaf，需要在html文件中导入命名空间的约束，方便提示。

我们可以去官方文档的#3中看一下命名空间拿来过来：

```html
 xmlns:th="http://www.thymeleaf.org"
```

3、我们去编写下前端页面

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--和vue一样，这里是通过th:绑定标签-->
<div th:text="${msg}"></div>

</body>
</html>
```

4、启动测试！

![image-20210212163853269](F:\Java学习\截图\image-20210212163853269.png)



**OK，入门搞定，我们来认真研习一下Thymeleaf的使用语法！**

**1、我们可以使用任意的 th:attr 来替换Html中原生属性的值！**

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7Idia351qHgmH2vbzurk1Pp6fGYIwv043icVDYuybRJDCGTSNTMEibFzzMdlKS4t07TQoicQJKQAe0slQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1&retryload=1)

**2、我们能写哪些表达式呢？**

```python

Simple expressions:（表达式语法）
Variable Expressions: ${...}：获取变量值；OGNL；
    1）、获取对象的属性、调用方法
    2）、使用内置的基本对象：#18
         #ctx : the context object.
         #vars: the context variables.
         #locale : the context locale.
         #request : (only in Web Contexts) the HttpServletRequest object.
         #response : (only in Web Contexts) the HttpServletResponse object.
         #session : (only in Web Contexts) the HttpSession object.
         #servletContext : (only in Web Contexts) the ServletContext object.

    3）、内置的一些工具对象：
　　　　　　#execInfo : information about the template being processed.
　　　　　　#uris : methods for escaping parts of URLs/URIs
　　　　　　#conversions : methods for executing the configured conversion service (if any).
　　　　　　#dates : methods for java.util.Date objects: formatting, component extraction, etc.
　　　　　　#calendars : analogous to #dates , but for java.util.Calendar objects.
　　　　　　#numbers : methods for formatting numeric objects.
　　　　　　#strings : methods for String objects: contains, startsWith, prepending/appending, etc.
　　　　　　#objects : methods for objects in general.
　　　　　　#bools : methods for boolean evaluation.
　　　　　　#arrays : methods for arrays.
　　　　　　#lists : methods for lists.
　　　　　　#sets : methods for sets.
　　　　　　#maps : methods for maps.
　　　　　　#aggregates : methods for creating aggregates on arrays or collections.
==================================================================================

  Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
  Message Expressions: #{...}：获取国际化内容
  Link URL Expressions: @{...}：定义URL；
  Fragment Expressions: ~{...}：片段引用表达式

Literals（字面量）
      Text literals: 'one text' , 'Another one!' ,…
      Number literals: 0 , 34 , 3.0 , 12.3 ,…
      Boolean literals: true , false
      Null literal: null
      Literal tokens: one , sometext , main ,…
      
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
    
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
    
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
    
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
    
Conditional operators:条件运算（三元运算符）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
    
Special tokens:
    No-Operation: _
```



**练习测试：**

**取文本数据**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
转义的：
<div th:text="${msg}"></div><br/>
不转义的：
<div th:utext="${msg}"></div>
</body>
</html>
```

结果显示

![image-20210212164910849](F:\Java学习\截图\image-20210212164910849.png)



**遍历数据**

1、 我们编写一个Controller，放一些数据

```java
@RequestMapping("/t2")
public String test2(Map<String,Object> map){
    //存入数据
    map.put("users", Arrays.asList("xiaozhi","mazhi"));
    //classpath:/templates/test.html
    return "test2";
}
```

2、测试页面取出数据

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--推荐使用第一种方式-->
<!--第一种遍历方式-->
<div th:each="user:${users}" th:text="${user}"></div>

<!--第二种遍历方式-->
<!--<div th:each="user:${users}">[[${user}]]</div>-->
</body>
</html>
```



3、启动项目测试！

**我们看完语法，很多样式，我们即使现在学习了，也会忘记，所以我们在学习过程中，需要使用什么，根据官方文档来查询，才是最重要的，要熟练使用官方文档！**



**th:unless语法**：这个和if是差不多的用法，但是又有点区别，if是如果什么什么，它是除非什么什么，当很多的数据需要满足是一个条件的时候就可以使用这个th:unless来进行判断

**th:block**：这个标签是thymeleaf的土著标签，表示块的意思



### 异常

==org.thymeleaf.exceptions.TemplateInputException: Error resolving template [/admin/login], template might not exist or might not be accessible by any of the configured Template Resolvers，出现这个异常事因为我们在使用静态资源过滤的时候没有把html文件加入到我们的过滤中==

![image-20210401192435323](F:\Java学习\截图\image-20210401192435323.png)

这样我们就可以访问到我们的thymeleaf就可以解析到我们的页面了



### 片段插入

**使用**

注意：要在template目录下放片段的文件，默认是找这个文件夹下的文件

insert、replace、include三种用法的区别

```html
<footer th:fragment="copy">
  &copy; 2011 The Good Thymes Virtual Grocery
</footer>
```

```html
<body>

  ...

  <div th:insert="footer :: copy"></div>

  <div th:replace="footer :: copy"></div>

  <div th:include="footer :: copy"></div>
  
</body>
```

查看网页源代码知道

```html
<body>

  ...

  <div>
    <footer>
      &copy; 2011 The Good Thymes Virtual Grocery
    </footer>
  </div>

  <footer>
    &copy; 2011 The Good Thymes Virtual Grocery
  </footer>

  <div>
    &copy; 2011 The Good Thymes Virtual Grocery
  </div>
 
</body>
```

**总结**：insert是将片段插入到指定的元素中，replace是将指定的元素替换成片段，include是将内容插入到指定的元素中

==**注意**：引用对应片段的时候开头的是片段所在文件的文件名，不然就会报错==

![image-20210407231617458](F:\Java学习\截图\image-20210407231617458.png)





### 修改对应的title和link

官方文档中有这样的例子

```html
<head th:fragment="common_header(title,links)">

  <title th:replace="${title}">The awesome application</title>

  <!-- Common styles and scripts -->
  <link rel="stylesheet" type="text/css" media="all" th:href="@{/css/awesomeapp.css}">
  <link rel="shortcut icon" th:href="@{/images/favicon.ico}">
  <script type="text/javascript" th:src="@{/sh/scripts/codebase.js}"></script>

  <!--/* Per-page placeholder for additional links */-->
  <th:block th:replace="${links}" />

</head>
```

```html
...
<!--将对应标签的值转入到片段中,然后片段进行引用再来进行插入或者覆盖-->
<head th:replace="base :: common_header(~{::title},~{::link})">

  <title>Awesome - Main</title>

  <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
  <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">

</head>
...
```

```html
...
<head>

  <title>Awesome - Main</title>

  <!-- Common styles and scripts -->
  <link rel="stylesheet" type="text/css" media="all" href="/awe/css/awesomeapp.css">
  <link rel="shortcut icon" href="/awe/images/favicon.ico">
  <script type="text/javascript" src="/awe/sh/scripts/codebase.js"></script>

  <link rel="stylesheet" href="/awe/css/bootstrap.min.css">
  <link rel="stylesheet" href="/awe/themes/smoothness/jquery-ui.css">

</head>
...
```

**说明**：通过传值的方式来进行一个动态的修改

**两种传参的方式**：

1	获取标签值传参		例如：~{::title},~{::link}这两个就是典型的标签值传参

2	普通传参				   例如：common :: menu(1)这个就是传了一个1过去



==**其他的可以参照官方文档来进行学习**==



### 语句拼接

在表达式中使用语句拼接，要使用`| |`来包起来

```html
<!--错误做法，不能这样写-->
<span th:text="Welcome to our application, ${user.name}!">
<!--正确写法-->
<span th:text="|Welcome to our application, ${user.name}!|">
<span th:class="|aaa ${user.age == 0 ? 'bbb' : ''}|"
```



### 自动拼接`？`

使用`?`进行参数自动拼接，可以使用括号包裹，这样它会自动帮我们生成`?`进行拼接

```html
<!-- 最终效果：http://xxx.xxx.com?current=1&limit=5 -->
<a class="page-link" th:href="@{${page.path}(current=1,limit=5)}">首页</a>
```







## 4、使用引擎渲染页面

1.  将页面TemplateEngine注入进来

    ```java
    @Autowired
    private TemplateEngine templateEngine;
    ```

2.  编写模板

    ```java
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>模板示例</title>
    </head>
    <body>
        <p>欢迎您，<span style="color: red;" th:text="${username}"></span>！</p>
    </body>
    </html>
    ```

3.  使用引擎渲染模板

    ```java
    Context context = new Context();
    context.setVariable("username", "小智");
    // 这个字符串就是渲染完成的html页面
    String content = templateEngine.process("/demo", context);
    ```













# 数据库整合

## 一、SQL

### 1、数据源的自动配置

#### 1.导入JDBC场景

```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```

![image-20210630161640814](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630161640814.png)



**数据库驱动**

官方没有导入驱动，因为官方不知道我们要操作什么数据库，所以需要我们自己手动导入

```xml
<!--官方给我们的数据库驱动做了版本仲裁，默认是8版本的-->
<mysql.version>8.0.25</mysql.version>

<!--需要我们自己来修改版本-->
<!--第一种方式：直接引入-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>

<!--第二种方式：重新声明版本-->
<properties>
    <java.version>1.8</java.version>
    <mysql.version>5.1.49</mysql.version>
</properties>
```



#### 2.分析自动配置

**DataSourceAutoConfiguration**：数据源的自动配置

-   修改数据源相关的配置：spring.datasource

    ```java
    @ConfigurationProperties(prefix = "spring.datasource")
    public class DataSourceProperties implements BeanClassLoaderAware, InitializingBean {
    ```

-   数据库连接池的配置，容器中没有数据源它才会配置

    ![image-20210630163018075](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630163018075.png)

-   因此我们默认使用的就是springBoot给我们配置好的。**HikariDataSource**，因为引入jdbc场景的时候就引入了它的包



**DataSourceTransactionManagerAutoConfiguration**：事物管理器的自动配置

**JdbcTemplateAutoConfiguration**：JDBC模板的自动配置，它可以对数据库crud

**JndiDataSourceAutoConfiguration**：Jndi的自动配置

**XADataSourceAutoConfiguration**：分布式事物相关的自动配置



#### 3、修改配置项

```yml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/db4?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.jdbc.Driver
```



#### 4、测试

```java
@SpringBootTest
class AdminApplicationTests {
    // springBoot帮我们自动引入了
    @Autowired
    JdbcTemplate jdbcTemplate;

    @Test
    void contextLoads() {
        String sql = "select count(*) from stu";
        System.out.println(jdbcTemplate.queryForObject(sql, Integer.class));
    }
}
```





### 2、使用Druid数据源

**druid官方github地址**

https://github.com/alibaba/druid

**整合第三方技术的两种方式**：

-   自定义
-   官方starter



**Druid基本配置**

| 配置                          | 缺省值             | 说明                                                         |
| ----------------------------- | ------------------ | ------------------------------------------------------------ |
| name                          |                    | 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。 如果没有配置，将会生成一个名字，格式是：“DataSource-” + System.identityHashCode(this) |
| jdbcUrl                       |                    | 连接数据库的url，不同数据库不一样。例如： mysql : jdbc:mysql://10.20.153.104:3306/druid2 oracle : jdbc:oracle:thin:@10.20.149.85:1521:ocnauto |
| username                      |                    | 连接数据库的用户名                                           |
| password                      |                    | 连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。详细看这里：https://github.com/alibaba/druid/wiki/%E4%BD%BF%E7%94%A8ConfigFilter |
| driverClassName               | 根据url自动识别    | 这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName(建议配置下) |
| initialSize                   | 0                  | 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 |
| maxActive                     | 8                  | 最大连接池数量                                               |
| maxIdle                       | 8                  | 已经不再使用，配置了也没效果                                 |
| minIdle                       |                    | 最小连接池数量                                               |
| maxWait                       |                    | 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。 |
| poolPreparedStatements        | false              | 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。 |
| maxOpenPreparedStatements     | -1                 | 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100 |
| validationQuery               |                    | 用来检测连接是否有效的sql，要求是一个查询语句。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会其作用。 |
| testOnBorrow                  | true               | 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。 |
| testOnReturn                  | false              | 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能 |
| testWhileIdle                 | false              | 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。 |
| timeBetweenEvictionRunsMillis |                    | 有两个含义： 1) Destroy线程会检测连接的间隔时间2) testWhileIdle的判断依据，详细看testWhileIdle属性的说明 |
| numTestsPerEvictionRun        |                    | 不再使用，一个DruidDataSource只支持一个EvictionRun           |
| minEvictableIdleTimeMillis    |                    |                                                              |
| connectionInitSqls            |                    | 物理连接初始化的时候执行的sql                                |
| exceptionSorter               | 根据dbType自动识别 | 当数据库抛出一些不可恢复的异常时，抛弃连接                   |
| filters                       |                    | 属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有： 监控统计用的filter:stat日志用的filter:log4j防御sql注入的filter:wall |
| proxyFilters                  |                    | 类型是List<com.alibaba.druid.filter.Filter>，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系 |





#### 1.自定义方式

**中文文档**：https://github.com/alibaba/druid/wiki/%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98



##### ①引入依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.17</version>
</dependency>
```



##### ②创建数据源注入到容器中

可以使用xml文件的方式去配置，在这里使用配置类的方式配置

```java
@Configuration
public class DataSourceConfig {

    // 绑定配置文件之后，我们只需要修改配置文件中的就可以了
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
//        dataSource.setUsername("root");     // 设置用户名
//        dataSource.setPassword("root");     // 设置密码
        return dataSource;
    }
}
```



**测试**

```java
@Autowired
DataSource dataSource;

@Test
void test1() {
    System.out.println(dataSource.getClass());
}
```

![image-20210630175449405](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630175449405.png)



##### ③StatViewServlet

StatViewServlet的用途包括：

-   提供监控信息展示的html页面
-   提供监控信息的JSON API

```java
/**
 * 配置druid监控页功能
 * @return
 */
@Bean
public ServletRegistrationBean statViewServlet() {
    StatViewServlet statViewServlet = new StatViewServlet();
    ServletRegistrationBean<Servlet> servletServletRegistrationBean =
            new ServletRegistrationBean<>(statViewServlet,"/druid/*");
    return servletServletRegistrationBean;
}

// 输入网址访问http://localhost:8080/druid/datasource.html
```



##### ④监控统计功能

```java
// 绑定配置文件之后，我们只需要修改配置文件中的就可以了
@ConfigurationProperties(prefix = "spring.datasource")
@Bean
public DataSource dataSource() throws SQLException {
    DruidDataSource dataSource = new DruidDataSource();
    //        dataSource.setUsername("root");     // 设置用户名
    //        dataSource.setPassword("root");     // 设置密码
    dataSource.setFilters("stat");  // 开启监控功能
    return dataSource;
}
```

也可以使用配置文件

```yml
spring:
  datasource:
    filters: 'stat'
```

**结果显示**

![image-20210630205734319](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630205734319.png)



##### ⑤web和spring关联监控

```java
/**
 * web关联监控
 * @return
 */
@Bean
public FilterRegistrationBean webStatFilter() {
    WebStatFilter webStatFilter = new WebStatFilter();
    FilterRegistrationBean<Filter> filterFilterRegistrationBean =
        new FilterRegistrationBean(webStatFilter);
    filterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/*"));
  filterFilterRegistrationBean.addInitParameter("exclusions","*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*");
    return filterFilterRegistrationBean;
}
```



##### ⑥设置登录账号和密码

![image-20210630211034742](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630211034742.png)

![image-20210630211002821](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630211002821.png)



##### ⑦不懂看官网，有教程



#### 2.官方starter

**官方文档**：https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter



##### ①引入官方的stater

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>druid-spring-boot-starter</artifactId>
   <version>1.1.17</version>
</dependency>
```



##### ②Druid自动配置

```java
@Configuration
@ConditionalOnClass(DruidDataSource.class)
@AutoConfigureBefore(DataSourceAutoConfiguration.class)	// 它要在spring默认的数据源启动前启动
// 和配合文件进行绑定
// DruidStatProperties	spring.datasource.druid
@EnableConfigurationProperties({DruidStatProperties.class, DataSourceProperties.class})
@Import({DruidSpringAopConfiguration.class,
    DruidStatViewServletConfiguration.class,
    DruidWebStatFilterConfiguration.class,
    DruidFilterConfiguration.class})
public class DruidDataSourceAutoConfigure {
```

-   **DruidSpringAopConfiguration**	aop相关的
-   **DruidStatViewServletConfiguration**    监控页的配置，默认开启
-   **DruidWebStatFilterConfiguration**    监控配置，默认开启
-   **DruidFilterConfiguration**    Druid自己的Filter配置
    -   ![image-20210630212359730](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210630212359730.png)



**代码实现**

```yml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/db4?studyserverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.jdbc.Driver

    druid:
      initial-size: 5   # 初始数
      max-active: 10    # 最大活动数
      aop-patterns: com.xiaozhi.admin.*  # 监控SpringBean
      filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）

      stat-view-servlet:  # 配置监控页功能
        login-username: admin
        login-password: admin
        url-pattern: /druid/*
        enabled: true

      web-stat-filter:  # 监控web
        enabled: true
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*"
        url-pattern: /*

      filter:
        stat:   # 对上面filters里面的stat的详细配置
          slow-sql-millis: 1000
          enabled: true
        wall:
          enabled: true
          config:
            drop-table-allow: false
```





### 3、整合mybatis操作

https://github.com/mybatis

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

![image-20210701154247628](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701154247628.png)



#### 1.配置模式

-   全局配置文件

-   **SqlSessionFactory**    自动配好了

-   **SqlSessionTemplate**   自动配好了

-   **@EnableConfigurationProperties(MybatisProperties.class)**     绑定了配置文件 mybatis.xxxx

-   ```java
    @org.springframework.context.annotation.Configuration
    // 扫描标注有@Mapper注解的接口
    @Import(AutoConfiguredMapperScannerRegistrar.class)
    @ConditionalOnMissingBean({ MapperFactoryBean.class, MapperScannerConfigurer.class })
    public static class MapperScannerRegistrarNotFoundConfiguration implements InitializingBean {
    ```



**注意**：一定要给mapper接口标注@Mapper注解，要不然就使用@MapperScan()注解扫描进去，不然会识别不了



##### 配置方式

```yml
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml  # 全局配置文件位置
  mapper-locations: classpath:mybatis/mapper/*.xml  #sql映射文件位置
  type-aliases-package: com.xiaozhi.admin.pojo  # 开启类别名
```

也可以使用全部都是配置文件的方式

```yml
mybatis:
  # 如果使用configuration配置项，那么就不需要全局配置文件了，两个只能存在一个，同时存在会报错
  #  config-location: classpath:mybatis/mybatis-config.xml  # 全局配置文件位置
  mapper-locations: classpath:mybatis/mapper/*.xml  #sql映射文件位置
  type-aliases-package: com.xiaozhi.admin.pojo  # 开启类别名
  configuration:  # 相当于全局配置文件的配置项
    map-underscore-to-camel-case: true  # 开启驼峰命名
```



##### **测试**

**sql语句**

```sql
CREATE TABLE city
(
  id      INT(11) PRIMARY KEY AUTO_INCREMENT,
  NAME    VARCHAR(30) ,
  state   VARCHAR(30),
  country VARCHAR(30)
);
```

**实体类**

```java
@Data
public class City {

    private Long id;
    private String name;
    private String state;
    private String country;
}
```

**Mapper接口**

```java
@Mapper
public interface CityMapper {

    void insert(City city);
}
```

也可以

```java
@MapperScan(basePackages = "com.xaiozhi.admin.mapper")
@Configuration
public class MybatisConfig {
}
```

**编写映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xiaozhi.admin.mapper.CityMapper">

   <insert id="insert">
       INSERT INTO city (name, state, country) VALUES(#{name}, #{state}, #{country})
   </insert>

</mapper>
```

**测试**

```java
@Test
public void Test2(){
    cityMapper.insert(new City(null, "111", "222", "333"));
}

@Test
public void Test3(){
    System.out.println(cityMapper.findById(1L));
}
```







#### 2.注解模式

```java
@Mapper
public interface CityMapper {

    @Select("select * from city where id = #{id}")
    void insert(City city);

    @Insert("INSERT INTO city (name, state, country) VALUES(#{name}, #{state}, #{country})")
    City findById(long id);
}
```



##### 扩展

注解也可以进行一些较复杂的动作

编写一个完整的开发流程

**service**

```java
@Service
public class CityServiceImpl implements CityService {
    
    @Autowired
    CityMapper cityMapper;
    
    @Override
    public void saveCity(City city) {
        cityMapper.insert(city);
    }
}
```

**controller**

```java
@Controller
public class CityController {

    @Autowired
    CityService cityService;

    @ResponseBody
    @PostMapping("/city")
    public City city(City city) {
        cityService.saveCity(city);
        return city;
    }
}
```

**使用postman进行测试**

![image-20210701165424357](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701165424357.png)

返回的id是为null的，我们想要插入的同时也要设置我们的id值，我们可以这样做

**配置文件的形式**

```java
   <insert id="insert" useGeneratedKeys="true" keyProperty="id">
       INSERT INTO city (name, state, country) VALUES(#{name}, #{state}, #{country})
   </insert>
```

**使用注解**

使用@Options注解放我们其他的功能

```java
@Insert("INSERT INTO city (name, state, country) VALUES(#{name}, #{state}, #{country})")
@Options(useGeneratedKeys = true, keyProperty = "id")
void insert(City city);
```

**结果显示**

![image-20210701165853045](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701165853045.png)



#### 3.混合模式

混合模式就是注解 + xml映射文件

简单的sql语句我们可以使用注解

复杂的语句我们可以使用映射文件来处理



### 4、整合Mybatis-Plus完成CRUD

https://mp.baomidou.com/



##### 1.引入stater

```xml
<dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-boot-starter</artifactId>
      <version>3.4.1</version>
</dependency>
```

它引入了mybatis的整合包，所以引入它就可以有mybatis的全部功能了

==**注意**：它和配置文件绑定的前缀是mybatis-plus==

![image-20210701173428504](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701173428504.png)



##### 2.按照官方的文档来学习





## 二、NoSQL

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、**缓存**和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。



### 1、reids自动配置

#### ①引入starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

![image-20210701203653280](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701203653280.png)

默认导入的是kettuce客户端，需要jedis的需要导入jedis的包，还需要手动开启



#### ②自动配置

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {
```

-   绑定了配置文件  **spring.redis.xxxx**

-   @Import({ **LettuceConnectionConfiguration**.class, **JedisConnectionConfiguration**.class })    支持的两个客户端

    ![image-20210701204546088](F:/Java%25E5%25AD%25A6%25E4%25B9%25A0/%25E6%2588%25AA%25E5%259B%25BE/image-20210701204546088.png)

    如果想要使用jedis就需要导入jedis的包，还需要手动修改客户端类型

    

-   **RedisTemplate<Object, Object>**    自动配好了

-   **StringRedisTemplate**    自动配好了

-   我们使用RedisTemplate和 **StringRedisTemplate**    操作redis数据库就可以了



### 2、RedisTemplate与Lettuce

```yml
# redis配置
spring
  redis:
    host: 192.168.6.156
    port: 6379
    # 有密码的要加上密码
```

```java
@Autowired
StringRedisTemplate redisTemplate;

@Test
public void Test4(){
    ValueOperations<String, String> operations = redisTemplate.opsForValue();
    operations.set("hello", "world");
    String hello = operations.get("hello");
    System.out.println(hello);
}
```







### 3、切换到jedis

```xml
<!-- 导入jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

```yml
  # redis配置
spring
  redis:
    host: 192.168.6.156
    port: 6379
    # 有密码的要加上密码
    client-type: jedis    # 修改客户端为jedis
    # 修改jedis客户端的配置
    jedis:
      pool:
        max-active: 10
        max-wait: 1s
```



### 4、使用redis来记录访问次数

**使用拦截器来处理**

```java
@Component
public class RedisInterceptor implements HandlerInterceptor {

    @Autowired
    StringRedisTemplate redisTemplate;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String uri = request.getRequestURI();
        System.out.println(uri);
        redisTemplate.opsForValue().increment(uri); // 每次访问 + 1
        return true;
    }
}
```

```java
@Configuration
public class MyConfig implements WebMvcConfigurer {

    @Autowired
    RedisInterceptor redisInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(redisInterceptor)
                .addPathPatterns("/*")
                // 放行所有的静态资源
                .excludePathPatterns("/","/login","/css/**","/js/**","/fonts/**","/images/**");
    }
}
```







# Swagger2.0版本

项目集成Swagger

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZTC6jIsjNx49oFBGgaKyeYOEwIDAabKy11vOWkXYau0uYkH2RG5Rkvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**学习目标：**

-   了解Swagger的概念及作用
-   掌握在项目中集成Swagger自动生成API文档

## Swagger简介

**前后端分离**

-   前端 -> 前端控制层、视图层
-   后端 -> 后端控制层、服务层、数据访问层
-   前后端通过API进行交互
-   前后端相对独立且松耦合

**产生的问题**

-   前后端集成，前端或者后端无法做到“及时协商，尽早解决”，最终导致问题集中爆发

**解决方案**

-   首先定义schema [ 计划的提纲 ]，并实时跟踪最新的API，降低集成风险

**Swagger**

-   号称世界上最流行的API框架
-   Restful Api 文档在线自动生成器 => **API 文档 与API 定义同步更新**
-   直接运行，在线测试API
-   支持多种语言 （如：Java，PHP等）
-   官网：https://swagger.io/



## 集成Swagger

**SpringBoot集成Swagger** => **springfox**，两个jar包

-   **Springfox-swagger2**
-   swagger-springmvc

**使用Swagger**

要求：jdk 1.8 + 否则swagger2无法运行

步骤：

1、新建一个SpringBoot-web项目

2、添加Maven依赖

```java
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger2</artifactId>
   <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```

3、编写HelloController，测试确保运行成功！

4、要使用Swagger，我们需要编写一个配置类-SwaggerConfig来配置 Swagger

```java
@Configuration //配置类
@EnableSwagger2// 开启Swagger2的自动配置
public class SwaggerConfig {  
}
```

5、访问测试 ：http://localhost:8080/swagger-ui.html ，可以看到swagger的界面；

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZzSgcwYhS2RhtRXv0Wfg9OkiaE6xDEQibt8TSJTt9OHzFzeq9NrQCJNZQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 配置Swagger

1、Swagger实例Bean是Docket，所以通过配置Docket实例来配置Swaggger。

```java
@Bean //配置docket以配置Swagger具体参数
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2);
}
```

2、可以通过apiInfo()属性配置文档信息

```java
//配置文档信息
private ApiInfo apiInfo() {
   Contact contact = new Contact("联系人名字", "http://xxx.xxx.com/联系人访问链接", "联系人邮箱");
   return new ApiInfo(
           "Swagger学习", // 标题
           "学习演示如何配置Swagger", // 描述
           "v1.0", // 版本
           "http://terms.service.url/组织链接", // 组织链接
           contact, // 联系人信息
           "Apach 2.0 许可", // 许可
           "许可链接", // 许可连接
           new ArrayList<>()	// 扩展
  );
}
```

3、Docket 实例关联上 apiInfo()

```java
@Bean
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo());
}
```

4、重启项目，访问测试 http://localhost:8080/swagger-ui.html  看下效果；



## 配置扫描接口

1、构建Docket时通过select()方法配置怎么扫描接口。

```java
@Bean
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .select()// 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
      .apis(RequestHandlerSelectors.basePackage("com.kuang.swagger.controller"))
      .build();
}
```

2、重启项目测试，由于我们配置根据包的路径扫描接口，所以我们只能看到一个类

3、除了通过包路径配置扫描接口外，还可以通过配置其他方式扫描接口，这里注释一下所有的配置方式：

```java
any() // 扫描所有，项目中的所有接口都会被扫描到
none() // 不扫描接口
// 通过方法上的注解扫描，如withMethodAnnotation(GetMapping.class)只扫描get请求
withMethodAnnotation(final Class<? extends Annotation> annotation)
// 通过类上的注解扫描，如.withClassAnnotation(Controller.class)只扫描有controller注解的类中的接口
withClassAnnotation(final Class<? extends Annotation> annotation)
basePackage(final String basePackage) // 根据包路径扫描接口
```

4、除此之外，我们还可以配置接口扫描过滤：

```java
@Bean
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .select()// 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
      .apis(RequestHandlerSelectors.basePackage("com.kuang.swagger.controller"))
       // 配置如何通过path过滤,即这里只扫描请求以/kuang开头的接口
      .paths(PathSelectors.ant("/kuang/**"))
      .build();
}
```

5、这里的可选值还有

```java
any() // 任何请求都扫描
none() // 任何请求都不扫描
regex(final String pathRegex) // 通过正则表达式控制
ant(final String antPattern) // 通过ant()控制
```



## 配置Swagger开关

1、通过enable()方法配置是否启用swagger，如果是false，swagger将不能在浏览器中访问了

```java
@Bean
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .enable(false) //配置是否启用Swagger，如果是false，在浏览器将无法访问
      .select()// 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
      .apis(RequestHandlerSelectors.basePackage("com.kuang.swagger.controller"))
       // 配置如何通过path过滤,即这里只扫描请求以/kuang开头的接口
      .paths(PathSelectors.ant("/kuang/**"))
      .build();
}
```

2、添加对应的配置文件

![image-20210306194705088](F:\Java学习\截图\image-20210306194705088.png)

3、如何动态配置当项目处于test、dev环境时显示swagger，处于prod时不显示？

```java
@Bean
public Docket docket(Environment environment) {
   // 设置要显示swagger的环境
   Profiles of = Profiles.of("dev", "test");
   // 判断当前是否处于该环境
   // 通过 enable() 接收此参数判断是否要显示
   boolean b = environment.acceptsProfiles(of);
   
   return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .enable(b) //配置是否启用Swagger，如果是false，在浏览器将无法访问
      .select()// 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
      .apis(RequestHandlerSelectors.basePackage("com.kuang.swagger.controller"))
       // 配置如何通过path过滤,即这里只扫描请求以/kuang开头的接口
      .paths(PathSelectors.ant("/kuang/**"))
      .build();
}
```

4、当环境是开发环境的时候，就可以显示出来，上线环境的时候就会

![image-20210306194907439](F:\Java学习\截图\image-20210306194907439.png)





## 配置API分组

1、如果没有配置分组，默认是default。通过groupName()方法即可配置分组：

```java
@Bean
public Docket docket(Environment environment) {
   return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo())
      .groupName("hello") // 配置分组
       // 省略配置....
}
```

2、重启项目查看分组

3、如何配置多个分组？配置多个分组只需要配置多个docket即可：

```java
@Bean
public Docket docket1(){
   return new Docket(DocumentationType.SWAGGER_2).groupName("group1");
}
@Bean
public Docket docket2(){
   return new Docket(DocumentationType.SWAGGER_2).groupName("group2");
}
@Bean
public Docket docket3(){
   return new Docket(DocumentationType.SWAGGER_2).groupName("group3");
}
```

4、重启项目查看即可



## 实体配置

1、新建一个实体类

```java
@ApiModel("用户实体")
public class User {
   @ApiModelProperty("用户名")
   public String username;
   @ApiModelProperty("密码")
   public String password;
}
```

2、只要这个实体在**请求接口**的返回值上（即使是泛型），都能映射到实体项中：

```java
@RequestMapping("/getUser")
public User getUser(){
   return new User();
}
```

3、重启查看测试

==**注意**：属性一定要是public才能识别出来属性上面注解的名字==

注：并不是因为@ApiModel这个注解让实体显示在这里了，而是只要出现在接口方法的返回值上的实体都会显示在这里，而@ApiModel和@ApiModelProperty这两个注解只是为实体添加注释的。

@ApiModel为类添加注释

@ApiModelProperty为类属性添加注释



## 常用注解

Swagger的所有注解定义在io.swagger.annotations包下

下面列一些经常用到的，未列举出来的可以另行查阅说明：

| Swagger注解                                            | 简单说明                                             |
| ------------------------------------------------------ | ---------------------------------------------------- |
| @Api(tags = "xxx模块说明")                             | 作用在模块类上                                       |
| @ApiOperation("xxx接口说明")                           | 作用在接口方法上                                     |
| @ApiModel("xxxPOJO说明")                               | 作用在模型类上：如VO、BO                             |
| @ApiModelProperty(value = "xxx属性说明",hidden = true) | 作用在类方法和属性上，hidden设置为true可以隐藏该属性 |
| @ApiParam("xxx参数说明")                               | 作用在参数、方法和字段上，类似@ApiModelProperty      |

我们也可以给请求的接口配置一些注释

```java
@ApiOperation("小智的接口")
@PostMapping("/xiaozhi")
@ResponseBody
public String kuang(@ApiParam("这个名字会被返回")String username){
   return username;
}
```

这样的话，可以给一些比较难理解的属性或者接口，增加一些配置信息，让人更容易阅读！

相较于传统的Postman或Curl方式测试接口，使用swagger简直就是傻瓜式操作，不需要额外说明文档(写得好本身就是文档)而且更不容易出错，只需要录入数据然后点击Execute，如果再配合自动化框架，可以说基本就不需要人为操作了。

Swagger是个优秀的工具，现在国内已经有很多的中小型互联网公司都在使用它，相较于传统的要先出Word接口文档再测试的方式，显然这样也更符合现在的快速迭代开发行情。当然了，提醒下大家在正式环境要记得关闭Swagger，一来出于安全考虑二来也可以节省运行时内存。



## 拓展：其他皮肤

我们可以导入不同的包实现不同的皮肤定义：

1、默认的  **访问 http://localhost:8080/swagger-ui.html**

```xml
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZrYUroibnsmILAYo1PyuaSDAkrqUvlNibxW9S9niaRomPFd9rrD6SY4wjA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2、bootstrap-ui  **访问 http://localhost:8080/doc.html**

```xml
<!-- 引入swagger-bootstrap-ui包 /doc.html-->
<dependency>
   <groupId>com.github.xiaoymin</groupId>
   <artifactId>swagger-bootstrap-ui</artifactId>
   <version>1.9.1</version>
</dependency>
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZxQ9fXkPFt9TtX6PiaPDWWFSCJQK6H0ibiagM2w2f99zqHuOJffyRycCIg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3、Layui-ui  **访问 http://localhost:8080/docs.html**

```xml
<!-- 引入swagger-ui-layer包 /docs.html-->
<dependency>
   <groupId>com.github.caspar-chen</groupId>
   <artifactId>swagger-ui-layer</artifactId>
   <version>1.1.3</version>
</dependency>
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZYA6g5VyspYIqFMokAGg7dbx47P2ibC8Z80saA7XdrByPFhgmrduSHbA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4、mg-ui  **访问 http://localhost:8080/document.html**

```xml
<!-- 引入swagger-ui-layer包 /document.html-->
<dependency>
   <groupId>com.zyplayer</groupId>
   <artifactId>swagger-mg-ui</artifactId>
   <version>1.0.6</version>
</dependency>
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IExpkhknhzRFQicsic8yibm9ZBJPCcHFicV2dklg3l88IuYia3OIFNfNVbWZXpppPS93jghTUJiaeJQx6Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





# 任务

## 异步任务

定义一个Service

```java
@Service
public class AsyncService {
    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("数据正在处理");
    }
}
```

定义一个请求,在请求中调用service中的方法

```java
@ResponseBody
@RequestMapping("/hello")
public String hello(){
  asyncService.hello();
  return "OK";
}
```

在Service的方法中使用`@Async`,并在主入口上使用`@EnableAsync`开启异步任务





## 邮件发送

### 1、导入场景启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
    <version>2.5.6</version>
</dependency>
```



### 2、配置

```yml
  # MailProperties
spring:  
  mail:
    host: smtp.sina.com
    port: 465
    # 账号
    username: xxx
    # 授权码
    password: xxx
    # 发送的协议
    protocol: smtps
    # 使用ssl安全连接
    properties:
      mail.smtp.ssl.enable: true
```



### 3、封装邮件发送类

```java
package com.newkewang.utils;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.mail.MailSender;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Component;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;

@Component
public class MailClient {

    private static final Logger logger = LoggerFactory.getLogger(MailClient.class);

    @Autowired
    private JavaMailSender mailSender;

    @Value("${spring.mail.username}")
    private String from;

    public void setMail(String to, String subject, String content) {
        MimeMessage message = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(message);
        try {
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content, true);
            mailSender.send(helper.getMimeMessage());
        } catch (MessagingException e) {
            logger.error("发送邮件失败：" + e.getMessage());
        }
    }
}
```



### 4、thymeleaf发送HTML邮件

首先写好HTML模板

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>邮件示例</title>
</head>
<body>
    <p>欢迎您，<span style="color: red;" th:text="${username}"></span>！</p>
</body>
</html>
```

然后使用Thymeleaf解析模板

```java
/**
 * 注入template引擎
 */
@Autowired
private TemplateEngine templateEngine;
@Autowired
private MailClient mailClient;

@Test
public void testSendHtml(){
    Context context = new Context();
    context.setVariable("username", "xiaozhi");
    // 使用模板解析 -
    String content = templateEngine.process("/mail/demo", context);
    System.out.println(content);
    // 发送html到邮箱
    mailClient.setMail("xxx@xxx.com", "HTML", content);
}
```









## 定时任务

```java
TaskScheduler //任务调度程序
TaskExecutor	//任务执行者
@EnableScheduling //开启定时功能的注解，放在主入口
@Scheduled		//什么时候执行
  
cron表达式
```

在方法上面加上`@Scheduled(cron = "0 43 14 * * ?")`并加上相应的表达式即可

常用表达式例子：

```shell
（1）0 0 2 1 * ? *   表示在每月的1日的凌晨2点调整任务
（2）0 15 10 ? * MON-FRI   表示周一到周五每天上午10:15执行作
（3）0 15 10 ? 6L 2002-2006   表示2002-2006年的每个月的最后一个星期五上午10:15执行作
（4）0 0 10,14,16 * * ?   每天上午10点，下午2点，4点
（5）0 0/30 9-17 * * ?   朝九晚五工作时间内每半小时
（6）0 0 12 ? * WED    表示每个星期三中午12点
（7）0 0 12 * * ?   每天中午12点触发
（8）0 15 10 ? * *    每天上午10:15触发
（9）0 15 10 * * ?     每天上午10:15触发
（10）0 15 10 * * ? *    每天上午10:15触发
（11）0 15 10 * * ? 2005    2005年的每天上午10:15触发
（12）0 * 14 * * ?     在每天下午2点到下午2:59期间的每1分钟触发
（13）0 0/5 14 * * ?    在每天下午2点到下午2:55期间的每5分钟触发
（14）0 0/5 14,18 * * ?     在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
（15）0 0-5 14 * * ?    在每天下午2点到下午2:05期间的每1分钟触发
（16）0 10,44 14 ? 3 WED    每年三月的星期三的下午2:10和2:44触发
（17）0 15 10 ? * MON-FRI    周一至周五的上午10:15触发
（18）0 15 10 15 * ?    每月15日上午10:15触发
（19）0 15 10 L * ?    每月最后一日的上午10:15触发
（20）0 15 10 ? * 6L    每月的最后一个星期五上午10:15触发
（21）0 15 10 ? * 6L 2002-2005   2002年至2005年的每月的最后一个星期五上午10:15触发
（22）0 15 10 ? * 6#3   每月的第三个星期五上午10:15触发
```







# 整合日志

## springBoot中使用logback

springBoot已经给我们集成进来了，我们只需要配置好文件即可

### 配置文件中配置

```yml
# 设置对应的包的日志等级
logging.level.com.newkewang: debug
# 设置日志文件存放位置
logging:
  file:
    path: ./log
```



### 在日志配置文件中配置

在springBoot中默认是在resource目录下创建`logback-spring.xml`文件即可

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <contextName>community</contextName>
    <!--日志存放位置-->
    <property name="LOG_PATH" value="./log"/>
    <!--项目日志对应文件夹-->
    <property name="APPDIR" value="community"/>

    <!-- error file -->
    <appender name="FILE_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${APPDIR}/log_error.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志的文件名格式-->
            <fileNamePattern>${LOG_PATH}/${APPDIR}/error/log-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!--日志文件的最大内存，超出这个内存就创建一个新的日志文件进行存储-->
                <maxFileSize>5MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--最大存储时间，超过这个时间就删除日志文件-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <append>true</append>
        <!--日志输出信息-->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--输出信息的格式-->
            <pattern>%d %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
            <!--编码集-->
            <charset>utf-8</charset>
        </encoder>
        <!--过滤器，只写入对应级别的日志信息-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- warn file -->
    <appender name="FILE_WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${APPDIR}/log_warn.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${APPDIR}/warn/log-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>5MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <append>true</append>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>warn</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- info file -->
    <appender name="FILE_INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/${APPDIR}/log_info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/${APPDIR}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>5MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <append>true</append>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>info</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- console -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
            <charset>utf-8</charset>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>debug</level>
        </filter>
    </appender>

    <!--记录日志的对应包-->
    <logger name="com.newkewang" level="debug"/>

    <root level="info">
        <appender-ref ref="FILE_ERROR"/>
        <appender-ref ref="FILE_WARN"/>
        <appender-ref ref="FILE_INFO"/>
        <appender-ref ref="STDOUT"/>
    </root>

</configuration>
```

![image-20220324160146603](https://gitee.com/xiaozhi-oos/save-img/raw/upload-img/image-20220324160146603.png)





## 开启某一个类的日志级别

在yaml文件中进行设置

```yaml
logging:
  level:
  	# 设置com.xiaozhi.service包下的所有类的日志级别为debug
  	com.xiaozhi.service.*: debug
```



# 整合dubbo







