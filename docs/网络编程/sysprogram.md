---
layout: default
title:  linux 内核及系统编程
parent: 网络编程
last_modified_date:  

---

# 文件描述符与文件描述符表

前面我们介绍过Linux中有一个结构体task_sturct专门用来控制进程叫做进程描述符，在它的里面存放了各种关于进程的信息，其中有一个指针，源码中给出的定义为：struct file_struct *file ,它指向一个file_struct结构体，即文件描述符表，每个进程都有一个自己的文件描述符表。

![](../../docimgs/sysprogram_imgs/2022-10-23-21-40-24-image.png)

我们所说的 **文件描述符（fd）** 就被写在这个file_struct之中，那么什么是文件描述符呢？

其实简单来说就是file_struct中的索引。文件描述符表的每一个表项都指向一个打开的文件，用户程序不能直接访问内核中的文件描述符表,而只能使用文件描述符表的索引 (即0、1、2、3这些数字),这些索引就称为文件描述符,用int 型变量保存。当调用open 打开一个文件或创建一个新文件时,内核分配一个文件描述符并返回给用户程序,该文件描述符表项中的指针指向新打开的文件。当读写文件时,用户程序把文件描述符传给read 或write ,内核根据文件描述符找到相应的表项,再通过表项中的指针找到相应的文件。每一个程序打开的时候都会自动打开文件描述符表中的0，1，2，他们分别是标准输入，标准输出和标准错误。

默认情况下(没有重定向),每个进程的标准输入(stdin)、标准输出(stdout)和标准错误输出(stderr)都指向控制终端,因为在程序启动时(在main 函数还 没开始执行之前)会自动把控制终端打开三次,分别赋给三个FILE *指 针stdin 、stdout和stderr,这三个文件指针是libc 中定义的全局变量,这三个文件的描述符分别是0、1、2,保存在相应的FILE 结构体中。进程从标准输入读也就是读用户的键盘输入,进程往标准输出或标准错误输出写也就是输出到显示器上。

## 文件表

文件描述符里存放的内容就是一个指向文件表的指针。
Linux中文件描述符的定义

```cpp
struct fd {
 struct file *file;//指向文件表的指针
 unsigned int flags;
};
```

它所指向的结构体file就是我们所说的文件表。下面我们来看一下file的源码

```cpp
struct file {
    union {
        struct llist_node   fu_llist;
        struct rcu_head     fu_rcuhead;
    } f_u;
    struct path     f_path;//文件的存放路径
    struct inode        *f_inode;   /* cached value */
    const struct file_operations    *f_op;//指向结构体file_operations的指针

    /*
     * Protects f_ep_links, f_flags.
     * Must not be taken from IRQ context.
     */
    spinlock_t      f_lock;
    atomic_long_t       f_count;//文件引用计数
    unsigned int        f_flags;//文件状态标记符
    fmode_t         f_mode;
    struct mutex        f_pos_lock;
    loff_t          f_pos;//文件读写位置
    struct fown_struct  f_owner;
    const struct cred   *f_cred;
    struct file_ra_state    f_ra;

    u64         f_version;
#ifdef CONFIG_SECURITY
    void            *f_security;
#endif
    /* needed for tty driver, and maybe others */
    void            *private_data;

#ifdef CONFIG_EPOLL
    /* Used by fs/eventpoll.c to link all the hooks to this file */
    struct list_head    f_ep_links;
    struct list_head    f_tfile_llink;
#endif /* #ifdef CONFIG_EPOLL */
    struct address_space    *f_mapping;
} __attribute__((aligned(4)));  /* lest something weird decides that 2 is OK */
```

这个结构中包含了文件的一些基本信息。
其中几个比较重要的有
struct path f_path：文件存放的路径,它通过目录表（dentry）找到inode的目录。
const struct file_operations *f_op：指向文件操作表的指针，这个表里面存放了对文件的操作函数例如：read，write…..
struct inode *f_inode：指向inode的指针，inode存放了从磁盘上读上来的信息。
atomic_long_t f_count：文件的引用计数，当引用计数为零的时候文件就关闭了。所以，同时也允许多个多个进程打开一个文件。
unsigned int f_flags：存放了文件的状态，例如只读，只写，或者可读可写。
loff_t f_pos：存放当前文件读写的位置。

# 文件描述符和FILE结构体

[探索文件描述符（fd）与FILE结构体之间的关系_小葱1024的博客-CSDN博客](https://blog.csdn.net/qq_34992845/article/details/71446333)
