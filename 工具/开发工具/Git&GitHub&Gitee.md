# git概述

Git 是一个免费的、开源的分布式版本控制系统，可以快速高效地处理从小型到大型的各种
项目。
Git 易于学习，占地面积小，性能极快。 它具有廉价的本地库，方便的暂存区域和多个工作
流分支等特性。 其性能优于 Subversion、 CVS、 Perforce 和 ClearCase 等版本控制工具。

## 什么是版本控制？

版本控制是一种记录文件内容变化，以便将来查阅特定版本修订情况的系统。
版本控制其实最重要的是可以记录文件修改历史记录，从而让用户能够查看历史版本，
方便版本切换。  



## **为什么需要版本控制**  

我们从个人过度到团队，团队之间进行协作

![image-20210430200503860](E:\学习笔记\img\20210726004858.png)

比如两个人来进行合作，第一个人上传了一个版本，我们第二个人就是要在第一个版本来进行修改形成第二个版本，这样的话就可以进行更好的合作



### 版本控制工具  

**集中式版本控制工具**  

集中化的版本控制系统诸如 CVS、 SVN 等  ，都有一个单一的服务器，保存所有的文件和修订版本，团队协作的人只需要连接到这台服务器，然后取出最新的文件或者更新文件。

**好处**：每个人在一定程度上看到项目中的其他人正在干什么。而管理员页可以轻松掌握每个开发者的权限

**坏处**：服务器的单点故障，也就是服务器宕机，那么谁也无法提交更新，也就无法协同合作，很不利于我们的工作开展

![image-20210430201336222](E:\学习笔记\img\20210726004927.png)



**分布式版本控制工具**  

Git、 Mercurial、 Bazaar、 Darcs……  

Git、 Mercurial、 Bazaar、 Darcs……
像 Git 这种分布式版本控制工具，客户端提取的不是最新版本的文件快照，而是把代码
仓库完整地镜像下来（本地库）。这样任何一处协同工作用的文件发生故障，事后都可以用
其他客户端的本地仓库进行恢复。因为每个客户端的每一次文件提取操作，实际上都是一次
对整个文件仓库的完整备份。
分布式的版本控制系统出现之后,解决了集中式版本控制系统的缺陷:

1.  服务器断网的情况下也可以进行开发（因为版本控制是在本地进行的）
2.  每个客户端保存的也都是整个完整的项目（包含历史记录， 更加安全） 

![image-20210430201524239](E:\学习笔记\img\20210726004947.png)

我们需要从远程仓库pull到本地仓库或者本地仓库push到远程仓库



## git的工作机制

![image-20210430201626521](E:\学习笔记\img\20210726005043.png)

**工作区**：也就是我们写代码保存的目录

**临时存储**：我们要让git知道我们想要传到本地仓库的代码在哪里，所以需要将我们的工作区的文件添加到暂存区

**本地仓库**：我们的代码在本地存放的地方

**历史版本**：就是我们每一次commit到本地仓库都会形成一个版本，那么这个就是历史版本，历史版本是不能进行删除的，如果你想修改内容，你需要提交一个新的版本，我们新的版本是基于我们的历史版本的，所以是不能被删除的

**远程仓库**：将我们本地仓库的代码放到远程的仓库，那么访问这个仓库的人都可以看到我们的代码



**代码托管中心**

代码托管中心是基于网络服务器的远程代码仓库，一般我们简单称为远程库。

-   局域网

    GitLab

-   互联网
    GitHub（外网）
    Gitee 码云（国内网站）  



# git安装

官方：https://git-scm.com/

![image-20210430204734755](E:\学习笔记\img\image-20210430204734755.png)

选择 Git 安装位置，要求是非中文并且没有空格的目录，然后下一步。  

![image-20210430204748370](E:\学习笔记\img\image-20210430204748370.png)

Git 选项配置，推荐默认设置，然后下一步  。

![image-20210430204804236](E:\学习笔记\img\image-20210430204804236.png)

Git 安装目录名，不用修改，直接点击下一步。  

![image-20210430204817846](E:\学习笔记\img\image-20210430204817846.png)

Git 的默认编辑器，建议使用默认的 Vim 编辑器，然后点击下一步。  

![image-20210430204837848](E:\学习笔记\img\image-20210430204837848.png)

默认分支名设置，选择让 Git 决定，分支名默认为 master，下一步。  ![image-20210430204854472](E:\学习笔记\img\image-20210430204854472.png)

修改 Git 的环境变量，选第一个，不修改环境变量，只在 Git Bash 里使用 Git  。

![image-20210430204908606](E:\学习笔记\img\image-20210430204908606.png)

选择后台客户端连接协议，选默认值 OpenSSL，然后下一步。  

![image-20210430204919158](E:\学习笔记\img\image-20210430204919158.png)

配置 Git 文件的行末换行符， Windows 使用 CRLF， Linux 使用 LF，选择第一个自动转换，然后继续下一步。  

![image-20210430204937652](E:\学习笔记\img\image-20210430204937652.png)

选择 Git 终端类型，选择默认的 Git Bash 终端，然后继续下一步。  

![image-20210430204953211](E:\学习笔记\img\image-20210430204953211.png)

选择 Git pull 合并的模式，选择默认，然后下一步。  

![image-20210430205017287](E:\学习笔记\img\image-20210430205017287.png)

选择 Git 的凭据管理器，选择默认的跨平台的凭据管理器，然后下一步。  

![image-20210430205029290](E:\学习笔记\img\image-20210430205029290.png)

其他配置，选择默认设置，然后下一步。  

![image-20210430205040507](E:\学习笔记\img\image-20210430205040507.png)

实验室功能， 技术还不成熟， 有已知的 bug，不要勾选，然后点击右下角的 Install按钮，开始安装 Git  

