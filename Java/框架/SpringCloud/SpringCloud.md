# 一、SpringCloud概述

## 1.1 微服务是什么

由于单体应用的局限性和性能瓶颈，使得微服务技术的出现。微服务就是将单体应用的各个功能拆分成各种服务，每一个服务是一个单独的进程。服务与服务之间互不干扰，也就是说在开发的时候可以去并行开发，每个人可以负责开发不同的模块，也就是我们所说的服务。由于是单个服务，所以在请求流量多的情况下我们可以扩容当前服务，从而减少资源的浪费，就好比如我们的上传和下载服务是不经常使用，而下订单的服务是流量巨大的，那么我们就可以对下单的这个服务进行扩容。相比于单体应用，微服务的可扩展性提高了。服务拆分必然会出现很多的问题，服务与服务之间的调用问题，服务的监控问题，服务的链路追踪问题。

SpringCound提供了分布式微服务架构的一站式解决方案，俗称微服务全家桶，它是微服务开发的主流技术栈，在国内开发者社区非常火爆。spring Cloud Alibaba较为突出。它通过一系列的技术来解决分布式微服务所带来的问题。

例子：京东2018年的架构

![image-20230205120403321](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230205120403321.png)

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230205114858713.png" alt="image-20230205114858713" style="zoom:67%;" />

业务和非业务的模块进行拆分



## 1.2 SpringCloud主流技术栈

![image-20230205115124762](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230205115124762.png)

上图所示技术在2020年几乎都已经被新技术替代，但是他们的使用方式和原理大体类似，这里我们还是会学习这些技术，因为一些老项目会用到这些技术

![image-20230205161827474](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230205161827474.png)





## 1.3 Boot和Could技术选型

在官网中可以看到springCloud对应建议的Boot版本





# 二、微服务工程搭建

**五大步骤**

1.  建模块
2.  改pom
3.  改yalml
4.  主启动
5.  业务类

==最重要的一步：先搭建好环境，防止环境出现问题！！！==

需要构建两个服务，订单服务和支付服务，订单服务去调用支付服务的接口





## 父工程搭建

创建一个空的Maven项目

修改POM

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.com</groupId>
    <artifactId>cloud2020</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <modules>
        <module>cloud-provider-payment8001</module>
    </modules>

    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>5.1.47</mysql.version>
        <druid.version>1.2.3</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>


    <!-- 子模块继承之后，提供作用：锁定版本+子modlue不用写groupId和version  -->
    <dependencyManagement>
        <dependencies>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```



## 支付模块搭建

### ①创建模块

cloud-provider-payment



### ②修改Pom

```xml
<parent>
    <artifactId>cloud2020</artifactId>
    <groupId>com.atguigu.com</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>

<artifactId>cloud-provider-payment8001</artifactId>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <!--mysql-connector-java-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <!--jdbc-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```



### ③改yalml

```yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包 com.mysql.jdbc.Driver
    url: jdbc:mysql://study-mysql.com:3306/spring-cloud-study?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: abc123


mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```



### ④主启动

```java
@SpringBootApplication
public class PaymentMain8001 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```



### ⑤业务类

-   entity

    ```java
    @Data
    public class Payment implements Serializable {
    
        private Long id;
        private String serial;
    
        private static final long serialVersionUID = 1L;
    }
    ```

-   mapper

    ```java
    @Mapper
    public interface PaymentMapper {
    
        int create(Payment payment);
    
        Payment getPaymentById(long id);
    }
    ```

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
    <mapper namespace="com.atguigu.springcloud.mapper.PaymentMapper">
    
        <resultMap id="BaseResultMap" type="payment">
            <id column="id" property="id" jdbcType="BIGINT"/>
            <result column="serial" property="serial" jdbcType="VARCHAR"/>
        </resultMap>
    
        <insert id="create" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
            insert into payment(SERIAL) VALUES (#{serial})
        </insert>
    
        <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
            SELECT * FROM payment where `id`= #{id};
        </select>
    
    </mapper>
    ```

-   service

    ```java
    @Service
    public class PayServiceImpl implements PayService {
    
        @Resource
        private PaymentMapper paymentMapper;
    
        @Override
        public int create(Payment payment) {
            return paymentMapper.create(payment);
        }
    
        @Override
        public Payment getPaymentById(Long id) {
            return paymentMapper.getPaymentById(id);
        }
    }
    ```

-   controller

    ```java
    @RestController
    @Slf4j
    @RequestMapping("payment")
    public class PaymentController {
    
        @Resource
        private PayService payService;
    
        @PostMapping("/add")
        public CommonResult addPayment(@RequestBody Payment payment) {
            int res = payService.create(payment);
            log.info("插入结果为 --- {}", res);
            if (res > 0) {
                return new CommonResult(200, "添加成功", res);
            } else {
                return new CommonResult(444, "添加失败");
            }
        }
    
        @GetMapping("/getPaymentById")
        public CommonResult getPaymentById(@RequestParam("id") Long id) {
            Payment payment = payService.getPaymentById(id);
            log.info("查询结果为 --- {}", payment);
            if (payment != null) {
                return new CommonResult(200, "查询成功", payment);
            } else {
                return new CommonResult(444, "查询失败");
            }
        }
    }
    ```






## 订单模块搭建

和支付模块一样的步骤，同时假设订单模块调用支付模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2020</artifactId>
        <groupId>com.atguigu.com</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-order8002</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

```yml
server:
  port: 8002
```

```
CommonResult和Payment类
```



**RestTemplate**

spring提供的发起http请求的请求类，和`Httpclient`的功能类似，它是web模块下的，所以不需要引入额外依赖，只需要将它注册到容器中即可

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

接下来就是使用`RestTemplate`请求服务

```java
@RestController
@Slf4j
@RequestMapping("order")
public class OrderController {

    @Resource
    private RestTemplate restTemplate;

    public static final String PaymentSrv_URL = "http://localhost:8001";

    @GetMapping("/addOrder")
    public CommonResult addPayment(Payment payment) {
        return restTemplate.postForObject(PaymentSrv_URL + "/payment/add",
                payment, CommonResult.class);
    }

    @GetMapping("/getOrderById")
    public CommonResult getPaymentById(@RequestParam("id") Long id) {
        System.out.println("");
        return restTemplate.getForObject(PaymentSrv_URL + "/payment/getPaymentById?id=" + id,
                CommonResult.class);
    }
}
```

测试通过~~~~







## 抽取公共模块

观察发现两个模块都有相同的两个实体类，而且`CommonResult`应该不属于是业务类中的，所以需要将他们抽取出来放入到公共模块`common`或者`model`模块

接下进行改造，将entities移动到`common`模块下，同时将一下公共的依赖也放到`common`下

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>5.8.8</version>
    </dependency>
</dependencies>
```

![image-20230205174812928](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230205174812928.png)

其他模块引用它即可

```xml
<dependency>
    <groupId>com.atguigu.com</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

以后就可以实体类放到common中了，这样就不用重复写



# 三、服务注册与发现

**什么是服务治理**

在我们的前一章节，我们使用了`RestTemplate`来直接请求目标地址，这种调用方式是非常方便的，但是会出现一个问题，那就是一旦微服务模块多了之后，它们之间的依赖关系就非常复杂，难以管理。想想一下，如果某些服务地址改变，我们程序中的请求地址也需要修改，如果项目非常大的话，改起来也是很麻烦的。所以需要使用服务治理，管理服务与服务之间的依赖，实现服务的调用、负载均衡、容错等，实现服务注册与发现。



**什么是服务注册与发现**

服务注册：要对服务进行管理，首先需要告诉我服务在哪，它叫什么，所以在调度之间需要将服务的信息注册到服务注册中心。

发现：通过轮询的负载算法发送心跳，默认周期是30秒，如果在多个周期没有收到来自服务的心跳，注册中心就会移除这个服务。

![image-20230206114138304](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206114138304.png)

上图左边是Eureka的架构，右图是Dubbo的架构。都是先注册，然后再调用





## Eureka（停更维护⚠）

![image-20230206114554754](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206114554754.png)

***Eureka有两大组件***

-   Eureka Server：提供服务注册服务。

    各个微服务节点通过配置启动后，会在Eureka Server进行注册，在Eureka的管理界面可以看到已注册的服务信息

-   Eureka Client：通过注册中心进行访问。

    是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）



### 搭建单机服务

改造我们之前的两个服务（订单和支付），使用Eureka对他们进行管理



#### ①搭建注册中心

1.  建模块 -> `cloud-eureka-server7001`

2.  改pom

    ```xml
    <artifactId>cloud-eureka-server7001</artifactId>
    
    <dependencies>
        <!--eureka-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
    ```

3.  改yml

    ```yaml
    server:
      port: 7001
    
    eureka:
      instance:
        hostname: localhost
      client:
        # 是否向注册中心注册 -> 当前是注册中心，所以为false
        register-with-eureka: false
        # 是否需要检索服务 -> false表示当前端是服务中心
        fetch-registry: false
        # 注册中心的地址
        service-url:
          defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableEurekaServer
    public class EurekaMain7001 {
    
        public static void main(String[] args) {
            SpringApplication.run(EurekaMain7001.class, args);
        }
    }
    ```

==注意：Server端的启动注解是`@EnableEurekaServer`，客户端的启动注解是`@EnableEurekaClient`==

启动测试：浏览器中输入`localhost:7001`访问控制页面

![image-20230206121914411](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206121914411.png)

测试成功~~~



#### ②将模块注册

两个模块执行同样的操作

1.  引入依赖

    ```xml
    <!--eureka-client-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    ```

2.  改yml -> 添加eurake的配置

    ```yaml
    eureka:
      client:
        # 是否注册到eureka中 -> true为是
        register-with-eureka: true
        # 是否是否从EurekaServer抓取已有的注册信息，默认为true。
        # 单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
        fetch-registry: true
        # 注册中心的服务地址
        service-url:
          defaultZone: http://localhost:7001/eureka
    ```

3.  启动测试

    ![image-20230206122324638](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206122324638.png)

    可以看到服务都注册进来



==***出现一个小问题***==![image-20230206122047376](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206122047376.png)

注意：出现`UNKNOWN`是因为我们没有给服务起名字，加上名字就可以显示了

```yml
spring:
  application:
    name: cloud-order-service
```

成功~~~



#### ③调用服务

这里不用修改，还是使用`RestTemplate`发起请求





### 搭建集群服务

 问题：微服务RPC远程调用服务的核心是什么？

高可用，服务中心挂了，整个微服务不可用，所以要采用集群服务保证它的高可用



#### ①集群原理说明

Eurake 服务和 Eurake服务之间相互注册，彼此守望。



#### ②搭建

1.  创建第二个Eurake Server，模块名为`cloud-eureka-server7002`，==端口号为7002==

2.  pom一致

3.  yaml些许不同

    ![image-20230206142300581](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206142300581.png)

    首先，因为是搭建集群，所以会有很多台机器，所以它们的主机名不能为同样，我们是在本地测试的，因此使用主机名映射的方式来测试。在本地的`host`文件修改映射

    | 域名           | ip        |
    | -------------- | --------- |
    | eureka7001.com | 127.0.0.1 |
    | eureka7002.com | 127.0.0.1 |

    映射完成之后还需要修改需要将服务注册的目标注册中心，集群之间的服务相互注册，也就是说7001需要注册到7002，7002需要注册到7001。接下来需要修改`yml配置`

    **服务主机名修改，两个都要修改成对应的主机名**

    ![image-20230206143606854](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206143606854.png)

    **注册中心地址修改**

    ![image-20230206142837211](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206142837211.png)

    ```yml
    # 7001 配置
    service-url:
      # defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://eureka7002.com:7002.comeureka/
    ```

    ```yml
    # 7002 配置
    service-url:
      # defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://eureka7001.com:7001.comeureka/
    ```

