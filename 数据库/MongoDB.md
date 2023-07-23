# MongoDB简介

## 1	NoSQL简介

NoSQL(NoSQL = Not Only SQL)，意即反SQL运动，指的是非关系型的数据库，是一项全新的数据库革命性运动，早期就有人提出，发展至2009年趋势越发高涨。NoSQL的拥护者们提倡运用非关系型的数据存储，相对于目前铺天盖地的关系型数据库运用，这一概念无疑是一种全新的思维的注入

为什幺使用NoSQL :

1、对数据库高并发读写。

2、对海量数据的高效率存储和访问。

3、对数据库的高可扩展性和高可用性。

弱点：

1、数据库事务一致性需求

2、数据库的写实时性和读实时性需求

3、对复杂的SQL查询，特别是多表关联查询的需求



## 2	什么是MongoDB ?

MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。

在高负载的情况下，添加更多的节点，可以保证服务器性能。

MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

![image-20210513170203147](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20210513170203147.png)



## 3	mongoDB的特点

1、MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。

2、你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。

3、你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。

4、如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。

5、Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。

6、MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。

7、Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。

8、Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。

9、Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。

10、GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。

11、MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。

12、MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。

13、MongoDB安装简单。



## 4	安装mongodb

官网下载地址：https://www.mongodb.com/try/download/enterprise



### ①docker安装

```sh
#拉取镜像 
docker pull mongo:latest

#创建和启动容器 
docker run -d --restart=always -p 27017:27017 --name mymongo -v /data/db:/data/db -d mongo

#进入容器 
docker exec -it mymongo /bin/bash 

#使用MongoDB客户端进行操作 
mongo 

> show dbs #查询所有的数据库 
admin 0.000GB 
config 0.000GB 
local 0.000GB 
```



### ②linux下安装

安装参照该博主：https://juejin.cn/post/6844903828811153421







## 5	适用场景

1、网站数据：Mongo非常适合实时的插入，更新与查询，并具备网站实时数据存储所需的复制及高度伸缩性。

2、缓存：由于性能很高，Mongo也适合作为信息基础设施的缓存层。在系统重启之后，由Mongo搭建的持久化缓存层可以避免下层的数据源过载。

3、大尺寸，低价值的数据：使用传统的关系型数据库存储一些数据时可能会比较昂贵， 在此之前，很多时候程序员往往会选择传统的文件进行存储。

4、高伸缩性的场景：Mongo非常适合由数十或数百台服务器组成的数据库。Mongo的路线图中已经包含对Map Reduce弓摩的内置支持。

5、用于对象及 JSON数据的存储：Mongo的BSON数据格式非常适合文档化格式的存储 及查询。



## 6	不适用场合

1、高度事务性的系统：例如银行或会计系统。传统的关系型数据库目前还是更适用于需要大量原子性复杂事务的应用程序。

2、传统的商业智能应用：针对特定问题的BI数据库会对产生高度优化的查询方式。对于此类应用，数据仓库可能是更合适的选择。





# MongoDB入门

## 1	常用操作

### ①INSERT

```sh
> db.User.save({name:'zhangsan',age:21,sex:true})
> db.User.find()
{"_id": Objectld("4f69e680c9106ee2ec95da66"), "name": "zhangsan", "age": 21,
"sex": true}
```

_id组合

Objectld是、id”的默认类型。Objectld使用12字节的存储空间，每个字节二位十六进制数字， 是一个24位的字符串

![img](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/clip_image002-1620912114152.jpg)

\1. 时间戳：时间不断变化的

\2. 机器：主机的唯_标识码。通常是机器主机名的散列值，这样可以确保不同主机 

生成不同的Objectld ,不产生冲突。

\3. PID:为了确保在同一台机器上并发的多个进程产生的Objectld是唯一的，

所以加上进程标识符(PID).

\4. 计数器：前9个字节保证了同一秒钟不同机器不同进程产生的Objectld是唯一的。 

后3个字节就是一个自动增加的计数器，确保相同进程同一秒产生的Objectld也是 

不一样。同一秒最多允许每个进程拥有IS 777 2托个不同的Objectld。



### ②Query

#### 1、WHERE

```sh
# select * from User where name = 'zhangsan'
> db.User.find({name:"zhangsan"})
```



#### 2、FIELDS

```sh
# select name, age from User where age = 21
> db.User.find({age:21}, {'name':1, 'age':1})
```

**说明**：字段的值为1就是不要这个，为0就是将这个显示出来



#### 3、SORT

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

```sh
# select * from User order by age
> db.User.find().sort({age:1})
```



#### 4、SKIP&LIMIT

在 MongoDB 中使用 limit()方法来读取指定数量的数据，skip()方法来跳过指定数量的数据

```sh
# select * from User skip 2 limit 3
> db.User.find().skip(0).limit(3)
```



#### 5、IN

```sh
# select * from User where age in (21, 26, 32)
> db.User.find({age:{$in:[21,26,32]}})
```



#### 6、COUNT

```sh
# select count(*) from User where age >20
> db.User.find({age:{$gt:20}}).count()
```



#### 7、OR

