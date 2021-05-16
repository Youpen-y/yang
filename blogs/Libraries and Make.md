---
permalink: /Library.html
title: Library and Make
anthor: yang
---



### Libraries and Make

如何构建动态库与静态库？（以构建 `libheader` 为例 ）



##### 问题引入：（函数声明 `.h` 与函数定义 `.c` 分离的好处？）

1. 在头文件 `header.h` 中同时声明定义库函数， 在 `main.c` 中调用。

```c++
// header.h
#ifndef HEADER_H
#define HEADER_H
#include<cstdlib>	// 调用的c++标准库头文件
...
// 函数的声明
...
// 函数的定义
#endif
```

```c++
// main.c
#include "header.h"
int main()
{
    ...
}
```

预处理器语句 `#include "header.h"`把所有在 `header.h` 中使用到的代码（用户代码，调用的库 eg. `cstdlib`的代码）合并到一个文件，然后编译它。

（存在问题：每次修改`main.c`，不管有没有修改 `header.h` 都要重新编译 `header.h` ）



2. 函数声明与定义分离

```C++
// header.h
#ifndef HEADER_H
#define HEADER_H
#include <cstdlib>
...
// 函数的声明
#endif
```

```C++
// header.c
#include "header.h"
...
// 函数的定义
#endif
```

```C++
// main.c 同上
```

这种方式通过创建中间目标文件（object file），在编译或运行时链接，从而减少对不变文件的返回编译。**实际上动态库与静态库的本质在某种程度上也就是一种 `.o` 文件。**

以上述代码为例：

```shell
>> gcc -c header.c   			# 编译产生header.o文件， 只需执行一次
>> gcc main.c header.o			# 编译 main.c 并在 header.o 文件中链接生成 a.out，修改 main.c 后执行
```

这样的话，修改`main.c` 后，通过编译 `main.c` ，链接 `header.o` ，无需重新编译`header.c`,`header.h`。



*一点 `make` 基础*

#### Make

```makefile
CPP = gcc		# g++, CPP just a variable name
OPTS = 			# any options, e.g. -O for 优化
DEBUG =			# empty now, but assign -g for (gdb) debugging

#[executable]: [dependencies]
#	[commands]
a.out: main.o header.o header.h		# 主依赖
	${CPP} ${OPTS} ${DEBUG} -o a.out main.o header.o
	
main.o: main.c
	${CPP} ${OPTS} ${DEBUG} -c main.c

header.o: header.c header.h
	${CPP} ${OPTS} ${DEBUG} -c header.c

clean:
		rm -f a.out *.o core
```

```shell
示例：
>> make			# 比较依赖文件.o文件的更新程度以决定是否重新编译
>> make main.o	# 指定编译
>> make clean	# 执行清理指令
```



### Libraries

中间目标文件（`.o`文件）存在UNIX/LINUX系统中的archive （`.a`文件）文件内。

静态库：`.a` (LINUX), `.lib` (WINDOWS)

动态库：`.so, .a`(LINUX), `.dll`(WINDOWS)



eg: 

LINUX: /usr/lib

```shell
>> nm -g /usr/lib/libm.a			# 使用 nm 显示 math 库符号链接
>> ar -t /usr/lib/libm.a | more		# 使用 ar 展示 libm( math )内容
>> gcc main.c -lm					# 指明 libm 编译
```

规范：	

`-l[libname]`  指定`gcc `编译器寻找库文档 `lib[libname].a`

`-Lpath` 其中 `path` 指定寻找的路径		 e.g. `-L/yang/lib/`



#### 示例：创建`header.a` 静态链接库

```shell
>> ls
header.c	header.h	header.o	main.c
>> ar -cq libheader.a header.o
>> ls
header.c	header.h	header.o	main.c	libheader.a
>> gcc main.c -lheader -L		# 带库编译 
```

```shell
#ar 语法
ar [-dmpqrtx][cfosSuvV][a<成员文件>][b<成员文件>][i<成员文件>][备存文件][成员文件]
#---------------------------------------------------------
#-d 　删除备存文件中的成员文件。
#-m 　变更成员文件在备存文件中的次序。
#-p 　显示备存文件中的成员文件内容。
#-q 　将文件附加在备存文件末端。
#-r 　将文件插入备存文件中。
#-t 　显示备存文件中所包含的文件。
#-x 　自备存文件中取出成员文件
#---------------------------------------------------------
#c 　建立备存文件。
#v 　程序执行时显示详细的信息。
#s 　若备存文件中包含了对象模式，可利用此参数建立备存文件的符号表。
#S 　不产生符号表。
#---------------------------------------------------------
e.g.
>> ar rv two.bak *.c		# 打包以.c 结尾的文件
>> ar d two.bak a.c b.c		# 删除打包文件中的a.c b.c
>> ar t two.bak				# 显示打包文件的内容
```





#### 示例：创建`header.so`动态链接库 (so: shared object)

```shell
# 通过目标文件产生共享库（.so file）
>> ld -shared header.o -o header.so
# 使用
>> gcc main.c header.so
```

```shell
# ld 语法(link-editor for object files)


```

```shell
# 将当前路径加入到 LD_LIBRARY_PATH
>> setenv LD_LIBRARY_PATH ${LD_LIBRARY_PATH}:.
>> ./a.out
```

----------



附：

##### Listing symbols tool: `nm`,`readelf`,`objdump`（列举索引表）

```shell
>> nm -g yourLib.a 						# 静态链接库符号显示
>> nm -gD yourLib.so					# 只显示外部可见的符号（动态）
>> readelf -Ws /usr/lib/libexample.so	# 显示所有符号（动态/静态）
>> objdump -TC /usr/lib/libexample.so	# 动态
```



##### 几种文件`.so`, `.a`, `.la` 

`.so` 动态库（shared object）, 所用调用库者链接这一个文件，而不是在每个可执行文件中添加一个副本。

`.a` 静态库（archive），通过使用`ar`--(`tar`的前身，现在只用来根据`.o`目标文件制作库)。

`.la` GNU `libtools`软件包用来描述组成相应库的文件的**文本文件**（平台独立）

