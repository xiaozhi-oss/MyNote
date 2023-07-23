# 快捷键使用



## 系统快捷键

|          ctrl + alt + 空格          | 提示信息                                       |
| :---------------------------------: | :--------------------------------------------- |
|           ctrl + alt + v            | 相当于Ecelipse里面的ctr+1，显示左边的内容      |
|               alt + n               | 抛异常                                         |
|              ctrl + o               | 查看关系                                       |
|          ctrl + shift + T           | 查看文档                                       |
|           ctrl + alt + T            | 包起来                                         |
|           ctrl + alt + L            | 格式化                                         |
|              alt + ins              | 自动生成get和set方法等                         |
|              ctrl + r               | 搜索关键字、替换                               |
|              ctrl + f               | 搜索关键字                                     |
|             ctrl +   H              | 显示出Hierarchy（继承树）                      |
|          ctrl + shift + u           | 转换大小写                                     |
|          ctrl + shift + I           | 预览某个类、某个方法、某个字段、某个变量的代码 |
| ctrl + alt + B 或 ctrl + alt + 左键 | 跳转实现类                                     |
|                 F11                 | 添加书签                                       |
|                TODO                 | 待完成                                         |
|       ctrl + alt + shift + U        | 查看依赖关系                                   |
|           ctrl + alt + U            | 查看依赖关系（只有查看功能）                   |
|                                     |                                                |
|                                     |                                                |





## 自定义快捷键

| alt + c | 创建class文件 |
| :-----: | :------------ |
|   a+k   | 创建model     |
| alt + e | 创建文件夹    |
| alt + r | new file      |
| alt + = | 展开文件夹    |
| alt + - | 折叠文件夹    |
|         |               |
|         |               |
|         |               |





# Debug奇技淫巧😈

## 1、简单用法

### 1.1 行断点

顾名思义，在任意一行打上一个断点，执行就会跳到断点处



### 1.2  详细断点

在需要debug的一行`shift + 左键`就会弹出一个选项框，我们就可以进行特定的操作

<img src="E:\学习笔记\img\image-20221209202604230.png" alt="image-20221209202604230" style="zoom:67%;" />

也可以先打上断点，然后右键，也可以弹出常用选项

<img src="E:\学习笔记\img\image-20221209203511421.png" alt="image-20221209203511421" style="zoom:67%;" />

常用的几个选项

-   Enabled：开启断点
-   Suspend：挂起
    -   All：所有线程
    -   Thread：当前线程就停，不是就不停，调试多线程用
-   Condition：条件，我们可以在这个输入框中写入表达式，当这个表达式成立，那么断点就会停在这





### 1.3 异常断点

我们可以设置当出现什么异常的时候就停留在出现异常的哪一行

![image-20221209203657085](E:\学习笔记\img\image-20221209203657085.png)

点击这个小圆点会出现debug停留的选项

<img src="E:\学习笔记\img\image-20221209203907374.png" alt="image-20221209203907374"  />

在异常断点中可以定义需要拦截的异常断点

<img src="E:\学习笔记\img\image-20221209204144850.png" alt="image-20221209204144850"  />

当出现异常的时候就会跳到出现异常的那一行

<img src="E:\学习笔记\img\image-20221209204257839.png" alt="image-20221209204257839" style="zoom:67%;" />

可以看到ieda中断点的图标和其他的类型是不一样的





### 1.4 字段断点

将断电打在对象的字段上，他会跳到字段对应的`getter`或`setter`方法上（主要是看你是获取值还是设置值）

<img src="E:\学习笔记\img\image-20221209204609530.png" alt="image-20221209204609530" style="zoom:50%;" />

<img src="E:\学习笔记\img\image-20221209204703617.png" alt="image-20221209204703617" style="zoom: 67%;" />



### 1.5 接口方法断点

我们在接口方法中打上断点，那么它会停留在这个接口的实现类的对应实现方法上

<img src="E:\学习笔记\img\image-20221209205002306.png" alt="image-20221209205002306" style="zoom:67%;" />

<img src="E:\学习笔记\img\image-20221209205100612.png" alt="image-20221209205100612" style="zoom:67%;" />

这样我们就需要看是那个实现类下的方法，非常nice~~~





## 2、进阶用法

### 2.1 条件调试`condtion`

我们上面也讲过，在条件框中输入停留的条件，当满足这个条件的时候就会停留在断点处

<img src="E:\学习笔记\img\image-20221209211458192.png" alt="image-20221209211458192" style="zoom: 67%;" />

![image-20221209211544768](E:\学习笔记\img\image-20221209211544768.png)

这样他就会跳过单数的情况



只有当前线程为`thread1`的线程才停留

```java
// 内部类
static class ThreadTest implements Runnable {

    public void run() {
        // 输出当前线程的名字
        System.out.println(Thread.currentThread().getName());
    }
}
```

```java
public static void threadTest(){
    ThreadTest runnable = new ThreadTest();
    Thread thread1 = new Thread(runnable, "thread1");
    Thread thread2 = new Thread(runnable, "thread2");
    Thread thread3 = new Thread(runnable, "thread3");
    thread1.start();
    thread2.start();
    thread3.start();
}
```

