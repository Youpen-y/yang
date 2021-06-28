---
premalink: /blogs/compiletricks.html
title: compiletricks
---

#### gcc/g++（技巧）

编译4阶段：

- **预处理**		-> `.i` file (预处理后的C 源代码),	-> `.ii` file(预处理后的C++源代码)
- **编译**            -> `.s` file (汇编语言源代码)
- **汇编**            -> `.o` file (目标文件)
- **链接**            -> `.exe` (可执行文件)



总结**gcc/g++** 使用(以**gcc** 为例， **g++** accepts mostly the same options as **gcc**)

1. `-s` 产生汇编文件

~~~shell
>> gcc -S main.c		// 产生main.s 汇编文件
>> size ./main.exe		// 查看各个段（text, data/bss, stack, heap）的大小
~~~

2.   `-c` 只编译不链接，编译产生目标文件 `.o` file

```
>> gcc -c main.c
```

3. `-E` 选项告诉GCC在某一过程后停止编译过程

```
# 在预处理后停止，之后继续编译至main.exe
>> gcc -E main.c -o main.i
>> gcc -S main.i -o main.s
>> gcc -c main.s -o main.o
>> gcc main.o -o main.exe
```

4. `-o` EXECUTABLE， 生成可执行文件 `EXECUTABLE` ， 默认 `a.out` (assembly output)
5.  `-g` 在可执行程序中包含标准调试信息。之后可使用 `gdb` 调试 。 [gdb](./gdb.html)
6. `-O` 优化编译过的代码
7. `-Wall` 允许发出GCC 能提供的所有有用的警告
8. `-werror` 将警告转化为错误
9. `-v` 显示在编译过程的每一步中用到的命令
10. `-I{DIRNAME}` 增加GCC搜索的路径，以便找到个人在 `DIRNAME` 存放的头文件(`/home/yang/include/hello.h`)

```shell
>> gcc main.c -I /home/yang/include	-o main
```

11. `-L{DIRNAME}` 增加GCC搜索库的路径。(`/home/yang/lib/libhello.so`)， 详见[Library](./Library.html)

```shell
>> gcc main.c -L/home/yang/lib -lhello -o main
>> gcc main.c -L/home/yang/lib -I /home/yang/include -lhello -o main
```

`-std=` 指定语言的标准。

e.g.`-std=c89`, `-std=c++98`,  `std=c++0x` (*Enable experimental features that may be removed in future versions of gcc*)



#### 代码优化



#### 其他指令

 `time` 量测特定指令执行时所需消耗的时间及系统资源等。

```shell
>> type time
time is a shell keyword
>> time ./a.exe
Allocated 1898 total MB

real    0m0.062s
user    0m0.000s
sys     0m0.000s
---------------------------------------
# 查看执行时间及优化
>> gcc -Q hello.c
 getc putc getchar putchar _get_output_format _set_output_format _get_printf_count_output _set_printf_count_output fopen64 ftello64 main
Analyzing compilation unit
Performing interprocedural optimizations
 <*free_lang_data> <visibility> <early_local_cleanups> functions:
 main
Execution times (seconds)
 phase setup             :   0.02 (50%) usr     529 kB (61%) ggc
 phase opt and generate  :   0.02 (50%) usr      22 kB ( 3%) ggc
 rest of compilation     :   0.02 (50%) usr       1 kB ( 0%) ggc
 TOTAL                 :   0.03               868 kB

-------------------------------------
# 查看交换空间
>> /usr/sbin/swap -s
...

-------------------------------------
# 展示ELF文件信息
>> file a.exe
a.exe: PE32 executable (console) Intel 80386 (stripped to external PDB), for MS Windows

-------------------------------------
# 查看ELF文件各段大小
>> size a.exe
   text    data     bss     dec     hex filename
  36908    1748    2608   41264    a130 a.exe
```



