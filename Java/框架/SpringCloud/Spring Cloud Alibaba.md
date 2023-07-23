# 一、概述

## 1.1 Spring Cloud Alibaba为什么出现

由于`Spring Cloud Netflix`项目进入维护模式，`Spring Cloud`官方和`Alibaba`合作搞出一套来替换`Netflix`的各个技术

![image-20230221210223336](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221210223336.png)

进入维护模式意味着Spring Cloud Netflix 将不再开发新的组件，我们都知道Spring Cloud 版本迭代算是比较快的，因而出现了很多重大ISSUE都还来不及Fix就又推另一个Release了。进入维护模式意思就是目前一直以后一段时间Spring Cloud Netflix提供的服务和功能就这么多了，不在开发新的组件和功能了。以后将以维护和Merge分支Full Request为主，所以新组件功能将以其他替代平代替的方式实现。





## 1.2 是什么

官网：`https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md`

诞生：2018.10.31，Spring Cloud Alibaba 正式入驻了 Spring Cloud 官方孵化器，并在 Maven 中央库发布了第一个版本。

![image-20230221210453424](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230221210453424.png)

### 1.2.1 主要功能*

-   **服务限流降级**：默认支持 WebServlet、WebFlux、OpenFeign、RestTemplate、Spring Cloud Gateway、Zuul、Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
-   **服务注册与发现**：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
-   **分布式配置管理**：支持分布式系统中的外部化配置，配置更改时自动刷新。
-   **消息驱动能力**：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
-   **分布式事务**：使用 @GlobalTransactional 注解， 高效并且对业务零侵入地解决分布式事务问题。
-   **阿里云对象存储**：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
-   **分布式任务调度**：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。
-   **阿里云短信服务**：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。



### 1.2.2 组件