```sh
# select * from User where age = 21 or age = 28
> db.User.find({$or:[{age:21}, {age:28}]})
```



### ③Update

可直接用类似T-SQL条件表达式更新，或用SaveO更新从数据库返回到文档对象。

```sh
# update Userset age = 100, sex = 0 where name = 'user1'
> db.User.update({name:"zhangsan"}, {$set:{age:100, sex:0}})
```

Update()有几个参数需要注意。

db.collection.update(criteria, objNew, upsert, mult)

criteria:需要更新的条件表达式

objNew:更新表达式

upsert:如FI标记录不存在，是否插入新文档。 

multi:是否更新多个文档。



④Remove

removeO用于删除单个或全部文档，删除后的文档无法恢复。

```sh
> db.User.remove(id)
//移除对应id的行
> db.User.remove({})
//移除所有
```



### ⑤aggregate

MongoDB中聚合(aggregate)主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的 count(*)

#### 1、插入数据

```sh
>db.article.insert({
    title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   by_user: 'runoob.com',
   url: 'http://www.runoob.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
})
>db.article.insert({
   title: 'NoSQL Overview', 
   description: 'No sql database is very fast',
   by_user: 'runoob.com',
   url: 'http://www.runoob.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 10
})
>db.article.insert({
   title: 'Neo4j Overview', 
   description: 'Neo4j is no sql database',
   by_user: 'Neo4j',
   url: 'http://www.neo4j.com',
   tags: ['neo4j', 'database', 'NoSQL'],
   likes: 750})
```



#### 2、统计sum

```sh
# select by_user, count(*) from article group by by_user
> db.article.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
```

在上面的例子中，我们通过字段 by_user 字段对数据进行分组，并计算 by_user 字段相同值的总和。



#### 3、常见的聚合函数表达式

