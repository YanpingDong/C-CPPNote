# Makefile解释

Makefile 可以简单的认为是一个工程文件的编译规则，描述了整个工程的编译和链接等规则。其中包含了那些文件需要编译，那些文件不需要编译，那些文件需要先编译，那些文件需要后编译，那些文件需要重建等等。编译整个工程需要涉及到的，在 Makefile 中都可以进行描述。换句话说，Makefile 可以使得我们的项目工程的编译变得自动化，不需要每次都手动输入一堆源文件和参数。

我们如果只编写简单程序直接使用g++/gcc来做编译链接没什么问题，但大型项目模块之间的依赖问题复杂手动编译会出很多问题，所以使用Makefile使其自动化，编写一次就可以多次运行，很方便。

我们编写 Makefile 的时可以使用的文件的名称 "GNUmakefile" 、"makefile" 、"Makefile" ，make 执行时回去寻找 Makefile 文件，找文件的顺序也是这样的。

我们编译项目文件的时候，默认情况下，make 执行的是 Makefile 中的第一规则（Makefile 中出现的第一个依赖关系），此规则的第一目标称之为“最终目标”或者是“终极目标”。

推荐使用 Makefile（一般在工程中都这么写，大写的会比较的规范）。如果文件不存在，make 就会给我们报错，提示：


```
make：*** 没有明确目标并且找不到 makefile。停止
```

# Makefile书写规则

Makefile描述的是文件编译的相关规则，它的规则主要是两个部分组成，分别是依赖的关系和执行的命令，其结构如下所示：

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

# 一个简单的makefile

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

## makefile包含内容

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

## Makefile的工流程

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

# 通配符

## $@ $^

$@ ：表示规则中的目标文件集。

$^ : 所有的依赖文件

```makefile
test:a.c b.c
    #等同gcc -o test a.c b.c
	gcc -o $@ $^
```

## %

"%"匹配任意个字符，使用在规则当中。 

```makefile
test:test.o test1.o
	gcc -o $@ $^
%.o:%.c
	gcc -o $@ $^
```
"%.o" 把我们需要的所有的 ".o" 文件组合成为一个列表，从列表中挨个取出的每一个文件，"%" 表示取出来文件的文件名（不包含后缀），然后找到文件中和 "%"名称相同的 ".c" 文件，然后执行下面的命令，直到列表中的文件全部被取出来为止。

## $< 

$< : 表示第一个依赖文件

```makefile
# 原始写法
root_num.o: root_num.c my_root.h  
    gcc -c root_num.c  
my_root.o: my_root.c my_root.h  
    gcc -c my_root.c  

# 简化后写法
root_num.o: root_num.c my_root.h  
    gcc -c $<  
my_root.o: my_root.c my_root.h  
    gcc -c $<   
```

# 变量

可以利用Makefile 中的变量来表示某些多处使用而又可能发生变化的内容，不仅可以节省重复修改的工作，还可以避免遗漏。 Makefile 文件中定义变量的基本语法如下：

```makefile
#定义
变量的名称=值列表
#使用
$(变量的名称)
```
Makefile 中的变量的使用其实非常的简单，因为它并没有像其它语言那样定义变量的时候需要使用数据类型。变量的名称可以由大小写字母、阿拉伯数字和下划线构成.

```makefile
OBJ=main.o test.o test1.o test2.o
test:$(OBJ)
	gcc -o test $(OBJ)
```

Makefile 的变量的四种基本赋值方式：

- 简单赋值 ( := ) 编程语言中常规理解的赋值方式，只对当前语句的变量有效。
- 递归赋值 ( = ) 赋值语句可能影响多个变量，所有目标变量相关的其他变量都受影响。
- 条件赋值 ( ?= ) 如果变量未定义，则使用符号中的值定义变量。如果该变量已经赋值，则该赋值语句无效。
- 追加赋值 ( += ) 原变量用空格隔开的方式追加一个新值。

**简单赋值**

```makefile
x:=foo
y:=$(x)b
x:=new
test：
	@echo "y=>$(y)"
	@echo "x=>$(x)"

#在 shell 命令行执行make test我们会看到:
y=>foob
x=>new
```

**递归赋值**
```makefile
x=foo
y=$(x)b
x=new
test：
	@echo "y=>$(y)"
	@echo "x=>$(x)"

#在 shell 命令行执行make test我们会看到:
y=>newb
x=>new
```

**条件赋值**

```makefile
x:=foo
y:=$(x)b
x?=new
test：
	@echo "y=>$(y)"
	@echo "x=>$(x)"

#在 shell 命令行执行make test 我们会看到:
y=>foob
x=>foo
```

**追加赋值**

```makefile
x:=foo
y:=$(x)b
x+=$(y)
test：
	@echo "y=>$(y)"
	@echo "x=>$(x)"

#在 shell 命令行执行make test我们会看到:
y=>foob
x=>foo foob
```

# VPATH vpath使用

对于简单工程这样指出头文件和源文件位置是可以的，如果对于复杂工程还是可以看后面的多模块makefile编写。而且编写的时候必须使用$@ $< $^来来指定规则种的名称，不能直接使用文件名，会把找不到。这点需要注意

demo的头文件在include里，c文件在src里面

