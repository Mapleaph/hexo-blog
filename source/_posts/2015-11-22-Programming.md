title: Programming
date: 2015-11-22 10:47:47
categories: Techniques
---

## 读取文件内容，每次处理一行

``` bash
#!/bin/bash
while read <line>
do
    echo $<line> # or something else you want to do
done < <filename>
```

## 同时打开两个新终端并执行程序

源代码

``` c
/*
 * test.c
 */
 
#include <pthread.h>

void* func1(void* args)
{
    system("gnome-terminal -e ./test1");
    
    return NULL;
}

void* func2(void* args)
{
    system("gnome-terminal -e ./test2");
    
    return NULL;
}

int main()
{
    pthread_t thread1, thread2;
    pthread_create(&thread1, NULL, func1, NULL);
    pthread_create(&thread1, NULL, func1, NULL);
    
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);
    
    return 0;
}
```

生成可执行程序

``` bash
$ gcc test.c -o test -lpthread
```

## 为新线程传递参数

源代码

``` c
#include <stdio.h>
#include <pthread.h>

struct thread_args {
    int arg1;
    int arg2;
};

/* 无论是否传递参数，线程所执行的函数都应该具有如下声明 */
void* func(void* args)
{
    struct thread_args* myargs = (struct thread_args*) args;
    
    printf("arg1 is %d, arg2 is %d\n", myargs->arg1, myargs->arg2);
    
    return NULL;
}

int main()
{
    pthread_t mythread;
    struct thread_args myargs;
    
    myargs.arg1 = 100;
    myargs.arg2 = 200;
    
    /* 必须以结构体的形式，将参数传递给新线程 */
    pthread_create(&mythread, NULL, func, &myargs);
    
    pthread_join(mythread, NULL);
    
    return 0;
}
```

## warning: address of stack memory...

如果我写了一个返回指针的函数，而在函数体的 return 后面返回的是变量的地址，编译器就会给出警告，说这样是不安全的，所以我们再增加一个指针变量，将那个变量的地址赋给指针变量，最后再返回就好了。

即：应返回指针，而不是变量地址。

## Simple Intro to GDB

``` c
// this is a really simple program called fact.c 
// to calculate the factorial of 4
// the expecting result is 24

#include <stdio.h>

int main()
{
    int i, j;
    
    for (j=1; j<5; j++) {
        i = i * j;
    }
    
    printf("the result is %d\n", i);
    
    return 0;
}
```

``` bash
# we use gcc to compile it
$ gcc -o fact fact.c
# then run it
$ ./fact
the result is 0
# that is not what we expeted!
```

So we use gdb to check what is going on~~~

To use gdb, we should add -ggdb to the gcc command line to include information for debugging.

``` bash
$ gcc -ggdb -o fact fact.c
```

Then, We just run the following command to start gdb.

``` bash
$ gdb ./fact
```

and you will get a prompt like this,

``` bash
(gdb)
```

Next, type **r** or **run** and hit enter, you will get the entire program running just like what running the fact executable.

``` bash
(gdb) r
```

What if we want to stop the program somewhere, just type **b fact.c:5** or **break fact.c:5** to stop the program at line 5 in the source code, or type **b fact.c:main** to stop the program from the very beginning.

``` bash
(gdb) b fact.c: main
```

If you want to see the source code to determine which line you should break, just type **l** to show a piece of the source code, then another **l**, you will get another piece, also **l 12** will get you to the line 12.

``` bash
(gdb) l 12
```

Suppose now you are at the **for** loop line, and you want to check the value of i, you can simply type **p i** or **print i** to print the current value of the variable i, further more, if you want to show all the variables, just type **info locals**.

``` bash
(gdb) info locals
```

If you want to change the value of some variable, say change the value of i from nothing to 1, you can type **p i = 1**, then hit enter, then **c** or **continue** to continue the rest of the program or **n** to run just the next line of code, if we want to execute the program line by line, you can save you typing by just hit enter, the enter will execute the previous command which is **n**.

``` bash
(gdb) p i = 1
```

If you want to quit the gdb, just type **q** or **quit**.

