# CMake

是一个跨平台的安装（编译）工具，可以用简单的语句来描述所有平台的安装(编译过程)。他能够输出各种各样的makefile。也就是说Cmake并不直接建构出最终的软件，而是产生标准的建构档（如 Unix 的 Makefile 或 Windows Visual C++ 的 projects/workspaces）。并且能测试编译器所支持的C++特性。

```
CMake is a meta-build system that can be used to create the build files for many other build tools
```

## CMake可以生成的编译工具控制文件

运行`cmake --help`会显示可以使用的生成器。默认的是使用Makefile生成器。使用对应的生成器可以直接生成对应IDE的项目。

```
Generators

The following generators are available on this platform (* marks default):
* Unix Makefiles               = Generates standard UNIX makefiles.
  Green Hills MULTI            = Generates Green Hills MULTI files
                                 (experimental, work-in-progress).
  Ninja                        = Generates build.ninja files.
  Watcom WMake                 = Generates Watcom WMake makefiles.
  CodeBlocks - Ninja           = Generates CodeBlocks project files.
  CodeBlocks - Unix Makefiles  = Generates CodeBlocks project files.
  CodeLite - Ninja             = Generates CodeLite project files.
  CodeLite - Unix Makefiles    = Generates CodeLite project files.
  Sublime Text 2 - Ninja       = Generates Sublime Text 2 project files.
  Sublime Text 2 - Unix Makefiles
                               = Generates Sublime Text 2 project files.
  Kate - Ninja                 = Generates Kate project files.
  Kate - Unix Makefiles        = Generates Kate project files.
  Eclipse CDT4 - Ninja         = Generates Eclipse CDT 4.0 project files.
  Eclipse CDT4 - Unix Makefiles= Generates Eclipse CDT 4.0 project files.

```

所以一般流程（ubuntu）

1. cmake .
2. make .

## cmake主要构成

CMakeLists.txt ： 你需要运行的所有CMake命令，cmake会自动在运行目录查找这个文件。

# cmake教程实例

跳转连接里有实例代码和对应的CMakeLists.txt。并附有相关的讲解。[跳转连接](https://github.com/ttroy50/cmake-examples)

# 常见的变量

PROJECT_NAME：project()方法被调用后，会创建该变量。并把project的入参设置给该变量,见下实例：

CMAKE_SOURCE_DIR: The root source directory

CMAKE_CURRENT_SOURCE_DIR: The current source directory if using sub-projects and directories.

PROJECT_SOURCE_DIR: The source directory of the current cmake project.

CMAKE_BINARY_DIR: The root binary / build directory. This is the directory where you ran the cmake command.

CMAKE_CURRENT_BINARY_DIR: The build directory you are currently in.

PROJECT_BINARY_DIR: The build directory for the current project.

CMAKE_C_COMPILER: The program used to compile c code.

CMAKE_CXX_COMPILER: The program used to compile c++ code.

CMAKE_LINKER: The program used to link your binary.

对照下面项目结构看后面的变量using the project() command, CMake will automatically create a number of variables which can be used to reference details about the project. 所以每次使用project()命令时候都会刷新以下的变量，sublibarary1和sublibrary2的下列变量是不同的！！！

```
$ tree
.
├── CMakeLists.txt
├── subbinary
│   ├── CMakeLists.txt
│   └── main.cpp
├── sublibrary1
│   ├── CMakeLists.txt
│   ├── include
│   │   └── sublib1
│   │       └── sublib1.h
│   └── src
│       └── sublib1.cpp
└── sublibrary2
    ├── CMakeLists.txt
    └── include
        └── sublib2
            └── sublib2.h
```
PROJECT_NAME： The name of the project set by the current project().

CMAKE_PROJECT_NAME： the name of the first project set by the project() command, i.e. the top level project.

PROJECT_SOURCE_DIR： The source director of the current project.

PROJECT_BINARY_DIR： The build directory for the current project.

name_SOURCE_DIR： The source directory of the project called "name". In this example the source directories created would be sublibrary1_SOURCE_DIR, sublibrary2_SOURCE_DIR, and subbinary_SOURCE_DIR

name_BINARY_DIR： The binary directory of the project called "name". In this example the binary directories created would be sublibrary1_BINARY_DIR, sublibrary2_BINARY_DIR, and subbinary_BINARY_DIR


使用方式如下：

```
# 通过命令行使用
cmake . -DCMAKE_C_COMPILER=clang-3.6 -DCMAKE_CXX_COMPILER=clang++-3.6

# 在CMakeLists.txt中使用
cmake_minimum_required(VERSION 2.6)
project (hello_cmake)
add_executable(${PROJECT_NAME} main.cpp)

```