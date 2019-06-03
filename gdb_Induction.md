## GDB入门
```
[Docker:316/test_crash]$ arm-none-linux-gnueabi-gdb -c core-dump-493-mgd-1514819806 -e bin/Fast-install-make-relwithdebinfo/bin/main -s debug/main.debug
```
GDB 参数:   
-c 指定core文件   
-e 指定binary   
-s 指定符号表   

### gdb启动的方式
1. gdb [program] 
```
$ gdb bin/Fast-install-make-relwithdebinfo/bin/main 
```
2. gdb [program] core
```
$ gdb bin/Fast-install-make-relwithdebinfo/bin/main core-dump-493-mgd-1514819806
```
3. gdb [program] [PID]
```
$ gdb bin/Fast-install-make-relwithdebinfo/bin/main 187
```
如果程序是一个服务程序并且已经启动，指定进程PID。gdb会自动attach上去。
4. 通过gdb参数指定启动
```
$ gdb -e bin/Fast-install-make-relwithdebinfo/bin/main -c core-dump-493-mgd-1514819806
$ gdb bin/Fast-install-make-relwithdebinfo/bin/main -c core-dump-493-mgd-1514819806
$ gdb -c core-dump-493-mgd-1514819806 -e bin/Fast-install-make-relwithdebinfo/bin/main
```

注： "program"为执行文件。

### gdb调试方式(包括嵌入式)
1. 直接在目标平台上运行gdb进行调试(嵌入式使用交叉编译过的gdb)。
2. 在本地搭建与目标平台一样的文件系统(busybox)，进行gdb调试。
3. gdb+gdbserver远程调试