![image-20210430205059063](E:\学习笔记\img\image-20210430205059063.png)

点击 Finsh 按钮， Git 安装成功！  

![image-20210430205117939](E:\学习笔记\img\image-20210430205117939.png)

## 测试

鼠标右键打开Git Bash 终端里输入 git --version 查看 git 版本，如下所示，说明 Git 安装成功  

```shell
$ git --version
git version 2.31.1.windows.1
```



# Git常用命令

| 命令名称                             | 作用           |
| ------------------------------------ | -------------- |
| git config --global user.name 用户名 | 设置用户签名   |
| git config --global user.email 邮箱  | 设置用户签名   |
| git init                             | 初始化本地库   |
| git status                           | 查看本地库状态 |
| git add 文件名                       | 添加到暂存区   |
| git commit -m "日志信息" 文件名      | 提交到本地库   |
| git reflog                           | 查看历史记录   |
| git reset --hard 版本号              | 版本穿梭       |



## 设置用户签名

```shell
# 基本语法
git config --global user.name 用户名
git config --global user.email 邮箱  
```

**实操案例**

```shell
git config --global user.name xiaozhi
git config --global user.email xiaozhi@qq.com	# 这个邮箱是虚拟的，可以随意填写
```

完成之后我们可以打开C	->	用户	->	当前的用户目录下，勾选显示隐藏文件，找到	.gitconfig	文件，打开这个文件

![image-20210430205824408](E:\学习笔记\img\image-20210430205824408.png)

可以看到我们之前填写的信息

**说明**：签名的作用是区分不同操作者身份。用户的签名信息在每一个版本的提交信息中能够看到，以此确认本次提交是谁做的。 Git 首次安装**必须设置**一下用户签名，否则无法提交代码。

**注意**： 这里设置用户签名和将来登录 GitHub（或其他代码托管中心）的账号没有任何关系。  





## 设置代理

因为Github是国外的网站，有时候访问很慢。所以可以使用vpn去请求，这个时候就要设置一下git的代理。



### 设置代理

```sh
# http | https
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```



### 查看代理

```sh
 git config --global --get http.proxy
 git config --global --get https.proxy
```



### 取消代理

```sh
git config --global --unset http.proxy
git config --global --unset https.proxy
```





## 初始化本地库 

告诉git本地仓库在哪，并进行初始化



**测试**

找到要初始化的目录，右键打开Git bash，然后使用命令进行初始化

```shell
猛男@LAPTOP-EGA99NIR MINGW64 /e/Java/Git-Space/git-demo (master)
$ git init
Initialized empty Git repository in E:/Java/Git-Space/git-demo/.git/
# 查看当前目录下有哪些目录
$ ll -a
total 4
drwxr-xr-x 1 猛男 197121 0 Apr 30 21:02 ./
drwxr-xr-x 1 猛男 197121 0 Apr 30 16:56 ../
drwxr-xr-x 1 猛男 197121 0 Apr 30 21:02 .git/

$ cd ./.git/
# 查看git初始化生成了那些文件
$ ll
total 7
-rw-r--r-- 1 猛男 197121  23 Apr 30 21:02 HEAD
-rw-r--r-- 1 猛男 197121 130 Apr 30 21:02 config
-rw-r--r-- 1 猛男 197121  73 Apr 30 21:02 description
drwxr-xr-x 1 猛男 197121   0 Apr 30 21:02 hooks/
drwxr-xr-x 1 猛男 197121   0 Apr 30 21:02 info/
drwxr-xr-x 1 猛男 197121   0 Apr 30 21:02 objects/
drwxr-xr-x 1 猛男 197121   0 Apr 30 21:02 refs/
```





## 查看本地仓库状态

```shell
# 查看状态，一定要在仓库初始化的位置查看
$ git status
On branch master	# 在那个分支下

No commits yet		# 表示当前仓库为空

nothing to commit (create/copy files and use "git add" to track)	# 没有可以提交的文件
```

**测试**：我们来创建一个文件，然后再次查看

```sh
$ vim hello.txt
# 在编辑器里面yy是复制，p是粘贴
hello,world!!!
hello,world!!!
......
# 然后保存退出
# 然后进行查看
$ cat hello.txt
hello,world!!!
hello,world!!!
......
```

再次进行查看

```sh
$ git status
On branch master

No commits yet

# 发现了未被git追踪的文件，它提示你可以add添加到暂存区
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```



首次查看仓库



## 添加暂存区

基本语法

```sh
git add 文件名
```

测试

```sh
$ git add hello.txt
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory

# 再次进行查看
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hello.txt
# 提示可以使用rm 命令删除我们暂存区的内容
```



## 提交到本地库

基本语法

```sh
git commit -m "日志信息" 文件名
```

测试

```sh
$ git commit -m "my first commit" hello.txt
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory
[master (root-commit) eee2ae5] my first commit
 1 file changed, 11 insertions(+)
 create mode 100644 hello.txt

# 再次查看
$ git status
On branch master
nothing to commit, working tree clean
```

**查看版本信息命令**

```sh
猛男@LAPTOP-EGA99NIR MINGW64 /e/Java/Git-Space/git-demo (master)
$ git reflog	# 查看版本
eee2ae5 (HEAD -> master) HEAD@{0}: commit (initial): my first commit	# 前面的一串代码是精简版的版本号

猛男@LAPTOP-EGA99NIR MINGW64 /e/Java/Git-Space/git-demo (master)
$ git log		# 查看版本详细信息
commit eee2ae5b4b6e0d94dfe37e2348ef8fd660c60c17 (HEAD -> master)	# 这一串代码是它的完整版本号
Author: xiaozhi <xiaozhi@qq.com>		# 谁提交的
Date:   Fri Apr 30 22:37:39 2021 +0800	# 时间

    my first commit	# 日志信息

```



