title: 不同内核版本间的差异
date: 2015-11-22 09:59:02
categories: Techniques
---

## ioctl and unlocked_ioctl

从 2.6.36 版本的内核开始，file\_operations 结构体弃用了原来的 ioctl 成员，改用了新的名为 unlocked\_ioctl 成员，该成员指向的函数删除了之前函数的第一个参数 inode，所以为了使设备驱动适应多个不同的内核版本，就需要在代码中对内核版本进行判断，进而采用不同的函数的声明：

这里，我们需要包含头文件 <linux/version.h>。

``` c
#if LINUX_VERSION_CODE < KERNEL_VERSION(2, 6, 36)
    static int xxx_ioctl(struct inode *inode, struct file *filp, unsigned int cmd
, unsigned long *arg);
#else
    static int xxx_ioctl(struct file *filp, unsigned int cmd, unsigned long *arg);
#endif
```

注意：在函数定义的时候也需要用上面的判断来写函数头。

同样：

``` c
static const struct file_operations xxx_fops = 
{
    ......
    #if LINUX_VERSION_CODE < KERNEL_VERSION(2, 6, 36)
    .ioctl = xxx_ioctl,
    #else
    .unlocked_ioctl = xxx_ioctl,
    #endif
    ......
}
```

## init\_MUTEX_LOCKED and sema\_init

从 2.6.37 版本的内核开始，init\_MUTEX\_LOCKED 宏遭到了弃用，我们只能使用 sema\_init 来对锁进行操作，具体代码如下：

``` c
struct semaphore sem;
#if LINUX_VERSION_CODE < KERNEL_VERSION(2, 6, 37)
    init_MUTEX_LOCKED(&sem);
#else
    sema_init(&sem, 0);
#endif
```

## smp\_lock.h and hardirq.h

从 2.6.39 版本的内核开始，内核不再包含 smp\_lock.h，取而代之的是 hardirq.h。

``` c
#if LINUX_VERSION_CODE < KERNEL_VERSION(2, 6, 39)
    #include <linux/smp_lock.h>
#else
    #include <linux/hardirq.h>
#endif
```

## asm/system.h 文件

从 3.4 版本开始，内核不再提供 asm/system.h 文件。
