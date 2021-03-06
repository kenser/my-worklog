# 引导扇区-内存

## 概念

内存偏移量、指针


## 目标

**了解计算机内存是如何组织的**


## 内存布局

![引导后的典型低位内存布局](https://raw.githubusercontent.com/garenchan/my-worklog/master/操作系统篇/os-tutorial/03-引导扇区-内存/memory-layout.png)

本课的唯一目标是参照上图了解引导扇区存储在哪里。

BIOS将其放置在`0x7C00`处并将其处理完毕，但是使用错误解决方案的示例将使事情更清楚。

我们想在屏幕上打印一个X，我们将尝试4种不同的策略，看看哪种有效，为什么有效。

**打开文件`boot_sect_memory.asm`**

首先，我们将X定义为数据，并有一个标签：
```nasm
the_secret:
    db "X"
```

然后，我们将尝试以许多不同的方式访问`the_secret`：

1. `mov al, the_secret`
2. `mov al, [the_secret]`
3. `mov al, the_secret + 0x7C00`
4. `mov al, 2d + 0x7C00`, `2d`是X字节在二进制文件中的实际位置

查看代码并阅读注释。

编译并运行代码。你应该看到类似于`1[2¢3X4X`的字符串，1和2后面的字节只是随机的垃圾。

如果你添加或删除指令，请记住通过计算字节数来获取X的新偏移量，并用新偏移量替换`0x2d`。

请不要继续下一节，除非你已经100%了解启动扇区偏移量和内存地址。


## 全局偏移量

现在，由于到处写偏移量`0x7c00`非常不方便，因此汇编器允许我们使用`org`命令为每个内
存位置定义一个“全局偏移量”：

```nasm
[org 0x7c00]
```

继续并打开**`boot_sect_memory_org.asm`**，你将看到使用引导扇区打印数据的规范方式，
也就是现在的`attempt 2`。编译代码并运行它，你将看到`org`命令是如何影响之前的每个解
决方案的。
