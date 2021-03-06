# Shell


## 概念

- Shell：俗称壳（用来区别于核），是指“为使用者提供操作界面”的软件（命令解析器）。


## 目标

**稍微清理一下代码并解析用户输入**

在这节课我们将做两件事。首先，我们将稍微清理一下代码，以便为进一步的课程做好准备。
在之前的课程中，我们试着把代码放在最容易预测的地方，但知道代码库何时增长，并使其
适应当前和进一步的需求，也是一个很好的练习。


## 代码清理

首先，我们很快将需要更多的实用函数来处理字符串等等。在常规操作系统中，这称为C库，
简称`libc`。

现在我们有一个`util.c`，我们将会把它分解成`mem.c`和`string.c`，以及它们各自的头文件。

然后，我们将创建一个新的函数`irq_install()`，这样内核只需执行一次调用就可以初始化
所有的`IRQs`。该函数类似于`isr_install()`，并放置在相同的`isr.c`上。在这里，我们
将禁用`timer_callback()`上的`kprint()`，以避免用垃圾填充屏幕，现在我们已经知道它
可以正常工作了。

`cpu/`和`drivers/`之间没有明显的区别。请记住，作者在创建本教程的同时还在学习其他
许多教程，并且每个教程都有不同的目录结构。我们现在要做的唯一更改是移动`drivers/port.*`
到`cpu/`，因为它显然是CPU相关的代码。`boot/`也是CPU相关的代码，但是在为不同的机器
实现引导序列之前，我们不会对其进行修改。

`Makefile`中`CFLAGS`的开关也将增多，因为我们现在将开始为我们的C库创建更高级别的函
数，并且如果我们在声明中犯了错误，我们不希望编译器为此错误地引入任何外部代码。我
们还添加了一些标志来将警告转换为错误，因为转换指针的一个明显的小错误可能会在稍后
才爆发。这也迫使我们修改代码中混杂的一些指针声明。

最后，我们将添加一个宏，以避免`libc/function.h`上未使用的参数出现警告错误。


## 键盘字符

那么，如何访问键入的字符呢？

- 按下一个键时，回调通过在`keyboard.c`开始处定义的新数组获取对应的ASCII码
- 然后回调将该字符追加到缓冲区`key_buffer`
- 它也会被打印在屏幕上
- 当操作系统需要读取用户输入时，它调用`libc/io.c:readline()`

`keyboard.c`也可以解析退格，方法是删除键缓冲区的最后一个元素，并通过调用
`screen.c:kprint_backspace()`从屏幕上删除它。为此，我们需要修改一下`print_char()`，
以便在打印退格时不推进偏移量。


## 用户输入响应

键盘回调检查换行符，然后调用内核，告诉它用户有输入。最后一个libc函数是`strcmp()`，
它比较两个字符串，如果它们相等就返回0。如果用户输入“END”，我们将停止CPU。

这是最基本的Shell，但是你应该感到自豪，因为我们从头开始实现了它。你知道这有多酷吗？

如果你有需要，可以扩展`kernel.c`来解析更多的东西。将来，当我们拥有一个文件系统时，
我们将允许用户运行一些基本命令。
