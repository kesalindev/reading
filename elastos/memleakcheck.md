# 内存泄露检测与调试

## 使用 bionic 自带内存检测工具
---
步骤：
``` shell
# cd PathToElastosRDKXXX/DevDoc/dbglibs/
# adb push libc_malloc_debug_leak.so  /system/lib
# adb shell setprop libc.debug.malloc 10
# adb shell stop
# adb shell start
```

命令<code>adb shell setprop libc.debug.malloc 10</code>中的参数说明参考 android 源码：<code>android/bionic/libc/bionic/malloc_debug_common.cpp</code>:

>
/ * Handle to shared library where actual memory allocation is implemented.
* This library is loaded and memory allocation calls are redirected there
* When libc.debug.malloc environment variable contains value other than
* Zero:
* __1 - For memory leak detections. __
* __5 - For filling allocated / freed memory with patterns defined by__
* __CHK_SENTINEL_VALUE, and CHK_FILL_FREE macros.__
* __10 - For adding pre-, and post-allocation stubs in order to detect buffer overruns.__
* Note that emulator's memory allocation instrumentation is not controlled by
* libc.debug.malloc value, but rather by emulator, started with-memcheck
* option. Note also, that if emulator has started with-memcheck option,
* emulator's instrumented memory allocation will take over value saved in
* libc.debug.malloc. In other words, if emulator has started with-memcheck
* option, libc.debug.malloc value is ignored.
* Actual functionality for debug levels 1-10 is implemented in
* libc_malloc_debug_leak.so, while functionality for emultor's instrumented
* allocations is implemented in libc_malloc_debug_qemu.so and can be run inside
* the emulator only.
* /

## 使用 DumpMemery 方式
---
这种方式的原理是比较两次重复运行的内存 dump 情况，找出可疑的内存分配，然后用 <code>arm-linux-androideabi-addr2line</code> 将这些可疑分配的堆栈地址转换为源码位置。

* 将如下代码放到需要重复运行以便排查内存泄漏的地方：
    ``` cpp
     #include "os/CDebug.h"

     using Elastos::Droid::Os::IDebug;
     using Elastos::Droid::Os::CDebug;
     using Elastos::IO::IFile;
     using Elastos::IO::CFile;
     using Elastos::IO::IFileDescriptor;
     using Elastos::IO::CFileDescriptor;
     using Libcore::IO::IOsConstants;
     using Libcore::IO::COsConstants;
     using Libcore::IO::ILibcore;
     using Libcore::IO::CLibcore;
     using Libcore::IO::IOs;
     using Libcore::IO::IIoBridge;
     using Libcore::IO::CIoBridge;

     static void MyDumpMemery()
     {
         AutoPtr<IOsConstants> osConstans;
         COsConstants::AcquireSingleton((IOsConstants**)&osConstans);
         Int32 m1, m2, m3;
         osConstans->GetOsConstant(String("O_RDWR"), &m1);
         osConstans->GetOsConstant(String("O_CREAT"), &m2);
         osConstans->GetOsConstant(String("O_TRUNC"), &m3);

         AutoPtr<IFile> file;
         CFile::New(String("/data/debug"), (IFile**)&file);
         Boolean bval;
         file->Exists(&bval);
         if (!bval) {
             file->Mkdirs(&bval);
         }

         AutoPtr<IFileDescriptor> ifd;
         CFileDescriptor::New((IFileDescriptor**)&ifd);
         AutoPtr<IIoBridge> ioBridge;
         CIoBridge::AcquireSingleton((IIoBridge**)&ioBridge);
         Int32 fd;
         ioBridge->Open(String("/data/debug/1.txt"), m1 | m2 | m3, &fd);
         ifd->SetDescriptor(fd);

         AutoPtr<IDebug> dbg;
         CDebug::AcquireSingleton((IDebug**)&dbg);
         dbg->DumpHeap(ifd);
     }
    ```

* 进入 <code>adb shell</code> 环境，赋予 <code>/data/debug</code> 写的权限： <code>chmod 777 /data/debug</code>，然后启用内存调试开关：
    ``` shell
     adb shell setprop libc.debug.malloc 1
     adb shell stop
     adb shell start
    ```

* 杀死要调试的进程，让其重启，常见需要杀死的进程有 <code>elzygote</code>, <code>elsystemserver</code>, <code>SystemUI</code>。

* 在 terminal 上进入 <code>PathToElastosRDKXXX/DevDoc/DbgTools</code> 目录，使用脚本<code>loc_memleak.sh</code>进行内存检测。
    * 执行<code>./loc_memleak.sh -p</code> 开启内存调试开关
    * 执行<code>./loc_memleak.sh -all</code>整体运行。在运行过程中，会出现用户交互。
        * Please run the target program, and then start the task(yes or no): 表示执行脚本后，需要运行一个目标程序，然后输入yes
        * Please input the target program's NAME(such as: SystemUI PinyinIME): 表示需要输入一个调试进程的名称，目的是获取该进程的id
        * Please run multiple times the target program, and then start the task(yes or no): 再次运行几次目标程序，输入yes
        * 脚本会将最后的结果输出到屏幕中，并且保存到一个文件中。
    * 脚本生成的文件：
        * first.txt 为首次运行目标程序生成的内存信息
        * second.txt 为再次运行多次目标程序生成的内存信息
        * diff.txt 为内存信息变动情况的文件
        * logcat.txt 为开发板运行“MemoryDummper”生成的文件, 同时也是“MemDumpFormatter.exe”的输入文件
        * addr2line.txt 为定位文件
        * result.txt 为最终有可能内存泄露的源码位置信息