## 修改文件

首先我们来修改一下文件，再查看我们工作区的状态

![image-20210502113847674](E:\学习笔记\img\image-20210502113847674.png)

所以我们要让git追踪我们修改过的文件

![image-20210502113939626](E:\学习笔记\img\image-20210502113939626.png)

现在就已经将我们修改的文件添加到了暂存区，然后我们就可以commit到我们的本地仓库了

![image-20210502114147224](E:\学习笔记\img\image-20210502114147224.png)

**注意**：在git里面的修改是将之前内容删除，然后将修改的内容插入进来，所以日志中显示两行插入和两行删除

**查看历史版本**

![image-20210502114625448](E:\学习笔记\img\image-20210502114625448.png)

可以看到提交的第二个版本



## 历史版本

```sh
# 基本语法
git reflog 查看版本信息
git log 查看版本详细信息  

# 测试
$ git reflog
ded5477 (HEAD -> master) HEAD@{0}: commit: second version
eee2ae5 HEAD@{1}: commit (initial): my first commit
```

![image-20210502131113887](E:\学习笔记\img\image-20210502131113887.png)

**发现**：log只能查看当前以及历史版本信息，而reflog是可以查看所有的操作记录



## 版本穿梭

**说明**：版本穿梭就是可以在提交的版本之间进行切换，它的实现原理就是通过HEAD指针来进行指定的，HEAD指定某一个分支，然后由我们的分支来指定版本，从而来达到版本切换

![image-20210502115212427](E:\学习笔记\img\image-20210502115212427.png)

```sh
# 基本语法
git reset --hard 版本号

# 测试
# 查看历史版本
$ git reflog
ded5477 (HEAD -> master) HEAD@{0}: commit: second version
eee2ae5 HEAD@{1}: commit (initial): my first commit

# 回退到第一版本
$ git reset --hard eee2ae5
HEAD is now at eee2ae5 my first commit

# 查看文件，我们第二版本是有在第二行加了数字的
$ cat hello.txt
hello,world!!!
hello,world!!!
......
```

在工作区的refs目录下的heads目录中有一个master文件，这个文件的文件内容就是我们当前的版本号

![image-20210502130734718](E:\学习笔记\img\image-20210502130734718.png)





# Git分支操作

## 什么是分支？

在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务的单独分支。使用分支意味着程序员可以把自己的工作从开发主线上分离开来， 开发自己分支的时候，不会影响主线分支的运行 

![image-20210502145130926](E:\学习笔记\img\image-20210502145130926.png)

**说明**：可以看得出来，我们有四个三个分支，master是主分支，hont-fix是出问题进行维修的分支，其余的两个分支是增加功能的分支，多个分支之间同时进行开发，相互不会有影响，当我们的分支的工作完成之后就可以和主分支进行合并



**分支的好处**

同时并推进多个功能的开发，大大提高开发效率

分支之间相互不影响，如果一个分支开发失败，是不会影响到其他的分支的，失败的分支删除重新开始即可



**分支的实现原理**

![image-20210502154224218](E:\学习笔记\img\image-20210502154224218.png)

其他分支在创建的时候会将主分支的内容复制一份，然后在这基础上进行开发，我们通过HEAD指针来进行分支之间的切换





## 分支的操作命令

| 命令名称            | 作用                         |
| ------------------- | ---------------------------- |
| git branch 分支名   | 创建分支                     |
| git branch -v       | 查看分支                     |
| git checkout 分支名 | 切换分支                     |
| git merge 分支名    | 把指定的分支合并到当前分支上 |



### 查看分支

```sh
# git branch -v 	查看分支
$ git branch -v
* master eee2ae5 my first commit		# *表示当前所在分区
```



### 创建分区

```sh
# git branch 分支名	创建分支
$ git branch test

猛男@LAPTOP-EGA99NIR MINGW64 /e/Java/Git-Space/git-demo (master)
# 查看分支
$ git branch -v
* master eee2ae5 my first commit
  test   eee2ae5 my first commit
```

**说明**：我们创建新的分支，新的分区会将主分支的内容复制一份，在主分支的基础上进行操作



### 修改分区

我们在master分支上进行操作，其他其他分支会不会改变

```sh
# 修改文件内容
vim hello.txt

$ cat hello.txt
hello,world!!!
hello,world!!!  22222
hello,world!!!  33333	# 第三次版本添加的
.....
# 然后提交到本地库
# 查看版本信息
$ git log
commit 67b9635765bafcf1eef3541977f5d99f7c7e3d94 (HEAD -> master)
Author: xiaozhi <xiaozhi@qq.com>
Date:   Sun May 2 15:09:34 2021 +0800

    第三次版本提交

commit ded54776297b34cf87e24d623959c8fd5af98e52
Author: xiaozhi <xiaozhi@qq.com>
Date:   Sun May 2 11:40:40 2021 +0800

    second version

commit eee2ae5b4b6e0d94dfe37e2348ef8fd660c60c17 (test)
Author: xiaozhi <xiaozhi@qq.com>
Date:   Fri Apr 30 22:37:39 2021 +0800

    my first commit
```

切换到test分支中查看是否改变



### 切换分区

```sh
# git checkout 分支名
```

![image-20210502151448567](E:\学习笔记\img\image-20210502151448567.png)

查看内容是否发生改变

![image-20210502151551091](E:\学习笔记\img\image-20210502151551091.png)

**结果显示**：主分支改变，其他分支不会跟着改变



### 合并分支

在test分支中修改内容，然后跟master分支进行合并

```sh
$ cat hello.txt
hello,world!!!
hello,world!!!
hello,world!!!
hello,world!!!  4444
......
```

在master分支上进行合并

