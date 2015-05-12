# 搭建Elastos开发环境

## 前言
---
首先，热烈欢迎你加入Elastos系统开发，与我们一道学习、研究和研发一款稳定的智能移动终端操作系统。

接下来我将向你介绍如何下载源码、搭建开发环境，以及一些推荐了解的技术资料。我们的开发环境推荐使用 ubuntu 12.04，编辑器推荐 Subline Text 3。

## 环境篇
---

### 注册账户（ElastosID）
---

我们使用 git/gerrit 管理源码，所以你需要在 gerrit 上创建账户（ElastosID），请联系裴老师（<code>pei.xilong@kortide.com</code>）获取 ElastosID 邀请码，并参考如下链接完成 ElastosID 的创建。

[Create Your ElastosID Account](http://elastos.org/wiki/index.php/Create_Your_ElastosID_Account)
[Setting Your ElastosID Account in the Project Management Syetem](http://elastos.org/wiki/index.php/Setting_Your_ElastosID_Account_in_Management_Project_System)

### 下载源码
---

ElastosID 设置好之后，并获得相应权限（请联系 <code>pei.xilong@kortide.com</code>）之后，就可以使用 git 下载源码了。Elastos 最新代码已不再支持模拟器开发，只支持在真实硬件设备上进行开发与调试。

请在 terminal 中输入如下命令下载源码：(请将**YourElastosID**替换为你的 ElastosID)

Elastos 源码：
> git clone ssh://**YourElastosID**@elastos.org:29418/ElastosRDK4_2_2

对应的 Android 源码：
> git clone ssh://**YourElastosID**@elastos.org:29418/KortideInsideProject/Allwinner4_2_2


### 安装工具链
---
* 安装依赖库
    请根据不同的操作系统版本选择相应的命令下载安装链：

    Ubuntu 14.04:
    > $ sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev:i386 zip

    Ubuntu 12.04:
    > $ sudo apt-get install git gnupg flex bison gperf build-essential \
  zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev \
  libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 \
  libgl1-mesa-dev g++-multilib mingw32 tofrodos \
  python-markdown libxml2-utils xsltproc zlib1g-dev:i386

    > $ sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so

* 安装 JDK 6：
    > $ sudo apt-get update

    > $ sudo apt-get install openjdk-6-jdk

    更新环境变量:
    > $ sudo update-alternatives --config java

    > $ sudo update-alternatives --config javac

* 安装 android device:
    下载 70-android.rules：[http://pan.baidu.com/s/1pJDlPQb](http://pan.baidu.com/s/1pJDlPQb)，提取密码：bd27

    下载完毕之后，执行如下命令：
    > $ sudo cp 70-android.rules /etc/udev/rules.d/

    > $ adb kill-server

    > $ adb start-server

    > $ adb devices

    应该能够看到类似如下的输出：
    > 20080411 device

### 编译
---
* 设置编译环境
Elastos 源码下载完毕之后，打开 terminal 进入 ElastosRDKXXX/Setup 目录，运行如下命令设置 arm 平台编译环境：
    > source SetEnv.sh arm_android

    设置编译环境之后，你就可以使用相关编译命令进行编译了：

* 普通编译：
    > emake （编译）
    > emake rebuild（重新编译）
    > emake clobber（清除 target 目录）

* 并行编译：(速度快，推荐)
    > para-emake arm_android

* 拷贝编译结果到设备上
    > eldrop（拷贝 Elastos 相关库）
    > eldrop java（拷贝 android 兼容相关库）

* 重启设备，就能体验Elastos系统了。

* 其他命令
    > pd @: 从当前目录跳转到target目录

    > pd: 从target目录返回到之前目录

### 运行
---

* Elastos控制台程序
    进入ElastosRDKXXX/Sources/Elastos/LibCore/tests/Quintet目录，编译运行该示例：
执行 <code>emake</code> 编译，然后执行 <code>pd @</code>命令跳转到 target 目录，执行 <code>ls</code> 查看是否生成了 testQuintet 可执行文件；若是，执行<code>elcopy testQuintet</code> 命令将生成的可执行文件拷贝到设备中（将被拷贝到设备的/data/data/com.elastos.runtime/elastos目录下）。

    然后，执行<code>adb shell</code>进入adb shell 环境下，执行：<code>cd data/data/com.elastos.runtime/elastos</code> 命令进入该目录，然后执行 <code>ls</code>命令查看是否成功拷贝了 testQuintet 文件。输入命令 <code>./testQuintet</code> 执行程序，应该能看到该程序的输出。

* 运行Elastos App
    进入ElastosRDKXXX/Sources/Elastos/Frameworks/Droid/DevSamples/ImageViewDemo 目录，执行 <code>emake</code>编译，然后执行 <code>pd @</code> 命令跳转到 target 目录，执行 <code>ls</code> 查看是否生成了 ImageViewDemo.epk 文件，然后执行 <code>elcopy ImageViewDemo.epk /data/app</code> 拷贝目录到设备中（将被拷贝到设备的/data/app目录下）。

    然后，重启设备，在系统应用程序界面上就能看到该 app，双击即可运行。

* 运行Android App
    Elastos 系统兼容Android app，将现有的apk文件，通过执行 <code>elcopy XXX.apk /data/app</code> 拷贝目录到设备中（将被拷贝到设备的/data/app目录下）。

    然后，重启设备，在系统应用程序界面上就能看到该 app，双击即可运行。

## 资料篇
---
### Elastos
---
问答系统：[http://elastos.org/q2a/index.php](http://elastos.org/q2a/index.php)
讨论组：[http://elastos.org/groups/elastos/](http://elastos.org/groups/elastos/)
开发文档：[http://elastos.org/elorg_common/ElastosManuals/ElastosManual/userguide.html](http://elastos.org/elorg_common/ElastosManuals/ElastosManual/userguide.html)

###  C++
---
我们是使用 C++ 来开发类似Android的操作系统，因此对 C++ 和 Android 都要了解。

C++ 书籍：
[C++从入门到精通](http://www.douban.com/doulist/271354/) ，请尽量阅读所有该豆列中的书籍，尤其是 [《Effective C++》](http://book.douban.com/subject/1842426/)/[《Effective STL》](http://book.douban.com/subject/1792179/)/[《深度探索 C++ 对象模型》](http://book.douban.com/subject/1091086/)；由于 CAR 与 COM 的实现原理类似，请阅读[《COM技术内幕》](http://book.douban.com/subject/1231596/)的前八章，以了解 CAR 的实现思想。

C++博客篇：
[C++ 虚函数表解析](http://blog.csdn.net/haoel/article/details/1948051)
[C++ 对象的内存布局（上）](http://blog.csdn.net/haoel/article/details/3081328)
[C++ 对象的内存布局（下）](http://blog.csdn.net/haoel/article/details/3081385)

### Android
---
Android书籍：
[Android框架研究](http://www.douban.com/doulist/38068301/) ，请尽量阅读所有该豆列中的书籍

Android 博客篇：
[老罗的Android之旅](http://blog.csdn.net/Luoshengyang)

### 其他
---
我们使用 git 进行代码管理，请阅读[《Pro Git》](http://book.douban.com/subject/3420144/)了解其使用方法以及运作机理。

我们非常看重良好的编码习惯与素养，请阅读类似如下的书籍:
[《重构-改善既有代码的设计》](http://book.douban.com/subject/1229923/)
[《代码大全》](http://book.douban.com/subject/1477390/)
[《代码整洁之道》](http://book.douban.com/subject/4199741/)