4.  启动测试

    7001显示

    ![image-20230206144705582](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206144705582.png)

    7002显示

    ![image-20230206144738040](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206144738040.png)

    完美~~~，老子真是天才，哈哈哈



#### ③服务注册到注册中心集群

需要修改yml文件，将注册中心的地址进行添加，两个模块都要

```yml
# 注册中心的服务地址
service-url:
  defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```

![image-20230206145858717](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206145858717.png)

测试成功~~~,芜湖



### 负载均衡

我i们说订单模块它是消费者，支付模块是提供者，那么在微服务中，服务者一般也是以集群的形式部署，eureka可以对请求进行负载均衡，让集群平摊请求流量



#### ①提供者增加

当前我们的支付模块只有一个，我们给它增加一个，其他的保持不变，端口号改为`8003`，模块名为`cloud-eutake-server8003`



#### ②修改消费者的请求路径

![image-20230206150900172](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206150900172.png)

在之前我们这个请求地址是写死的，这样就达不到负载均衡的效果，所以需要将它的地址修改为eureka提供的地址`http://CLOUD-PAYMENT-SERVICE`，可以看到这个域名就是我们的应用名，所以这个应用名是很重要的!!!

![image-20230206151223413](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206151223413.png)

接着需要修改一下我们的支付模块接口，让它输出的信息可以看到是调用那个端口号的服务。

```java
@Value("${server.port}")
private Integer serverPort;
```

![image-20230206151534475](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206151534475.png)

最后，需要添加一个注解开启负载功能

```java
@Bean
@LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```



#### ③启动测试

![image-20230206152450167](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206152450167.png)

![image-20230206152435291](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206152435291.png)

完美~~~





### actuator微服务信息完善

#### 监控接口名

![image-20230206165752800](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206165752800.png)

在eueka的管理界面可以看到有`actuator`信息相关的链接，它的命名是`主机名:服务名:端口号`，我们可以将它修改成我们的业务名称。这个链接跳转的url是`主机名:端口号/actuator/info`，是以主机名的方式去访问，我们可以修改成以ip的方式去访问，这样可以在排查错误的时候定位那个机子出现了问题。



#### 修改服务id和显示ip

```yaml
eureka:
  instance:
    instance-id: payment8001
    prefer-ip-address: true
```

**测试**

![image-20230206171048764](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206171048764.png)

当前只修改了一台机子，所以另外两台还是默认的命名

点击链接跳转可以看到ip显示出来了

![image-20230206171133576](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206171133576.png)





### 服务发现Discovery

怎么获取注册中心上的服务信息呢，通过服务发现机制获取

这里我们获取`payment8001`服务的信息

1.  依赖`DiscoveryClient`

    ```java
    @Resource
    private DiscoveryClient discoveryClient;
    ```

2.  修改PaymentController  -> 创建访问接口

    ```java
    @GetMapping("/discovery")
    public DiscoveryClient getDiscovery() {
        // 获取所有服务
        List<String> services = discoveryClient.getServices();
        for (String service : services) {
            log.info(service);
        }
        // 获取实例
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId() + "\t" + instance.getHost()
                     + "\t" + instance.getPort() + "\t" + instance.getUri());
        }
        return this.discoveryClient;
    }
    ```

3.  开启服务发现机制

    ```java
    // 主类添加
    @EnableDiscoveryClient
    ```

4.  ==注意：需要将`eureka.client.fetch-registry`改为`true`才能获取到服务信息，因为这个功能就是控制我们是否能获取注册中心信息的开关.==

**测试**

访问`http://localhost:8001/payment/discovery`

![image-20230206173433796](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206173433796.png)

可以看到返回的是注册的服务列表

查看日志输出

![image-20230206173647625](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206173647625.png)

可以看到我们获取的指定服务id的服务实例，可以看到我们修改和没修改的两个服务





### Eureka自我保护模式

![image-20230206164113933](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230206164113933.png)

在eureka的管理界面可以看到这一行话，它的意思是Eureka进入了保护模式



#### ***什么是自我保护模式***

eureka发现机制是默认每30秒发送一起心跳，如果30过后没有发送心跳，开启自我保护模式的话，那么它默认将此服务信息保存90秒，在90秒内还是没有发送心跳，那么它就会将此服务移除。关闭自我保护模式的话，在指定时间没有收到Eureka Clinet发送的心跳，它会直接移除，不进行自我保护（残忍的Eureka~~~）。

自我保护的好处就是在发送心跳的过程中服务网络拥塞或者服务执行缓慢导致心跳发送不及时，这个时候eureka会给它一定的时间发送心跳，如果超出这一段时间，那么eureka只能狠心的将它移除。

自我保护模式符合CSP中的AP机制



#### ***如何关闭自我保护模式***

它默认是开启的，我们想要关闭就需要修改它的配置，也就是修改`yaml配置文件`

-   Eureka Server端

    ```yaml
    server:
      # 禁用自我保护模式
      enable-self-preservation: false
    ```

-   Eureka Client端

    ```yaml
    eureka:
      instance:
        # 发送心跳间隔时间，默认是30s
        lease-renewal-interval-in-seconds: 1
        # 等待时间，默认是90s，开启自我保护模式会等待到指定的时间，在指定时间内没有发送心跳，eureka移除该服务信息
        lease-expiration-duration-in-seconds: 2
    ```



**测试**

启动Server，在启动client，然后停掉client，看管理页面是否还有该服务





## Zookeeper

zookeeper是一个分布式协调工具，可以实现注册中心功能。一般是用它去替代eureka，但是用的少，这里介绍一下单机版的zookeeper，集群的可以自行学习。安装这里也不讲解。当前使用的cloud对应的zookeeper版本是`3.5.3-beta`



### ①构建服务提供者

向zk注册信息

1.  建模块 -> 新建cloud-provider-payment8004

2.  改pom

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    </dependency>
    ```

3.  改yml

    ```yaml
    server:
      port: 8004
    
    #服务别名----注册zookeeper到注册中心名称
    spring:
      application:
        name: cloud-provider-payment
      cloud:
        zookeeper:
          # zk服务器地址，我这里做了映射
          connect-string: study-zookeeper.com:2181
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient  //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
    public class PaymentMain8004 {
    
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain8004.class, args);
        }
    }
    ```

5.  业务类

    

**查看**

启动zk的client端查看注册的信息

![image-20230207111756161](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207111756161.png)

zk是无状态的，只要服务下线，立刻将服务信息删除，再次上线，重新分配新的id。不像eureka那样拥有自我保护机制。



### ②构建服务消费者

调用提供者的接口

1.  新建cloud-consumerzk-order8002模块

2.  pom

    ```xml
    <dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  yml

    ```yaml
    server:
      port: 8004
    
    #服务别名----注册zookeeper到注册中心名称
    spring:
      application:
        name: cloud-provider-payment
      cloud:
        zookeeper:
          connect-string: study-zookeeper.com:2181
    ```

4.  启动类

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient  //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
    public class PaymentMain8004 {
    
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain8004.class, args);
        }
    }
    ```

5.  业务

    ```java
    @RestController
    public class PaymentController {
    
        @Value("${server.port}")
        private Integer serverPort;
    
        @GetMapping("/payment/zk")
        public String paymentZK() {
            return "springcloud with ck：" + serverPort + "\t" + UUID.randomUUID().toString();
        }
    }
    ```

查看服务是否注册进zk

![image-20230207113727953](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207113727953.png)

测试服务是否正常 -> 访问`http://localhost:8002/consumer/payment/zk`

![image-20230207113755471](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207113755471.png)

正常~~~😋😋😋





## Consul

### 1.简介

官网：`https://www.consul.io/`

![image-20230207141931306](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207141931306.png)

上图是官网的说明。

翻译：HashiCorp Consul 是一种服务网络解决方案，使团队能够管理服务之间以及跨本地和多云环境和运行时的安全网络连接。Consul 为网络基础设施设备提供服务发现、服务网格、流量管理和自动更新。您可以在单个 Consul 部署中单独或一起使用这些功能。

人话：就是服务注册与发现功能和服务管理，当然还有一些其他的功能

| 功能          | 实现                                                 |
| ------------- | ---------------------------------------------------- |
| 服务发现      | 提供HTTP和DNS两种发现方式。                          |
| 健康监测      | 支持多种方式，HTTP、TCP、Docker、Shell脚本定制化监控 |
| KV存储        | Key、Value的存储方式                                 |
| 多数据中心    | Consul支持多数据中心                                 |
| 可视化Web界面 |                                                      |





### 2.安装

在官网下载，这里选择的是`1.13.0`版本，选择适合你的机器的系统和架构类型，我这里是window和x86架构

下载完解压后是一个`.exe`文件。

***运行***：打开cmd界面输入`consul agent -dev`，它默认是在本机的`8500`端口

***访问***：浏览器输入`http://localhost:8500/`或`http://localhost:8500/ui/dc1/services`访问可视化web界面

![image-20230207141708460](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207141708460.png)





### 3.使用

还是一样的套路，五步走，建model、改pom、改yml、主启动、业务类

#### 3.1 构建提供者模块

1.  新建`cloud-providerconsul-payment8006`模块

2.  pom

    ```xml
    <dependencies>
            <!--SpringCloud consul-server -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-consul-discovery</artifactId>
            </dependency>
            <!-- SpringBoot整合Web组件 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
            <!--日常通用jar包配置-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <scope>runtime</scope>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <optional>true</optional>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
    ```

3.  yml

    ```yaml
    ###consul服务端口号
    server:
      port: 8006
    
    spring:
      application:
        name: consul-provider-payment
    ####consul注册中心地址
      cloud:
        consul:
          host: localhost
          port: 8500
          discovery:
            #hostname: 127.0.0.1
            service-name: ${spring.application.name}
    
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient
    public class PaymentConsulMain8004 {
    
        public static void main(String[] args) {
            SpringApplication.run(PaymentConsulMain8004.class, args);
        }
    }
    ```

5.  业务

    ```java
    @RestController
    public class PaymentController {
    
        @Value("${server.port}")
        private Integer serverPort;
    
        @GetMapping("/payment/zk")
        public String paymentZK() {
            return "springcloud with consul：" + serverPort + "\t" + UUID.randomUUID().toString();
        }
    }
    ```



#### 3.2 构建消费者模块

1.  新建`cloud-consumerconsul-order8002`模块

