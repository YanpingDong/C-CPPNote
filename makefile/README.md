# Makefile解释

Makefile 可以简单的认为是一个工程文件的编译规则，描述了整个工程的编译和链接等规则。其中包含了那些文件需要编译，那些文件不需要编译，那些文件需要先编译，那些文件需要后编译，那些文件需要重建等等。编译整个工程需要涉及到的，在 Makefile 中都可以进行描述。换句话说，Makefile 可以使得我们的项目工程的编译变得自动化，不需要每次都手动输入一堆源文件和参数。

我们如果只编写简单程序直接使用g++/gcc来做编译链接没什么问题，但大型项目模块之间的依赖问题复杂手动编译会出很多问题，所以使用Makefile使其自动化，编写一次就可以多次运行，很方便。

我们编写 Makefile 的时可以使用的文件的名称 "GNUmakefile" 、"makefile" 、"Makefile" ，make 执行时回去寻找 Makefile 文件，找文件的顺序也是这样的。

推荐使用 Makefile（一般在工程中都这么写，大写的会比较的规范）。如果文件不存在，make 就会给我们报错，提示：


```
make：*** 没有明确目标并且找不到 makefile。停止
```

# Makefile 书写规则

我们已经知道了 Makefile 描述的是文件编译的相关规则，它的规则主要是两个部分组成，分别是依赖的关系和执行的命令，其结构如下所示：

```
targets : prerequisites
    command

或

targets : prerequisites; command
    command
```

说明如下：

- targets：规则的目标，可以是 Object File（一般称它为中间文件），也可以是可执行文件，还可以是一个标签；
- prerequisites：是我们的依赖文件，要生成 targets 需要的文件或者是目标。可以是多个，也可以是没有；
- command：make 需要执行的命令（任意的 shell 命令）。可以有多条命令，每一条命令占一行。

`注意：我们的目标和依赖文件之间要使用冒号分隔开，命令的开始一定要使用Tab键。如果不是tab用空格代替会报“missing separator.  Stop.”`

## 一个简单的makefile

先看一个简单的C++程序：

```cpp
#include <stdio.h>
#include <stdlib.h>

int main(void) {
	puts("Hello World!!!");
	return EXIT_SUCCESS;
}
```

在命令行中编译的话命令是：`g++ -o sample1 sample1.cpp`

我们放到Makefile中如下：

```makefile
sample1:	sample1.cpp
	g++ -o sample1 sample1.cpp
```
编译的话使用`make`命令，同样会生成sample1可执行文件。

[sample1细节](sample/sample1)

简单的概括一下Makefile 中的内容，它主要包含有五个部分，分别是：
1) 显式规则
显式规则说明了，如何生成一个或多的的目标文件。这是由 Makefile 的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。
2) 隐晦规则
由于我们的 make 命名有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写 Makefile，这是由 make 命令所支持的。
3) 变量的定义
在 Makefile 中我们要定义一系列的变量，变量一般都是字符串，这个有点像C语言中的宏，当 Makefile 被执行时，其中的变量都会被扩展到相应的引用位置上。
4) 文件指示
其包括了三个部分，一个是在一个 Makefile 中引用另一个 Makefile，就像C语言中的 include 一样；另一个是指根据某些情况指定 Makefile 中的有效部分，就像C语言中的预编译 #if 一样；还有就是定义一个多行的命令。
5) 注释
Makefile 中只有行注释，和 UNIX 的 Shell 脚本一样，其注释是用“#”字符，这个就像 C/C++ 中的“//”一样。如果你要在你的 Makefile 中使用“#”字符，可以用反斜框进行转义，如：“\\#”。

# Makefile的工流程

[sample2细节](sample/sample2)