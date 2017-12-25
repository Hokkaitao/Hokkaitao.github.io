---
layout: post
published: true
title: "C++格式化输出"
description: format, c++
---
## 使用控制符控制输出格式

```
//以十进制形式输出整数
cout<<"dec:"<<dec<<a<<endl;
//以十六进制形式输出
cout<<"hex:"<<hex<<a<<endl;
//以八进制形式输出
cout<<"oct:"<<setbase(8)<<a<<endl;
//指定域宽
cout<<setw(10)<<pt<<endl;
//指定域宽和填充
cout<<setfill('*')<<stw(10)<<pt<<endl;
//按指数形式输出，8位小数
cout<<setiosflags(ios::scientific)<<setprecision(8);
//按小数形式输出，4位小数
cout<<setiosflags(ios::fixed)<<setprecision(4);
```

## 使用流对象的成员函数控制输出

```
//显示基数符号
cout.self(ios::showbase);
//终止十进制格式设置
cout.unsetf(ios::dec);
//设置十六进制输出
cout.setf(ios::hex);
//设置域宽
cout.width(10);
//设置填充
cout.fill('*');
//科学计数法形式输出
cout.setf(ios::scientific);
//设置精度
cout.precision(6);
```

## 注意

- 成员函数width(n)和控制符setw(n)只对其后的第一个输出项有效

- 对输出格式的控制，即可以使用控制符，也可以使用cout流有关的成员函数，二者的作用是相同的。控制符是在头文件iomanip中定义的，因此用控制符时，必须包含iomanip头文件。cout流的成员函数是在头文件iostream中定义的，因此只需包含头文件iostream，不必包含iomanip。

## 参考
- [C++格式化输出](https://www.cnblogs.com/yongdaimi/p/7126409.html)