2.  pom

    ```xml
    <dependencies>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  yml

    ```yaml
    ###consul服务端口号
    server:
      port: 8002
    
    spring:
      application:
        name: cloud-consumer-order
    ####consul注册中心地址
      cloud:
        consul:
          host: localhost
          port: 8500
          discovery:
            #hostname: 127.0.0.1
            service-name: ${spring.application.name}
    
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
    public class OrderConsulMain8002
    {
        public static void main(String[] args)
        {
            SpringApplication.run(OrderConsulMain8002.class,args);
        }
    }
    ```

5.  业务

    ```java
    @Configuration
    public class ApplicationContextBean
    {
        @Bean
        @LoadBalanced
        public RestTemplate getRestTemplate()
        {
            return new RestTemplate();
        }
    }
    ```

    ```java
    public class OrderController {
    
        @Resource
        private RestTemplate restTemplate;
    
        public static final String INVOKE_URL = "http://cloud-provider-payment";
    
        @GetMapping("/consumer/payment/consul")
        public String paymentInfo() {
            return "消费者调用支付服务 ---> result:" + restTemplate.getForObject(
                    INVOKE_URL + "/payment/zk", String.class);
        }
    }
    ```



#### 3.3 测试

***查看web界面***

![image-20230207153540785](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207153540785.png)

可以看到已经注册进去的两个服务



***测试是否可以调用提供者接口***

![image-20230207153822298](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207153822298.png)

成功~~~😋😋😋





## 三个注册中心异同点

### CAP

CAP理论关注粒度是数据，而不是整体系统设计的策略

-   C:Consistency（强一致性）
-   A:Availability（可用性）
-   P:Partition tolerance（分区容错性）



### 案例

#### AP

`eureka`就是AP。

![image-20230207160815582](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207160815582.png)

AP的特点就是A系统和B系统数据同步失败时，它优先考虑的是可用性，所以它将旧的值返回，对于一些不是很重要的数据时使用CP架构是合理的。



#### CP

`zookeeper`和`consul`是CP

![image-20230207160934013](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230207160934013.png)

CP就是如果数据同步失败，那么它就会返回一个异常。它损失了高可用，但是保证了一致性，对于一些重要的数据使用CP是合理的。







# 四、负载均衡服务调用







## Ribbon

### 1、概述

在我们学习Eureka的那一章节，我们在`RestTemplate`上使用`@LoadBalanced `注解，用它来开启负载均衡，它的底层是使用了`Ribbon`来做负载均衡

![image-20230212111414993](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230212111414993-16761716561541.png)



**LB（负载均衡）**

-   集中式LB：将请求用过某些策略转发到服务方，可以是硬件、也可以是软件，如nginx
-   进程式LB：它是客户端上的一个进程，它负责获取提供方的提供服务的一系列主机地址，通过负载均衡算法将请求发送给提供方集群。

ribbon属于是进程式LB，eureka的例子我们可以看出来我们只在客户端开启了负载均衡。ribbon是一个框架，在客户端通过算法来做负载均衡调用。它还可以结合其他的客户端使用，不仅限于eureka

==Ribbon进入了维护模式，后面有新的技术可以替代它==





### 2、Ribbon核心组件IRule

IRule：根据特定算法从服务列表中获取服务

![image-20230212113530326](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230212113530326.png)

上面是IRule的实现类，可以看到所有的负载均衡规则都继承了`AbstractLoadBalancerRule`

它们对应的规则

| 实现类                    | 规则                                                         |
| ------------------------- | ------------------------------------------------------------ |
| RoundRobinRule            | 轮询                                                         |
| RandomRule                | 随机                                                         |
| RetryRule                 | 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务 |
| WeightedResponseTimeRule  | 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择 |
| BestAvailableRule         | 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务 |
| AvailabilityFilteringRule | 先过滤掉故障实例，再选择并发较小的实例                       |
| ZoneAvoidanceRule         | 默认规则,复合判断server所在区域的性能和server的可用性选择服务器 |





### 3、替换默认的IRule

在之前的`cloud-consumer-order8002（rureka工程）`上添加下面的操作

1.  创建一个新的package

    `com.atguigu.myrule`

2.  重新实现IRule

    ```java
    @Configuration
    public class MySelfRule {
    
        @Bean
        public IRule myRule() {
            return new RandomRule();
        }
    }
    ```

3.  主启动类添加`@RibbonClient`

    ```java
    @SpringBootApplication
    @EnableEurekaClient
    @RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class)
    public class OrderMain8002 {
    
        public static void main(String[] args) {
            SpringApplication.run(OrderMain8002.class, args);
        }
    }
    ```

注意点：

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230212114841739.png" alt="image-20230212114841739" style="zoom:50%;" />

**官方文档明确给出了警告**：
这个自定义配置类不能放在@ComponentScan所扫描的当前包下以及子包下，否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享，达不到特殊化定制的目的了。springBoot它默认是扫描当前启动类所在包的组件以及所在包子包下的所有组件，所以我们要将自定义的`Rule`放到其他包下





### 4、RoundRobinRule源码分析

`RoundRobinRule`它的规则是轮询，轮询就是 `获取的服务index = 请求的次数 % 服务提供者的数`，通过这个`index`来获取对应位置的服务信息，然后再向目标主机发起请求

**源码分析**

IRule中有一个`choose`方法，这个方法就是定义如何进行选择。我们看一下`RoundRobinRule`中的`choose`就知道是怎样的规则

```java
public Server choose(ILoadBalancer lb, Object key) {
    if (lb == null) {
        log.warn("no load balancer");
        return null;
    }

    Server server = null;
    int count = 0;
    while (server == null && count++ < 10) {
        List<Server> reachableServers = lb.getReachableServers();
        List<Server> allServers = lb.getAllServers();
        // 正常运行的提供者数量
        int upCount = reachableServers.size();
        // 总的提供者数量
        int serverCount = allServers.size();

        if ((upCount == 0) || (serverCount == 0)) {
            log.warn("No up servers available from load balancer: " + lb);
            return null;
        }
	    // 最主要获取index的方法 
        int nextServerIndex = incrementAndGetModulo(serverCount);
        server = allServers.get(nextServerIndex);

        if (server == null) {
            /* Transient. */
            Thread.yield();
            continue;
        }

        if (server.isAlive() && (server.isReadyToServe())) {
            return (server);
        }

        // Next.
        server = null;
    }

    if (count >= 10) {
        log.warn("No available alive servers after 10 tries from load balancer: "
                + lb);
    }
    return server;
}
```

```java
// 核心方法
private int incrementAndGetModulo(int modulo) {
    // 自旋锁
    for (;;) {
        int current = nextServerCyclicCounter.get();
        // current -> 当前请求次数
        // modulo -> 提供者数量
        // 当前请求数 % 提供者数量 = 实际调用的提供者
        int next = (current + 1) % modulo;
        if (nextServerCyclicCounter.compareAndSet(current, next))
            return next;
    }
}
```

例如：提供者数量为3

| 请求数 | 结果                   |
| ------ | ---------------------- |
| 1      | 1 % 3 = 1，list.get(1) |
| 2      | 2 % 3 = 2，list.get(2) |
| 3      | 3 % 3 = 0，list.get(0) |
| 4      | 4 % 3 = 1，list.get(1) |

观察发现，请求很均匀的分发给提供者。





### 5、手写一个负载均衡调用

在上一节我们知道了轮询算法的原理，接下来我们自己来手写一个轮询算法做负载均衡



#### 提供者改造

`cloud-provider-payment8001`和`cloud-provider-payment8003`两个模块添加接口

```java
@GetMapping("lb")
public String lb() {
    return serverPort.toString();
}
```



#### 消费者改造

`cloud-consumer-order8002`

1.  首先定义一个获取服务信息的规则，这里我们使用的还是轮询策略

    

    ```java
    package com.atguigu.springcloud.lb;
    public interface LoadBalancer {
    
        /**
         * 获取服务实例
         * @param serviceInstances 服务实例列表
         * @return
         */
        ServiceInstance instance(List<ServiceInstance> serviceInstances);
    }
    ```

    ```java
    package com.atguigu.springcloud.lb;
    @Component
    @Slf4j
    public class CustomLB implements LoadBalancer {
    	
        // 原子类
        private AtomicInteger atomicInteger = new AtomicInteger(0);
    
        public final  int getAndIncrement() {
            int current;
            int next;
            do {
                // 获取当前值
                current = this.atomicInteger.get();
                // 下一次请求数，超过int最大值就设置为0,
                next = current >= Integer.MAX_VALUE ? 0 : current + 1;
            } while (!this.atomicInteger.compareAndSet(current, next));
            log.info("*****next*****: {}", next);
            return next;
        }
    
        @Override
        public ServiceInstance instance(List<ServiceInstance> serviceInstances) {
            int index = getAndIncrement() % serviceInstances.size();
            return serviceInstances.get(index);
        }
    }
    ```

2.  增加接口方法

    ```java
    @GetMapping("/consumer/payment/lb")
    public String getPaymentLB() {
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        // 通过我们自定义的规则来获取处理请求的服务实例
        ServiceInstance serviceInstance = loadBalancer.instance(instances);
        if (instances == null || instances.size() <= 0) {
            return null;
        }
        // 获取服务的uri
        URI uri = serviceInstance.getUri();
        return restTemplate.getForObject(uri + "/payment/lb", String.class);
    }
    ```

3.  去掉`RestTemplate`中的`@LoadBalanced`注解

4.  测试 -> 每次出现不同的端口号表示成功





# 五、服务接口调用

## OpenFeign

### 1、概述

#### 1.1 Feign是什么

**官网原话**：

[Feign](https://github.com/OpenFeign/feign) is a declarative web service client. It makes writing web service clients easier. To use Feign create an interface and annotate it. It has pluggable annotation support including Feign annotations and JAX-RS annotations. Feign also supports pluggable encoders and decoders. Spring Cloud adds support for Spring MVC annotations and for using the same `HttpMessageConverters` used by default in Spring Web. Spring Cloud integrates Eureka, Spring Cloud CircuitBreaker, as well as Spring Cloud LoadBalancer to provide a load-balanced http client when using Feign.

Feign 是一个声明式的 Web 服务客户端。它使编写 Web 服务客户端变得更加容易。**要使用 Feign，请创建一个接口并对其进行注释**。它具有可插入的注释支持，包括 Feign 注释和 JAX-RS 注释。Feign 还支持可插入的编码器和解码器。 Spring Cloud 添加了对 Spring MVC 注释的支持，并支持使用在 Spring Web 中默认使用的相同HttpMessageConverters。Spring Cloud 集成了 Eureka，Spring Cloud CircuitBreaker，以及 Spring Cloud LoadBalancer，在使用 Feign 时提供负载均衡的 http 客户端。

通过这段话可以看出来使用Feign可以使用请求变得更加方便和简单，只需要一个接口和配置即可。



#### 1.2 Feign能做什么？

Feign使编写Java Http客户端变得更容易

在前面的章节中我们学习了`Ribbon + RestTemplate`请求服务，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际的开发中，由于对服务的依赖可能不止一处，往往一个接口会被多处调用，所以需要将这些接口进行封装，然后再进行调用。Feign帮我们实现了封装，它只需要我们定义一个接口和标注注解就会自动帮我们生成对服务的调用封装。

Feign它集成了Ribbon，所以Feign也可以做负载均衡调用。只需要做一些配置即可。



#### 1.3 Feign和OpenFeign的区别

| Feign                                                        | OpenFeign                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Feign是Spring Cloud组件中的一个轻量级RESTful的HTTP服务客户端，Feign内置了Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。Feign的使用方式是：使用Feign的注解定义接口，调用这个接口，就可以调用服务注册中心的服务 | OpenFeign是Spring Cloud 在Feign的基础上支持了SpringMVC的注解，如@RequesMapping等等。OpenFeign的@FeignClient可以解析SpringMVC的@RequestMapping注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。 |
| `<dependency>    <groupId>org.springframework.cloud</groupId>    <artifactId>spring-cloud-starter-feign</artifactId></dependency>` | `<dependency>    <groupId>org.springframework.cloud</groupId>    <artifactId>spring-cloud-starter-openfeign</artifactId></dependency>` |





### 2、使用OpenFeign

微服务调用接口+`@FeignClient`来使用

1.  新建`cloud-consumer-feign-order80`

2.  POM

    ```xml
    <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  Yaml

    ```yaml
    server:
      port: 8002
    
    spring:
      application:
        name: cloud-order-service
    
    eureka:
      client:
        register-with-eureka: false
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka/
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableFeignClients
    public class OrderFeignMain80 {
    
        public static void main(String[] args) {
            SpringApplication.run(OrderFeignMain80.class, args);
        }
    }
    ```

