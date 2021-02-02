foo = test
all:
#不能写成 $(foo)这样实际检测的是test
ifdef foo
	@echo yes
else
	@echo  no
endif

ifeq ($(CC),gcc)
	@echo gcc
else
	@echo $(CC)
endif

```
NOTE：在 make 读取 Makefile 文件时计算表达式的值，并根据表达式的值决定判断语句中的哪一个部分作为此 Makefile 所要执行的内容。因此在条件表达式中不能使用自动化变量，自动化变量在规则命令执行时才有效，更不能将一个完整的条件判断语句分卸在两个不同的 Makefile 的文件中。在一个 Makefile 中使用指示符 "include" 包含另一个 Makefile 文件。
```

# 伪目标

避免我们的 Makefile 中定义的只执行的命令的目标和工作目录下的实际文件出现名字冲突。

看一个常用的清理makefile写法

```Makefile
clean:
    rm -rf *.o 
```
规则中 rm 命令不是创建文件 clean 的命令，而是执行删除任务，删除当前目录下的所有的 .o 结尾的文件。当工作目录下不存在以 clean 命令的文件时，在 shell 中输入 make clean 命令，命令 rm -rf *.o test 总会被执行 ，这也是我们期望的结果。

如果当前目录下存在文件名为clean的文件时情况就会不一样了，当我们在 shell 中执行命令 make clean，由于这个规则没有依赖文件，所以目标被认为是最新的而不去执行规则所定义的命令。因此命令 rm 将不会被执行。为了解决这个问题，删除 clean 文件或者是在 Makefile 中将目标 clean 声明为伪目标。将一个目标声明称伪目标的方法是将它作为特殊的目标.PHONY的依赖，如下：

```Makefile
.PHONY:clean
clean:
    rm -rf *.o 
```
这样 clean 就被声明成一个伪目标，无论当前目录下是否存在 clean 这个文件，当我们执行 make clean 后 rm 都会被执行。
另外当一个目标被声明为伪目标之后，make在执行此规则时也不会去试图去查找隐含的关系去创建它。这样同样提高了 make 的执行效率，同时也不用担心目标和文件名重名而使我们的编译失败。

伪目标的另一种使用的场合是在 make 的并行和递归执行的过程中，此情况下一般会存在一个变量，定义为所有需要 make 的子目录。对多个目录进行 make 的实现，可以在一个规则的命令行中使用 shell 循环来完成。如下：

```Makefile
SUBDIRS=foo bar baz
subdirs:
	for dir in $(SUBDIRS);do $(MAKE) -C $$dir;done
```
代码表达的意思是当前目录下存在三个子文件目录，每个子目录文件都有相对应的 Makefile 文件，代码中实现的部分是用当前目录下的 Makefile 控制其它子模块中的 Makefile 的运行，但是这种实现方法存在以下问题：
- 当子目录执行 make 出现错误时，make 不会退出。就是说，在对某个目录执行 make 失败以后，会继续对其他的目录进行 make。在最终执行失败的情况下，我们很难根据错误提示定位出具体实在那个目录下执行 make 发生的错误。这样给问题定位造成很大的困难。为了解决问题可以在命令部分加入错误检测，在命令执行的错误后主动退出。不幸的是如果在执行 make 时使用了 "-k" 选项，此方式将失效。
- 另外一个问题就是使用这种 shell 循环方式时，没有用到 make 对目录的并行处理功能由于规则的命令时一条完整的 shell 命令，不能被并行处理。

有了伪目标之后，我们可以用它来克服以上方式所存在的两个问题，代码展示如下：

```makefile
SUBDIRS=foo bar baz
.PHONY:subdirs $(SUBDIRS)
subdirs:$(SUBDIRS)
$(SUBDIRS):
	$(MAKE) -C $@
foo:baz
```
上面的实例中有一个没有命令行的规则“foo:baz”，这个规则是用来规定三个子目录的编译顺序。因为在规则中 "baz" 的子目录被当作成了 "foo" 的依赖文件，所以 "baz" 要比 "foo" 子目录更先执行，最后执行 "bar" 子目录的编译。

## 伪目标实现多文件编辑

如果在一个文件里想要同时生成多个可执行文件，我们可以借助伪目标来实现。使用方式如下：

```makefile
.PHONY:all
all:test1 test2 test3
test1:test1.o
    gcc -o $@ $^
test2:test2.o
    gcc -o $@ $^
test3:test3.o
    gcc -o $@ $^
```