---
layout: post
published: true
title: "C++中容易混淆的关键字"
description: c++, keyword
---
## volatile

volatile关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器位置的因素更改，比如：操作系统、硬件或是其他线程等。遇到该关键字声明的变量，编译器对访问该变量的代码不再进行优化，从而可以提供对特殊地址的稳定访问。如果被volatile修饰，系统总是重新从它所在的内存读取数据，即使它前面的指令刚刚从该处读取过数据。

## volatile指针

和const修饰词类似，const有常量指针和指针常量的说法，volatile也有相应的概念，不再赘述。

注意：

- 可以把一个非volatile int赋值给volatile int，但是不能把非volatile对象赋值给一个volatile对象

- 除了基本类型外，对用户定义类型也可以用volatile类型进行修饰

- C++中有一个有volatile标识符的类只能访问它接口的子集，一个由类的实现者控制的子集。用户只能用const_cast来获得对类型接口的完全访问。此外，volatile向const一样会从类传递到它的成员。

## 多线程下的volatile

有些变量是用volatile关键字声明的。当两个线程都要用到某一个变量且该变量的值会被改变时，应该使用volatile声明，该关键字的作用是防止优化编译器把变量从内存装入CPU寄存器中。如果变量被装入寄存器，那么两个线程有可能一个使用内存中的变量，一个使用寄存器中的变量，这会造成程序的错误执行。volatile的意思是让编译器每次操作该变量时一定要从内存中真正取出，而不是使用已经存在在寄存器中的值，如下：

```
volatile bool bStop = FALSE;
(1) 在一个线程中
while(!bStop) { ... }
bStop = FALSE;
return

(2) 在另外一个线程中，要终止上面的线程循环
bStop = TRUE;
while(bStop);//等待上面的线程终止，如果bStop不适用volatile申明，那么这个循环将是一个死循环，因为bStop已经读取到了寄存器中，寄存器中的bStop的值会永远不会变成FALSE。加上volatile，程序在执行时，每次均从内存中读出bStop的值，就不会死循环。
```

这个关键字是用来设定某个对象的存储位置在内存中，而不是在寄存器中。因为一般的对象编译器可能会将其拷贝放在寄存器中用以加快指令的执行速度，例如：

```
int nMyCounter = 0;
for(; nMyCounter<100; nMyCounter++) {
    ....
}
```

在此段代码中，nMyCounter的拷贝可能只存放到某个寄存器中（循环中，对nMyCounter的测试及操作总是对该寄存器中的值进行），但是另外又有一段代码执行了这样的操作：nMyCounter -= 1;这个操作中，对nMyCounter的改变是对内存中的nMyCounter的改变是对内存中的nMyCounter进行操作，于是出现了这样一个现象：nMyCounter的改变不同步。

## 参考
- [volatile关键字详解](https://www.cnblogs.com/yc_sunniwell/archive/2010/07/14/1777432.html)
