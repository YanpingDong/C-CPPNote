# Makefile解释

Makefile 可以简单的认为是一个工程文件的编译规则，描述了整个工程的编译和链接等规则。其中包含了那些文件需要编译，那些文件不需要编译，那些文件需要先编译，那些文件需要后编译，那些文件需要重建等等。编译整个工程需要涉及到的，在 Makefile 中都可以进行描述。换句话说，Makefile 可以使得我们的项目工程的编译变得自动化，不需要每次都手动输入一堆源文件和参数。

我们如果只编写简单程序直接使用g++/gcc来做编译链接没什么问题，但大型项目模块之间的依赖问题复杂手动编译会出很多问题，所以使用Makefile使其自动化，编写一次就可以多次运行，很方便。

我们编写 Makefile 的时可以使用的文件的名称 "GNUmakefile" 、"makefile" 、"Makefile" ，make 执行时回去寻找 Makefile 文件，找文件的顺序也是这样的。

在我们编译项目文件的时候，默认情况下，make 执行的是 Makefile 中的第一规则（Makefile 中出现的第一个依赖关系），此规则的第一目标称之为“最终目标”或者是“终极目标”。

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

我们假设有个项目依赖其他模块，这里用model来模拟。这样我们就有如下的稍微复杂的makefile。

```makefile
sample2:	sample2.o model.o
	g++  sample2.o model.o -o sample2

sample2.o:   sample2.cpp model.h
	g++ -c sample2.cpp -o sample2.o

model.o:    model.cpp model.h
	g++ -c model.cpp -o model.o

clean:
	rm -f sample2 model.o sample2.o
```

我们知道第一个目标是终极目标。也就是说上面的makefile最终要生成sample2 和中间文件sample2.o model.o。

它的具体工作顺序是：当在 shell 提示符下输入 make 命令以后。 make 读取当前目录下的 Makefile 文件，并将 Makefile 文件中的第一个目标作为其执行的“终极目标”，开始处理第一个规则（终极目标所在的规则）。在我们的例子中，第一个规则就是目标 "sample2" 所在的规则。规则描述了 "sample2" 的依赖关系，并定义了链接 ".o" 文件生成目标 "sample2" 的命令；make 在执行这个规则所定义的命令之前，首先处理目标 "sample2" 的所有的依赖文件（例子中的那些 ".o" 文件）的更新规则（以这些 ".o" 文件为目标的规则）。

对这些 ".o" 文件为目标的规则处理有下列三种情况：

- 目标 ".o" 文件不存在，使用其描述规则创建它；
- 目标 ".o" 文件存在，目标 ".o" 文件所依赖的 ".c" 源文件 ".h" 文件中的任何一个比目标 ".o" 文件“更新”（在上一次 make 之后被修改）。则根据规则重新编译生成它；
- 目标 ".o" 文件存在，目标 ".o" 文件比它的任何一个依赖文件（".c" 源文件、".h" 文件）“更新”（它的依赖文件在上一次 make 之后没有被修改），则什么也不做。

通过上面的更新规则我们可以了解到中间文件的作用，也就是编译时生成的 ".o" 文件。作用是检查某个源文件是不是进行过修改，最终目标文件是不是需要重建。我们执行 make 命令时，只有修改过的源文件或者是不存在的目标文件会进行重建，而那些没有改变的文件不用重新编译，这样在很大程度上节省时间，提高编程效率。小的工程项目可能体会不到，项目工程文件越大，效果才越明显。

[sample2细节](sample/sample2)