# Mybaits-plus

## 是什么❓

**MyBatis-Plus**（简称 MP）是一个 **MyBatis**的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

![X0__PD8A__MZOMQ`@H1WR_D.png](https://i.loli.net/2021/04/28/GD4cQkNXirICVeS.png)

**特性**

-   **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
-   **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
-   **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
-   **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
-   **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
-   **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
-   **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
-   **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
-   **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
-   **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
-   **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
-   **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作



👀官网：https://mp.baomidou.com/

👁官方文档：https://mp.baomidou.com/guide/

我们按照官方文档来进行学习



## 入门

### 1	 引入依赖

```xml
<!--mybaits-plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.2</version>
</dependency>
<!--mysql依赖-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

### 2	创建表和添加数据

```sql
CREATE DATABASE IF NOT EXISTS mybatis_plus;
CREATE TABLE USER
(
    id BIGINT(20)NOT NULL COMMENT '主键ID',
    NAME VARCHAR(30)NULL DEFAULT NULL COMMENT '姓名',
    age INT(11)NULL DEFAULT NULL COMMENT '年龄',
    email VARCHAR(50)NULL DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY (id)
);
INSERT INTO USER (id, NAME, age, email)VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');
```

### 3	创建实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {

    private Long id;
    private String username;
    private String password;
    private String email;
}
```

### 4	创建mapper类

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
    
}
```

在主启动类中开启mapper扫描