5.  业务类

    新建一个包放我们的服务调用接口，这里使用service包

    ```java
    package com.atguigu.springcloud.service;
    
    @Component
    // value为服务名
    @FeignClient("CLOUD-PAYMENT-SERVICE")
    public interface PaymentFeignService {
    
        @GetMapping("/payment/getPaymentById")
        CommonResult getPaymentById(@RequestParam("id") Long id);
    }
    ```

    新建一个controller

    ```java
    @RestController
    @RequestMapping("order")
    public class OrderFeignController {
    
        @Resource
        private PaymentFeignService paymentFeignService;
    
        @GetMapping("/get/{id}")
        public CommonResult getPaymentById(@PathVariable("id") Long id) {
            return paymentFeignService.getPaymentById(id);
        }
    }
    ```

6.  启动服务测试

    由于OpenFeign自带Ribbon，所以它也有负载均衡的功能。

    ![image-20230213154951123](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230213154951123.png)

    ![image-20230213155017116](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230213155017116.png)





### 3、OpenFeign超时控制

默认情况下，OpenFeign等待1秒钟，**如果服务端处理超过1秒钟**，Feign客户端就会报错。在某些场景下允许服务处理超过一秒钟，为了避免报错的情况发生，我们需要设置Feign客户端的超时时间，也就是超时控制。在yml文件中进行配置

![image-20230213160629506](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230213160629506.png)

这个是超时的报错。

yml文件配置超时控制

```yml
#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```



### 4、开启日志打印功能

Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。就是对Feign接口的调用情况进行监控和输出



#### 4.1 日志级别

| 级别    | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| NONE    | 默认的，不显示任何日志；                                     |
| BASIC   | 仅记录请求方法、URL、响应状态码及执行时间；                  |
| HEADERS | 除了 BASIC 中定义的信息之外，还有请求和响应的头信息；        |
| FULL    | 除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。 |



#### 4.2 配置输出日志级别

将我们需要修改的日志级别注入到容器中即可

```java
@Configuration
public class FeignConfig {
    @Bean
    Logger.Level feignLoggerLevel()
    {
        return Logger.Level.FULL;
    }
}
```



#### 4.3 配置文件中设置需要开启的Feign客户端

```yaml
logging:
  level:
  	# 开启service下的所有Feign客户端的
    com.atguigu.springcloud.service.*: debug
```



#### 4.4 查看日志输出结果

![image-20230213161648831](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230213161648831.png)





# 六、服务降级

## Hystrix（停更维护⚠）

### 1、概述

#### 1.1 分布式系统面临的问题

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。

![image-20230213180236426](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230213180236426.png)

**服务雪崩**

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”，“扇出就是向扇子打开的时候一样一个个褶子向外张开。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”.

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。
所以，
通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量，然后这个有问题的模块还调用了其他的模块，这样就会发生级联故障，或者叫雪崩。

***总结就是***：微服务调用链路上的一个服务出现问题就会导致整个服务的调用出现问题，这个问题可以是延迟或者是错误。



#### 1.2 Hystrix是什么

Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（**FallBack**），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。



#### 1.3 最主要的功能

***服务降级***

在服务发生超时或者异常错误的时候对服务进行处理，返回一个友好的提示(`fallback`)

例如：返回信息 -> 服务器繁忙，请稍后重试

那些情况会发生降级

-   程序运行异常
-   超时
-   服务熔断触发服务降级
-   线程池/信号量打满也会导致服务降级



***服务熔断***

请求达到服务能处理的最大值，直接拒接访问。就像保险丝一样，电量到达最大值就会跳闸。



***服务限流***

秒杀等高并发操作。不能一下子请求过来，规定一秒能处理的请求，其他请求需要进行排队。



***服务监控***

对服务进行监控。





### 2、服务降级

#### 2.1 搭建工程

##### 2.1.1提供者搭建

1.  创建`cloud-provider-hystrix-payment8001`模块

2.  POM

    ```xml
    <dependencies>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 8001
    
    spring:
      application:
        name: cloud-provider-hystrix-payment
    
    eureka:
      client:
        register-with-eureka: true
        fetch-registry: true
        service-url:
          #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
          defaultZone: http://eureka7001.com:7001/eureka
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableEurekaClient
    public class PaymentHystrixMain8001 {
    
        public static void main(String[] args) {
            SpringApplication.run(PaymentHystrixMain8001.class, args);
        }
    }
    ```

5.  业务类

    ***service***

    ```java
    @Service
    public class PaymentService {
    
        public String paymentInfo_OK(Integer id) {
            return String.format("当前线程：%s\tpaymentInfo_OK\tid:%d\tO(∩_∩)O哈哈~",
                    Thread.currentThread().getName(), id);
        }
    
        public String paymentInfo_TimeOut(Integer id) {
            int number = 3;
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            return String.format("当前线程：%s\tpaymentInfo_TimeOut\tid:%d\tO(∩_∩)O哈哈~，耗费%d秒",
                    Thread.currentThread().getName(), id, number);
        }
    }
    ```

    ***controller***

    ```java
    @Slf4j
    @RestController
    @RequestMapping("/payment")
    public class PaymentController {
    
        @Resource
        private PaymentService paymentService;
    
        @Value("${server.port}")
        private Integer serverPort;
    
        @GetMapping("/hystrix/ok/{id}")
        public String paymentInfo_OK(@PathVariable("id") Integer id) {
            String result = paymentService.paymentInfo_OK(id);
            log.info("result******：{}", result);
            return result;
        }
    
        @GetMapping("/hystrix/timeout/{id}")
        public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
            String result = paymentService.paymentInfo_TimeOut(id);
            log.info("result******：{}", result);
            return result;
        }
    }
    ```



##### 2.1.2消费者工程搭建

1.  创建``




#### 2.2 模拟并发



##### 2.2.1 JMater配置

使用`JMater`向`8001`服务发起20000个请求

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214111847339.png" alt="image-20230214111847339" style="zoom: 67%;" />

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214111955082.png" alt="image-20230214111955082" style="zoom:67%;" />



##### 2.2.2 并发下的消费接口

在并发下访问`http://localhost:8002/consumer/payment/hystrix/timeout/1`，这个接口是消费者访问提供者

![image-20230214115816121](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214115816121.png)

结果发现报错了或者是等待加载，这个错误是Feign调用的超时错误。

很明显客户看不懂这个错误，我们应该返回友好的提示而不是错误，所以需要做服务降级，在发生异常或错误的时候`fallback`，**返回一个兜底的方法**。

>   Q：我们使用Feign的超时控制处理不就好了
>
>   A：那得将Feign的超时控制设置很长的时间，这个确实也可以解决问题，但是让客户等那么长时间不合适，而且占资源，会把服务器拖垮。想象一下一个请求没解决完，下一个请求又进来了，这样一堆积，服务器资源很快就会用完。所以这个时候使用服务降级是比较合适的。



#### 2.3 设置服务降级

可能发生的问题：

| 问题                     | 解决方案                                                     |
| ------------------------ | ------------------------------------------------------------ |
| 超时导致服务变慢         | 超时不再等待，提供者端需要做服务降级，超出指定时间就返回兜底方法 |
| 出错(宕机或程序运行出错) | 出错要有兜底，提供者端宕机或者出错，消费者端需要有兜底的方法(fallback) |



##### 2.3.1 提供者端降级

***开启服务降级功能***

主启动类添加注解

```java
@EnableCircuitBreaker   // 开启服务降级
```

***设置异常或超时并设置服务降级***

`localhost:8001/payment/hystrix/timeout/{id}`这个接口是我们模拟的超时接口。它实际调用返回的是`PaymentService#paymentInfo_TimeOut`方法

修改`paymentInfo_TimeOut`方法：设置超时或者异常

```java
// 开启服务降级 -> 处理超过3秒就fallback，执行兜底方法 paymentInfo_TimeOutHandler 返回
@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler", commandProperties = {
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "3000")
})
public String paymentInfo_TimeOut(Integer id) {
    // 制造异常
    // int = 10 / 0;
    int number = 5;
    // 制造超时
    try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { throw new RuntimeException(e);}
    return String.format("当前线程：%s\tpaymentInfo_TimeOut\tid:%d\tO(∩_∩)O哈哈~，耗费%d秒",
            Thread.currentThread().getName(), id, number);
}

public String paymentInfo_TimeOutHandler(Integer id) {
    return "/(ㄒoㄒ)/调用支付接口超时或异常：\t"+ "\t当前线程池名字" + Thread.currentThread().getName();
}
```

说明：此时开启了服务降级。当方法处理超过3秒或者发生错误就返回兜底方法。

***测试***

提供者端：访问`http://localhost:8001/payment/hystrix/timeout/1`，返回的是兜底方法

![image-20230214142611023](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214142611023.png)

消费者端：访问`http://localhost:8002/consumer/payment/hystrix/timeout/1`,返回的是错误信息

![image-20230214142824832](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214142824832.png)

Feign调用超时异常，这是因为提供者端做了服务降级，但是消费者端没有做服务降级，所以提供者端发生异常或超时，消费端还是会报错的。所以需要在两个端都要进行服务降级才能保证服务正常返回。



##### 2.3.2 消费者端降级

***开启服务降级***

1.  主启动类添加注解

    ```java
    @EnableHystrix
    ```

2.  修改yml文件

    ```yaml
    # 用于服务降级 在注解@FeignClient中添加fallbackFactory属性值
    feign:
      hystrix:
        # 开启hystrix
        enabled: true
    ```

***设置服务降级***

```java
// 设置服务降级 -> 处理时间超过2秒就fallback
@HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value="2000")
})
@GetMapping("/payment/hystrix/timeout/{id}")
public String paymentInfoTimeOut(@PathVariable("id") Integer id) {
    return paymentHystrixService.paymentInfo_TimeOut(id);
}

public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id)
{
    return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
}
```

***测试***

此时访问`http://localhost:8002/consumer/payment/hystrix/timeout/1`就不会有错误信息，而是fallback

![image-20230214143825300](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214143825300.png)



#### 2.4 代码耦合问题

##### 2.4.1 耦合问题

观察我们的代码发现，业务代码和服务降级的代码耦合了

![image-20230214144356338](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214144356338.png)

如果每一个业务方法都要有一个兜底方法的话，代码就会很混乱，也不符合我们面向对象的思想，所以这些和业务无关的代码我们应该给它解耦。



##### 2.4.2 设置全局fallback方法

设置一个全局fallback方法，所以服务降级的方法都返回全局的，个别需要定制的就自己设置自己的fallback方法。

类上标注注解并设置fallback方法

```java
@DefaultProperties(defaultFallback = "paymentGlobalFallbackMethod")
public class OrderHystirxController {
    
    public String paymentGlobalFallbackMethod() {
        return "Global 444 对方系统繁忙或宕机了，请稍后重试，O(∩_∩)O哈哈~~~";
    }
```

所有服务熔断方法都修改成默认的，个别需要定制的就不需要修改。

```java
@HystrixCommand(commandProperties = {
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value="2000")
})
@GetMapping("/payment/hystrix/timeout/{id}")
public String paymentInfoTimeOut(@PathVariable("id") Integer id) {
    return paymentHystrixService.paymentInfo_TimeOut(id);
}
```



***测试***

