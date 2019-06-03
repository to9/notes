##  交叉编译环境下GDB调试core文件
### 编译类型
1. Release
2. RelWithDebInfo
3. Debug

注： 在编译过程中，需使用-g参数。编译后不能使用arm-linux-gnueabi-strip去掉应用和动态库中符号表。

### 设备资源
当我们遇到资源有限设备时候，可以使用strip去掉编译后的符号表，减少编译后*.bin,*.so大小，经过strip处理后，gdb调试缺少相关符号表。结果显示如下
```
#0 0xff3b9814 in ?? ()
(gdb) bt
#0 0xff3b9814 in ?? ()
```
这时候我们可以在本地预留一份未进过strip处理的(预留的文件必须是和经过strip处理的文件是同一次编译出来的文件)，出现问题可以使用strip处理前的文件来定位！
通常做法:   
strip前的库用来调试， strip后的库用来实际发布， 他们两者有对应关系。 一旦发布的strip后的库出了问题， 就可以找对应的未strip的库来定位。

### GDB调试方式
在宿主机上搭建与一套与设备完全一致的busybox文件系统(非gdbServer)。GDB调试时候在搭建好的环境中指定路径等相关信息。宿主机环境与嵌入式设备完全一致文件系统(一致/lib,/usr/lib等系统so文件)。
```
[Docker:317/test_crash]$ ls /opt/test_sdk/
bin  boot  dev  etc  home  lib  media  opt  proc  readme  root  run  sbin  sys  tmp  usr  var  version
```

```
[Docker:316/test_crash]$ arm-none-linux-gnueabi-gdb Fast-install-make-relwithdebinfo/bin/main

GNU gdb (GDB) 8.2
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "--host=x86_64-pc-linux-gnu --target=arm-none-linux-gnueabi".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from Fast-install-make-relwithdebinfo/bin/main...done.
(gdb) set sysroot /opt/test_sdk/
(gdb) set auto-load safe-path /opt/test_sdk/
(gdb) set solib-search-path /opt/test_sdk/usr/lib/:Fast-install-make-relwithdebinfo/lib/
(gdb) core-file core-dump-493-mgd-1514819806
"core-dump-493-mgd-1514819806" is not a core dump: file format not recognized
(gdb) bt
(gdb) ...
```

### GDB相关参数
#### set sysroot
Specifies the local directory that contains copies of target libraries in the corresponding subdirectories. 

```
set sysroot [Directory]
set solib-absolute-prefix [Directory]
show sysroot
```
set sysroot是set solib-absolute-prefix 的别名

#### set auto-load safe-path
Set the list of directories (and their subdirectories) trusted for automatic loading and execution of scripts.

```
set auto-load safe-path [directories]
```
#### set solib-search-path
Specifies directories where GDB will search for shared libraries with symbols. 

```
set solib-search-path [Directories]
show solib-search-path
```
总体上来说solib-absolute-prefix设置库的绝对路径前缀，只对绝对路径有效；而solib-search-path设置库的搜索路径，对绝对路径和相对路径均起作用。（编译器自动链接的so库多采用绝对路径）

#### core-file
加载corefile路径。

```
core-file [path]
```