![AP__QI485AJNVXGS75S6WU7.png](https://i.loli.net/2021/04/28/wKABkJdQUgFS8se.png)

### 5	测试

```java
@SpringBootTest
class MybatisPlusApplicationTests {

    @Autowired
    UserMapper userMapper;
    @Test
    void findAll() {
        List<User> users = userMapper.selectList(null);
        for (User user : users) {
            System.out.println(user);
        }
    }

}
```

```properties
# mybatis日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![CL~D43Z7A3FPOYKMNGW7HF0.png](https://i.loli.net/2021/04/28/ZIm5fXJAls7LpqR.png)





## 主键策略

mp的默认主键策略是ASSIGN_ID （使用了雪花算法）



### 插入一条记录

**测试**：我们插入一条记录

```java
@Test
public void insertTest(){
    User user = new User();
    user.setName("小智");
    user.setAge(18);
    user.setEmail("123@qq.com");
    userMapper.insert(user);
    System.out.println(user);
}
```

**查看结果**

![image-20210428214102978](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20210428214102978.png)





### 雪花算法

分布式ID生成器，雪花算法是由Twitter公布的分布式主键生成算法，它能够保证**不同表的主键的不重复性**，以及相同表的主键的有序性。



**核心思想：**

长度共64bit（一个long型）。

首先是一个符号位，1bit标识，由于long基本类型在Java中是带符号的，最高位是符号位，正数是0，负数是1，所以id一般是正数，最高位是0。

41bit时间截(毫秒级)，存储的是时间截的差值（当前时间截 - 开始时间截)，结果约等于69.73年。

10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID，可以部署在1024个节点）。

12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID）。

![IMG_256](F:\Java学习\截图\clip_image002.jpg)

**优点**：整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞，并且效率较高。



### 主键自增

在主键的字段上添加注解

```java
@TableId(type = IdType.AUTO)
private Long id;
```

![image-20210428215610052](F:\Java学习\截图\image-20210428215610052.png)

设置完成之后进行测试

![image-20210428215807166](F:\Java学习\截图\image-20210428215807166.png)

可以看到我们的的主键自动递增了



**设置全局主键生成策略**

```properties
#全局设置主键生成策略
mybatis-plus.global-config.db-config.id-type=auto
```





## 自动填充

1	数据库中添加两个datatime类型的字段

2	实体类中添加两个变量并添加注释

```java
    @TableField(fill = FieldFill.INSERT)    // 创建时插入数据
    private Date createTime;
    @TableField(fill = FieldFill.UPDATE)    // 更新时插入数据
    private Date updateTime;
```

源码中很清楚的写着

![image-20210428221250899](F:\Java学习\截图\image-20210428221250899.png)

3	自定义接口实现MetaObjectHandler 接口，自定义策略

官网上的例子

```java
@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill ....");
        this.strictInsertFill(metaObject, "createTime", LocalDateTime.class, LocalDateTime.now()); // 起始版本 3.3.0(推荐使用)
        // 或者
        this.strictUpdateFill(metaObject, "createTime", () -> LocalDateTime.now(), LocalDateTime.class); // 起始版本 3.3.3(推荐)
        // 或者
        this.fillStrategy(metaObject, "createTime", LocalDateTime.now()); // 也可以使用(3.3.0 该方法有bug)
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill ....");
        this.strictUpdateFill(metaObject, "updateTime", LocalDateTime.class, LocalDateTime.now()); // 起始版本 3.3.0(推荐)
        // 或者
        this.strictUpdateFill(metaObject, "updateTime", () -> LocalDateTime.now(), LocalDateTime.class); // 起始版本 3.3.3(推荐)
        // 或者
        this.fillStrategy(metaObject, "updateTime", LocalDateTime.now()); // 也可以使用(3.3.0 该方法有bug)
    }
}
```

我们模仿官网上的例子来进行测试

```java
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}
```

我们插入一条数据

![image-20210429112243781](F:\Java学习\截图\image-20210429112243781.png)

可以看到我们的时间是自动生成的，不用我们手动进行添加

我们再刚才插入的数据进行更新操作

![image-20210429113422875](F:\Java学习\截图\image-20210429113422875.png)

**注意**：

![image-20210429112635032](F:\Java学习\截图\image-20210429112635032.png)





## 乐观锁

**主要适用场景：**当要更新一条记录的时候，希望这条记录没有被别人更新，也就是说实现线程安全的数据更新

**为什么需要乐观锁**

多个人同时更新一条记录，一开始拿到的数据是一样的，但是不能进行实时的更新，因为上一个人已经把数据修改了，而另外的人拿的是没有更新的数据，那么这就会有可能造成更新丢失，就是丢失上一个人更新的数据，所以我们需要用乐观锁来防止更新丢失



**实现原理**

添加一个version字段，也就是版本，我们每一次更新的时候都要到数据库中比对一下版本，如果版本不对，那么更新失败，确保数据的有效性



**代码实现**

1	添加一个version字段 

![image-20210429114506553](F:\Java学习\截图\image-20210429114506553.png)

2	配置插件

官网的示例

```java
// Spring Boot 方式
@Configuration
@MapperScan("按需修改")
public class MybatisPlusConfig {
    /**
     * 旧版
     */
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }
    
    /**
     * 新版
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

我们按照官网的来

```java
@Configuration
@MapperScan("com.xiaozhi.mybatisplus.mapper")
public class MpConfig {

    // 乐观锁配置
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return interceptor;
    }
}
```

3	实体类添加@Version注解

```java
    @Version
    private Integer version;
```

这样就完成了我们的乐观锁的配置了





## 查询

### 通过多个ID批量查询

```java
@Test
public void batchTest(){
    // 批量查询
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
    for (User user : users) {
        System.out.println(user);
    }
}
```



### 简单的条件查询

```java
@Test
public void conditionSelectTest(){
    // 简单的条件查询
    HashMap<String , Object> map = new HashMap<>();
    // 名字是Tom并且年龄是28
    map.put("name","Tom");
    map.put("age","28");
    List<User> users = userMapper.selectByMap(map);
    System.out.println(users);
}
```



### 分页查询

**测试selectPage分页**

1	配置分页插件

按照官网的来

```java
// 最新版
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
        return interceptor;
    }
```

2	测试

```java
// 测试分页查询
@Test
public void pageSelectTest(){
    Page<User> page = new Page<>(1, 3);     // 当前页和每页显示条数
    Page<User> selectPage = userMapper.selectPage(page, null);
    List<User> records = selectPage.getRecords();   // 查询到的数据
    long current = selectPage.getCurrent();         // 当前页
    long size = selectPage.getSize();               // 页记录数
    long total = selectPage.getTotal();             // 总记录数
    boolean hasNext = selectPage.hasNext();         // 是否有下一页
    boolean hasPrevious = selectPage.hasPrevious(); // 是否有上一页

    System.out.println(records);
    System.out.println(current);
    System.out.println(size);
    System.out.println(total);
    System.out.println(hasNext);
    System.out.println(hasPrevious);
}
```

3	查看结果

![image-20210429161029809](F:\Java学习\截图\image-20210429161029809.png)



**测试selectMapsPage分页**

当指定了特定的查询列时，希望分页结果列表只返回被查询的列，而不是很多null值

测试selectMapsPage分页：结果集是Map

```java
// 测试特定条件分页查询
@Test
public void selectMapsPageTest(){
    Page<Map<String,Object>> page = new Page<>(1, 3);
    Page<Map<String, Object>> mapPage = userMapper.selectMapsPage(page, null);
    mapPage.getRecords().forEach(System.out::println);
}
```

![image-20210429162308496](F:\Java学习\截图\image-20210429162308496.png)





## 删除与逻辑删除

### 删除

1	根据id删除

```java
// 根据id删除
@Test
public void deleteByIdTest(){
    int i = userMapper.deleteById(1);
    System.out.println(i);
}
```

2	批量删除

```java
// 批量删除
@Test
public void deleteBatchTest(){
    int i = userMapper.deleteBatchIds(Arrays.asList(2,3,4));
    System.out.println(i);
}
```

3	简单条件删除

```java
// 简单条件删除
@Test
public void deleteConditionTest(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","Billie");      // 删除名字为Billie的记录
    int i = userMapper.deleteByMap(map);
    System.out.println(i);
}
```



### 逻辑删除

物理删除：数据库上直接删除

逻辑删除：经过删除的数据是查询不到的，但是数据库上依然存在

实现原理：加上一个字段，没有逻辑删除的为1，删除的为0，我们查询的时候就会加上一个条件查询，通过这个来完成我们的逻辑删除



**代码实现**

1	添加deleted字段

```sql
ALTER TABLE `user` ADD `deleted` BOOLEAN DEFAULT FALSE
```

2	实体类添加字段

添加deleted 字段，并加上 @TableLogic 注解 

```java
@TableField
private Integer deleted;
```

3	配置com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig

```properties
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以不用加@TableLogic注解)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

4	测试

```java
// 测试逻辑删除
@Test
public void deletedTest(){
    int i = userMapper.deleteById(7);
    System.out.println(userMapper.selectById(6));
}
```

![image-20210429170345905](F:\Java学习\截图\image-20210429170345905.png)



原理上就是加了一个条件查询

![image-20210429170314310](F:\Java学习\截图\image-20210429170314310.png)





## 条件构造器和常用接口

### Wapper接口

![image-20210429170453028](F:\Java学习\截图\image-20210429170453028.png)

Wrapper ： 条件构造抽象类，最顶端父类  

  AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件

​    	QueryWrapper ： 查询条件封装

​    	UpdateWrapper ： Update 条件封装

  AbstractLambdaWrapper ： 使用Lambda 语法

  	  LambdaQueryWrapper ：用于Lambda语法使用的查询Wrapper

​		LambdaUpdateWrapper ： Lambda 更新封装Wrapper



### 常用接口

官网上都有解释，可以查阅官网来进行使用，这里使用一些常用的接口



#### 条件查询

```java
@Test
public void WrapperTest(){
    QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
    // 查询年龄是20，名字是孙悟空的字段
    userQueryWrapper.eq("age",20)
            .eq("name","孙悟空");
    List<User> users = userMapper.selectList(userQueryWrapper);
    users.forEach(System.out::println);
}
```



#### 模糊查询

like		两边都有%

```java
// 模糊查询
    @Test
    public void mohuTest(){
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        // 查询名字中有智的
        userQueryWrapper.like("name","智");
        List<User> users = userMapper.selectList(userQueryWrapper);
        users.forEach(System.out::println);
    }
```

likeLeft	likeRight		

```java
// 模糊左查询
@Test
public void leftTest(){
    QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
    // 查询姓孙的
    userQueryWrapper.likeRight("name","孙");
    List<User> users = userMapper.selectList(userQueryWrapper);
    users.forEach(System.out::println);
}
```

![image-20210429172259569](F:\Java学习\截图\image-20210429172259569.png)

左右就是%的位置



#### 分组查询

orderBy、orderByDesc、orderByAsc

```java
// 分组查询
@Test
public void orderByTest(){
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    // 前后的位置就是先比较那个，如果相同，那么再比较那个
    wrapper.orderByDesc("id","age");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
```

![image-20210429173034089](F:\Java学习\截图\image-20210429173034089.png)





### 查询方式

| **查询方式**     | **说明**                          |
| ---------------- | --------------------------------- |
| **setSqlSelect** | 设置 SELECT 查询字段              |
| **where**        | WHERE  语句，拼接  + WHERE 条件   |
| **and**          | AND  语句，拼接  + AND 字段=值    |
| **andNew**       | AND  语句，拼接  + AND (字段=值)  |
| **or**           | OR  语句，拼接  + OR 字段=值      |
| **orNew**        | OR  语句，拼接  + OR (字段=值)    |
| **eq**           | 等于=                             |
| **allEq**        | 基于 map 内容等于=                |
| **ne**           | 不等于<>                          |
| **gt**           | 大于>                             |
| **ge**           | 大于等于>=                        |
| **lt**           | 小于<                             |
| **le**           | 小于等于<=                        |
| **like**         | 模糊查询 LIKE                     |
| **notLike**      | 模糊查询 NOT LIKE                 |
| **in**           | IN  查询                          |
| **notIn**        | NOT  IN 查询                      |
| **isNull**       | NULL  值查询                      |
| **isNotNull**    | IS  NOT NULL                      |
| **groupBy**      | 分组 GROUP BY                     |
| **having**       | HAVING  关键词                    |
| **orderBy**      | 排序 ORDER BY                     |
| **orderAsc**     | ASC  排序 ORDER  BY               |
| **orderDesc**    | DESC  排序 ORDER  BY              |
| **exists**       | EXISTS  条件语句                  |
| **notExists**    | NOT  EXISTS 条件语句              |
| **between**      | BETWEEN  条件语句                 |
| **notBetween**   | NOT  BETWEEN 条件语句             |
| **addFilter**    | 自由拼接 SQL                      |
| **last**         | 拼接在最后，例如：last(“LIMIT 1”) |



# 使用CRUD接口

service继承Iservice接口

serviceImpl继承ServiceImpl类，再实现service类