访问`http://localhost:8002/consumer/payment/hystrix/timeout/1`

![image-20230214145620146](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214145620146.png)

成功，nice~~~



##### 2.4.3 统一管理fallback方法

>   Q：我想每一个都有专属的fallback方法可不可以
>
>   A：当然可以

创建一个类实现服务调用接口，在重写的方法中写我们的兜底方法，对应调用的接口方法发生超时或者异常就会调用实现类中对应方法作为兜底方法，这样我们的每一个调用都可以定制自己的fallback方法

```java
@Component
public class PaymentFallbackService implements PaymentHystrixService{

    @Override
    public String paymentInfo_OK(Integer id) {
        return "服务调用失败，提示来自：" +
                "cloud-consumer-feign-order8002#paymentInfo_OK";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id) {
        return "服务调用失败，提示来自：" +
                "cloud-consumer-feign-order8002#paymentInfo_TimeOut";
    }
}
```

==注意：一定要将它注入到容器中，也就是一定要加`@component`注解==

在服务调用接口中声明fallback方法所在的实现类

```java
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT",fallback = PaymentFallbackService.class)
public interface PaymentHystrixService {
```

这个时候默认的fallback就不能用了，因为每个都有专属的fallback方法了。





### 3、服务熔断

#### 3.1 熔断机制概述

熔断机制是应对雪崩效应的微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间过长时，会对服务降级，当失败的次数或失败率达到指定值就会触发熔断机制，直接返回错误信息，减少响应时间。当检测该节点服务调用响应正常后，就会恢复链路调用。

在SpringCloud框架中，熔断机制通过Hystrix实现。Hystrix会监控微服务的调用状况。当失败的调用达到一定的阈值，缺省是5秒内20次调用失败，就会启动熔断机制。

>   大神论文：https://martinfowler.com/bliki/CircuitBreaker.html
>
>   这篇论文说的很详细

断路器负责开启和关闭熔断



#### 3.2 使用服务熔断

熔断机制的注解是`@HistrixCommand`



**修改`cloud-provider-hystrix-payment8001`工程**

-   添加service并设置熔断

    ```java
    @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
        	// 开启服务熔断
            @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),
        	// 滚动时间窗口期内失败10次开启熔断
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),
        	// 滚动时间窗口期，该时间用于断路器判断健康度时需要收集信息的持续时间
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),
        	// 滚动时间窗口期内，错误请求数百分比超过了60就打开熔断
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"),
    })
    public String paymentCircuitBreaker(@PathVariable("id") Integer id)
    {
        if(id < 0)
        {
            throw new RuntimeException("******id 不能负数");
        }
        String serialNumber = IdUtil.simpleUUID();
    
        return Thread.currentThread().getName()+"\t"+"调用成功，流水号: " + serialNumber;
    }
    public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id)
    {
        return "id 不能负数，请稍后再试，/(ㄒoㄒ)/~~   id: " +id;
    }
    ```

    ***说明***：@HystrixCommand属性的意思是开启熔断机制，在10秒内超过6次失败就开启断路器

-   添加controller

    ```java
    @GetMapping("/circuit/{id}")
    public String paymentCircuitBreaker(@PathVariable("id") Integer id)
    {
        String result = paymentService.paymentCircuitBreaker(id);
        log.info("****result: "+result);
        return result;
    }
    ```



**测试**

首先先不断访问`http://localhost:8001/payment/circuit/-1`，多次访问后再访问`http://localhost:8001/payment/circuit/1`，此时就是返回的是fallback方法，而不是业务方法

![image-20230214181312840](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214181312840.png)

等待一段时间再次访问又恢复了正常。

![image-20230214181337314](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214181337314.png)



#### 3.3 原理

##### 3.3.1 大神论文

