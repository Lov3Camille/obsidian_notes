---

layout:     post
title:      "Core Java - Daily Notes - 01"
subtitle:   " \"JAVA Knowledge - Advanced Features\""
date:       2020.08.27 15:00:00
author:     "Wuy"
header-img: "img/post-bg-2020.jpg"
catalog: true
tags:
    - 学习笔记
    - 读书
    - JAVA

---

> *"Keep Learning JAVA"*

# 异常、断言和日志

为了尽量避免程序的错误或者一些外部环境影响导致用户运行程序期间所有工作尽失，至少应该做到以下几点：

- 向用户通知错误；
- 保存所有的工作；
- 允许用户妥善地退出程序。

对于异常情况，JAVA使用了一种称为**异常处理（exception handling）**的错误捕获机制。

## 1. 处理错误

为了能够处理程序中的异常情况，需要考虑的问题包括：

- 用户输入错误；
- 设备错误；
- 物理限制：磁盘已满等；
- 代码错误：错误的调用、试图让一个空栈执行弹出操作等；

这里我们不能简单地使用返回错误码进行分析，以免造成误解。需要有一个异常处理机制，去搜索能够处理这种情况的异常处理器。

异常也有自己的语法和特定的继承层次结构，下面首先介绍语法。

### 1.1. 异常分类

所有的异常都是由Throwable继承而来的，而在下一层立即分解为两个分支：`Error`和`Exception`。

`Error`类层次结构描述了Java运行时系统的内部错误和资源耗尽错误，不常出现。

我们要重点关注Exception层次结构，而这个层次结构又分解为两个分支：`RuntimeException`和其他异常。

`RuntimeException`异常包括以下问题：

- 错误的强制类型转换；
- 数组访问越界；
- 访问`null`指针。

其他异常包括：

- 试图超越文件末尾继续读取数据；
- 试图打开一个不存在的文件；
- 试图根据给定的字符串查找Class对象，而这个字符串表示的类并不存在。

Java语言规范将派生于`Error`类或者`RuntimeException`类的所有异常称为非检查型异常，所有其他异常称为检查型异常。

### 1.2. 声明检查型异常

这种情况下，方法不仅需要告诉编译器将要返回什么值，还要告诉编译器可能发生什么错误。

例如，下面是标准类库中FileInputStream类的一个构造器的声明：

```java
public FileInputStream(String name) throws FileNotFoundException
```

也就是说，如果参数出错，就会抛出一个`FileNotFoundException`类对象，进而系统开始搜索知道如何处理这一对象的异常处理器。

其实不必声明这个方法可能抛出的所有异常，至于何时需要声明，已经声明哪些异常，需要记住以下四种情况：

- 调用了一个抛出检查型异常的方法；
- 检测到一个错误，并利用throw语句抛出一个检查型异常；
- 程序出现错误，例如`a[-1] = 0` 会抛出一个非检查型异常；
- Java虚拟机或运行时库出现内部错误。

**特别注意，前两种情况下，必须捕获异常，否则当前执行的线程就会终止。**

对于包含在对外提供的类中的方法，应该通过方法首部的异常规范声明这个方法可能抛出异常：

```java
class MyAnimation
{
    ...
        public Image loadImage(String s) throws IOException
    {
        ...
    }
}
```

如果一个方法可能抛出多个检查型异常类型，那么就必须在方法的首部列出所有异常类，之间用逗号隔开。

但是，不需要声明Java的内部错误，即从`Error`、`RuntimeException`继承的异常，因为我们对此完全无法控制。

总之，一个方法必须声明所有可能抛出的检查型异常，如果没有声明完全，编译器就会发出一个错误消息。

**警告：子类声明的异常不能比超类的更通用，另外，如果超类没有抛出任何检查型异常，那么子类也不能抛出任何检查型异常。**

**另外，抛出的异常可能属于某个特定类，也可能是这个类的任意一个子类。**