``` bash
(gdb) q
```

## C redifinition error

如果工程中有多个头文件，并且有的会互相包含，那么有可能会在编译的时候报出重复定义的错误，因为有的头文件被包含了不止一次，可以通过如下方式解决这个问题：

``` c
#ifndef __SOMEFILE\_H__
#define __SOMEFILE\_H__

/*
 *
 * things in the file
 *
 */
#endif
```

头文件只会在变量未定义的情况下被包含一次，这样就很好的避免了重复定义的错误。

## 如何编写并使用 DLL

### 生成 DLL

首先在 VC++ 6.0 中新建 Win32 Dynamic-Link Library 工程 testDLL，然后新建如下两个文件，EXPORT.h 和 testDLL.cpp。

``` c
/*
 * EXPORT.h
 */

#ifdef EXPORTING_DLL
extern "C" __declspec (dllexport) int add(int a, int b);
#else
extern "C" __declspec (dllimport) int add(int a, int b);
#endif

/*
 * testDLL.cpp
 */


#define EXPORTING_DLL
#include "EXPORT.h"

int add(int a, int b)
{
    return a + b;
}
```

编译之后，会生成两个文件 testDLL.dll 和 testDLL.lib。

### 使用 DLL

1. 新建一个 Win32 Console Application 工程 test，将 testDLL.lib 添加到
Project-\>Settings-\>Link 选项卡下的 Object/library modules 中。

2. 将刚才生成的 testDLL.dll，testDLL.lib 以及 EXPORT.h 复制到 test 的工程目录中。

3. 在 test 工程中新建文件 test.c 并添加如下内容

``` bash
/*
 * test.c
 */

#include "EXPORT.h"

int main()
{
    printf("the result of 1+2 is %d\n", add(1, 2));
    return 0;
}
```


### 使用 vs2013 需要注意的地方

#### 让 vs2013 生成兼容 XP 的应用程序

默认是不兼容 XP 的。

右键单击工程->Properties->General->General->Platform Toolset: Visual Studio 2013 - Windows XP (v120_xp)

#### 字符集设置

默认情况下，使用双引号字符串会报错，采用下面的方法避免这个错误的产生。

右键单击工程->Properties->General->Project Defaults->Character Set: Not Set/Use Multi-Byte Character Set

#### 动态库使用问题

右键单击工程->Properties->Linker->Input->Additional Dependencies，然后添加自己的 DLL。

#### 修改 MFC 默认工程的图标

ico 文件一般可以通过 png 文件转换得到。
直接将做好的 ico 文件替换到工程中的 res 目录，然后重新编译即可。

#### DeviceIoControl

如果想要使用 IOCTL 的特定控制字，需要调用 DeviceIoControl 函数，该函数需要头文件 windows.h。

## astyle Code Formatting Tool

astyle 是一个开源的代码格式化工具，现在将常用选项列在下面：

| 选项              | 描述            |
| ---------------- | --------------- |
| --style=kr / -A3 | 采用 K&R 代码格式 |
| --preserve-date / -Z | 不改变文件修改时间 |
| --unpad-paren / -U   | 将括号内侧空格去掉 |
| --pad-header / -H    | 在 if/else 等后面添加空格 |
| --align-pointer=type / -k1 | 将指针前的星号和指针类型连在一起 |
| --align-reference=name / -W3 | 将&符号同所引用的变量连在一起 ｜
| --convert-tabs / -c | 将 tab 转换为 space |
| --indent=spaces=4 / -s4 | 采用空格进行缩紧，一个单位为 4 个空格 |
| --break-blocks / -f | 在不相关的代码块间插入空格 |
| --add-brackets / -j | 在单行条件代码外添加大括号 |
| --errors-to-stdout / -X | 将错误输出到标准输出上 |

## referencing an external css file

``` html
<link rel="stylesheet" type="text/css" href="stylesheet.css"\>
```

## module object has no attribute 'OrderedDict'

OrderedDict 在 Python2.7 才开始加入了该支持，所以如果 Python 的当前版本低于 2.7，就会报这个错误。