![image-20221209212343844](E:\学习笔记\img\image-20221209212343844.png)

这个时候它就只会在线程为`thread1`的时候停留



### 2.2 表达式解析`Evalaute`

在进行调试的时候，我们需要知道一个表达式的值为多少时，我们可以使用`Evaluate`进行计算

![image-20221209213025633](E:\学习笔记\img\image-20221209213025633.png)

点击这个小图标或者`Alt+F8`打开，在输入框中写入程序中你想要计算的表达式

<img src="E:\学习笔记\img\image-20221209213103005.png" alt="image-20221209213103005" style="zoom:80%;" />

😀**快捷计算技巧**：我们可以选中代码，然后右键找到`Evaluate Expression`选项或者按下快捷键`Alt+F8`，选择的表达式就会自动出现在输入框中





### 2.3 暴力返回 `force return`

一个方法中，前面一些数据操作，后面是操作数据库保存或者放入队列的一系列资源操作，我们在测试的时候不希望走到下面操作资源的步骤，只需要调试前面的操作，这个时候我们就可以使用`force return`暴力结束方法，程序就会直接结束方法跳到方法的下一行，==如果点击结束程序按钮是不行的，它会把当前执行的方法执行完毕，这个小细节注意一下==

<img src="E:\学习笔记\img\image-20221209214234392.png" alt="image-20221209214234392" style="zoom: 67%;" />

<img src="E:\学习笔记\img\image-20221209214325964.png" alt="image-20221209214325964" style="zoom:67%;" />

在方法栈中我们右键就会弹出上面的选项，点击暴力返回就不会继续往下执行



### 2.4 调试窗口的图标功能

![image-20221209215200085](E:\学习笔记\img\image-20221209215200085.png)

从左到右一个个来

| ![image-20221209215246864](E:\学习笔记\img\image-20221209215246864.png) | 回到当前停留的地方（Alt + F10）                |
| ------------------------------------------------------------ | ---------------------------------------------- |
| ![image-20221209215333313](E:\学习笔记\img\image-20221209215333313.png) | 继续往下走（F8）                               |
| ![image-20221209215340514](E:\学习笔记\img\image-20221209215340514.png) | 进入到我们自定义方法的内部(F7)                 |
| ![image-20221209215347624](E:\学习笔记\img\image-20221209215347624.png) | 进入到不是我们自定义方法的内部（Alt Shift F7） |
| ![image-20221209215355185](E:\学习笔记\img\image-20221209215355185.png) | 跳出当前方法                                   |
| ![image-20221209215402648](E:\学习笔记\img\image-20221209215402648.png) | 跳到当前光标处                                 |
| ![image-20221209215412678](E:\学习笔记\img\image-20221209215412678.png) | 计算表达式                                     |
| ![image-20221209215419822](E:\学习笔记\img\image-20221209215419822.png) | 调试Stream流                                   |





### 2.5 Steam流调试

![image-20221209215419822](E:\学习笔记\img\image-20221209215419822.png)

这个Stream流我们一般的端点调试是不行的，不能看到它执行的一些内部细节，借助这个功能我们就可以清晰的看到是怎么执行的了

有两种模式可以切换，一个是单个方法的内部执行细节，一个是总的流程

<img src="E:\学习笔记\img\image-20221209220122423.png" alt="image-20221209220122423" style="zoom:80%;" />

<img src="E:\学习笔记\img\image-20221209220253994.png" alt="image-20221209220253994" style="zoom:80%;" />





### 2.6 编译期断点

有时候我们需要调试一下在编译时期的一些逻辑，比如看lombok的生成逻辑

![image-20221231095543395](E:\学习笔记\img\image-20221231095543395.png)







# 代码模板

## Postfix Completion

这个是后缀代码模板，通过`.后缀`的方式来获取

eg：`list.for`就是这种方式





## Live Templates

这个是前缀代码模板，输入idea自带的模板或者我们自定义的模板前缀就可以获得定义好的模板

eg：main，sout



### 系统默认

| 前缀       | 快速生成                          |
| ---------- | --------------------------------- |
| sbc        | 块注释                            |
| main或psvm | mian方法                          |
| sout       | System.out.println()              |
| todo       | TODO注释                          |
| psf        | **public** static final           |
| psfs       | public static final String        |
| psfi       | public static final int           |
| prsf       | **private** static final          |
| geti       | 静态方法                          |
| key        | 以KEY_为前缀的私有静态final字符串 |
| const      | 生成私有静态final整型变量         |
|            |                                   |

**注意**：忘记了，可以使用`ctrl + j`打开代码生成菜单

![image-20221222120145455](E:\学习笔记\img\image-20221222120145455.png)





### 自定义模板

1.  首先打开设置找到`Live Templates`

2.  然后点击`+`号添加组，组名自定义

    ![image-20221216133327613](E:\学习笔记\img\image-20221216133327613.png)

3.  在创建好的组中添加自己的模板

4.  定义自己的模板

    模板中的变量是自定义的，使用`$名$`来定义，同样的变量名输出的内容是一样的

    `$END$`是最后结束光标停留的位置