```sh
# git merge 分支名		基本语法
$ git merge test
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.
```



#### 产生冲突

冲突产生的表现，后面状态为 **MERGING**  

```sh
猛男@LAPTOP-EGA99NIR MINGW64 /e/Java/Git-Space/git-demo (master|MERGING)
$
```

查看冲突的文件

```sh
$ cat hello.txt
<<<<<<< HEAD
hello,world!!!
hello,world!!!  22222
hello,world!!!  33333
hello,world!!!
=======
hello,world!!!
hello,world!!!
hello,world!!!
hello,world!!!  4444
>>>>>>> test
hello,world!!!
......
```



**冲突的原因**

合并分支时，两个分支在同一个位置操作同一个文件有两套不同的修改，Git无法替我们决定使用哪一个。必须人为决定新代码的内容



查看状态

![image-20210502152642555](E:\学习笔记\img\image-20210502152642555.png)



#### 解决冲突

编辑有冲突的文件，删除特殊符号，决定要使用的内容  

```sh
特殊符号： <<<<<<< HEAD 当前分支的代码 ======= 合并过来的代码 >>>>>>> test
```

修改之后的文件如下

![image-20210502152949586](E:\学习笔记\img\image-20210502152949586.png)

接下来添加到暂存区，然后提交到本地库即可

**注意**：提交的时候不能带文件名，带有文件名就会提交不成功

![image-20210502153544320](E:\学习笔记\img\image-20210502153544320.png)

查看文件是否合并成功

![image-20210502153645868](E:\学习笔记\img\image-20210502153645868.png)





# Git 团队协作机制  

## 团队内协作

![image-20210502155437873](E:\学习笔记\img\image-20210502155437873.png)

**说明**：老师开发了一个软件，想让学生做点小功能，老师就将他写好的代码上传到远程库，然后学生从远程库克隆下来，学生将代码修改完成之后push到远程库，前提是学生已经得到了老师的允许（也就是权限），然后老师就可以pull到本地库来更新他的代码，这样就完成了协作开发





## 跨团队协作

![image-20210502161052630](E:\学习笔记\img\image-20210502161052630.png)

说明：老师觉得自己写的代码不太行，就找大佬帮忙改一下，然而大佬并不是老师团队的成员，大佬通过fork到他的远程库，然后pull下来到本地，修改完成之后push到远程仓库，然后就发给我们老师了，那么因为大佬不是团队成员，所以他是没有权限直接上传到老师的远程库的，他可以通过pull request发送请求，我们接受到请求之后进行审核，审核通过后就合并到我们的远程仓库，到此就完成了我们的跨团队协作，不得不说，大佬牛逼~~~



# GitHub操作

| 命令名称                           | 作用                                                      |
| ---------------------------------- | --------------------------------------------------------- |
| git remote -v                      | 查看当前所有远程地址别名                                  |
| git remote add 别名 远程地址       | 起别名                                                    |
| git push 别名 分支                 | 推送本地分支上的内容到远程仓库                            |
| git clone 远程地址                 | 将远程仓库的内容克隆到本地                                |
| git pull 远程库地址别名 远程分支名 | 将远程仓库对于分支最新内容拉下来后与 当前本地分支直接合并 |



## 创建远程仓库

![image-20210502163708725](E:\学习笔记\img\image-20210502163708725.png)

![image-20210502163829454](E:\学习笔记\img\image-20210502163829454.png)

![image-20210502164208086](E:\学习笔记\img\image-20210502164208086.png)



## 使用git连接到远程仓库

```sh
# 基本语法
# git remote -v 查看当前所有远程地址别名
# git remote add 别名 远程地址
```

**测试**

```sh
# 添加远程仓库并起别名
$ git remote add test https://github.com/mazhi-oss/git-demo.git
# 查看是否有添加
$ git remote -v
test    https://github.com/mazhi-oss/git-demo.git (fetch)
test    https://github.com/mazhi-oss/git-demo.git (push)r 
```



## 推送本地分支到远程仓库

```sh
# git push 别名 分支
$ git push test master
Enumerating objects: 15, done.
Counting objects: 100% (15/15), done.
Delta compression using up to 8 threads
Compressing objects: 100% (10/10), done.
Writing objects: 100% (15/15), 1.13 KiB | 385.00 KiB/s, done.
Total 15 (delta 4), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (4/4), done.
To https://github.com/mazhi-oss/git-demo.git
 * [new branch]      master -> master
```

我们在github中进行查看

![image-20210502210601124](E:\学习笔记\img\image-20210502210601124.png)





## 克隆远程仓库到本地

![image-20210502210713580](E:\学习笔记\img\image-20210502210713580.png)

```sh
# git clone 远程地址
$ git clone http://github.com/mazhi-oss/git-demo.git
Cloning into 'git-demo'...
warning: redirecting to https://github.com/mazhi-oss/git-demo.git/
remote: Enumerating objects: 15, done.
remote: Counting objects: 100% (15/15), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 15 (delta 4), reused 15 (delta 4), pack-reused 0
Receiving objects: 100% (15/15), done.
Resolving deltas: 100% (4/4), done.
```

![image-20210502211231814](E:\学习笔记\img\image-20210502211231814.png)

**小结**： clone 会做如下操作。 1、拉取代码。 2、初始化本地仓库。 3、创建别名  

 

## 邀请加入团队

![image-20210502211602143](E:\学习笔记\img\image-20210502211602143.png)

![image-20210502211652099](E:\学习笔记\img\image-20210502211652099.png)

![image-20210502211901051](E:\学习笔记\img\image-20210502211901051.png)

邀请人同意加入团队

![image-20210502211936217](E:\学习笔记\img\image-20210502211936217.png)

![image-20210502212005633](E:\学习笔记\img\image-20210502212005633.png)

成功加入！