```makefile
#demo
vpath %.c src
vpath %.h include
main:main.o list1.o list2.o
    #不能使用gcc -o main main.o,以下所有规则命令同理
    gcc -o $@ $<
main.o:main.c
    gcc -o $@ $^
list1.o:list1.c list1.h
    gcc -o $@ $<
list2.o:list2.c list2.h
    gcc -o $@ $<

#另一种写法
VPATH=src include
main:main.o list1.o list2.o
    gcc -o $@ $<
main.o:main.c
    gcc -o $@ $^
list1.o:list1.c list1.h
    gcc -o $@ $<
list2.o:list2.c list2.h
    gcc -o $@ $<
```

# 隐含规则

编写 Makefile 的时候，可以使用隐含规则来简化Makefile 文件编写。make可以推导规则，所以有的规则是可以不用写出来的。比如：

```makefile
#完整
test:test.o
	gcc -o test test.o
test.o:test.c
    gcc -c test.c
#简化1
test:test.o
	gcc -o test test.o
test.o:test.c
#进一步简化，test.o的生成是可以省略的，只要都是xxx.o-->xxx.c这种
test:test.o
	gcc -o test test.o
```

# 多模块makefile编写1

在大一些的项目里面，所有源代码不会只放在同一个目录，一般各个功能模块的源代码都是分开的，各自放在各自目录下，并且头文件和.c源文件也会有各 自的目录，这样便于项目代码的维护。这样我们可以在每个功能模块目录下都写一个Makefile，各自Makefile处理各自功能的编译链接工作，这样 我们就不必把所有功能的编译链接都放在同一个Makefile里面，这可使得我们的Makefile变得更加简洁，并且编译的时候可选择编译哪一个模块， 这对分块编译有很大的好处。

主要思路是，让主模块目录生成可执行文件，其他模块目录生成静态库文件，主模块链接时要用其他模块编译产生的库文件来生成最终的程序。

现在假设模块结构如下,Sample3项目依赖model模块。(规范的划分的话是可以再划分出include/module_name目录来专门放头文件，src/module_name来专门放.c/.cpp)

下面结构sample3.cpp所在的目录是主模块目录，model目录是子模块目录，model编译完成的模块会移动到libs供sample3.cpp连接。

```
sample3
  |---libs
  |---model
  |     |---model.cpp
  |     |---model.h
  |     |---Makefile
  |---sample3.cpp
  |---Makefile
```

model的Makefile文件,可以看到实际就是编译、生成静态文件、放入指定libs目录：

```makefile
CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I../../include

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o) 
SRC_LIB = libmodel.a  

all:  $(SRC_LIB) 

$(SRC_LIB): $(SRC_OBJ) 
	$(AR) rcs $@ $^  
	mv $@ ../../libs

clean:
	$(RM) $(SRC_OBJ) $(SRC_LIB) 
```

sample3.cpp主程序生成Makefile文件,功能是调用model模块makefile`$(MAKE) -C model`，然后连接model库并生成可运行文件sample3。所以只需要在sample3项目跟目录运行`make`命令就可以，这个根目录下的Makefile会帮我们调用模块的makefile文件完成构建。

```makefile
CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I./model
LDFLAGS += -L./libs 
LIBS += -lmodel 

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o)   
SRC_BIN = sample3  

all :  submode $(SRC_BIN)

submode:
	$(MAKE) -C model

$(SRC_BIN) : $(SRC_OBJ)
	g++ -o $@ $^ $(LDFLAGS) $(LIBS)


clean:
	$(RM) $(SRC_OBJ) $(SRC_BIN) 
```

[sample3细节](sample/sample3)

```
NOTE:
CFLAGS  CPPFLAGS 是make的变量设置后不需要
```

# 多模块编译2

前面说了模块的划分可以进一步将头文件和.c/.cpp文件进行划分，所以上面的结构可以进一步变成如下结构。

```
sample4
  |---libs
  |---include
  |    |---model
  |         |---model.h
  |---src
  |    |---model
  |    |    |---model.cpp
  |    |    |---Makefile
  |    |---sample4.cpp
  |    |---Makefile
  |---Makefile
```

对于所有.cpp文件的编译和以前没什么变化，只是-I添加的头文件路径发生了变化,还有就是拷贝时候的路径关系变化了，毕竟路径深度比以前多了一个目录，并且多了一个Makefile来处理两个Makefile的关系和自动调用，当然也可以把控制各个Makefile的关系写到sample4.cpp所在目录的Makefile里，但为了清晰还是做了一个总的Makefile

Model的Makefile

```makefile
CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I../../include

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o) 
SRC_LIB = libmodel.a  

all:  $(SRC_LIB) 

$(SRC_LIB): $(SRC_OBJ) 
	$(AR) rcs $@ $^  
	mv $@ ../../libs

clean:
	$(RM) $(SRC_OBJ) $(SRC_LIB) 
```

sample4的Makefile,编译连接主程序并移动到../build目录

```makefile
CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I../include
LDFLAGS += -L../libs 
LIBS += -lmodel 

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o)   
SRC_BIN = sample4  

all : $(SRC_BIN)


$(SRC_BIN) : $(SRC_OBJ)
	g++ -o $@ $^ $(LDFLAGS) $(LIBS)
	mv $@ ../build

clean:
	$(RM) $(SRC_OBJ) $(SRC_BIN) 

```

总的makefile,可以看到是先编译model然后在去编译sample4

```makefile
all :
	$(MAKE) -C src/model
	$(MAKE) -C src
```