**[Sentinel](https://github.com/alibaba/Sentinel)**：把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

**[Nacos](https://github.com/alibaba/Nacos)**：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

**[RocketMQ](https://rocketmq.apache.org/)**：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。

**[Seata](https://github.com/seata/seata)**：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。

**[Alibaba Cloud OSS](https://www.aliyun.com/product/oss)**: 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。

**[Alibaba Cloud SchedulerX](https://cn.aliyun.com/aliware/schedulerx)**: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。

**[Alibaba Cloud SMS](https://www.aliyun.com/product/sms)**: 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。



### 1.2.3 文档

-   官网：https://spring.io/projects/spring-cloud-alibaba#overview
-   英文：
    -   https://github.com/alibaba/spring-cloud-alibaba
    -   https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html
-   中文：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md







# 二、Nacos

## 2.1 概述和安装

### 2.1.1 概述

**Nacos**: `Dynamic Naming and Configuration Service`

Nacos就是注册中心 + 配置中心的组合，等价于`Eureka+Config +Bus`，它替换Eureka做为注册中心，替换Config做配置中心，反正就是牛逼~~~。

**Nacos官网**

-   Github官网：`https://github.com/alibaba/Nacos`
-   Nacos官网：`https://nacos.io/zh-cn/index.html`
-   Spring Cloud官网：`https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_nacos_discovery`



**Nacos的全景图**

![nacos_landscape.png](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/1533045871534-e64b8031-008c-4dfc-b6e8-12a597a003fb.png)

非常有野心~~~



### 2.1.2 安装

官网下载即可，这里给出下载地址：`https://github.com/alibaba/nacos/releases`

下载完解压，打开cmd，在bin目录输出`startup.cmd`启动

启动完成后访问`http://localhost:8848/nacos`，默认账号和密码是`nacos`

![image-20230222162250461](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222162250461.png)

成功~~~😈😈😈





## 2.2 服务注册中心

### 2.2.1 服务提供者

创建一个9001工程和**9011工程的端口映射工程**

**9001工程**

1.  创建`cloud-alibaba-provider-payment9001`

2.  POM

    ```xml
    <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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

3.  YAML

    配置Nacos地址，暴露端口

    ```yaml
    server:
      port: 9001
    
    spring:
      application:
        name: nacos-payment-provider
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848 #配置Nacos地址
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```

4.  主启动

    标注`@EnableDiscoveryClient`开启服务发现

    ```java
    @EnableDiscoveryClient
    @SpringBootApplication
    public class PaymentMain9001 {
    
        public static void main(String[] args) {
            SpringApplication.run(PaymentMain9001.class, args);
        }
    }
    ```

5.  业务类

    ```java
    @RestController
    public class PaymentController {
        @Value("${server.port}")
        private String serverPort;
    
        @GetMapping(value = "/payment/nacos/{id}")
        public String getPayment(@PathVariable("id") Integer id) {
            return "nacos registry, serverPort: "+ serverPort+"\t id"+id;
        }
    }
    ```

**创建端口映射工程**

![image-20230222164755995](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222164755995.png)

![image-20230222165034779](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222165034779.png)



**测试**

启动9001和9011工程，观察Nacos控制台的服务列表

![image-20230222165254764](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222165254764.png)

成功~~~😈😈😈



### 2.2.2 服务消费者

#### 消费者工程

端口83

1.  创建`cloud-alibaba-consumer-nacos-order83`模块

2.  POM

    ```xml
    <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
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

3.  YAML

    配置Nacos地址

    ```yaml
    server:
      port: 83
    spring:
      application:
        name: nacos-order-consumer
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
    
    # 消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)（自定义）
    service-url:
      nacos-user-service: http://nacos-payment-provider
    ```

4.  主启动

    ```java
    @EnableDiscoveryClient	// 开启服务发现
    @SpringBootApplication
    public class OrderNacosMain83 {
        public static void main(String[] args) {
            SpringApplication.run(OrderNacosMain83.class,args);
        }
    } 
    ```

5.  业务类

    -   config

        ```java
        @Configuration
        public class ApplicationContextBean {
            
            @Bean
            @LoadBalanced   // 开启负载均衡
            public RestTemplate getRestTemplate() {
                return new RestTemplate();
            }
        }
        ```

    -   controller

        ```java
        @RestController
        public class OrderNacosController {
            @Resource
            private RestTemplate restTemplate;
        
            @Value("${service-url.nacos-user-service}")
            private String serverURL;
        
            @GetMapping("/consumer/payment/nacos/{id}")
            public String paymentInfo(@PathVariable("id") Long id) {
                return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
            }
        }
        ```

**启动测试**

![image-20230222170017238](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222170017238.png)

web控制台查看没问题

访问`http://localhost:83/consumer/payment/nacos/1`

![image-20230222170124564](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222170124564.png)

![image-20230222170845225](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222170845225.png)

nice~~~





#### 负载均衡

Nacos自带负载均衡

![image-20230222171031349](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222171031349.png)

可以看到它的依赖中集成了`ribbon`

替换负载规则，将默认的替换掉

```java
@Bean
public IRule myRule() {
    // 默认规则
    return new RandomRule();
}
```





### 2.2.3 各大注册中心的比较



![image-20230222161651663](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230222161651663.png)

C是所有节点在同一时间看到的数据是一致的；而A的定义是所有的请求都会收到响应。

何时选择使用何种模式？
一般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如 Spring cloud 和 Dubbo 服务，都适用于AP模式，AP模式为了服务的可用性而减弱了一致性，因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或者存储配置信息，那么 CP 是必须，K8S服务和DNS服务则适用于CP模式。
CP模式下则支持注册持久化实例，此时则是以 Raft 协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

==**Nacos支持AP和CP模式的转换**==

```sh
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
```





## 2.3 服务配置中心

### 2.3.1 工程搭建

1.  `cloudalibaba-config-nacos-client3377`

2.  POM

    ```xml
    <dependencies>
        <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--web + actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础配置-->
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

    bootstrap.yml

    ```yaml
    server:
      port: 3377
    
    spring:
      application:
        name: nacos-config-client
    
      cloud:
        nacos:
          discovery:
            server-addr: 127.0.0.1:8848
          config:
            server-addr: 127.0.0.1:8848
            file-extension: yaml #指定yaml格式的配置
    ```

    application.yml

    ```yaml
    spring:
      profiles:
        active: dev # 表示开发环境
    ```

4.  主启动

    ```java
    @EnableDiscoveryClient
    @SpringBootApplication
    public class NacosConfigClientMain3377 {
    
        public static void main(String[] args) {
                SpringApplication.run(NacosConfigClientMain3377.class, args);
        }
    }
    ```

5.  业务类

    ```java
    @RestController
    @RefreshScope // 在控制器类加入@RefreshScope注解使当前类下的配置支持Nacos的动态刷新功能。
    public class ConfigClientController {
        @Value("${config.info}")
        private String configInfo;
    
        @GetMapping("/config/info")
        public String getConfigInfo() {
            return configInfo;
        }
    }
    ```

启动查看nacos服务列表，有表示成功~~~



### 2.3.2 配置文件匹配规则

![image-20230223160434807](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223160434807.png)

所以当前模块的dataId为：

-   开发环境配置文件：`nacos-config-client-dev.yaml`
-   测试环境配置文件：`nacos-config-client-test.yaml`
-   ......

==它的匹配规则不止只有dataId一种，下一章会讲解另外两种方式==



**测试**

添加配置文件，然后访问`http://localhost:3377/config/info`

![image-20230223160921130](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223160921130.png)

点击加号添加

![image-20230223161055146](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223161055146.png)

在管理界面可以看到我们添加的配置文件

![image-20230223161123107](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223161123107.png)

![image-20230223161215723](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223161215723.png)

成功获取最新配置，成功~~~😈😈😈

==配置会自动刷新，不需要我们发送刷新请求==





### 2.3.3 三种方式加载配置

#### Data Id

![image-20230223160434807](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230223160434807.png)

设置成规定格式即可



####  Group

分组，通过组来划分环境或者服务

1.  创建分组

    ![image-20230226105457582](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105457582.png)

    直接填写组即可

2.  YAML文件中指定对应的组

    ```yaml
    spring:
      cloud:
        nacos:
          config:
            group: DEV_GROUP
    ```



**案例**

创建三个相同名字的文件，但是组不同

![image-20230226105717782](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105717782.png)

指定组

![image-20230226105748966](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105748966.png)

重新启动查看结果

![image-20230226105812244](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105812244.png)

没问题~~~





#### Namespace

命名空间，可以用作服务与服务之间的区分

默认的命名空间是`public`，不可删除

![image-20230226105911258](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105911258.png)

新建命名空间

![image-20230226105941897](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226105941897.png)

![image-20230226110116772](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226110116772.png)

命名空间ID在指定命名空间的时候需要填写

![image-20230226110427164](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226110427164.png)

`bootstrap.yaml`指定命名空间

```yaml
spring:
  cloud:
    nacos:
      config:
      	# 指定命名空间
        namespace: # 命名空间ID
```





**案例**

创建一个以`user-service`命名的命名空间，并指定它

`bootstrap.yaml`中指定命名空间

![image-20230226110602107](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226110602107.png)

启动测试

![image-20230226125039397](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226125039397.png)

成功~~~





## 2.4 集群与持久化

### 2.4.1 官方文档

集群搭建官方文档：`https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html`

官网架构图

![deployDnsVipMode.jpg](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/deployDnsVipMode.jpg)

SLB：负载均衡层，一般用nginx来做负载

真实部署情况

![image-20230226163948896](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226163948896.png)



### 2.4.2 nacos持久化

![image-20230226164457969](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226164457969.png)

默认单击使用的是名为`derby`的嵌入式数据库，生产环境下，我们需要使用MySQL来替换。

在nacos的项目POM中可以看到依赖中有`derby`的依赖

`https://github.com/alibaba/nacos/blob/develop/config/pom.xml`

![image-20230226165012019](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226165012019.png)



**如何替换？**

![image-20230226164844235](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226164844235.png)

1.  初始化数据库	

    `https://github.com/alibaba/nacos/blob/master/distribution/conf/mysql-schema.sql`

2.  修改nacos的配置环境

    ![image-20230226165351401](https://xiaozhi-blog.oss-cn-guangzhou.aliyuncs.com/img/image-20230226165351401.png)

    ```properties
    ### Count of DB:
    # db.num=1
    
    ### Connect URL of DB:
    # db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
    # db.user.0=nacos
    # db.password.0=nacos
    ```



### 2.4.3 生产环境集群搭建

在linux环境下搭建集群

需要启动的服务

-   三台 nacos -> 搭建集群
-   一台 nginx -> 转发
-   一台 mysql -> 持久化

Linux下安装nacos，直接解压，nacos的bin目录下输入一下命令启动或关闭服务

![image-20230312152045657](E:\学习笔记\img\img01\image-20230312152045657.png)



#### ①修改nacos配置

1.  修改`application.properties`文件 -> 配置外部数据源

    ```properties
    ### If user MySQL as datasource:
    spring.datasource.platform=mysql
    ### Count of DB:
    db.num=1
    ### Connect URL of DB:
    # 修改成我们自己的数据库地址和配置
    db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
    db.user=root
    db.password=abc123
    ```

2.  配置集群配置文件

    在nacos的解压目录nacos/的conf目录下，有配置文件cluster.conf，请每行配置成ip:port。（请配置3个或3个以上节点）

    ```sh
    #example
    192.168.10.10:3333
    192.168.10.10:4444
    192.168.10.10:5555
    ```

3.  修改启动脚本，我们要启动三台nacos，所以需要在不同的端口启动，脚本修改成可指定端口号启动

    修改bin目录下的`startup.sh`启动脚本（记得备份，防止坏了又重新安装）

    ![image-20230312231022976](E:\学习笔记\img\img01\image-20230312231022976.png)

    添加虚拟机参数，设置端口号为我们启动时标注的端口号

    ![image-20230312231135790](E:\学习笔记\img\img01\image-20230312231135790.png)

    **注意**😮：要放在第一行的位置，放在最后面会启动失败（测试过~~~）



#### ②创建数据库表

创建数据库`nacos`

sql文件在`https://github.com/alibaba/nacos/blob/master/distribution/conf/mysql-schema.sql`和`/nacos/conf`下都可以获取，获取执行即可。



#### ③修改nginx配置

配置nginx做负载均衡

```sh
#gzip  on;

upstream cluster {
    server 127.0.0.1:3333;
    server 127.0.0.1:4444;
    server 127.0.0.1:5555;
}
	

server {
	listen       1111;
	server_name  localhost;

	#charset koi8-r;

	#access_log  logs/host.access.log  main;
	#

	###############  Nacos集群配置   #########################
	location / {

		proxy_pass http://cluster;
	}
}
```



#### ④测试

**启动nacos集群**

```sh
[root@xiaozhi bin]# ./startup.sh -n 3333	# 这个-n是我们定义的
[root@xiaozhi bin]# ./startup.sh -n 4444
[root@xiaozhi bin]# ./startup.sh -n 5555
```

==**😅注意**：这里有一个问题，我的虚拟机是2G的内存，ncaos的初始内存是2g，所以三台同时启动的话会把内存撑爆，导致服务启动失败，所以我们需要将它的虚拟机参数进行修改==

![image-20230312230915474](E:\学习笔记\img\img01\image-20230312230915474.png)

设置完成后再次启动

访问`主机或IP:1111/nacos`，出现界面说明启动成功

![image-20230312225950897](E:\学习笔记\img\img01\image-20230312225950897.png)

查看三台nacos服务的状态

![image-20230312230055242](E:\学习笔记\img\img01\image-20230312230055242.png)

nice~~~😋😋😋



**注册服务**

随便找一个微服务注册到nacos中，查看控制台，有该服务则我们的集群搭建成功

![image-20230312231554336](E:\学习笔记\img\img01\image-20230312231554336.png)

漂亮O(∩_∩)O哈哈~😙😙😙



# 三、Sentinel

## 3.1 Sentinel介绍

### 3.1.1 它是什么？

随着微服务的流行，服务和服务之间的稳定性变得越来越重要。Sentinel 是面向分布式、多语言异构化服务架构的流量治理组件，主要以流量为切入点，从流量路由、流量控制、流量整形、熔断降级、系统自适应过载保护、热点流量防护等多个维度来帮助开发者保障微服务的稳定性。

它是alibaba微服务体系的`hystrix`，但是它比`Hystrix`更加好用，可以通过web界面来对服务进行配置，以此来达到保护服务的效果。

![image-20230314113750794](E:\学习笔记\img\img01\image-20230314113750794.png)



### 3.1.2 Sentinel 基本概念

**资源**

资源是 Sentinel 的关键概念。它可以是 Java 应用程序中的任何内容，例如，由应用程序提供的服务，或由应用程序调用的其它应用提供的服务，甚至可以是一段代码。在接下来的文档中，我们都会用资源来描述代码块。

只要通过 Sentinel API 定义的代码，就是资源，能够被 Sentinel 保护起来。大部分情况下，可以使用方法签名，URL，甚至服务名称作为资源名来标示资源。

**规则**

围绕资源的实时状态设定的规则，可以包括流量控制规则、熔断降级规则以及系统保护规则。所有规则可以动态实时调整。这些规则可以通过web控制台来进行配置。







## 3.2 Sentinel入门

Sentinel它分前台和后台，前台就是它的控制台，通过控制台可以对服务进行动态配置来实现流量控制、服务降级、熔断降级等保护机制。



### 3.2.1 安装Sentinel控制台

**下载地址**：https://github.com/alibaba/Sentinel/releases

**运行**：java -jar 直接运行

访问 http://localhost:8080 打开控制台

![image-20230314113145802](E:\学习笔记\img\img01\image-20230314113145802.png)



### 3.2.2 测试工程

#### ①搭建

1.  创建`cloudalibaba-sentinel-service8401`模块

2.  POM

    ```xml
    <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel-datasource-nacos 后续做持久化用到-->
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件+actuator -->
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
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>4.6.3</version>
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
      port: 8401
    
    spring:
      application:
        name: cloudalibaba-sentinel-service
    
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            # sentinel web监控台地址
            dashboard: localhost:8080
            # sentinel后台的启动端口，默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
            port: 8719
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```

4.  主启动

    ```java
    @EnableDiscoveryClient
    @SpringBootApplication
    public class MainApp8401 {
        public static void main(String[] args) {
            SpringApplication.run(MainApp8401.class, args);
        }
    }
    ```

5.  业务类

    ```java
    @RestController
    public class FlowLimitController {
    
        @GetMapping("/testA")
        public String testA() {
            return "-------TestA------";
        }
    
        @GetMapping("/testB")
        public String testB() {
            return "-------TestB------";
        }
    }
    ```

    

#### ②测试

启动nacos服务

**注意**：sentinel采用的是懒加载机制，只有服务被请求了才会出现在控制台中，所以我们需要先访问服务，再去查看控制台变化

![image-20230314120121496](E:\学习笔记\img\img01\image-20230314120121496.png)

成功监控到了服务

访问多次`/testA`和`/testB`，可以看到监控发生的变化

![image-20230314120230007](E:\学习笔记\img\img01\image-20230314120230007.png)

成功~~~





## 3.3 流量控制

### 3.3.1 概述

![image-20230315150850016](E:\学习笔记\img\img01\image-20230315150850016.png)

**注意**：线程没有流控效果，只有流控模式

web控制页面

![image-20230315150915997](E:\学习笔记\img\img01\image-20230315150915997.png)



### 3.3.2 流控模式

#### ①直接

流量超过了设定的QPS或者执行的线程数，就会直接报错



#### ②关联

![image-20230315151244904](E:\学习笔记\img\img01\image-20230315151244904.png)

A资源关联B资源，当B资源达到设置的阈值时，A就会被限流，触发流控效果

**实验**：可以使用`JMeter`模拟并发访问`/testB`，再访问`/testA`，发现`/tsetA`报错了（流控效果为快速失败）



#### ③链路 

多个链路调用一个资源，对资源进行保护，对某个或某些链路进行限流，当被访问的次数超过了设定的阈值，就会报错



**代码实现**

-   添加`OrderService`

    ```java
    @Service
    @Slf4j
    public class OrderService {
    	
        @SentinelResource("getOrder")
        public void getOrder() {
            log.info("访问getOrder");
        }
    }
    ```

    **注**：sentinue默认是将controller中的方法识别为资源，除此之外的要想被识别为资源需要添加`@SentinelResource注解来标注`

-   修改controller

    ```java
    @RestController
    @Slf4j
    public class FlowLimitController {
    
        @Resource
        private OrderService orderService;
    
        @GetMapping("/testA")
        public String testA() {
            orderService.getOrder();
            return "-------TestA------";
        }
    
        @GetMapping("/testB")
        public String testB() {
            orderService.getOrder();
            return "-------TestB------";
        }
    }
    ```

-   ==禁止收敛URL入口处的context==

    ```
    从1.6.3 版本开始，Sentinel Web fifilter默认收敛所有URL的入口context，因此链路限流不生效。
    
    1.7.0 版本开始（对应SCA的2.1.1.RELEASE)，官方在CommonFilter 引入了
    
    WEB_CONTEXT_UNIFY 参数，用于控制是否收敛context。将其配置为 false 即可根据不同的URL 进行链路限流。
    
    SCA 2.1.1.RELEASE之后的版本,可以通过配置spring.cloud.sentinel.web-context-unify=false即可关闭收敛我们当前使用的版本是SpringCloud Alibaba 2.1.0.RELEASE，无法实现链路限流。
    
    目前官方还未发布SCA 2.1.2.RELEASE，所以我们只能使用2.1.1.RELEASE，需要写代码的形式实现
    ```

-   自定义 web filter

    ```java
    @Configuration
    class FilterContextConfig {
    
        @Bean
        public FilterRegistrationBean sentinelFilterRegistration() {
            FilterRegistrationBean registration = new FilterRegistrationBean();
            registration.setFilter(new CommonFilter());
            registration.addUrlPatterns("/*");
            // 入口资源关闭聚合
            registration.addInitParameter(CommonFilter.WEB_CONTEXT_UNIFY, "false");
            registration.setName("sentinelFilter");
            registration.setOrder(1);
            return registration;
        }
    }
    ```

-   修改配置文件

    ```yaml
    spring:
      cloud:
        sentinel:
          filter:
          	# 关闭sentinel原本的filter，使用我们自定义的
            enabled: false
    ```

-   配置限流规则

    ![image-20230315163310568](E:\学习笔记\img\img01\image-20230315163310568.png)

    

**测试**

多次访问`/testA`，报错了，它告诉我们要设置一个`fallback`方法

![image-20230315162243832](E:\学习笔记\img\img01\image-20230315162243832.png)





### 3.3.3 流控效果

当 QPS 超过某个阈值的时候，则采取措施进行流量控制。流量控制的手段包括下面 3 种，对应 `FlowRule` 中的 `controlBehavior` 字段：

1.  直接拒绝（`RuleConstant.CONTROL_BEHAVIOR_DEFAULT`）方式。该方式是默认的流量控制方式，当QPS超过任意规则的阈值后，新的请求就会被立即拒绝，拒绝方式为抛出`FlowException`。这种方式适用于对系统处理能力确切已知的情况下，比如通过压测确定了系统的准确水位时。具体的例子参见 [FlowqpsDemo](https://github.com/alibaba/Sentinel/blob/master/sentinel-demo/sentinel-demo-basic/src/main/java/com/alibaba/csp/sentinel/demo/flow/FlowQpsDemo.java)。

2.  冷启动（`RuleConstant.CONTROL_BEHAVIOR_WARM_UP`）方式。该方式主要用于系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮。通过"冷启动"，让通过的流量缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮的情况。具体的例子参见 [WarmUpFlowDemo](https://github.com/alibaba/Sentinel/blob/master/sentinel-demo/sentinel-demo-basic/src/main/java/com/alibaba/csp/sentinel/demo/flow/WarmUpFlowDemo.java)。

    

    **注意**：这种方式在一段时间不访问之后它的阈值就会降为3，当有流量来访问时才会慢慢提高阈值

    ![image-20230315171108042](E:\学习笔记\img\img01\image-20230315171108042.png)

    通常冷启动的过程系统允许通过的 QPS 曲线如下图所示：

    ![冷启动过程 QPS 曲线](E:\学习笔记\img\img01\warmup.gif)

3.  匀速器（`RuleConstant.CONTROL_BEHAVIOR_RATE_LIMITER`）方式。这种方式严格控制了请求通过的间隔时间，也即是让请求以均匀的速度通过，对应的是漏桶算法。具体的例子参见 [PaceFlowDemo](https://github.com/alibaba/Sentinel/blob/master/sentinel-demo/sentinel-demo-basic/src/main/java/com/alibaba/csp/sentinel/demo/flow/PaceFlowDemo.java)。



#### 测试

**直接失败方式**

超过了设定的阈值就会直接报错



**Wran Up方式**

配置限流规则

![image-20230315165740458](E:\学习笔记\img\img01\image-20230315165740458.png)

在5秒内多次访问`/testA`（狂点鼠标），可以看到一开始会被限流，随着启动时间慢慢就消失了



**排队等待方式**

修改`/testA`，让他执行以此休息1秒，接着配置限流规则

![image-20230315165016208](E:\学习笔记\img\img01\image-20230315165016208.png)

![image-20230315165034910](E:\学习笔记\img\img01\image-20230315165034910.png)

可以看到处理请求的时间是1秒一个，排队执行





## 3.4 熔断降级

### 3.4.1 概述

和Hystrix一样，Sentinel也有服务降级和熔断功能。依赖服务不稳定导致的延时或者异常会导致调用方等待或者错误，所以需要防止过长的超时等待，依赖服务在超过设置的阈值就直接返回`fallback`方法，以此来防止服务雪崩，通过熔断机制来保护服务不被巨大的流量搞崩。

和Histrix的断路器不同，Sentinel没有半开状态的，它只有开和关两种状态。服务可以手动设置熔断的时间，当达到指定之间才会开。



### 3.4.2 熔断策略

Sentinel 提供以下几种熔断策略：

-   **慢调用比例 (`SLOW_REQUEST_RATIO`)**：选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。

    ![image-20230322150302032](E:\学习笔记\img\img01\image-20230322150302032.png)

-   **异常比例 (`ERROR_RATIO`)**：当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。

    ![image-20230322150317609](E:\学习笔记\img\img01\image-20230322150317609.png)

-   **异常数 (`ERROR_COUNT`)**：当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。

    ![image-20230322150329730](E:\学习笔记\img\img01\image-20230322150329730.png)



### 3.4.3 降级规则

**熔断降级规则说明**

![image-20230322003513526](E:\学习笔记\img\img01\image-20230322003513526.png)

熔断降级规则（DegradeRule）包含下面几个重要的属性：

|       Field        | 说明                                                         | 默认值     |
| :----------------: | :----------------------------------------------------------- | :--------- |
|      resource      | 资源名，即规则的作用对象                                     |            |
|       grade        | 熔断策略，支持慢调用比例/异常比例/异常数策略                 | 慢调用比例 |
|       count        | 慢调用比例模式下为慢调用临界 RT（超出该值计为慢调用）；异常比例/异常数模式下为对应的阈值 |            |
|     timeWindow     | 熔断时长，单位为 s                                           |            |
|  minRequestAmount  | 熔断触发的最小请求数，请求数小于该值时即使异常比率超出阈值也不会熔断（1.7.0 引入） | 5          |
|   statIntervalMs   | 统计时长（单位为 ms），如 60*1000 代表分钟级（1.8.0 引入）   | 1000 ms    |
| slowRatioThreshold | 慢调用比例阈值，仅慢调用比例模式有效（1.8.0 引入）           |            |

==注意：最小请求数（`minRequestAmount`）和统计时长（`statIntervalMs`）是不能在控制台直接修改的，启动控制台时指定JVM参数修改==



### 3.4.3 案例

#### 慢比例调用

`/testA`方法中设置线程休眠5秒

```java
TimeUnit.SECONDS.sleep(6);
```

设置熔断规则

![image-20230322152319371](E:\学习笔记\img\img01\image-20230322152319371.png)

说明：1秒内超过五次百分百比例超过RT，则服务开启熔断

测试：使用JMeter并发请求

![image-20230322152613488](E:\学习笔记\img\img01\image-20230322152613488.png)

![image-20230322152453983](E:\学习笔记\img\img01\image-20230322152453983.png)



#### 异常比例

修改`/testA`，制造异常

```java
// TimeUnit.SECONDS.sleep(1);
int a = 1 / 0;
```

![image-20230322153509301](E:\学习笔记\img\img01\image-20230322153509301.png)

说明：请求数大于等于5，1秒内发生异常的比例时百分之五十，则服务熔断



#### 异常数

![image-20230322153707002](E:\学习笔记\img\img01\image-20230322153707002.png)

说明：请求数大于等于5，1秒内发生10次异常，则服务熔断







## 3.5 热点参数限流

### 3.5.1 什么是热点参数限流

热点即经常访问的数据，参数就是url上的传输，热点参数就是经常访问的参数，比如：

-   商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
-   用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流，热点限流和流量限流一样，但是它比流量限流的粒度更小，它是对某个参数进行监控限流

![Sentinel Parameter Flow Control](E:\学习笔记\img\img01\sentinel-hot-param-overview-1.png)

Sentinel 利用 LRU 策略统计最近最常访问的热点参数，结合令牌桶算法来进行参数级别的流控。



### 3.5.2 热点参数规则

![image-20230322161737048](E:\学习笔记\img\img01\image-20230322161737048.png)

热点参数规则（`ParamFlowRule`）类似于流量控制规则（`FlowRule`）：

|       属性        | 说明                                                         | 默认值   |
| :---------------: | :----------------------------------------------------------- | :------- |
|     resource      | 资源名，必填                                                 |          |
|       count       | 限流阈值，必填                                               |          |
|       grade       | 限流模式                                                     | QPS 模式 |
|   durationInSec   | 统计窗口时间长度（单位为秒），1.6.0 版本开始支持             | 1s       |
|  controlBehavior  | 流控效果（支持快速失败和匀速排队模式），1.6.0 版本开始支持   | 快速失败 |
| maxQueueingTimeMs | 最大排队等待时长（仅在匀速排队模式生效），1.6.0 版本开始支持 | 0ms      |
|     paramIdx      | 热点参数的索引，必填，对应 `SphU.entry(xxx, args)` 中的参数索引位置 |          |
| paramFlowItemList | 参数例外项，可以针对指定的参数值单独设置限流阈值，不受前面 `count` 阈值的限制。**仅支持基本类型和字符串类型** |          |
|    clusterMode    | 是否是集群参数流控规则                                       | `false`  |
|   clusterConfig   | 集群流控相关配置                                             |          |



### 3.5.3 基本使用

新增接口方法`testC`

```java
@GetMapping("/testC")
// 标记为资源
@SentinelResource(value = "testC")
public String testC(@RequestParam(name = "p1", required = false) String p1,
                    @RequestParam(name = "p2", required = false) String p2) {
    return "testC--------------";
}
```

**注**：必须要使用`@SentinelResource`标注方法，不然是无效的

配置热点规则

![image-20230322162548732](E:\学习笔记\img\img01\image-20230322162548732.png)

测试，访问`http://localhost:8401/testC?p1=sdfds`，狂点小鼠~~~~😈😈😈

![image-20230322162628794](E:\学习笔记\img\img01\image-20230322162628794.png)

**结果**：报错了，这是因为我们没有做任何的处理，随意它默认是直接报错，我们可以通过设置`fallback`方法来做友好提示，后面的章节我们会讲到如何设置`fallback`方法



### 3.5.4 参数例外项

![image-20230322163110792](E:\学习笔记\img\img01\image-20230322163110792.png)

对资源中的参数的值进行配置，当这个参数是配置的值时就会触发对应的规则

![image-20230322163412065](E:\学习笔记\img\img01\image-20230322163412065.png)

**说明**：我们添加一个参数例外项，这个例外项表示当参数值为`a`时它的限流阈值变为200。当这个值不是`a`时它的限流阈值变为`单机阈值`

**测试**：

-   再次访问`http://localhost:8401/testC?p1=sdfds`，还是不变，当我们1秒内访问超过两次就会报错。

-   访问`http://localhost:8401/testC?p1=a`，狂点小鼠，我们可以发现它并没有报错。
-   因为我们的参数是a，所以此时的资源限流阈值是200





## 3.6 系统自适应保护

系统规则是针对整个应用层面的，当我们设置其中一个配置项达到对应阈值时就会限流整个应用，也就是整个应用不可用，对系统进行保护，因为一旦有过多的流量进啦就会导致请求的`RT`变长，因为请求过多导致处理不过来就会进行排队，而排队越长，时延也就会变得越长，所以需要设置当系统达到一定程度时就对系统进行保护。



### 3.6.1 系统规则

系统保护规则是从应用级别的入口流量进行控制，从单台机器的总体 Load、RT、入口 QPS 和线程数四个维度监控应用数据，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是应用整体维度的，而不是资源维度的，并且**仅对入口流量生效**。入口流量指的是进入应用的流量（`EntryType.IN`），比如 Web 服务或 Dubbo 服务端接收的请求，都属于入口流量。

系统规则支持以下的阈值类型：

-   **Load**（仅对 Linux/Unix-like 机器生效）：当系统 load1 超过阈值，且系统当前的并发线程数超过系统容量时才会触发系统保护。系统容量由系统的 `maxQps * minRt` 计算得出。设定参考值一般是 `CPU cores * 2.5`。
-   **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0）。
-   **RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
-   **线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
-   **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。





### 3.6.2 案例

![image-20230322164827108](E:\学习笔记\img\img01\image-20230322164827108.png)

此时应用1秒内只能处理1次请求。



## 3.7 @SentinelResource 注解

### 3.7.1 概述

在3.5章我们使用了`@SentinelResource注解`来标记资源，但是我们没有给它设置任何兜底方法，所以当请求超过过我们设定的阈值它就会直接报错，但是这对我们的用户来说时不友好的，所以我们需要有兜底方法来给用户友好提示。



`@SentinelResource` 用于定义资源，并提供可选的异常处理和 fallback 配置项。 `@SentinelResource` 注解包含以下属性：

-   `value`：资源名称，必需项（不能为空）

-   `entryType`：entry 类型，可选项（默认为 `EntryType.OUT`）

-   `blockHandler` / `blockHandlerClass`: `blockHandler` 对应处理 `BlockException` 的函数名称，可选项。blockHandler 函数访问范围需要是 `public`，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 `BlockException`。blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `blockHandlerClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。

-   fallback：fallback 函数名称，可选项，用于在抛出异常的时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了exceptionsToIgnore里面排除掉的异常类型）进行处理。fallback 函数签名和位置要求：

    -   返回值类型必须与原函数返回值类型一致；
    -   方法参数列表需要和原函数一致，或者可以额外多一个 `Throwable` 类型的参数用于接收对应的异常。
    -   fallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `fallbackClass` 为对应的类的 `Class` 对象，**注意对应的函数必需为 static 函数**，否则无法解析。

-   defaultFallback

    （since 1.6.0）：默认的 fallback 函数名称，可选项，通常用于通用的 fallback 逻辑（即可以用于很多服务或方法）。默认 fallback 函数可以针对所以类型的异常（除了exceptionsToIgnore里面排除掉的异常类型）进行处理。若同时配置了 fallbackhe和defaultFallback，则只有 fallback 会生效。defaultFallback 函数签名要求：

    -   返回值类型必须与原函数返回值类型一致；
    -   方法参数列表需要为空，或者可以额外多一个 `Throwable` 类型的参数用于接收对应的异常。
    -   defaultFallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `fallbackClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。

-   `exceptionsToIgnore`（since 1.6.0）：用于指定哪些异常被排除掉，不会计入异常统计中，也不会进入 fallback 逻辑中，而是会原样抛出。

>   注：1.6.0 之前的版本 fallback 函数只针对降级异常（`DegradeException`）进行处理，**不能针对业务异常进行处理**。

特别地，若 blockHandler 和 fallback 都进行了配置，则被限流降级而抛出 `BlockException` 时只会进入 `blockHandler` 处理逻辑。若未配置 `blockHandler`、`fallback` 和 `defaultFallback`，则被限流降级时会将 `BlockException` **直接抛出**。

>   注意：注解方式埋点不支持 private 方法。

==简单点来说就是`fallback`处理java异常，`blockHandler`处理限流降级。两个一起设置时出现异常就会先`fallback`，当异常数或者异常比例达到设置的阈值时就会触发`blockHandler`。==



### 3.8.1 使用

新增一个接口`/testD`，给它增加限流规则进行测试

```java
@GetMapping("/testD")
@SentinelResource(value = "testD")
public String testD() {
    return "testD--------------";
}
```



#### blockHandler

添加兜底方法，注解属性`blockHandler`指定

```java
@SentinelResource(value = "testD", blockHandler = "blockHandlerMethod")

// 超出阈值兜底方法，它不能为private!!!
// Block 异常处理函数，参数最后多一个 BlockException，其余与原函数一致.
public String blockHandlerMethod(BlockException ex) {
    System.out.println(ex.getMessage());
    return "return blockHandlerMethod";
}
```

可以看到，我们的兜底方法和业务耦合在一起了，所以我们可以使用解耦的方式。使用`blockHandlerClass`属性来指定兜底的类，再指定类中对应的方法作为兜底方法

```java
public class BlockHandlerMethod {

    public static String returnMethod01(BlockException exception) {
        return "BlockHandlerMethod1 Method";
    }

    public static String returnMethod02(BlockException exception) {
        return "BlockHandlerMethod2 Method";
    }
}
```

```java
@GetMapping("/testD")
@SentinelResource(value = "testD",
        // 指定处理的类
        blockHandlerClass = BlockHandlerMethod.class,
	    // 指定兜底方法
        blockHandler = "returnMethod01")
public String testD() {
    return "testD--------------";
}
```

测试，设置限流规则，接着狂点小鼠

![image-20230322210556648](E:\学习笔记\img\img01\image-20230322210556648.png)



#### fallback

和`blockHandler`不同的是`fallback`处理的是java异常，所以我们需要制造异常

```java
int i = 10 / 0;
```

和`blockHandler`一样，它也是可以指定处理的类和处理的方法

```java
public class FallbackMethod {
	// Fallback 函数，函数签名与原函数一致或加一个 Throwable 类型的参数.
    public static String fallbackMethod01(FlowException exception) {
        return "fallbackMethod01 Method";
    }
   	// Fallback 函数，函数签名与原函数一致或加一个 Throwable 类型的参数.
    public static String fallbackMethod02(FlowException exception) {
        return "fallbackMethod02 Method";
    }
}
```

```java
@GetMapping("/testD")
@SentinelResource(value = "testD", fallbackClass = FallbackMethod.class, fallback = "fallbackMethod01")
public String testD() {
    int i = 10 / 0;
    return "testD--------------";
}
```

测试，狂点小鼠~~~



#### 两个一起使用

```java
@GetMapping("/testD")
@SentinelResource(value = "testD",
        blockHandlerClass = BlockHandlerMethod.class, blockHandler = "returnMethod01",
        fallbackClass = FallbackMethod.class, fallback = "fallbackMethod01")
public String testD() {
    int i = 10 / 0;
    return "testD--------------";
}
```

测试，狂点鼠标~~~

结果：两个一起使用时，先是出现异常被fallback了，接着就出现了限流的兜底方法返回信息。





#### 忽略属性

设置`exceptionsToIgnore`属性，当出现属性中标记的异常时就会不管他，让它飞，它就会直接被抛出去

![image-20230322212842108](E:\学习笔记\img\img01\image-20230322212842108.png)

![image-20230322213106605](E:\学习笔记\img\img01\image-20230322213106605.png)

飞的更高~~~😈😈😈



## 3.8 整合Openfeign

### 3.8.1 如何整合？

-   修改yaml文件

    ```yaml
    # 激活Sentinel对Feign的支持
    feign:
      sentinel:
        enabled: true
    ```

-   主启动类添加`@EnableFeignClients`开启





### 3.8.2 工程搭建

#### 提供者

-   模块`cloudalibaba-provider-payment9003`

-   POM

    ```xml
    <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
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

-   YAML

    ```yaml
    server:
      port: 9003
    
    spring:
      application:
        name: nacos-payment-provider
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
    
    management:
      endpoints:
        web:
          exposure:
            include: '*'
    ```

-   主启动

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient
    public class PaymentMain9003 {
        public static void main(String[] args) {
                SpringApplication.run(PaymentMain9003.class, args);
        }
    }
    ```

-   业务类

    ```java
    @RestController
    public class PaymentController {
    
        @Value("${server.port}")
        private String serverPort;
    
        public static HashMap<Long, Payment> hashMap = new HashMap<>();
    
        static {
            hashMap.put(1L,new Payment(1L,"28a8c1e3bc2742d8848569891fb42181"));
            hashMap.put(2L,new Payment(2L,"bba8c1e3bc2742d8848569891ac32182"));
            hashMap.put(3L,new Payment(3L,"6ua8c1e3bc2742d8848569891xt92183"));
        }
    
        @GetMapping("/paymentSql/{id}")
        public CommonResult<Payment> paymentSql(@PathVariable("id") Long id) {
            Payment payment = hashMap.get(id);
            return new CommonResult<>(200, "from mysql serverPort：" + serverPort, payment);
        }
    }
    ```

测试访问没问题表示成功~~~



#### 消费者

-    模块`cloudalibaba-consumer-nacos-order84`

-   POM

    ```xml
    <dependencies>
        <!--SpringCloud openfeign -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
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

-   YAML

    ```yaml
    server:
      port: 84
    
    spring:
      application:
        name: nacos-order-consumer
      cloud:
        nacos:
          discovery:
            server-addr: localhost:8848
        sentinel:
          transport:
            #配置Sentinel dashboard地址
            dashboard: localhost:8080
            #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
            port: 8719
    
    # 激活Sentinel对Feign的支持
    feign:
      sentinel:
        enabled: true
    ```

-   主启动

    ```java
    @EnableDiscoveryClient
    @SpringBootApplication
    @EnableFeignClients     // 开启Feign支持
    public class OrderNacosMain84 {
        public static void main(String[] args) {
                SpringApplication.run(OrderNacosMain84.class, args);
        }
    }
    ```

-   业务类

    -   PaymentController

        ```java
        @RestController
        public class PaymentController {
        
            @Resource
            private PaymentService paymentService;
        
            @GetMapping("/paymentSql/{id}")
            public CommonResult<Payment> paymentSql(@PathVariable("id") Long id) {
                if (id.equals(4L)) {
                    throw new IllegalArgumentException("没有该id");
                }
                return paymentService.paymentSql(id);
            }
        }
        ```

    -   PaymentService

        ```java
        @FeignClient(value = "nacos-payment-provider", fallback = PaymentFallbackService.class)// 调用中关闭9003服务提供者
        public interface PaymentService {
            @GetMapping("/paymentSql/{id}")
            public CommonResult<Payment> paymentSql(@PathVariable("id") Long id);
        }
        ```

    -   PaymentFallbackService

        ```java
        @Component
        public class PaymentFallbackService implements PaymentService{
        
            @Override
            public CommonResult<Payment> paymentSql(Long id) {
                return new CommonResult<>(444, "请求人数过多，请销后重试~~~");
            }
        }
        ```



#### 测试

访问`http://localhost:84/paymentSql/1`，出现数据表示成功

访问`http://localhost:84/paymentSql/4`，它会直接报错，`OpenFeign`对不是它能处理的异常会直接抛出去，不会对它fallback

停止提供者工程，再次访问，它就会直接返回fallback方法。





## 3.9 规则持久化

### 3.9.1 规则持久化？

当我们的服务重新启动，我们设置的规则就户全部没有了，也就意味着我们每次重启服务都要重写设置一遍，这是个费劲的事情，因此我们应该就规则持久化，那应该持久化到哪呢？答案就是持久化到nacos中，naocs它可以持久化到数据库中，我们每次启动服务并访问资源的时候就将我们的规则重新写入到sentinel中。



### 3.9.2 怎么做

1.  项目中添加依赖

    ```xml
    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-nacos</artifactId>
    </dependency>
    ```

2.  写入规则到nacos

    ![image-20230323142030712](E:\学习笔记\img\img01\image-20230323142030712.png)

    注意：规则持久化是没有`.yaml`后缀的

    配置内容解析：

    ```json
    [
        {
            "resource": "/testA",
            "limitApp": "default",
            "grade": 1,
            "count": 1,
            "strategy": 0,
            "controlBehavior": 0,
            "clusterMode": false
        }
    ]
    ```

    | key             | value                                                |
    | --------------- | ---------------------------------------------------- |
    | resource        | 资源名称                                             |
    | limitApp        | 来源应用                                             |
    | grade           | 阈值类型，0表示线程数，1表示QPS                      |
    | count           | 单机阈值                                             |
    | strategy        | 流控模式，0表示直接，1表示关联，2表示链路            |
    | controlBehavior | 流控效果，0表示快速失败，1表示Warm Up，2表示排队等待 |
    | clusterMode     | 是否集群                                             |
    |                 |                                                      |

    

3.  指定规则

    在项目的yaml文件中指定要使用的规则

    ```yaml
    spring:
      cloud:
        sentinel:
          datasource:
            ds1:
              nacos:
                server-addr: localhost:8848
                dataId: cloudalibaba-sentinel-service
                groupId: DEFAULT_GROUP
                data-type: json
                rule-type: flow		# 规则类型 flow表示是流控规则
    ```





### 3.9.3 测试

配置完成之后我们要启动服务，当前测试使用的是8401服务。

重启服务后查看sentinel控制台，此时还是空的，需要我们先访问一下服务接口它才会有显示。

![image-20230323143057398](E:\学习笔记\img\img01\image-20230323143057398.png)

可以看到持久化成功了。接下来狂点小鼠，看一下设置的规则是否起效

![image-20230323143130708](E:\学习笔记\img\img01\image-20230323143130708.png)

没有问题。nice~~~😝😝😝









# 四、Seata

![img](E:\学习笔记\img\img01\TB1qTjWw.T1gK0jSZFhXXaAtVXa-4802-1285.png)



## 4.1 概述

### 4.1.1 是什么

Seata 是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata 将为用户提供了 AT、TCC、SAGA 和 XA 事务模式，为用户打造一站式的分布式解决方案。

**官网**：`https://seata.io/zh-cn/index.html`



**什么是分布式事物呢？**

在微服务架构中，往往一个服务中可能调用多个服务，而单个服务中可以使用`@Transactiona`来处理事物，但是它不能控制调用的事物，也就是说，在整个调用中有一个调用出现了错误，它只会回滚当前调用方，而调用的服务是没有回滚的，这就会导致事物一致性的问题。

比如：在A服务中先是调用B服务，然后再调用C服务，在调用C服务的过程中C服务发生了异常，并将信息返回给了A服务，A服务发现后进行了回滚，但是B服务是没有进行回滚的，因为它是执行成功的。

所以，对于以上的问题，我们就需要有一个东西来保证总的事物，要么同时都成功，要么同时都失败。而seata就是用来解决分布式事物的一站式解决方案。



### 4.1.2 术语

下面是一次事物的调度图

![image](E:\学习笔记\img\img01\145942191-7a2d469f-94c8-4cd2-8c7e-46ad75683636.png)

**官方术语**：

-   TC (Transaction Coordinator) - 事务协调者

    维护全局和分支事务的状态，驱动全局事务提交或回滚。

-   TM (Transaction Manager) - 事务管理器`

    定义全局事务的范围：开始全局事务、提交或回滚全局事务。

-   RM (Resource Manager) - 资源管理器

    管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

**自我理解**：

-   TC可以理解为是整个事物的总控制，控制全局的事物回滚和提交，也就是`seata server`
-   TM可以理解为事物开启放，也就是整个事物的入口，比如A服务调用B、C服务，那么A就是`TM`，也就是标注`@GlobalTansactional`注解的接口，我们可以称它为事物的发起者
-   RM可以理解为每个服务上和TC打交道的资源管理器，TC告诉他们回滚或者提交，所以他们是执行者，TC是管理者，TC也可以叫做事物的参与者



  

## 4.2 搭建seata server

**下载地址**：`https://seata.io/zh-cn/blog/download.html`

**数据库**

-   创建seata数据库

-   导入sql脚本创建表，sql表的位置在安装包的`script\server\db`目录下

    ![image-20230327103900744](E:\学习笔记\img\img01\image-20230327103900744.png)

**配置server**

配置文件在安装包的`conf`目录下，低版本的`conf`目录下是`.conf`文件，我使用的是`1.6.1`版本，配置文件使用的`.yml`文件替换了`.conf`文件

修改seata模块中的`registry`、`conf`、`store`模块，它们分别表示注册、配置中心的配置和数据源的配置。

```yml
seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    nacos:
      server-addr: 127.0.0.1:8848
      namespace: ""
      group: SEATA_GROUP
      username: nacos
      password: nacos
      context-path:
      ##if use MSE Nacos with auth, mutex with username/password attribute
      #access-key:
      #secret-key:
      data-id: seataServer.yml

  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: nacos
    nacos:
      application: seata-server
      server-addr: 127.0.0.1:8848
      group: SEATA_GROUP
      namespace: ""
      cluster: "default"
      username: nacos
      password: nacos
  store:
    # support: file 、 db 、 redis
    mode: db
    db:
      datasource: druid
      db-type: mysql
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://192.168.10.10:3306/seata?rewriteBatchedStatements=true
      user: root
      password: abc123
      min-conn: 10
      max-conn: 100
      # 对应的四个表名字
      global-table: global_table
      branch-table: branch_table
      lock-table: lock_table
      distributed-lock-table: distributed_lock
      query-limit: 1000
      max-wait: 5000
```

配置完成后启动服务即可

==注意：如果是要注册到注册中心，需要先启动注册中心，在本示例中使用的是nacos作为注册中心，所以要先启动nacos，再启动seata server==





## 4.3 入门案例

### 4.3.1 用例概述

用户购买商品的业务逻辑。整个业务逻辑由3个微服务提供支持：

-   仓储服务：对给定的商品扣除仓储数量。
-   订单服务：根据采购需求创建订单。
-   帐户服务：从用户帐户中扣除余额。

**调用规则**：订单服务 -> 仓储服务 -> 账户服务

**业务流程**：创建订单->修改仓库数量->修改账户金额->修改订单状态



### 4.3.2 服务搭建

#### ①创库和创表

```sql
CREATE DATABASE seata_order;
CREATE DATABASE seata_storage;
CREATE DATABASE seata_account;
```

创建表，对应表创建在对应库下

```sql
CREATE TABLE t_order (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
  `count` INT(11) DEFAULT NULL COMMENT '数量',
  `money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
  `status` INT(1) DEFAULT NULL COMMENT '订单状态：0：创建中；1：已完结' 
) ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
SELECT * FROM t_order;
```

```sql
 
CREATE TABLE t_storage (
 `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
 `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
 `total` INT(11) DEFAULT NULL COMMENT '总库存',
 `used` INT(11) DEFAULT NULL COMMENT '已用库存',
 `residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
INSERT INTO seata_storage.t_storage(`id`, `product_id`, `total`, `used`, `residue`)
VALUES ('1', '1', '100', '0', '100');
SELECT * FROM t_storage;
```

```sql
 
CREATE TABLE t_account (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
  `used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
  `residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
INSERT INTO seata_account.t_account(`id`, `user_id`, `total`, `used`, `residue`)  VALUES ('1', '1', '1000', '0', '1000');
SELECT * FROM t_account;
```

==下面这张表是每个表都要创建的表==

```sql
-- the table to store seata xid data
-- 0.7.0+ add context
-- you must to init this sql for you business databese. the seata server not need it.
-- 此脚本必须初始化在你当前的业务数据库中，用于AT 模式XID记录。与server端无关（注：业务数据库）
-- 注意此处0.3.0+ 增加唯一索引 ux_undo_log
DROP TABLE `undo_log`;
 
CREATE TABLE `undo_log` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT,
  `branch_id` BIGINT(20) NOT NULL,
  `xid` VARCHAR(100) NOT NULL,
  `context` VARCHAR(128) NOT NULL,
  `rollback_info` LONGBLOB NOT NULL,
  `log_status` INT(11) NOT NULL,
  `log_created` DATETIME NOT NULL,
  `log_modified` DATETIME NOT NULL,
  `ext` VARCHAR(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

结果如图

![image-20230327105252938](E:\学习笔记\img\img01\image-20230327105252938.png)



#### ②order服务

**业务**：创建订单表和修改订单状态

1.`seata-order-service2001`模块

2.POM

```xml
<dependencies>
    <!--nacos-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--seata-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        <exclusions>
            <exclusion>
                <groupId>io.seata</groupId>
                <artifactId>seata-all</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>io.seata</groupId>
        <artifactId>seata-all</artifactId>
        <version>1.6.1</version>
    </dependency>
    <!--feign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <!--web-actuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--mysql-druid-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.37</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
</dependencies>
```

3.YAML

```yaml
server:
  port: 2001

spring:
  application:
    name: seata-order-service

  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    alibaba:
      seata:
        # 指定事物分组 - 这个要和naocs对应的分组相同
        tx-service-group: fsp-tx-group

  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://192.168.10.10:3306/seata_order
    username: root
    password: abc123

feign:
  hystrix:
    enabled: false


logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.xiaozhi.springcloud.domain
  configuration:
    map-underscore-to-camel-case: true
```

4.配置文件

说明：spring-cloud-alibaba-seata 在 2.2.0.RELEASE 版本前 依赖的是seata-all 若继续使用低版本的 spring-cloud-alibaba-seata 可以使用高版本的 seata-all 取代内置的 seata-all 版本；

使用低版本的 spring-cloud-alibaba-seata 需要在 resource 目录下创建配置文件`registry.conf`

下载地址：`https://github.com/seata/seata/blob/develop/script/client/conf/registry.conf`

![image-20230327112755346](E:\学习笔记\img\img01\image-20230327112755346.png)



5.实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CommonResult<T> {
    private Integer code;
    private String  message;
    private T       data;

    public CommonResult(Integer code, String message) {
        this(code,message,null);
    }
}
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order {
    private Long id;

    private Long userId;

    private Long productId;

    private Integer count;

    private BigDecimal money;

    /**
     * 订单状态：0：创建中；1：已完结
     */
    private Integer status;
}
```

6.Mapper

```java
@Mapper
public interface OrderDao {

    /**
     * 创建订单
     */
    void create(Order order);

    /**
     * 修改订单金额
     */
    void update(@Param("userId") Long userId, @Param("status") Integer status);
}
```

xml略

7.service

```java
@Service
@Slf4j
public class OrderServiceImpl implements OrderService {

    @Resource
    private OrderMapper orderMapper;

    @Resource
    private StorageService storageService;

    @Resource
    private AccountService accountService;
    
    @Override
    public void createOrder(Order order) {
        log.info("======= 创建订单开始 ======");
        orderMapper.create(order);
        log.info("======= 远程调用扣减库存开始 ======");
        storageService.decrease(order.getProductId(), order.getCount());
        log.info("======= 远程调用扣减库存结束 ======");

        log.info("======= 远程调用扣减金额开始 ======");
        accountService.decrease(order.getUserId(), order.getMoney());
        log.info("======= 远程调用扣减金额结束 ======");

        log.info("======= 修改订单状态开始 ======");
        orderMapper.update(order.getUserId(), 1);
        log.info("======= 修改订单状态结束 ======");
        log.info("======= 下单结束 ======");
    }
}
```

8.controller

```java
@RestController
public class OrderController {

    @Autowired
    private OrderService orderService;

    /**
     * 创建订单
     */
    @GetMapping("/order/create")
    public CommonResult create( Order order) {
        orderService.create(order);
        return new CommonResult(200, "订单创建成功!");
    }
}
```

9.config

```java
@Configuration
@MapperScan({"com.atguigu.springcloud.alibaba.dao"})
public class MyBatisConfig {
}
```

```java
/**
 * @author XIAOZHI
 * 使用Seata对数据源进行代理
 */
@Configuration
public class DataSourceProxyConfig {

    @Value("${mybatis.mapperLocations}")
    private String mapperLocations;

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }

    @Bean
    public DataSourceProxy dataSourceProxy(DataSource dataSource) {
        return new DataSourceProxy(dataSource);
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSourceProxy);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
        sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
        return sqlSessionFactoryBean.getObject();
    }
}
```

10.启动类

```java
@EnableDiscoveryClient
@EnableFeignClients
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)// 取消数据源的自动创建
public class SeataOrderMainApp2001 {
    public static void main(String[] args) {
        SpringApplication.run(SeataOrderMainApp2001.class, args);
    }
}
```





#### ③storage和account服务

这两个服务搭建省略，都是将字段的值进行扣减，配置和Order服务一样

-   StorageMapper.xml

    ```xml
    <update id="decrease">
        update `t_storage`
        set `used` = `used` + #{count},
        `residue` = `residue` - #{count}
        where `product_id` = #{productId};
    </update>
    ```

-   AccountMapper.xml

    ```xml
    <update id="decrease">
        update `t_account`
        set residue = residue - #{money},
        used = used + #{money}
        where `user_id` = #{userId};
    </update>
    ```

详细请见：``





### 4.3.3 测试

此时我们还没有对 OrderService 做分布式事物管理，我们让 AccountService 延时5秒，OpenFeign调用默认超时时间是1秒，所以创建订单肯定是失败的。查看数据库发现订单成功创建了，证明了本次调用没有做分布式事物管理，没有保证一致性。

接下来，我们给 OrderService 加上分布式事物注解`@GlobalTransactional`

```java
@GlobalTransactional(name = "fsp-create-orde", rollbackFor = Exception.class)
@Override
public void createOrder(Order order) {
```

重新启动再次测试，OpenFeign 调用报错，查看数据库看是否创建订单和修改金额和库存，发现并没有修改，分布式事物管理成功~~~。





## 4.4 原理

### 4.4.1 AT模式

#### 前提

-   基于支持本地 ACID 事务的关系型数据库。
-   Java 应用，通过 JDBC 访问数据库。



#### 整体机制

两阶段提交协议的演变：

-   一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。
-   二阶段：
    -   提交异步化，非常快速地完成。
    -   回滚通过一阶段的回滚日志进行反向补偿。



#### 工作机制

![image-20230327184724199](E:\学习笔记\img\img01\image-20230327184724199.png)

以一个示例来说明整个 AT 分支的工作过程。

业务表：`product`

| Field | Type         | Key  |
| ----- | ------------ | ---- |
| id    | bigint(20)   | PRI  |
| name  | varchar(100) |      |
| since | varchar(100) |      |

AT 分支事务的业务逻辑：

```sql
update product set name = 'GTS' where name = 'TXC';
```



##### 一阶段

过程：

1.  解析 SQL：得到 SQL 的类型（UPDATE），表（product），条件（where name = 'TXC'）等相关的信息。
2.  查询前镜像：根据解析得到的条件信息，生成查询语句，定位数据。

```sql
select id, name, since from product where name = 'TXC';
```

得到前镜像：

| id   | name | since |
| ---- | ---- | ----- |
| 1    | TXC  | 2014  |

1.  执行业务 SQL：更新这条记录的 name 为 'GTS'。
2.  查询后镜像：根据前镜像的结果，通过 **主键** 定位数据。

```sql
select id, name, since from product where id = 1;
```

得到后镜像：

| id   | name | since |
| ---- | ---- | ----- |
| 1    | GTS  | 2014  |

1.  插入回滚日志：把前后镜像数据以及业务 SQL 相关的信息组成一条回滚日志记录，插入到 `UNDO_LOG` 表中。

```json
{
	"branchId": 641789253,
	"undoItems": [{
		"afterImage": {
			"rows": [{
				"fields": [{
					"name": "id",
					"type": 4,
					"value": 1
				}, {
					"name": "name",
					"type": 12,
					"value": "GTS"
				}, {
					"name": "since",
					"type": 12,
					"value": "2014"
				}]
			}],
			"tableName": "product"
		},
		"beforeImage": {
			"rows": [{
				"fields": [{
					"name": "id",
					"type": 4,
					"value": 1
				}, {
					"name": "name",
					"type": 12,
					"value": "TXC"
				}, {
					"name": "since",
					"type": 12,
					"value": "2014"
				}]
			}],
			"tableName": "product"
		},
		"sqlType": "UPDATE"
	}],
	"xid": "xid:xxx"
}
```

1.  提交前，向 TC 注册分支：申请 `product` 表中，主键值等于 1 的记录的 **全局锁** 。
2.  本地事务提交：业务数据的更新和前面步骤中生成的 UNDO LOG 一并提交。
3.  将本地事务提交的结果上报给 TC。

##### 二阶段-回滚

1.  收到 TC 的分支回滚请求，开启一个本地事务，执行如下操作。
2.  通过 XID 和 Branch ID 查找到相应的 UNDO LOG 记录。
3.  数据校验：拿 UNDO LOG 中的后镜与当前数据进行比较，如果有不同，说明数据被当前全局事务之外的动作做了修改。这种情况，需要根据配置策略来做处理，详细的说明在另外的文档中介绍。
4.  根据 UNDO LOG 中的前镜像和业务 SQL 的相关信息生成并执行回滚的语句：

```sql
update product set name = 'TXC' where id = 1;
```

1.  提交本地事务。并把本地事务的执行结果（即分支事务回滚的结果）上报给 TC。



##### 二阶段-提交

1.  收到 TC 的分支提交请求，把请求放入一个异步任务的队列中，马上返回提交成功的结果给 TC。
2.  异步任务阶段的分支提交请求将异步和批量地删除相应 UNDO LOG 记录。