**测试**

使用伙伴的本地仓库将项目克隆下来

对代码进行修改

```sh
$ cat hello.txt
hello,world!!!
hello,world!!!  22222
hello,world!!!  33333
hello,world!!!  4444
hello,world!!!
hello,world!!!  我是你最可爱的小伙伴
......
```

修改文件之后commit，然后push到远程仓库

```sh
$ git push origin master
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 296 bytes | 296.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/mazhi-oss/git-demo.git
   03cdf79..672098d  master -> master
```

到网页上进行查看

![image-20210502220534747](E:\学习笔记\img\image-20210502220534747.png)

![image-20210502220550515](E:\学习笔记\img\image-20210502220550515.png)





## 拉取远程库内容

我们的小伙伴已经修改了文件，所以我们将最新的文件pull到本地

```sh
# git pull 远程库地址别名 远程分支名
$ git pull test master
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), 276 bytes | 13.00 KiB/s, done.
From https://github.com/mazhi-oss/git-demo
 * branch            master     -> FETCH_HEAD
   03cdf79..672098d  master     -> test/master
Updating 03cdf79..672098d
Fast-forward
 hello.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

![image-20210502221140563](E:\学习笔记\img\image-20210502221140563.png)



## 跨团队协作

我们需要大佬来帮助我们开发，他不属于我们团队成员，所以我们要跨团队协作

首先我们先把仓库的地址发给他

![image-20210503110511563](E:\学习笔记\img\image-20210503110511563.png)

在大佬的账号上输入这个网址，然后fork到他的远程库

![image-20210503110612327](E:\学习笔记\img\image-20210503110612327.png)



![image-20210503110730855](E:\学习笔记\img\image-20210503110730855.png)

我们在大佬的github上编辑文件，然后j进行提交![image-20210503110855288](E:\学习笔记\img\image-20210503110855288.png)

![image-20210503110953661](E:\学习笔记\img\image-20210503110953661.png)

修改完之后我们创建一个请求

![image-20210503111248435](E:\学习笔记\img\image-20210503111248435.png)

![image-20210503111334631](E:\学习笔记\img\image-20210503111334631.png)



回到老师的账号我们就可以看到一个pull request请求

![image-20210503111527146](E:\学习笔记\img\image-20210503111527146.png)

点进去有一个聊天室，双方可以在里面进行沟通交流

![image-20210503111616080](E:\学习笔记\img\image-20210503111616080.png)

![image-20210503111918049](E:\学习笔记\img\image-20210503111918049.png)

然后就可以看到我们合并之后的代码了

![image-20210503112017893](E:\学习笔记\img\image-20210503112017893.png)



## SSH 免密登录  

为了使用方便，建议使用ssh免密登录

![image-20210503112147421](E:\学习笔记\img\image-20210503112147421.png)

复制后进入当前的家目录下打开Git，我的家目录在	C:\Users\猛男

```sh
# 运行命令生成.ssh密钥目录
$ ssh-keygen -t rsa -C xiaozhi@123.com		# -C后面的信息可以随便写，标记
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/猛男/.ssh/id_rsa):		# 回车
Created directory '/c/Users/\347\214\233\347\224\267/.ssh'.
Enter passphrase (empty for no passphrase):		# 回车
Enter same passphrase again:				   # 回车
Your identification has been saved in /c/Users/猛男/.ssh/id_rsa
Your public key has been saved in /c/Users/猛男/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:g2a1cFOZtHO+15yJjzMThnZSn3EJ9eTNLRAW4CWqygM xiaozhi@123.com
The key's randomart image is: 
+---[RSA 3072]----+
|         .=+*o...|
|         +o= o ++|
|      . = + . o B|
|       * o + . +.|
|  E   = S   + . +|
|   o +   . + =.=o|
|    +     . =.ooo|
|     .       =o  |
|             .+. |
+----[SHA256]-----+

$ cd .ssh/
# 查看目录内容
$ ll -a
total 21
drwxr-xr-x 1 猛男 197121    0 May  3 11:26 ./
drwxr-xr-x 1 猛男 197121    0 May  3 11:26 ../
-rw-r--r-- 1 猛男 197121 2602 May  3 11:26 id_rsa
-rw-r--r-- 1 猛男 197121  569 May  3 11:26 id_rsa.pub

