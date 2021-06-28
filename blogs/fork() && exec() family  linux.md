---
premalink: /blogs/fork&exec.html
title: linux task
---



### 										() && exec() family		<linux>

#### fork()	- create a child process

```c
#include <unistd.h>
pid_t fork(void);
```

通过复制调用进程（父进程）创建一个新的进程（子进程）。

成功，父进程返回创建子进程的`pid`，子进程返回0		`//(int)getpid();	//访问进程pid`

失败， 父进程返回 -1， 没有创建子进程，设置`errno`指示错误。	

访问错误原因:

```
#include <string.h>	// for strerror()
#include <errno.h>	// for errno(记录系统最后一次的error code)
strerror(errno);
```





#### exec() 	- execute a file

```C
#include <unistd.h>
extern char **environ;
int execl ( const char *path, const char *arg, ... );
// execl("/bin/ls", "ls", NULL);	
int execlp( const char *file, const char *arg, ... );
int execle( const char *path, const char *arg, ..., char *const envp[] );
int execv ( const char *path, char *const argv[] );
int execvp( const char *file, char *const argv[] );
/* char *argv[3] = {"Command-line", ".", NULL};
*  execvp("find", argv);	//以p结尾函数将自动查寻PATH(/bin/,/usr/bin/)
*/
//int execve( const char *file, char *const argv[], char *const envp[]);
//只有execve是真正意义上的系统调用，其它是在此基础上经过包装的库函数
int execvpe( const char *file, char *const argv[], char *const envp[] );

//char *envp[] = {"USER=youpen", "HOME=/home/youpen", NULL}
```

使用新的进程镜像取代当前进程镜像（根据指定的文件名找到可执行文件  e.g.`/bin/echo` bin 下的 echo 可执行文件）

`l` 表示逐个列举的方式 （参数作为一串strings传递给main()）

`v`表示将所有参数整体构造成指针数组传递

`p` 待运行程序的路径

`e`可以被调用者指定的环境

__execv, execvp, execvpe__ : 参数使用指向字符串的指针数组传递，最后一个条目为`NULL`

成功，函数不会返回

失败，返回-1，并设置errno

