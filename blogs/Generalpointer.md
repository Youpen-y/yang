---
premalink: /blogs/generalpointer.html
title: 通用目的指针
---



### 	void * - 通用目的指针

```c
void *ptr;
```

`ptr`:一个存储地址的变量但不指明存储数据的类型。

不需要显式转换就可以转换为指向不同类型对象的指针

```C
int *iptr = ptr;	// ok
double *dptr = ptr; // ok
struct Type *Tptr = ptr; //ok
```

Note：

1. 无法解引用。 i.e. `*ptr` wrong， 但可显示转换后解引用`*( (type*)ptr) )`	
2. 不支持指针运算



```C
void qsort(void *base, size_t nmemb, size_t size,
          	int (*compar)(const void *, const void *));		// c 排序数组声明
//or typedef int (*compar)(const void *, const void *), so we can replace it with compar in the declaration.
// base: 数组基址	nmemb: 数组元素个数	size: 数组元素大小	compar: 比较数组两元素的函数指针
int int_array[10];
long long_array[50];
...
qsort(int_array, sizeof int_array/ sizeof int_array[0], sizeof int_array[0], compareInt);

/*	compareInt() function
int compareInt (const void *lhs, const void *rhs)
{
	const int *x = lhs;	
	const int *y = rhs;
	
	if (*x > *y) return 1;
	if (*x == *y) return 0;
	return -1;
}
*/
```