>   大神论文[https://martinfowler.com/bliki/CircuitBreaker.html]中有说明断路器的机制

![image-20230214181615815](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214181615815.png)

**说明**：意思就是当服务调用的失败次数达到一定阈值就会打开断路器，过一段时间后就会以半开的状态来打开断路器。，如果调用成功则将重置断路器，否则将重新启动断路器。



##### 3.3.2 熔断类型

-   熔断打开

    请求不再进行调用当前服务，内部设置时钟一般为MTTR（平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态

-   熔断关闭

    熔断关闭不会对服务进行熔断

-   熔断半开

    部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断



##### 3.3.3 熔断器执行步骤

官网原文：

![image-20230214182658468](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230214182658468.png)

Hystrix 实现服务熔断的步骤如下：

1.  当服务的调用出错率达到或超过 Hystix 规定的比率（默认为 50%）后，熔断器进入熔断开启状态。
2.  熔断器进入熔断开启状态后，Hystrix 会启动一个休眠时间窗，在这个时间窗内，该服务的降级逻辑（fallback方法）会临时充当业务主逻辑，而原来的业务主逻辑不可用。
3.  当有请求再次调用该服务时，会直接调用降级逻辑（fallback方法）快速地返回失败响应，以避免系统雪崩。
4.  当休眠时间窗到期后，Hystrix 会进入半熔断转态，允许部分请求对服务原来的主业务逻辑进行调用，并监控其调用成功率。
5.  如果调用成功率达到预期，则说明服务已恢复正常，Hystrix 进入熔断关闭状态，服务原来的主业务逻辑恢复；否则 Hystrix 重新进入熔断开启状态，休眠时间窗口重新计时，继续重复第 2 到第 5 步。



#### 3.4 属性值及其作用

在`HystrixCommandProperties`类中可以找到所有的属性

| 属性值                                              | 作用                                                         |
| --------------------------------------------------- | ------------------------------------------------------------ |
| execution.isolation.strategy                        | 设置隔离策略，THREAD 表示线程池 SEMAPHORE：信号池隔离        |
| execution.isolation.semaphore.maxConcurrentRequests | 当隔离策略选择信号池隔离的时候，用来设置信号池的大小（最大并发数） |
| execution.isolation.thread.timeoutinMilliseconds    | 配置命令执行的超时时间                                       |
| execution.timeout.enabled                           | 是否启用超时时间                                             |
| execution.isolation.thread.interruptOnTimeout       | 执行超时的时候是否中断                                       |
| execution.isolation.thread.interruptOnCancel        | 执行被取消的时候是否中断                                     |
| fallback.isolation.semaphore.maxConcurrentRequests  | 允许回调方法执行的最大并发数                                 |
| fallback.enabled                                    | 服务降级是否启用，是否执行回调函数                           |
| circuitBreaker.enabled                              | 是否启用断路器                                               |
| circuitBreaker.requestVolumeThreshold               | 该属性用来设置在滚动时间窗中，断路器熔断的最小请求数。例如，默认该值为 20 的时候，如果滚动时间窗（默认10秒）内仅收到了19个请求， 即使这19个请求都失败了，断路器也不会打开。 |
| circuitBreaker.errorThresholdPercentage             | 该属性用来设置在滚动时间窗中，表示在滚动时间窗中，在请求数量超过，circuitBreaker.requestVolumeThreshold 的情况下，如果错误请求数的百分比超过50,就把断路器设置为 "打开" 状态，否则就设置为 "关闭" 状态。 |
| circuitBreaker.sleepWindowinMilliseconds            | // 该属性用来设置当断路器打开之后的休眠时间窗。 休眠时间窗结束之后，会将断路器置为 "半开" 状态，尝试熔断的请求命令，如果依然失败就将断路器继续设置为 "打开" 状态，如果成功就设置为 "关闭" 状态。 |
| ircuitBreaker.forceOpen                             | 断路器强制打开                                               |
| circuitBreaker.forceClosed                          | 断路器强制关闭                                               |
| metrics.rollingStats.timeinMilliseconds             | 滚动时间窗设置，该时间用于断路器判断健康度时需要收集信息的持续时间 |
| metrics.rollingStats.numBuckets                     | 该属性用来设置滚动时间窗统计指标信息时划分"桶"的数量，断路器在收集指标信息的时候会根据设置的时间窗长度拆分成多个 "桶" 来累计各度量值，每个"桶"记录了一段时间内的采集指标。比如 10 秒内拆分成 10 个"桶"收集这样，所以 timeinMilliseconds 必须能被 numBuckets 整除。否则会抛异常 |
| metrics.rollingPercentile.enabled                   | 该属性用来设置对命令执行的延迟是否使用百分位数来跟踪和计算。如果设置为 false, 那么所有的概要统计都将返回 -1 |
| metrics.rollingPercentile.timeInMilliseconds        | 该属性用来设置百分位统计的滚动窗口的持续时间，单位为毫秒     |
| metrics.rollingPercentile.numBuckets                | 该属性用来设置百分位统计滚动窗口中使用 “ 桶 ”的数量。        |
| metrics.rollingPercentile.bucketSize                | 该属性用来设置在执行过程中每个 “桶” 中保留的最大执行次数。如果在滚动时间窗内发生超过该设定值的执行次数，就从最初的位置开始重写。例如，将该值设置为100, 滚动窗口为10秒，若在10秒内一个 “桶 ”中发生了500次执行，那么该 “桶” 中只保留 最后的100次执行的统计。另外，增加该值的大小将会增加内存量的消耗，并增加排序百分位数所需的计算时间。 |
| metrics.healthSnapshot.intervalinMilliseconds       | 该属性用来设置采集影响断路器状态的健康快照（请求的成功、 错误百分比）的间隔等待时间。 |
| requestCache.enabled                                | 是否开启请求缓存                                             |
| requestLog.enabled                                  | HystrixCommand的执行和事件是否打印日志到 HystrixRequestLog 中 |
| coreSize                                            | 该参数用来设置执行命令线程池的核心线程数，该值也就是命令执行的最大并发量 |
| maxQueueSize                                        | 该参数用来设置线程池的最大队列大小。当设置为 -1 时，线程池将使用 SynchronousQueue 实现的队列，否则将使用 LinkedBlockingQueue 实现的队列。 |
| queueSizeRejectionThreshold                         | 该参数用来为队列设置拒绝阈值。 通过该参数， 即使队列没有达到最大值也能拒绝请求。该参数主要是对 LinkedBlockingQueue 队列的补充,因为LinkedBlockingQueue队列不能动态修改它的对象大小，而通过该属性就可以调整拒绝请求的队列大小了。 |
|                                                     |                                                              |





### 4、服务限流

在Spring Cloud Alibaba 中讲解



### 5、服务监控hystrixDashboard

#### 5.1 概述

除了隔离依赖服务的调用以外，Hystrix还提供了准实时的调用监控（Hystrix Dashboard），Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。Netflix通过hystrix-metrics-event-stream项目实现了对以上指标的监控。Spring Cloud也提供了Hystrix Dashboard的整合，对监控内容转化成可视化界面。



#### 5.2 搭建监控平台

##### 5.2.1 模块搭建

1.  `cloud-consumer-hystrix-dashboard9001`

2.  POM

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 9001
    ```

4.  主启动

    ```java
    @SpringBootApplication
    // 开启服务监控
    @EnableHystrixDashboard
    public class HystrixDashboardMain9001 {
    
        public static void main(String[] args) {
            SpringApplication.run(HystrixDashboardMain9001.class, args);
        }
    }
    ```

5.  业务类（没有业务）



**测试**

访问`http://localhost:9001/hystrix`，然后不断访问正常和错误的接口就会出现下图的情况

![image-20230215113239905](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215113239905.png)

可以看到这个表盘记录了访问的情况



##### 5.2.2 参数说明

实心圆：共有两种含义。它通过颜色的变化代表了实例的健康程度，它的健康度从绿色<黄色<橙色<红色递减。
该实心圆除了颜色的变化之外，它的大小也会根据实例的请求流量发生变化，流量越大该实心圆就越大。所以通过该实心圆的展示，就可以在大量的实例中快速的发现故障实例和高压力实例

曲线：用来记录2分钟内流量的相对变化，可以通过它来观察到流量的上升和下降趋势。

整图说明：

![image-20230215113802575](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215113802575.png)

![image-20230215113827704](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215113827704.png)

监控多个服务

![image-20230215113922130](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215113922130.png)







# 七、网关

## Zuul（停更维护⚠）和zuul2

zuul的技术上被淘汰了，会因为它是阻塞模型的。

zuul2因为公司内部的矛盾导致迟迟未开发，所以springcloud官方就自己研发了一个自己的网关出来，就是Gateway，它替代了zuul





## Gateway

### 1、概述

#### 1.1 简介

Spring Cloud Gateway是Spring Cloud中的网关。它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。**它是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty**。Spring Cloud Gateway的目标提供统一的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如：反向代理、安全，监控/指标、限流等等。

Gateway的依赖

![image-20230215185024466](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215185024466.png)

网关在微服务中的位置

![image-20230215184933478](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215184933478.png)

![image-20230215185103474](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215185103474.png)



#### 1.2 zuul VS Gateway

**Q：为什么选择Gateway而不是zuul？**

1、Zuul 1.x，是一个基于阻塞 I/ O 的 API Gateway

2、Zuul 1.x 基于Servlet 2. 5使用阻塞架构它不支持任何长连接(如 WebSocket) Zuul 的设计模式和Nginx较像，每次 I/ O 操作都是从工作线程中选择一个执行，请求线程被阻塞到工作线程完成，但是差别是Nginx 用C++ 实现，Zuul 用 Java 实现，而 JVM 本身会有第一次加载较慢的情况，使得Zuul 的性能相对较差。

3、Zuul 2.x理念更先进，想基于Netty非阻塞和支持长连接，但SpringCloud目前还没有整合。 Zuul 2.x的性能较 Zuul 1.x 有较大提升。在性能方面，根据官方提供的基准测试， Spring Cloud Gateway 的 RPS（每秒请求数）是Zuul 的 1. 6 倍。

4、Spring Cloud Gateway 建立 在 Spring Framework 5、 Project Reactor 和 Spring Boot 2 之上， 使用非阻塞 API。

5、Spring Cloud Gateway 还 支持 WebSocket， 并且与Spring紧密集成拥有更好的开发体验



#### 1.3 三大核心概念

Route（路由）：路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

Predicate(断言)：路由的规则，与规则匹配的请求就可以通过

Filter（过滤器）：指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。



#### 1.4 工作流程

![image-20230215200903043](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215200903043.png)

Clients make requests to Spring Cloud Gateway. If the Gateway Handler Mapping determines that a request matches a route, it is sent to the Gateway Web Handler. This handler runs the request through a filter chain that is specific to the request. The reason the filters are divided by the dotted line is that filters can run logic both before and after the proxy request is sent. All “pre” filter logic is executed. Then the proxy request is made. After the proxy request is made, the “post” filter logic is run.

客户端向 Spring Cloud Gateway 发出请求。如果 Gateway Handler Mapping 确定请求与路由匹配，则将其发送到 Gateway Web Handler。此处理程序通过特定于请求的过滤器链运行请求。过滤器被虚线分开的原因是过滤器可以在发送代理请求之前和之后运行逻辑。执行所有“预”过滤器逻辑。然后进行代理请求。发出代理请求后，运行“post”过滤器逻辑。

人话就是断言规则匹配就将请求交给过滤器前置处理器处理，然后再交给处理请求的微服务进行处理，最后返回结果再交给过滤器后置处理器处理。





### 2、入门案例

1.  cloud-gateway-gateway9527

2.  POM

    ```xml
    <dependencies>
        <!--gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 9527
    
    spring:
      application:
        name: cloud-gateway
    
      cloud:
        gateway:
          routes:
            - id: payment_route                       # 路由唯一ID
              uri: http://localhost:8001              # 路由匹配的路径
              uri: lb://CLOUD-PAYMENT-SERVICE         #匹配后提供服务的路由地址
              predicates:                             # 断言 -> 路由匹配规则
                - Path=/payment/getPaymentById/**     # 断言路径为true放行
    
            - id: payment_route2
              uri: http://localhost:8001
              predicates:
                - Path=/payment/lb/**
    
    eureka:
      instance:
        hostname: cloud-gateway-service
      client:
        register-with-eureka: true
        fetch-registry: true
        service-url:
          defaultZone: http://eureka7001.com:7001/eureka
    ```

4.  主启动

    ```java
    @SpringBootApplication
    public class GateWayMain9527 {
    
        public static void main(String[] args) {
            SpringApplication.run(GateWayMain9527.class, args);
        }
    }
    ```

启动测试访问`http://localhost:9527/payment/lb`，显示的是8001端口，目前我们没有设置动态路由，所以它不会有负载均衡的效果



### 3、动态路由

要实现动态路由需要将服务注册到eureka中，修改上面的模块

1.  POM -> 添加eureka starter

2.  YAML -> 开启动态路由功能，修改路由配置

    uri项设置为服务服务名

    ```yaml
    spring:
      cloud:
        gateway:
          discovery:
            locator:
              #开启从注册中心动态创建路由的功能，利用微服务名进行路由
              enabled: true
        
        routes:
          - id: payment_route                       # 路由唯一ID
    #          uri: http://localhost:8001              # 路由匹配的路径
            uri: lb://CLOUD-PAYMENT-SERVICE         #匹配后提供服务的路由地址
            predicates:                             # 断言 -> 路由匹配规则
              - Path=/payment/getPaymentById/**     # 断言路径为true放行
    
    	 - id: payment_route2
    	   uri: lb://CLOUD-PAYMENT-SERVICE         #匹配后提供服务的路由地址
    #          uri: http://localhost:8001
    	   predicates:
    	     - Path=/payment/lb/**
    ```

3.  启动类

    ```java
    @EnableEurekaClient
    ```

访问`http://localhost:9527/payment/lb`是有负载均衡的功能，成功~~~



### 4、Predicate(断言)

#### 4.1 说明

符合路由规则的就放行，不符合就报下面的404错误

![image-20230215165141349](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215165141349.png)

断言的规则在官方文档的第5章

![image-20230215195612847](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215195612847.png)



#### 4.2 案例

这里用After来举列子，其他的规则都是差不多的，满足就放行，不满足就报404

修改yml

```yaml
predicates:
  - Path=/payment/lb/**
  - After=2022-02-15T17:10:03.685+08:00[Asia/Shanghai]  # 断言，在指定时间之后才能访问
  - Between=2022-02-02T17:45:06.206+08:00[Asia/Shanghai],2023-03-25T18:59:06.206+08:00[Asia/Shanghai]	   # 断言，在指定时间范围内才能访问
  - Cookie=username,xiaozhi     # cookie中username=小智才能访问
  - Method=GET  				  # 请求方法是GET
  - Query=name				  # 请求携带name参数才能访问
```

说明：After断言就是请求要在指定时间之后才能访问，不然就报错

获取上述例子的时间

```java
// 获取当前时区的时间
ZonedDateTime now = ZonedDateTime.now();
```

**测试**

因为需要携带cookie，所以不能在浏览器测试，需要使用Postman或者curl工具测试。这里使用curl工具

打开cmd窗口，输入

```sh
curl http://localhost:9527/payment/lb?name=xiaozhi --cookie=xiaozhi
```



#### 4.3 规则列表

| 断言关键字           | 作用                         |
| -------------------- | ---------------------------- |
| After                | 指定时间之后访问             |
| Before               | 指定时间之前访问             |
| Between              | 指定时间范围内访问           |
| Cookie               | 携带符合规则的cookie才能访问 |
| Header               | 携带指定的请求头             |
| Host                 | 指定的主机名                 |
| Method               | 指定请求方法                 |
| Path                 | 匹配路径                     |
| Query                | 指定参数名                   |
| RemoteAddr           | 指定IP地址                   |
| Weight               | 权重                         |
| XForwardedRemoteAddr | 指定的代理服务器             |

注意：value都是正则表达式





### 5、Filter

![image-20230215202115225](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215202115225.png)

路由过滤器允许以某种方式修改传入的 HTTP 请求或传出的 HTTP 响应。路由过滤器的范围是特定路由。 Spring Cloud Gateway 包括许多内置的 GatewayFilter 工厂。

在官网的67章有说明，目前全局的有10个，网关过滤器的有37个，加起来47个

![image-20230215201428752](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215201428752.png)



#### 5.1 案例说明

修改之前的模块，添加Filters配置项

YAML

```yaml
spring:
  cloud:
    gateway:
      discovery:
        locator:
          #开启从注册中心动态创建路由的功能，利用微服务名进行路由
          enabled: true
      routes:
        - id: payment_route                       # 路由唯一ID
#          uri: http://localhost:8001              # 路由匹配的路径
          uri: lb://CLOUD-PAYMENT-SERVICE         #匹配后提供服务的路由地址
          predicates:                             # 断言 -> 路由匹配规则
            - Path=/payment/getPaymentById/**     # 断言路径为true放行

        - id: payment_route2
          uri: lb://CLOUD-PAYMENT-SERVICE         #匹配后提供服务的路由地址
#          uri: http://localhost:8001
          predicates:
            - Path=/payment/lb/**
            - After=2022-02-15T17:10:03.685+08:00[Asia/Shanghai]  # 断言，在指定时间之后才能访问
#            # 断言，在指定时间范围内才能访问
#            - Between=2022-02-02T17:45:06.206+08:00[Asia/Shanghai],2023-03-25T18:59:06.206+08:00[Asia/Shanghai]
#            - Cookie=username,xiaozhi     # cookie中username=小智才能访问
#            - Method=GET  # 请求方法是GET
#            - Query=name
#            - Host=localhost
          filters:
            - AddRequestHeader=X-Request-Id,xiaozhi666  # 过滤器会在匹配的请求头上加一对请求头，名为X-Request-Id，值为444
```

说明：`AddRequestHeader`过滤器会在匹配请求的请求头加上`X-Request-Id: xiaozhi666`

```java
@GetMapping("lb")
public String lb(HttpServletRequest request) {
    String header = request.getHeader("X-Request-Id");
    if (header == null) {
        header = "";
    }
    return serverPort.toString() + "---" + header;
}
```

提供者中获取这个请求头

![image-20230215203915360](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215203915360.png)

访问8001端口的服务出现请求头的内容，说明成功~~~

其他的Filter也是类似的功能，可以按照官方文档来学习。



#### 5.2 自定义Global Filter

案例：获取url参数，如果为空则不放行

方式一：实现GlobalFilter并注入容器中

```java
@Slf4j
@Component
public class CustomGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange,
                             GatewayFilterChain chain) {
        log.info("time：" + new Date() + "\t执行了自定义全局过滤器");
        String username = exchange.getRequest().getQueryParams().getFirst("username");
        if (username == null) {
            log.info("用户名不正确，无法登陆");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
```

方式二：使用@bean注解的方式注入容器

```java
@Order(0)
@Bean
public GlobalFilter myCustomGlobalFilter() {
    return (exchange, chain) -> {
        log.info("time：" + new Date() + "\t执行了自定义全局过滤器");
        String username = exchange.getRequest().getQueryParams().getFirst("username");
        if (username == null) {
            log.info("用户名不正确，无法登陆");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    };
}
```

和方式一是一样的功能效果。



**测试**

不带有参数访问

![image-20230215204603567](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215204603567.png)

可以看到使我们设置的错误状态码



带参数访问

![image-20230215204643821](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230215204643821.png)

很nice，测试通过了~~~😋







# 八、分布式配置中心

## Spring Cloud Config

### 1、概述

#### 1.1 分布式系统遇到的问题

分布式系统又一个个的微服务组成。修改一个配置就需要多台机器进行修改，然后还得重启，而且还有大量的配置是重复的，导致工作量巨大，所以非常需要一个集中式管理配置的服务。SpringCloud中由spring cloud config来做这个事。通过配置中心去动态修改我们的配置，不需要重启就可以修改我们的配置。大大缩减了任务量



#### 1.2 是什么

官网：`https://spring.io/projects/spring-cloud-config`

SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。

![image-20230216144050413](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216144050413.png)

上图是它的工作原理图。首先需要启动配置中心服务(`Config Server`)，服务的每一次请求就会获取一下Git上的配置文件，然后各个微服务从配置中心服务获取新的配置。所以我们只需要将需要修改的配置在Git仓库中进行修改即可



### 2、使用

SpringCloud Config分为**服务端和客户端**两部分。

服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口

客户端则是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息配置服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理，并且可以通过git客户端工具来方便的管理和访问配置内容。



#### 2.1 服务端搭建

**Git仓库设置**

1.  创建名为`spring-cloud-config`的仓库

2.  添加三个文件 yaml文件

    ![image-20230216145126104](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216145126104.png)

    ```yaml
    config:
      info: dev, version=1
    ```

    不同文件内容不同。



**工程搭建**

1.  创建`cloud-config-center-3344`模块

2.  POM

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 3344
    
    spring:
      application:
        name:  cloud-config-center #注册进Eureka服务器的微服务名
      cloud:
        config:
          server:
            git:
              uri: https://github.com/mazhi-oss/spring-cloud-config.git #GitHub上面的git仓库名字
              ####搜索目录
              search-paths:
                - spring-cloud-config
            # 由于github 2020-10-1 将主分支修改为main,所以要修改默认的label
            defaultLabel: main
          ####读取分支
          label: main
    
    
    
    #服务注册到eureka地址
    eureka:
      client:
        service-url:
          defaultZone: http://localhost:7001/eureka
    ```

4.  主启动

    ```java
    @SpringBootApplication
    @EnableConfigServer		// 开启服务
    public class ConfigCenterMain3344
    {
        public static void main(String[] args) {
                SpringApplication.run(ConfigCenterMain3344.class, args);
        }
    }
    ```



**测试**

启动`eureka7001`，启动配置中心服务器

访问`http://localhost:3344/main/config-dev.yml`

![image-20230216145506422](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216145506422.png)





#### 2.2 文件访问规则

访问规则如下图，没有标明`label`的访问的是默认的分支，可以在server端进行设置

![image-20230216145557360](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216145557360.png)

文件名说明：

-   application：应用名
-   label：分支名
-   profile：环境名

例如：`http://localhost:3344/main/config-dev.yml`，`main`就是分支名,`config`就是应用名，`dev`就是环境名

**不同访问格式会返回不同的内容**

比如`/{application}/{profile}/[/{label}]`这种格式

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216150345931.png" alt="image-20230216150345931" style="zoom:67%;" />



#### 2.3 客户端搭建

客户端就是我们各个微服务，通过`Config Server`获取修改的配置



**搭建工程**

1.  创建`cloud-config-client-3355`模块

2.  POM

    ```XML
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

    ==注意：Client端的POM和Server端的是不一样的，server端的名字为`spring-cloud-config-server`，Client端为`spring-cloud-starter-config`，要注意这两个的区别==

3.  YAML -> `bootstrap.yml`

    ```yaml
    server:
      port: 3355
    
    spring:
      application:
        name: config-client
      cloud:
        #Config客户端配置
        config:
          label: main  #分支名称
          name: config #配置文件名称
          #读取后缀名称   上述3个综合：main分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
          profile: dev
          uri: http://localhost:3344 #配置中心地址
    
    #服务注册到eureka地址
    eureka:
      client:
        service-url:
          defaultZone: http://localhost:7001/eureka
    ```

4.  主启动

    ```java
    @EnableEurekaClient
    @SpringBootApplication
    public class ConfigClientMain3355
    {
        public static void main(String[] args)
        {
            SpringApplication.run(ConfigClientMain3355.class,args);
        }
    }
    ```



**测试**

启动访问`http://localhost:3355/configInfo`

![image-20230216151104128](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216151104128.png)

获取到了配置，nice~~~😋😋😋





#### 2.4 bootstrap.yml文件说明

我们经常使用的`application.yml`，为什么刚才使用了`bootstrap.yml`，他们两个区别在哪？

-   applicaiton.yml是**用户级**的资源配置项
-   bootstrap.yml 是**系统级**的，优先级更加高

Spring Cloud会创建一个“Bootstrap Context”，作为Spring应用的`Application Context`的父上下文。初始化的时候，`Bootstrap Context`负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的`Environment`。

`Bootstrap`属性有高优先级，默认情况下，它们不会被本地配置覆盖。 `Bootstrap context`和`Application Context`有着不同的约定，所以新增了一个`bootstrap.yml`文件，保证`Bootstrap Context`和`Application Context`配置的分离。

要将Client模块下的`application.yml`文件改为`bootstrap.yml`,这是很关键的，因为`bootstrap.yml`是比`application.yml`先加载的。`bootstrap.yml`优先级高于`application.yml`。

==配置变更会覆盖`application`中的配置，所以一般不变更的配置放到`bootstrap.yml`中==





### 3、配置动态刷新

#### 3.1 修改Git仓库上的配置文件

设置版本为2

![image-20230216153110129](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216153110129.png)

接下来访问`Config Server`看是否已经更新了。

![image-20230216153209280](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216153209280.png)

可以看到服务器端已经更新了，我们再看一下客户端

![image-20230216153241470](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216153241470.png)

并没有刷新，因为我们还没有配置动态刷新~~~



#### 3.2 动态刷新配置

1.  首先需要引入`actuator`依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ```

2.  修改客户端YAML配置 -> `bootstrap.yml`

    ```yaml
    # 暴露监控端点
    management:
      endpoints:
        web:
          exposure:
          	# 这里为了方便暴露全部端点
            include: "*"
    ```

3.  controller上标注`@RefreshScope`注解

4.  重新启动服务

启动服务之后访问是可以获取到最新的配置的，但是我们要的就是不重启也可以获取，所以这里还是需要修改一下配置

修改version为3

![image-20230216153814993](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216153814993.png)

再次请求发现`Config Server`更新了，但是`Client端`还是没有更新，还需要我们手动发请求刷新一下

打开cmd窗口，使用`curl`工具发送`Post`请求`http://localhost:3355/actuator/refresh`

```sh
# 一定要加 -X
curl -X POST "http://localhost:3355/actuator/refresh"
# 修改的配置信息
["config.client.version","config.info"]
```

![image-20230216154318465](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216154318465.png)

发送完成后再次访问

![image-20230216154338147](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216154338147.png)

漂亮，你做到了，你真的做到了，它改变了，Oh~~~~😂



#### 3.3 问题

Q：对于上面的动态刷新配置我们已经实现了，但是发现一个问题，难道每次配置更改就要手动刷新一次吗？这样此不是每一个修改配置的微服务都要刷新一次，微服务多的话任务量还是有点大。能不能广播呢，一次通知，到处生效。

A：是的，spring cloud考虑到了这个问题，接下来我们就学习消息总线，它可以帮我们解决这个问题





# 九、消息总线

## SpringCloud Bus

### 1.概述

我们使用`SpringCloud Config`来管理配置，但是配置修改需要我们手动去刷新，如果服务太多的话，重复的工作就会变多，`SpringCloud BUS`就是来解决这个问题的，一次刷新，广播通知，处处生效。`Spring Cloud Config` + `SpringCloud BUS` 实现真正的动态刷新。只需要刷新一次，就能通过总线推送给各个服务。

Spring Cloud Bus是用来将分布式系统的节点与轻量级消息系统链接起来的框架，它整合了Java的事件处理机制和消息中间件的功能。**Spring Clud Bus目前支持RabbitMQ和Kafka。**

Spring Cloud Bus能管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，也可以当作微服务间的通信通道。

![image-20230216170503829](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230216170503829.png)

**什么是总线**
在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息。

**基本原理**
ConfigClient实例都订阅MQ中同一个topic(默认是springCloudBus)。当一个服务刷新数据的时候，它会把这个信息放入到Topic中，这样其它监听同一Topic的服务就能得到通知，然后去更新自身的配置。



**两种传播方式**：

方式一：利用配置中心触发一个客户端，再由客户端触发全部的客户端。

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219112506319.png" alt="image-20230219112506319" style="zoom: 67%;" />

方式二：利用消息总线触发一个服务端ConfigServer的/bus/refresh端点，而刷新所有客户端的配置（推荐使用这种方式）

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219112635696.png" alt="image-20230219112635696" style="zoom:67%;" />





### 2.安装RabbitMQ(省略)





### 3.动态刷新全局广播

在`SpringCloud Config`章节我们搭建了3355工程，现在我们再搭建一个3366工程，配置都是一样的，端口号不一致，业务上多一个端口号

```java
@RestController
@RefreshScope
public class ConfigClientController {
    @Value("${server.port}")
    private String serverPort;

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String configInfo()
    {
        return "serverPort: "+serverPort+"\t\n\n configInfo: "+configInfo;
    }

}
```

接着添加BUS的POM，两个都要添加

```xml
<!--bus整合RabbitMQ的POM-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

bootstrap.yml添加配置项

```yaml
spring:
  rabbitmq:
    host: 192.168.6.156
    port: 5672
    username: guest
    password: guest
```

**配置中心工程修改**

-   添加POM

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-bus-amqp</artifactId>
    </dependency>
    ```

-   修改yml

    ```yaml
      # rabbitmq配置
    spring:
      rabbitmq:
        host: 192.168.6.156
        port: 5672
        username: guest
        password: guest
        
    ##rabbitmq相关配置,暴露bus刷新配置的端点
    management:
      endpoints: #暴露bus刷新配置的端点
        web:
          exposure:
            include: 'bus-refresh'
    ```

    这个官网上可以找到暴露的端点

    ![image-20230219113404288](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219113404288.png)

    上图是4.0.1版本的，不是当前使用的这个版本！！！



**测试**

启动Eureka7001和3344、3355、3366这三台

在`RabbitMQ`控制台中可以看到`SpringCloudBus`的信息

![image-20230220202302139](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230220202302139.png)

修改github上的配置文件

![image-20230219113610248](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219113610248.png)

接着发送刷新请求

```sh
curl -X POST "http://localhost:3344/actuator/bus-refresh"
```

观察结果

![image-20230219113742325](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219113742325.png)

成功~~~😋😋😋





### 4.动态刷新定点通知

场景：某几台配置修改，其他的不修改

案例：通知3355，3366不修改

**公式**：`http://localhost:配置中心的端口号/actuator/bus-refresh/注册中心服务名:端口号`

例子：通知`config-client`服务的`3355`端口的服务修改

`localhost:3344/actuator/bus-refresh/config-client:3355`



**实现**

修改github的配置文件，在发送例子中的命令，查看变化

![image-20230219134237524](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219134237524.png)

![image-20230219132548049](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219132548049.png)

![image-20230219134151725](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230219134151725.png)







# 十、消息驱动

在我们的系统中，不仅有我们的中台，也就是Java Web程序，同时还有大数据平台，那么它会出现一种情况，就是我们Java程序用的消息队列是RabbitMQ，大数据平台用的是Kafka，**那么这个时候我们需要在这两种消息队列中来回切换**。这是相当费劲的，而且它的学习成本也提高了，虽然都是消息队列，但是在很多方面还是有很多的不同，学习起来需要花费许多的时间，那有没有一个框架或者软件来集成两个消息队列，让我们不再专注底层的细节，而是专注于我们的业务呢？是的，它就是我们的主角，消息驱动。



## SpringCloud Stream

### 1、概述

#### 1.1 是什么？

官方定义 Spring Cloud Stream 是一个构建消息驱动微服务的框架。

应用程序通过 inputs 或者 outputs 来与 Spring Cloud Stream中binder对象交互。通过我们配置来binding(绑定) ，而 Spring Cloud Stream 的 **binder对象负责与消息中间件交互**。所以，我们只需要搞清楚如何与 Spring Cloud Stream 交互就可以方便使用消息驱动的方式。

通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动。Spring Cloud Stream 为一些供应商的消息中间件产品提供了个性化的自动化配置实现，引用了**发布-订阅、消费组、分区**的三个核心概念。

==**目前仅支持RabbitMQ、Kafka。**==

![image-20230220152928327](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230220152928327.png)

**中文指导手册**：`https://m.wang1314.com/doc/webapp/topic/20971999.html`



#### 1.2 设计思想

![image-20230220202922897](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230220202922897.png)

标准消息队列的三个步骤：

-   消息：生产者/消费者之间通过消息媒介传递信息
-   通道：消息必须要走的特殊通道（`MessageChannel`）
-   处理：由`MessageHandler`消息处理器向订阅的客户端推送消息

使用`SpringCloud Stream`可以使我们屏蔽底层消息队列的操作细节。通过绑定器（`Binder`）作为中间层，完美的实现了应用程序和消息中间件细节之间的隔离，通过向应用程序暴露统一的Channel通道，使得应用程序不需要再考虑各种不同的消息中间件实现。Stream中的消息通信方式遵循了发布-订阅模式。



#### 1.3 核心组件和注解

-   Binder（绑定器）：Binder可以生成Binding，Binding用来绑定消息容器的生产者和消费者，它有两种类型，INPUT和OUTPUT，INPUT对应于消费者，OUTPUT对应于生产者
-   Channel（通道）：是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置
-   Source和Sink：简单的可理解为参照对象是Spring Cloud Stream自身，从Stream发布消息就是输出，接受消息就是输入。Suorce就是输出，Sink就是输入。

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230220204132411.png" alt="image-20230220204132411" style="zoom:67%;" />

<img src="https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230220204136473.png" alt="image-20230220204136473"  />





### 2、使用

#### 2.1 消息生产者搭建

1.  创建`cloud-stream-rabbitmq-provider8801`

2.  POM

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 8801
    
    spring:
      application:
        name: cloud-stream-provider   # 应用名
    
      rabbitmq:
        host: 192.168.6.156
        port: 5672
        username: guest
        password: guest
    
      cloud:
        ####################### stream配置 ########################
        stream:
          binders:  # 在此处配置要绑定的rabbitmq的服务信息
            defaultRabbit: # 表示定义的名称，用于于binding整合
              type: rabbit # 消息组件类型
    #          environment: # 设置rabbitmq的相关的环境配置
    
          bindings: # 服务的整合处理
            output:  # 这个名字是一个通道的名称
              destination: studyExchange      # 表示要使用的Exchange名称定义
              content-type: application/json  # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
              binder: defaultRabbit           # 设置要绑定的消息服务的具体设置
    
      ####################### rabbitmq配置 ########################
    
    
    ####################### eureka配置 ########################
    eureka:
      client:
        service-url:
          defaultZone: http://localhost:7001/eureka
      instance:
        instance-id: send-8801.com    # 在信息列表时显示主机名称
        prefer-ip-address: true       # 访问的路径变为IP地址
    ```

4.  启动类

    ```java
    @SpringBootApplication
    public class StreamMQMain8801
    {
        public static void main(String[] args) {
            SpringApplication.run(StreamMQMain8801.class,args);
        }
    }
    ```

5.  业务类

    ![image-20230221085217729](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221085217729.png)

    controller

    ```java
    @RestController
    public class MessageController {
    
        @Resource
        private IMessageProvider iMessageProvider;
    
        @GetMapping("/sendMessage")
        public String send() {
            return iMessageProvider.send();
        }
    }
    ```

    service

    ```java
    @Slf4j
    @EnableBinding(Source.class)
    public class MessageProviderImpl implements IMessageProvider {
    
        @Resource
        private MessageChannel output;
    
        @Override
        public String send() {
            String serial = UUID.randomUUID().toString();
            output.send(MessageBuilder.withPayload(serial).build());
            log.info("serial：{}", serial);
            return null;
        }
    }
    ```

测试，访问定义好的接口

![image-20230221085329782](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221085329782.png)

有日志输出，成功~~~😋😋😋



#### 2.2 消息消费者搭建

需要搭建两个同样的消费者，端口号不同

1.  创建`cloud-stream-rabbitmq-consumer8803`和`cloud-stream-rabbitmq-consumer8802`模块

2.  POM

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    ```

3.  YAML

    ```yaml
    server:
      port: 8803 # 8802
    
    spring:
      application:
        name: cloud-stream-consumer
    
      cloud:
        stream:
          binders: # 在此处配置要绑定的rabbitmq的服务信息；
            defaultRabbit: # 表示定义的名称，用于于binding整合
              type: rabbit # 消息组件类型
    #          environment: # 设置rabbitmq的相关的环境配置
    
          bindings: # 服务的整合处理
            input:  # 这个名字是一个通道的名称
              destination: studyExchange      # 表示要使用的Exchange名称定义
              content-type: application/json  # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
              binder: defaultRabbit           # 设置要绑定的消息服务的具体设置
    
      rabbitmq:
        host: 192.168.6.156
        port: 5672
        username: guest
        password: guest
    
    eureka:
      client: # 客户端进行Eureka注册的配置
        service-url:
          defaultZone: http://localhost:7001/eureka
      instance:
        lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
        lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
        instance-id: receive-8802.com  # 在信息列表时显示主机名称
        prefer-ip-address: true     # 访问的路径变为IP地址
    ```

4.  主启动

    ```java
    @SpringBootApplication
    public class StreamMQMain8803 {
        public static void main(String[] args)
        {
            SpringApplication.run(StreamMQMain8803.class,args);
        }
    }
    ```

5.  业务类

    service

    ```java
    @Slf4j
    @Component
    @EnableBinding(Sink.class)
    public class ReceiveMessageListener
    {
        @Value("${server.port}")
        private String serverPort;
    
        @StreamListener(Sink.INPUT)
        public void input(Message<String> message) {
            log.info("消费者------->接收到的消息：{}\t{}", message.getPayload(), serverPort);
        }
    }
    ```



***测试***

多次访问8801的发送消息接口，观察8802和8803的日志输出

![image-20230221084855659](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221084855659-16769405367371.png)

可以看到峰值的变化，当我们不点的时候它就归0了

8803日志输出

![image-20230221085747897](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221085747897.png)

8802日志输出

![image-20230221085825854](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221085825854.png)





### 3、分组消费和持久化

观察上面的案例发现，一个消息被两个服务消费了，这种被多个消费的叫重复消费。重复消费会导致数据的不一致性，所以我们要对消息进行分组，**不同组的服务可以多个消费，同个组的服务只能有一个消费**。



#### 3.1 分组

修改上面的案例，使他们之间只有一个服务能消费，所以我们需要将他们分在同一个组

自动分配的分组名

![image-20230221091256573](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221091256573.png)

可以看到两个是不同的分组，所以两个服务都消费了消息，接下来我们要给两个服务分到一个分组中。

**修改两个服务的`YAML`文件**

![image-20230221091515273](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221091515273.png)

现在他们都在一个组中

![image-20230221091657017](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221091657017.png)



**测试**

生产者发送4条消息。观察两个消费者的日志

-   8803

    ![image-20230221091827898](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221091827898.png)

-   8802

    ![image-20230221091846946](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221091846946.png)

可以看到很每个服务都消费了两条，没有被重复消费。



#### 3.2 持久化

服务宕机后，再次启动，如果没有设置分组，消息就会发生丢失的情况，这就是持久化问题。

**场景复现**

-   将两台消费者关掉。一台设置分组，一台不设置。

    8802的去掉

    ![image-20230221092224227](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221092224227.png)

-   生产者8801发送多条消息

-   启动两台消费者，观察日志是否有输出

    8803消费了消息

    ![image-20230221092500164](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221092500164.png)

    8802没有消费。



**解决问题**

给它设置一个分组。我们将两个服务分到不同的分组，因为分在一个分组先启动的就会将消息全部消费，后启动的就不用消费了，因为是同一个组。

![image-20230221100808511](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221100808511.png)



**测试**

先停掉两台服务。使用8801生产者生产消息，再次启动两台服务

观察发现两台服务器都有消息消费的日志，nice~~~😈😋😋







# 十一、分布式链路追踪

在微服务架构中，一次请求可能需要多个服务节点调用来协同产出结果，每一段请求都会形成一个复杂的分布式调用链路，链路中的任何一个服务出现高延时或错误都会引起整个请求的失败。

![image-20230221153148441](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221153148441.png)

分布式链路追踪可以帮助我们追踪这一段调用链路的服务，当出现问题时，可以很直观的看到出错的服务。



## Spring Cloud Sleuth

### 1、概述

Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且兼容支持了zipkin

![image-20230221155422621](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221155422621.png)

上图是完整的链路调用

![image-20230221155539663](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221155539663.png)

![image-20230221155757474](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221155757474.png)

一条链路通过Trace Id唯一标识，Span标识发起的请求信息，各span通过`parent id `关联起来，`parent id`可以理解为是调用它的服务

Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识

span:表示调用链路来源，通俗的理解span就是一次请求信息





**SpringCloud从F版起已不需要自己构建Zipkin Server了，只需调用jar包即可**

**启动命令**

```shell
java -jar jar包名
```

然后访问`http://localhost:9411`

![image-20230221155325873](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221155325873.png)

成功~~~





### 2、使用

添加它的POM和修改YAML。观察web 控制台即可。

1.  修改`cloud-consumer-order8002`和`cloud-provider-payment8001`工程

2.  POM

    ```xml
    <!--包含了sleuth+zipkin-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
    ```

3.  YAML

    ```yaml
    spring:
      zipkin:
        base-url: http://localhost:9411
        sleuth:
          sampler:
            #采样率值介于 0 到 1 之间，1 则表示全部采集
            probability: 1
    ```

4.  业务类

    -   8001-paymnt

        ```java
        @GetMapping("/zipkin")
        public String paymentZipkin() {
            return "hi ,I am xiaozhi";
        }
        ```

    -   8002-order

        ```java
        @GetMapping("/payment/zipkin")
        public String paymentZipkin() {
            String result = restTemplate.getForObject("http://localhost:8001"+"/payment/zipkin/", String.class);
            return result;
        }
        ```



**测试**

访问几次`http://localhost:8002/order/payment/zipkin`，观察web控制台变化

![image-20230221160533972](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221160533972.png)

3个spans，还有一个是`zipkin`本身，点击对应的栏目可以看到详细的信息

![image-20230221160625533](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221160625533.png)

**查看依赖**

![image-20230221160652692](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221160652692.png)



















