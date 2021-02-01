# 动态链接库

## 编译参数

-shared 该选项指定生成动态连接库（让连接器生成T类型的导出符号表，有时候也生成弱连接W类型的导出符号），不用该标志外部程序无法连接。相当于一个可执行文件。

-fPIC：表示编译为位置独立的代码，不用此选项的话编译后的代码是位置相关的所以动态载入时是通过代码拷贝的方式来满足不同进程的需要，而不能达到真正代码段共享的目的。

-L.：表示要连接的库在当前目录中。

-lmodel：编译器查找动态连接库时有隐含的命名规则，即在给出的名字前面加上lib，后面加上.so来确定库的名称

 LD_LIBRARY_PATH：这个环境变量指示动态连接器可以装载动态库的路径。

```shell
ldd sample
	linux-vdso.so.1 (0x00007ffea09c4000)
	libmodel.so => not found
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f9fb142b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f9fb163a000)
```

错误提示，找不到动态库文件 libmodel.so 。程序在运行时，会在 /usr/lib 和 /lib 等目录中 查找需要的动态库文件。若找到，则载入动态库，否则将提示类似上述错误而终止程序运行。我们将文件 libmodel.so 复制到目 录 /usr/lib 中，再试试。 （一般不建议采用此方式，最好用修改LD的方式）

[动态链接库实例](sample/sample1)

# 静态链接库

[动态链接库实例](sample/sample2)


 静态库链接时搜索路径顺序： 
1. ld会去找GCC命令中的参数-L
2. 再找gcc的环境变量LIBRARY_PATH
3. 再找内定目录 /lib /usr/lib /usr/local/lib 这是当初compile gcc时写在程序内的

动态链接时、执行时搜索路径顺序:
1. 编译目标代码时指定的动态库搜索路径；
2. 环境变量LD_LIBRARY_PATH指定的动态库搜索路径；
3. 配置文件/etc/ld.so.conf中指定的动态库搜索路径；
4. 默认的动态库搜索路径/lib；
5. 默认的动态库搜索路径/usr/lib。

有关环境变量： 
LIBRARY_PATH环境变量：指定程序静态链接库文件搜索路径
LD_LIBRARY_PATH环境变量：指定程序动态链接库文件搜索路径

# gcc/g++ -I -L -l的区别

`gcc -o hello hello.c -I /home/hello/include -L /home/hello/lib -lworld`

- -I /home/hello/include表示将/home/hello/include目录作为第一个寻找头文件的目录，寻找的顺序是：/home/hello/include-->/usr/include-->/usr/local/include

- -L /home/hello/lib表示将/home/hello/lib目录作为第一个寻找库文件的目录，寻找的顺序是：/home/hello/lib-->/lib-->/usr/lib-->/usr/local/lib

- -lworld表示在上面的lib的路径中寻找libworld.so动态库文件（如果gcc编译选项中加入了“-static”表示寻找libworld.a静态库文件）

-o：指定生成可执行文件的名称。使用方法为：g++ -o afile file.cpp file.h ... （可执行文件不可与待编译或链接文件同名，否则会生成相应可执行文件且覆盖原编译或链接文件），如果不使用-o选项，则会生成默认可执行文件a.out。

-c：只编译不链接，只生成目标文件。

-g：添加gdb调试选项。