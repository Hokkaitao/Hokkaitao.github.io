---
layout: post
published: true
title: "const/static 总结"
description: const, static
---
## 概述
- static

static 重要用作控制变量的存储方式和可见性。static修饰变量时，该变量被分配到全局空间，该方法比起单纯定义全局变量来说，能够更好的控制变量的访问范围。

static修饰全局变量时，会改变其可见性，使其重围内部链接，其反义词为“extern”。

类中的static成员，则是数据类的，而非单个实例，可以在各个实例间交互使用。类的静态成员函数同样数据整个类，所以其没有this指针，这就导致了其仅能访问类的静态数据和静态成员函数。

static类成员必须在全局范围进行初始化，初始化时不能有static限定符，注意，普通的数据成员也不能在类的内部直接初始化。

- const 

const的出现是为了取代预编译指令，预编译仅仅进行简单值替换，缺乏类型的检测机制。同时,const常量通常不分配存储空间，而是将其保存到符号表中，这使得其读取非常高效。编译器编译阶段不会读取存储的内容，如果编译器为const分配了存储空间，其就不能成为编译其的常量。

C++中，const 可用于对函数的重载，被const修饰的函数被成为常成员函数，其不能更新类的成员变量，也不能调用类中没有被const修饰的成员函数，只能调用常成员函数。

类中的const成员必须在构造函数初始化列表中被初始化。

## C++类数据成员为const数组怎么初始化

- 问题描述: 

```
# C++98/0 下如何对a[3]初始化
class A {
public: 
	A(){}
	~A(){}
private:
	const int a[3];
};
```

- 解决方案
这是C++98/03的缺陷。
如果你的编译器支持C++11可以在构造函数初始化函数中初始化该类成员数组。

```
#include <iostream>
using namespace std;
class A {
public:
	A():a{1, 2, 3}{}
	~A(){}
private:
	const int a[3];
};
```

不支持C++11的话，或者声明称static的？（感觉这不是好办法）

```
#include <iostream>
using namespace std;
class A {
public:
	A(){}
	~A(){}
private:
	static const int a[3];
};
const int A::a[3] = {1, 2, 3};
```

另一中解决方案是，将const int a[3]定义成全局变量。

```
const int a[3] = {1, 2, 3};
```

## 参考
- [C++类数据成员为Const数组怎么初始化](https://yq.aliyun.com/wenzhang/show_93)