**例子**

![image-20221216133721846](E:\学习笔记\img\image-20221216133721846.png)

==注意：模板定义完成之后一定要定义模板的范围，不然是不生效的==

```java
@RestController
public class $CLASS_NAME$Controller {

    @Resource
    private $NAME$ $PARAM_NAME$;
    
    public JsonObject $METHOD_NAME$() {
        $END$
    }
} 
```







# 项目

## 项目添加

### 添加maven支持

1.  右键点击你要加入的项目
2.  点击Add Frameworks Support选项
3.  选择maven项目支持即可



### 将不同项目添加到同一启动窗口

![image-20221220170550192](E:\学习笔记\img\image-20221220170550192.png)

点击添加即可

![image-20221220170633462](E:\学习笔记\img\image-20221220170633462.png)





## 编码

<img src="E:\学习笔记\img\image-20230205123619078.png" alt="image-20230205123619078" style="zoom: 67%;" />



## 过滤文件（隐藏）

`File Types` -> `ignored Files and Folders`添加想要隐藏的文件和文件夹

![image-20230205124010298](E:\学习笔记\img\image-20230205124010298.png)

将项目中的`.idea`和`*.iml`隐藏



## 热部署

![image-20230205155558610](E:\学习笔记\img\image-20230205155558610.png)





## 端口映射

我们在学习微服务的时候，需要启动多台同样配置的服务来实验，我们每次都复制一个Model就太麻烦了，在IDEA中可以使用端口映射来进行复制

![image-20230221204413873](E:\学习笔记\img\image-20230221204413873.png)

这里我们举例复制一个Eureka注册中心，右键选择`Copy Configuration`

![image-20230221204556619](E:\学习笔记\img\image-20230221204556619.png)

==注意：不指定端口号的话就会跟源服务同一个端口号。==

复制完就可以看到我们的`Service`管理器出现我们复制的服务了，内容和源服务一样，除非修改了某些配置，比如端口号修改，那么它的端口号就是修改的那个。

```sh
# 修改端口号
-DServer.port=7004
```





## 方法抽取

将公共的代码抽取成方法，然后调用它

使用：

-   选中代码块右键，选择`Refactor` -> `Extract Method`
-   快捷键`Ctrl + Alt + M`

![image-20230307162252345](E:\学习笔记\img\img01\image-20230307162252345.png)

![image-20230307162434821](E:\学习笔记\img\img01\image-20230307162434821.png)





# 小技巧



## 复制类

将一段类的代码直接`ctrl c`到指定包下，它会自动生成类文件，非常方便~~~





## 字符跳转

我们在使用`new 对象名`的时候光标是在`(|)`里面的，我们可以使用`tab`键跳出到括号外面，也可以使用`;`跳出括号





## 代码注释

![image-20230201162905499](E:\学习笔记\img\image-20230201162905499.png)

```java
// 修改前
//   	System.out.println(1);

// 修改后
	 // System.out.println(1);
```



## 复制整行

原来设置的是复制选择的部分，`Duplicate Line Or Selection`，现在我们需要将它的快捷键放到`Duplicate Entire Lines`这一选项中。我这里设置的是`ctrl + D`，

<img src="E:\学习笔记\img\image-20230201182506441.png" alt="image-20230201182506441" style="zoom: 80%;" />





## 查看代码

### 标签

选定行，然后按下F11就可以打上标签

![image-20230131131118072](E:\学习笔记\img\image-20230131131118072.png)

在标签控制台就可以看到我们的标签了，点击它就能跳转到打标签的地方



### 待办项

在注释中写`TODO + 空格 + 内容`即可标记当前未完成的功能，等待下次完成代码

```java
// TODO 保存用户到redis中
```

![image-20230131131400113](E:\学习笔记\img\image-20230131131400113.png)

在TODO控制台即可查看和跳转



### 依赖关系

选择需要查看的类，点击右键找到`diagrams`，点击选择即可，也可以使用快捷键

<img src="E:\学习笔记\img\image-20230131132016781.png" alt="image-20230131132016781" style="zoom:67%;" />

这里我们选择HashMap做例子，选择Map，然后按F4可以查看Map的源码



### 时序图

需要安装插件`sequenceDiagram`

使用方法：选中你要查看的方法，然后右键找到`Sequence Diagram`选项即可查看

![image-20230131132515113](E:\学习笔记\img\image-20230131132515113.png)





# 插件

## 规范扫描

### Alibaba Java Coding Guidelines

阿里巴巴Java代码归约





## 美化

### Atom Material Icons 

文件夹主题



### Nyan Progress Bar

彩虹进度条



### Rainbow Brackets

彩色括号







## 生成工具

### GenmerateAllSetter

生成getter和setter方法



### MapStruct Support

MapStruct支持





## 仓库管理

Gitee



## 搜索

### maven-search

maven依赖搜索



## 提示

### Key Promoter X

快捷键提示





## 代码预览

### CodeGlance Pro

生成和vs code一样的左边预览区

![image-20230204120233346](E:\学习笔记\img\image-20230204120233346.png)