| **表达式** | **描述**                                       | **实例**                                                     |
| ---------- | ---------------------------------------------- | ------------------------------------------------------------ |
| $sum       | 计算总和。                                     | db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
| $avg       | 计算平均值                                     | db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
| $min       | 获取集合中所有文档对应值得最小值。             | db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
| $max       | 获取集合中所有文档对应值得最大值。             | db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
| $push      | 在结果文档中插入值到一个数组中。               | db.mycol.aggregate([{$group  : {_id : "$by_user", url : {$push: "$url"}}}]) |
| $addToSet  | 在结果文档中插入值到一个数组中，但不创建副本。 | db.mycol.aggregate([{$group  : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
| $first     | 根据资源文档的排序获取第一个文档数据。         | db.mycol.aggregate([{$group  : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
| $last      | 根据资源文档的排序获取最后一个文档数据         | db.mycol.aggregate([{$group  : {_id : "$by_user", last_url : {$last : "$url"}}}]) |



### ⑥ 索引

索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。

这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构。

```sh
>db.User.createIndex({"name":1})
```

语法中 name值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可



## 2	SpringBoot集成MongoDB

### ①集成简介

spring-data-mongodb提供了MongoTemplate与MongoRepository两种方式访问mongodb，MongoRepository操作简单，MongoTemplate操作灵活，我们在项目中可以灵活适用这两种方式操作mongodb，MongoRepository的缺点是不够灵活，MongoTemplate正好可以弥补不足



### ②搭建开发环境

#### 1、初始化工程

创建springBoot项目



#### 2、引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>

    <dependency>
        <groupId>joda-time</groupId>
        <artifactId>joda-time</artifactId>
        <version>2.10.1</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```



### ③添加配置

在application.properties文件添加配置

```properties
spring.data.mongodb.uri=mongodb://IP:端口号/test
```





## 3	基于MongoTemplate 开发CRUD

### ①添加实体

```java
@Data
@Document("User")
public class User {
     @Id
     private String id;
     private String name;
     private Integer age;
     private String email;
     private String createDate;
}
```



### ②实现

**常用方法**
 mongoTemplate.findAll(User.class): 查询User文档的全部数据
 mongoTemplate.findById(<id>, User.class): 查询User文档id为id的数据
 mongoTemplate.find(query, User.class);: 根据query内的查询条件查询
 mongoTemplate.upsert(query, update, User.class): 修改
 mongoTemplate.remove(query, User.class): 删除
 mongoTemplate.insert(User): 新增



**Query对象**
 1、创建一个query对象（用来封装所有条件对象)，再创建一个criteria对象（用来构建条件）
 2、 精准条件：criteria.and(“key”).is(“条件”)
 模糊条件：criteria.and(“key”).regex(“条件”)
 3、封装条件：query.addCriteria(criteria)
 4、大于（创建新的criteria）：Criteria gt = Criteria.where(“key”).gt（“条件”）
 小于（创建新的criteria）：Criteria lt = Criteria.where(“key”).lt（“条件”）
 5、Query.addCriteria(new Criteria().andOperator(gt,lt));
 6、一个query中只能有一个andOperator()。其参数也可以是Criteria数组。
 7、排序 ：query.with（new Sort(Sort.Direction.ASC, "age"). and(new Sort(Sort.Direction.DESC, "date")))



### ③添加测试类

在/test/java下面添加测试类

```java
@SpringBootTest
class MongodbApplicationTests {

    @Autowired
    private MongoTemplate mongoTemplate;

    // 添加
    @Test
    public void Test1(){
        User user = new User();
        user.setName("xiaozhi");
        user.setAge(19);
        user.setEmail("23423432");
        User insert = mongoTemplate.insert(user);
        System.out.println(insert);
    }

    // 查询所有
    @Test
    public void Test2(){
        System.out.println(mongoTemplate.findAll(User.class));
    }

     //根据id查询
    @Test
    public void Test3(){
        User user = mongoTemplate.findById("609cf46c2ed6e460c8592fdc", User.class);
        System.out.println(user);
    }

    // 条件查询
    @Test
    public void Test4(){
        // 新建一个query对象
        Query query = new Query(Criteria
                .where("name").is("xiaozhi")
                .and("age").is(19));
        List<User> users = mongoTemplate.find(query, User.class);
        System.out.println(users);
    }

    // 模糊查询
    @Test
    public void Test5(){
        String name = "est";
        // 正则表达式
        // 只要包含est就进行匹配
        String regex = String.format("%s%s%s","^.*",name,".*$");
        Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);
        // 创建一个query对象
        Query query = new Query(Criteria.where("name").regex(pattern));
        List<User> users = mongoTemplate.find(query, User.class);
        System.out.println(users);
    }

    // 分页查询
    @Test
    public void Test6(){
        int pageNo = 1;
        int pageSize = 5;
        Query query = new Query(Criteria.where("name").is("xiaozhi"));
        long count = mongoTemplate.count(query, User.class);
        List<User> users = mongoTemplate.find(query.skip((pageNo - 1) * pageSize).limit(pageSize), User.class);
        System.out.println(users);
    }

    // 修改
    @Test
    public void Test7(){
        User user = mongoTemplate.findById("609cf46c2ed6e460c8592fdc", User.class);
        user.setName("heigui");
        user.setAge(40);
        Query query = new Query(Criteria.where("_id").is(user.getId()));
        Update update = new Update();
        update.set("name",user.getName());
        update.set("age",user.getAge());
        UpdateResult result = mongoTemplate.upsert(query, update, User.class);
        // 影响行数
        long count = result.getModifiedCount();
        System.out.println(count);
    }

    // 删除操作
    @Test
    public void Test8(){
        Query query = new Query(Criteria.where("name").is("heigui"));
        DeleteResult remove = mongoTemplate.remove(query, User.class);
        // 影响的行数
        long deletedCount = remove.getDeletedCount();
        System.out.println(deletedCount);
    }
}
```



## 4基于MongoRepository开发CRUD

### ①创建一个接口继承MongoRepository

```java
@Repository
public interface UserRepository extends MongoRepository<User,String> {

}
```



### ②进行操作

```java
@SpringBootTest
class MongodbApplicationTest2 {

    @Autowired
    private UserRepository userRepository;

    // 添加
    @Test
    public void Test1(){
        User user = new User();
        user.setName("lucy");
        user.setAge(18);
        user.setEmail("1@qq.com");
        userRepository.save(user);
    }

    // 查询所有
    @Test
    public void Test2(){
        System.out.println(userRepository.findAll());
    }

     //根据id查询
    @Test
    public void Test3(){
        // 加上get方法才是得到对应的实体类对象
        User user = userRepository.findById("60a1e470ac57e448a0a292ae").get();
        System.out.println(user);
    }


    // 条件查询
    @Test
    public void Test4(){
        // 查找名字为Lucy的记录
        User user = new User();
        user.setName("lucy");
        Example<User> example = Example.of(user);
        List<User> userList = userRepository.findAll(example);
        System.out.println(userList);
    }

    // 模糊查询
    @Test
    public void Test5(){
        // 创建匹配器，既如何使用查询条件
        ExampleMatcher matcher = ExampleMatcher.matching()      // 构建对象
                // 改变默认字符串匹配方式，模糊查询
                .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING)
                .withIgnoreCase(true);  // 改变默认大小写忽略方式：忽略大小写
        User user = new User();
        user.setName("l");      // 查询名字中有l的
        Example<User> userExample = Example.of(user, matcher);
        List<User> userList = userRepository.findAll(userExample);
        System.out.println(userList);
    }

    // 分页查询
    @Test
    public void Test6(){
        // 0表示是第一页
        Pageable pageable = PageRequest.of(0,5);
        // 生成一个page对象，对象中有对应的属性值
        Page<User> userPage = userRepository.findAll(pageable);
        // 调用get方法得到一个Stream流对象
        userPage.get().forEach(System.out::println);
    }

    // 修改
    @Test
    public void Test7(){
        User user = userRepository.findById("60a1e470ac57e448a0a292ae").get();
        user.setName("major");
        user.setAge(18);
        User save = userRepository.save(user);
        System.out.println(save);
    }

    // 删除操作
    @Test
    public void Test8(){
        userRepository.deleteById("60a1e470ac57e448a0a292ae");
    }
}
```