# 查看文件内容，复制那一串代码
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC3aKJ9y4K00xAh8Lmfpw6/ZCfhN4bhqbFU46ScmhX7hoeP69xIz3nneCfiuv0vQFlbkPdrwtrJuVTzMbUDrGqJfp6U4sdjo3ctb5v8S0TZNb4JF/rxUeWH6vBy4gFFB2AD00LMWlqAy5lVK+6WzzhRegeEE3Cg+2LF7kavGi6UrE0Srzq+64zLq3Q1dRA02DqiqdJHo9+OAoYxgRaCE0faoN1JUehnbC8rFouJX4oiF2vWl6JL87eC7+KFpTZ64ComaHsf64uFxkNZE/zE/eajjDOBd/FaAQC47lspjQpkaLwyZg2RgFtpcDT+16acbtYMsOv+K9QcDSX7GIb/rb/kLISyZSUR8tduNS6trpF8PfdL4hcO4Uedq02k2QK5qdZ54ZIDRohW0dumR9jbBlgRgiT4D46N1u2CLL3ZENtQBjrzcJslfolAp9kX03DtTl2AtfbivYAcfw7PRadnynG+tReeHNJ5x2qR9BEUuPN3W3Q+E1oh7i3HwqNEYV13Tj0= xiaozhi@123.com
```

复制 id_rsa.pub 文件内容，登录 GitHub，点击用户头像→Settings→SSH and GPG keys  

![image-20210503113221594](E:\学习笔记\img\image-20210503113221594.png)

![image-20210503113341562](E:\学习笔记\img\image-20210503113341562.png)

然后点击添加即可！

接下来再往远程仓库 push 东西的时候使用 SSH 连接就不需要登录了。  



# IDEA 集成 Git  

## 配置 Git 忽略文件  

**Eclipse 特定文件**  

![image-20210503114024645](E:\学习笔记\img\image-20210503114024645.png)

**IDEA特定文件**

![image-20210503114128041](E:\学习笔记\img\image-20210503114128041.png)

**Maven 工程的 target 目录**  

![image-20210503114147251](E:\学习笔记\img\image-20210503114147251.png)

**问题 1**:为什么要忽略他们？
因为他们与项目的实际功能无关，不参与服务器上部署运行。把它们忽略掉能够屏蔽 IDE 工具之间的差异。

**问题 2**：怎么忽略？
创建忽略规则文件 xxxx.ignore（前缀名随便起，建议是 git.ignore,这个文件的存放位置原则上在哪里都可以，为了便于让~/.gitconfig 文件引用，建议也放在用户家目录下  

**git.ignore 文件模版内容**如

```sh
# Compiled class file
*.class
# Log file
*.log
# BlueJ files
*.ctxt
# Mobile Tools for Java (J2ME)
.mtj.tmp/
# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar
# virtual machine crash logs, see
http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*
.classpath
.project
.settings
target
.idea
*.iml
```

2	在.gitconfig 文件中引用忽略配置文件（此文件在 Windows 的家目录中）

```sh
# 最后面加上
[core]
excludesfile = C:/Users/asus/git.ignore
# 注意：这里要使用“正斜线（/）”，不要使用“反斜线（\）”  
```



## 定位 Git 程序  

![image-20210504011547951](E:\学习笔记\img\image-20210504011547951.png)



## 初始化本地库  

![image-20210504012556177](E:\学习笔记\img\image-20210504012556177.png)

初始化当前项目



## 添加到暂存区  

![image-20210504012842989](E:\学习笔记\img\image-20210504012842989.png)

![image-20210504013002341](E:\学习笔记\img\image-20210504013002341.png)

提交之后文件会变成绿色

![image-20210504013044024](E:\学习笔记\img\image-20210504013044024.png)



## 提交到本地库  

![image-20210504013242786](E:\学习笔记\img\image-20210504013242786.png)

![image-20210504013324139](E:\学习笔记\img\image-20210504013324139.png)

提交之后文件就变成了黑色

## 切换版本

![image-20210504013918062](E:\学习笔记\img\image-20210504013918062.png)

选中要切换的版本，右键打开选项面板

![image-20210504014014329](E:\学习笔记\img\image-20210504014014329.png)

![image-20210504014028330](E:\学习笔记\img\image-20210504014028330.png)



## 创建分支  

**第一种方式**

![image-20210504014231901](E:\学习笔记\img\image-20210504014231901.png)

![image-20210504014321030](E:\学习笔记\img\image-20210504014321030.png)

**第二种方式**

![image-20210504014340922](E:\学习笔记\img\image-20210504014340922.png)



## 切换分支  

![image-20210504114938092](E:\学习笔记\img\image-20210504114938092.png)

![image-20210504114947764](E:\学习笔记\img\image-20210504114947764.png)

黄色的是HEAD指针，绿色的是分支指针



## 合并分支  

![image-20210504115234049](E:\学习笔记\img\image-20210504115234049.png)

进入到主界面中点击merge合并



## 解决冲突  

![image-20210504115318078](E:\学习笔记\img\image-20210504115318078.png)

这个界面就说明我们有代码冲突的问题

x	按钮就是删除这一行代码

》和《 就是移到合并的代码中

代码冲突解决，自动提交本地库。  

![image-20210504115646721](E:\学习笔记\img\image-20210504115646721.png)

# IDEA 集成 GitHub  

## 设置 GitHub 账号  

![image-20210504115932104](E:\学习笔记\img\image-20210504115932104.png)

账号登录可能会因为网络的原因登不上，所以我们使用令牌的方式登录

登录github，点击右上角头像，找到settings	

![image-20210504120120038](E:\学习笔记\img\image-20210504120120038.png)	

![image-20210504120218791](E:\学习笔记\img\image-20210504120218791.png)

![image-20210504120331367](E:\学习笔记\img\image-20210504120331367.png)

![image-20210504120404549](E:\学习笔记\img\image-20210504120404549.png)

![image-20210504120446329](E:\学习笔记\img\image-20210504120446329.png)

这个token浏览器一刷新就没了，所以记得保存下来

![image-20210504120611923](E:\学习笔记\img\image-20210504120611923.png)

我们就登录成功了！！！



## 分享工程到github

![image-20210504160920302](E:\学习笔记\img\image-20210504160920302.png)

IDEA会帮我们在github上创建仓库

![image-20210504161203453](E:\学习笔记\img\image-20210504161203453.png)

![image-20210504161341997](E:\学习笔记\img\image-20210504161341997.png)

创建成功呢，打开github就可以看到我们刚才创建的仓库

![image-20210504161308054](E:\学习笔记\img\image-20210504161308054.png)

进入仓库就可以看到我们的项目了

![image-20210504161446557](E:\学习笔记\img\image-20210504161446557.png)



## push本地库到远程库

![image-20210504161806600](E:\学习笔记\img\image-20210504161806600.png)

这里我们自定义一个

![image-20210504162119837](E:\学习笔记\img\image-20210504162119837.png)

![image-20210504162140396](E:\学习笔记\img\image-20210504162140396.png)

![image-20210504162511912](E:\学习笔记\img\image-20210504162511912.png)

就可以看到我们push的项目了

![image-20210504162607054](E:\学习笔记\img\image-20210504162607054.png)



## pull 拉取远程库到本地库 

我们在github上进行修改

![image-20210504162816740](E:\学习笔记\img\image-20210504162816740.png)

将修改过的项目pull到本地仓库

![image-20210504163036228](E:\学习笔记\img\image-20210504163036228.png)

![image-20210504163305566](E:\学习笔记\img\image-20210504163305566.png)



## clone克隆远程库到本地

![image-20210504163434268](E:\学习笔记\img\image-20210504163434268.png)

![image-20210504163534011](E:\学习笔记\img\image-20210504163534011.png)

就将项目克隆了下来

![image-20210504163708504](E:\学习笔记\img\image-20210504163708504.png)



# 国内代码托管中心-码云

## 简介  

众所周知， GitHub 服务器在国外， 使用 GitHub 作为项目托管网站，如果网速不好的话，严重影响使用体验，甚至会出现登录不上的情况。针对这个情况， 大家也可以使用国内的项目托管网站-码云。  

码云是开源中国推出的基于 Git 的代码托管服务中心， 网址是 https://gitee.com/ ，使用方式跟 GitHub 一样，而且它还是一个中文网站，如果你英文不是很好它是最好的选择。  



## 码云创建远程库

基本的操作和github差不多

点击首页右上角的加号，选择下面的新建仓库  

![image-20210504163933827](E:\学习笔记\img\image-20210504163933827.png)

![image-20210504163949123](E:\学习笔记\img\image-20210504163949123.png)

![image-20210504164149985](E:\学习笔记\img\image-20210504164149985.png)



## IDEA集成gitee

**安装插件**

idea默认不带gitee插件的，需要手动安装Gitee插件。插件商店搜搜Gitee进行安装就好了

然后添加账号，跟github的操作差不多

![image-20210504165425435](E:\学习笔记\img\image-20210504165425435.png)



## IDEA连接Gitee

Idea 连接码云和连接 GitHub 几乎一样，首先在 Idea 里面创建一个工程，初始化 git 工程，然后将代码添加到暂存区，提交到本地库。



### 分享到gitee上

![image-20210504170005355](E:\学习笔记\img\image-20210504170005355.png)



### 将本地库push到gitee远程库

![image-20210504170111686](E:\学习笔记\img\image-20210504170111686.png)

自定义一个链接

![image-20210504170251235](E:\学习笔记\img\image-20210504170251235.png)

点击push，然后我们在码云上就能看见push的文件了

**发现问题**：使用用户名的登录方式push不了

**解决**：使用邮箱的方式登录就可以了

![image-20210504171949363](E:\学习笔记\img\image-20210504171949363.png)

==只要码云远程库链接定义好以后， 对码云远程库进行 pull 和 clone 的操作和 Github 一致，这里不做过多操作==



## 码云复制 GitHub 项目

来到新建仓库界面

![image-20210504172200400](E:\学习笔记\img\image-20210504172200400.png)

然后将 GitHub 的远程库 HTTPS 链接复制过来，点击创建按钮即可。  

![image-20210504172256854](E:\学习笔记\img\image-20210504172256854.png)

![image-20210504172343309](E:\学习笔记\img\image-20210504172343309.png)

==如果 GitHub 项目更新了以后，在码云项目端可以手动重新同步，进行更新！==

![image-20210504172528743](E:\学习笔记\img\image-20210504172528743.png)

![image-20210504172630598](E:\学习笔记\img\image-20210504172630598.png)





# 自建代码托管平台-GitLab

## GitLab 简介

GitLab 是由 GitLabInc.开发，使用 MIT 许可证的基于网络的 Git 仓库管理工具，且具有wiki 和 issue 跟踪功能。使用 Git 作为代码管理工具，并在此基础上搭建起来的 web 服务。GitLab 由乌克兰程序员 DmitriyZaporozhets 和 ValerySizov 开发，它使用 Ruby 语言写成。后来，一些部分用 Go 语言重写。截止 2018 年 5 月，该公司约有 290 名团队成员，以及 2000 多名开源贡献者。 GitLab 被 IBM， Sony， JülichResearchCenter， NASA， Alibaba，Invincea， O’ReillyMedia， Leibniz-Rechenzentrum(LRZ)， CERN， SpaceX 等组织使用。  



**官网地址**： https://about.gitlab.com/
**安装说明**： https://about.gitlab.com/installation/  





## GitLab 安装  

### 服务器准备

准备一个系统为 CentOS7 以上版本的服务器， 要求内存 4G，磁盘 50G。关闭防火墙， 并且配置好主机名和 IP，保证服务器可以上网。虚拟经配置	主机名： gitlab-server IP 地址： 192.168.1.1 



### 安装包准备

在线安装：使用yum命令安装 gitlab- ce   

离线 rpm 的方式安装：

下载地址：https://packages.gitlab.com/gitlab/gitlabce/packages/el/7/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm  

下载完成之后上传到服务器/opt/module 目录下 即可



### 编写安装脚本

安装 gitlab 步骤比较繁琐，因此我们可以参考官网编写 gitlab 的安装脚本。  

```sh
# 就是统一将命令放到脚本中
[root@gitlab-server module]# vim gitlab-install.sh
sudo rpm -ivh /opt/module/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm
sudo yum install -y curl policycoreutils-python openssh-server cronie
sudo lokkit -s http -s ssh
sudo yum install -y postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlabce/script.rpm.sh | sudo bash
sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlabce

