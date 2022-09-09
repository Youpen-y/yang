---
premalink: /yang/blogs/specialmacro.html
title: special macro
---



```__BEGIN_DECLS```与``__END_DECLS`` 

macros are used to write C header files that are compatible with C++ compilers.

_Example:_

```
#ifndef __MY_H				/* Note: _MY_H can not be used because reserved */
#define __MY_H

__BEGIN_DECLS

//	your C code

__END_DECLS

#endif
```

_Macro:_

```c
#ifdef __cplusplus
#define __BEGIN_DECLS	extern "C"{
#define __END_DECLS	}			/* __END_DECLS is the same with '}' */ 
#else
#define __BEGIN_DECLS
#define __END_DECLS
#endif
```

-----

About `extern "C"{}` _macro:_

```
#ifdef __cplusplus
extern "C" {
#endif

// legacy C code

#ifdef __cplusplus
}
#endif
```

-------------------------------------------

`cat`的本质实现

```c
#define stdin	0
#define stdout	1
#define stderr	2

char buf[512];
int n;

for(; ;){
	n = read(0, buf, sizeof buf);		// prototype: read(fd, buffer, n)
	if(n == 0)
		break;
	if(n < 0){
		fprintf(2, "read error\n");
		exit();
	}
	if(write(1, buf, n) != n){			// fd == 1 means stdout
		fprintf(2, "write error\n");
		exit();
	}
}
```

------





