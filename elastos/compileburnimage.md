# 编译与烧写 Elastos image

## 前言
---
Elastos 最新代码已不再支持模拟器开发，只支持在真实硬件设备上进行开发与调试。若你还没有相应的硬件设备（Lamobo R1、Lammobo R1s、Lamobo M1均可），请从这里[http://elastos.org/](http://elastos.org/)购买硬件设备 Lamobo M1：
![Lamobo M1](http://elastos.org/elorg_common/img/elastos/m1.png)

为了运行 Elastos 系统，需要将 Elastos image 烧写到硬件设备上，有两种方式可以获取到 Elastos image：一是获取已经编译好的；二是自己下载源码编译。下面将先介绍在哪里获取已经编译好的 image 以及如何烧写。

## 获取已经编译好的 image
---
获取稳定版:[点此下载](http://elastos.org/home/#/get)
获取最新版:[点此下载](http://pan.baidu.com/s/1eQIUU58)，提取密码：<code>1jkw</code>

<span id="burningimage"></span>
## 烧写 image
---
下载烧写工具 PhoenixCard，可自行搜索下载，也可以在这里获取（[点此下载](http://pan.baidu.com/s/1kTquBX1)，提取密码：<code>ceyd</code>）。

通过 usb 读卡器将 SD 卡插上电脑，打开 PhoenixCard，选择 image 文件位置，改选烧写模式（Write Mode）为卡启动（Startup），然后点击烧写（Burn），开始烧写。当烧写完成时，下方的信息框会显示：
```
...
Magic Complete
Burn End...
```

烧写完成之后，将 SD 卡从电脑上拔出，插入硬件设备上，将开机启动硬件设备，你将会看到 Elastos 界面：
![ Elastos 界面](http://elastos.org/elorg_common/img/elastos/start.png)


## 下载源码编译生成 image
---

### 下载
最新源码提供对 Android 4.2.2 的兼容支持，Elastos 4.2.2 源码包括两个工程：<code>Allwinner4_2_2</code>和<code>ElastosRDK4_2_2</code>
下载源码：
> git clone ssh://**YourElastosID**@elastos.org:29418/KortideInsideProject/Allwinner4_2_2

> git clone ssh://**YourElastosID**@elastos.org:29418/KortideInsideProject/ElastosRDK4_2_2

### 编译<code>ElastosRDK4_2_2</code>
请参考[搭建Elastos开发环境](https://github.com/luozhaohui/reading/blob/master/elastos/beginelastos.md#%E6%90%AD%E5%BB%BAelastos%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83)设置好编译环境，并编译 <code>ElastosRDK4_2_2</code>。

### 编译<code>Allwinner4_2_2</code>
<code>ElastosRDK4_2_2</code>编译完成之后，拷贝相关编译结果到 <code>Allwinner4_2_2</code>　中：

* 将 <code>ElastosRDK4_2_2/Targets/obj/rdk/arm.gnu.android.dbg/inc</code> 下的所有文件及目录拷贝到 <code>Allwinner4_2_2/android/XDK_TOOLS/include</code> 目录下
* 将 <code>ElastosRDK4_2_2/Targets/obj/rdk/arm.gnu.android.dbg/lib</code> 目录下的 <code>Elastos.Core.lib</code>、<code>Elastos.Runtime.lib</code> 拷贝到 <code>Allwinner4_2_2/android/XDK_TOOLS/libs</code> 目录下
* 将 <code>ElastosRDK4_2_2/Targets/rdk/arm.gnu.android.dbg/bin</code> 目录下的 <code>Elastos.Core.eco</code>、 <code>Elastos.Droid.Core.eco</code>、 <code>Elastos.Droid.JavaProxy.eco</code>、 <code>Elastos.Runtime.eco</code>、 <code>Org.Kxml2.IO.eco</code>、 <code>Org.Xml.Sax.eco</code> 拷贝到 <code>Allwinner4_2_2/android/XDK_TOOLS/libs</code> 目录下

然后参照<code>Allwinner4_2_2/How_to_build</code> 文档编译、生成 image。最后参照 [烧写 image](#burningimage) 烧写该 image 到设备上即可。

### 部分更新
在烧写好某个 image 之后，通常我们不需要一有更新就完整地编译两个项目，并生成 image 烧写到设备上。我们可以部分地更新相关改动的库即可。

#### 更新 ElastosRDK
编译好 <code>ElastosRDK4_2_2</code> 之后，执行 <code>eldrop</code> 以及 <code>eldrop epk</code>将相关文件拷贝到设备上即可。

#### 更新 Allwinner
编译好 <code>Allwinner4_2_2</code> 之后，你可以通过两种途径拷贝文件到设备中：

* 一种是直接 <code>adb push</code>
    ``` shell
    # push files to device
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/lib/libandroid_runtime.so /system/lib
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/lib/libbinder.so /system/lib
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/lib/libmedia_jni.so /system/lib
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/framework.jar /system/framework
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/framework-res.apk /system/framework
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/services.jar /system/framework
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/android.policy.jar /system/framework
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/pm.jar /system/framework
adb push Allwinner4_2_2/android/out/target/product/sugar-evb/system/framework/core.jar /system/framework
    ```

* 另一种是通过拷贝 <code>Allwinner4_2_2</code> 的编译结果到 <code>ElastosRDK4_2_2</code> 中，然后通过<code>eldrop java</code> 进行的（参考 <code>Allwinner4_2_2/How_to_build</code> 文档）：
    ``` shell
    # copy files to ElastosRDK
cp Allwinner4_2_2/out/target/product/sugar-evb/system/lib/libandroid_runtime.so ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/lib/libbinder.so ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/lib/libmedia_jni.so ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/framework.jar ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/framework-res.apk ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/services.jar ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/android.policy.jar ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/pm.jar ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
cp Allwinner4_2_2/out/target/product/sugar-evb/system/framework/core.jar ~/ElastosRDK4_2_2/Build/Prebuilt/JavaFramwork
    ```

这两个步骤都可以根据更新的是哪一些具体的文件进一步细分拷贝。