# 给脚本增加执行权限  
[root@gitlab-server module]# chmod +x gitlab-install.sh

# 然后执行该脚本，开始安装 gitlab-ce。注意一定要保证服务器可以上网。
[root@gitlab-server module]# ./gitlab-install.sh
警告： /opt/module/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm: 头 V4
RSA/SHA1 Signature, 密钥 ID f27eab47: NOKEY
准备中... #################################
[100%]
正在升级/安装...
1:gitlab-ce-13.10.2-ce.0.el7
################################# [100%]
......
```



### 初始化 GitLab 服务 

```sh
# 执行以下命令初始化 GitLab 服务
[root@gitlab-server module]# gitlab-ctl reconfigure
......
Running handlers:
Running handlers complete
Chef Client finished, 425/608 resources updated in 03 minutes 08
seconds
gitlab Reconfigured!
```



### 启动 GitLab 服务  

```sh
[root@gitlab-server module]# gitlab-ctl start
ok: run: alertmanager: (pid 6812) 134s
ok: run: gitaly: (pid 6740) 135s
ok: run: gitlab-monitor: (pid 6765) 135s
ok: run: gitlab-workhorse: (pid 6722) 136s
ok: run: logrotate: (pid 5994) 197s
ok: run: nginx: (pid 5930) 203s
ok: run: node-exporter: (pid 6234) 185s
ok: run: postgres-exporter: (pid 6834) 133s
ok: run: postgresql: (pid 5456) 257s
ok: run: prometheus: (pid 6777) 134s
ok: run: redis: (pid 5327) 263s
ok: run: redis-exporter: (pid 6391) 173s
ok: run: sidekiq: (pid 5797) 215s
ok: run: unicorn: (pid 5728) 221s
```



### 使用浏览器访问 GitLab 

使用主机名或者 IP 地址即可访问 GitLab 服务。**一定要配一下 windows 的 hosts 文件**。  

![image-20210504194211259](E:\学习笔记\img\image-20210504194211259.png)

打开浏览器输入http://gitlab-server进行访问

![image-20210504194156943](E:\学习笔记\img\image-20210504194156943.png)

首次登陆之前，需要修改下 GitLab 提供的 root 账户的密码，要求 8 位以上，包含大小写子母和特殊符号。因此我们修改密码为xiaozhi@123.com

然后使用修改后的密码登录GitLab

  ![image-20210504194509147](E:\学习笔记\img\image-20210504194509147.png)

![image-20210504194616715](E:\学习笔记\img\image-20210504194616715.png)

登录成功！！！



### 创建项目

![image-20210504194949248](E:\学习笔记\img\image-20210504194949248.png)

![image-20210504195049827](E:\学习笔记\img\image-20210504195049827.png)

![image-20210504195216169](E:\学习笔记\img\image-20210504195216169.png)

![image-20210504195416443](E:\学习笔记\img\image-20210504195416443.png)

完成创建！！！



## IDEA集成GitLab

1	IDEA中安装GitLab插件，插件商店搜索安装即可

![image-20210504195648240](E:\学习笔记\img\image-20210504195648240.png)

2	添加GitLab服务

![image-20210504195741867](E:\学习笔记\img\image-20210504195741867.png)

![image-20210504200050262](E:\学习笔记\img\image-20210504200050262.png)

将远程库的链接复制下来

![image-20210504200603084](E:\学习笔记\img\image-20210504200603084.png)

**注意**： gitlab 网页上复制过来的连接是：http://gitlab.example.com/root/gitlab-test.git，需要手动修改为： http://gitlab-server/root/gitlab-test.git

然后在IDEA中点击push，选择自定义链接，将刚才修改过的链接放上去

![image-20210504200837263](E:\学习笔记\img\image-20210504200837263.png)

![image-20210504200858740](E:\学习笔记\img\image-20210504200858740.png)

输入对应的账号密码进行验证即可！！！



打开网页端gitlab进行查看

![image-20210504201222448](E:\学习笔记\img\image-20210504201222448.png)

就可以看到我们push到上面的项目了！！！



其他的操作和github差不多，这里演示一下pull拉取远程库到本地库

![image-20210504201519698](E:\学习笔记\img\image-20210504201519698.png)

![image-20210504201720069](E:\学习笔记\img\image-20210504201720069.png)







# Github使用技巧

## 1、查看热门的库

![image-20220301182852992](E:\学习笔记\img\20220301182854.png)



## 2、搜索

### 2.1 搜索框放大

在搜索框中按下回车键可以进入到纯搜索界面

<img src="E:\学习笔记\img\20220301183131.png" alt="image-20220301183130260"/>

​																							⬇

<img src="E:\学习笔记\img\20220301183158.png" alt="image-20220301183157561" style="zoom:67%;" />



### 2.2 搜索过滤

可以通过关键字来得到我们想要的项目



#### in:name 名字

搜索对应名字的项目

![image-20220301195840513](E:\学习笔记\img\20220301195842.png)



#### in:description 名字

描述里面有这个名字的项目

![image-20220301200202813](E:\学习笔记\img\20220301200204.png)



#### 其他的限定

```java
语法：
// stars:>数值		  表示star数要超过这个数值
// forks:>数值		  表示fork数要超过这个数值
// pushed:>xxxx-xx-xx	表示最后更新的时间在xxxx-xx-xx
// language:java 	    表示是用java语言写的项目
```

![image-20220301201552612](E:\学习笔记\img\20220301201553.png)



#### 相关话题搜索

```
topik 话题
```

![image-20220301201807201](E:\学习笔记\img\20220301201808.png)





# 新改动

## git不支持密码登录

使用SSH秘钥push的时候发生了下面的错误

![image-20230216105603947](E:\学习笔记\img\image-20230216105603947.png)

意思是git自21年8月13后不再支持用户名密码的方式验证，需要创建个人token才能访问

访问的方式：

```sh
# 修改当前的项目
git remote set-url origin  https://<your_token>@github.com/<USERNAME>/<REPO>.git
```

修改完成再次push，成功😋😋😋





## 主分支名字改变

以前主分支的名字是master，现在默认是main



