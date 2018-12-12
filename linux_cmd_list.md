
## linux命令列表

#### 1: 压缩解压

- .tar.bz2
```
  tar -jxvf *.tar.bz2
```

- .tar.gz
```
  tar - zxvf *.tar.gz
  tar -czf *.tar.gz [folder]
```

- .tar
```
  tar -xvf *.tar
```

- .zip
```
  unzip *.zip
```

#### 2: gcc编译器 - strip命令

从特定文件中剥掉一些符号信息和调试信息，使文件变小。strip不仅仅可以针对可执行文件， 还能针对目标文件和动态库等.在实际的开发中这就节省了很多空间.而在调试的时候就需要符号了。
通常的做法是： 
1. strip前的库用来调试， strip后的库用来实际发布， 他们两者有对应关系。 一旦发布的strip后的库出了问题， 就可以找对应的未strip的库来定位。
2. 在开发过程中，经常涉及到上传很多库， 库又很大，传输太耗时间， 可以先用strip来处理一下比较好。

```
# strip --help
Usage: strip <option(s)> in-file(s)
 Removes symbols and sections from files
 The options are:
  -I --input-target=<bfdname>      Assume input file is in format <bfdname>
  -O --output-target=<bfdname>     Create an output file in format <bfdname>
  -F --target=<bfdname>            Set both input and output format to <bfdname>
  -p --preserve-dates              Copy modified/access timestamps to the output
  -D --enable-deterministic-archives
                                   Produce deterministic output when stripping archives (default)
  -U --disable-deterministic-archives
                                   Disable -D behavior
  -R --remove-section=<name>       Also remove section <name> from the output
  -s --strip-all                   Remove all symbol and relocation information
  -g -S -d --strip-debug           Remove all debugging symbols & sections
     --strip-dwo                   Remove all DWO sections
     --strip-unneeded              Remove all symbols not needed by relocations
     --only-keep-debug             Strip everything but the debug information
  -N --strip-symbol=<name>         Do not copy symbol <name>
  -K --keep-symbol=<name>          Do not strip symbol <name>
     --keep-file-symbols           Do not strip file symbol(s)
  -w --wildcard                    Permit wildcard in symbol comparison
  -x --discard-all                 Remove all non-global symbols
  -X --discard-locals              Remove any compiler-generated symbols
  -v --verbose                     List all object files modified
  -V --version                     Display this program's version number
  -h --help                        Display this output
     --info                        List object formats & architectures supported
  -o <file>                        Place stripped output into <file>
strip: supported targets: elf64-x86-64 elf32-i386 elf32-iamcu elf32-x86-64 a.out-i386-linux pei-i386 pei-x86-64 elf64-l1om elf64-k1om elf64-little elf64-big elf32-little elf32-big pe-x86-64 pe-bigobj-x86-64 pe-i386 plugin srec symbolsrec verilog tekhex binary ihex
```

例如：
```
# arm-linux-gnueabi-strip bin/* lib/* usr/lib/libware.so
# strip SensorServer
```

nm命令来列出一个目标文件中的各种符号。

```
# nm SensorServer 
                 U atoi@@GLIBC_2.2.5
0000000000603110 B __bss_start
                 U bufferevent_disable
                 U bufferevent_enable
                 U bufferevent_free
                 U bufferevent_get_output
                 U bufferevent_setcb
                 U bufferevent_socket_new
                 U bufferevent_write
0000000000603128 b completed.7594
0000000000603100 D __data_start
0000000000603100 W data_start
0000000000400ef0 t deregister_tm_clones
...

# strip SensorServer
# nm SensorServer
nm: SensorServer: no symbols

# ls -la
-rwxrwxrwx 1 position position 14728 12月 11 21:09 SensorServer
-rwxrwxrwx 1 position position 18656 12月 11 21:03 SensorServer_bak
```
可用明显看出SensorServer大小变化。

