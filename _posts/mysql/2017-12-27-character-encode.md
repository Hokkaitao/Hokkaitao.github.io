---
layout: post
published: true
title: "字符编码"
description: character encoding
---
## ASCII码

计算机中信息都是通过二进制进行表示的，每个二进制位（bit）都有0和1两种状态，因此一个字节（8bit）可以组合出256中状态，每一种状态对应一个符号，就是256中符号，从00000000到11111111。

上世纪60年代，美国制定了一套字符编码，0-32中状态规定了特殊的用途，一旦终端设备或是打印机遇上这些约定好的字节时，就要做之前约定的动作，例如遇上0x10，终端就要换行因此将这些0x20（十进制32）以下的字节状态成为“控制码”。后来又吧所有的空格、标点符号、数字、大小写字母分别用连续的自解状态表示，一直编到了127号，这样计算机就可以用不同字节来存储英文的文字了，这就是ANSI的ASCII编码（American Standard Code for Information Interchange，美国信息交换标准代码）。当时世界上所有的计算机都用同样的ASCII方案来保存英文文字。这一个字节中只占用了一个字节的后面7位，最前面的一位统一规定为0。

## 非ASCII编码

英语用128个符号编码就够了，但是用来表示其他语言，128个符号是不够的。于是一些欧洲国家决定，利用字节中限制的最高位编入新的符号。这样一来，这些欧洲国家使用的编码体系，可以表示最多256个符号。

但是，又出现了新的问题，不同国家有不同的字母，相同的编码对应的符号却不同。因此不管怎样，所有这些编码方式中，0-127表示的符号是一样的，不一样的是128-255这一段。

至于压轴国家的文字，符号就更多了，仅汉字就多大10万左右。一个字节只能表示256中发挥好，肯定不够，就必须使用多个字节表达一个符号。比如简体中文常见的编码方式是GB2312，使用两个字节表示一个汉字，所以理论上最多可以表示256*256=65536个字符。

## 中文编码

中文编码时，把ASCII中127号之后的奇异符号直接去掉，并且规定：一个小于127的字符的意义与原来相同，单两个大于127的字符连在一起时，就表示一个汉字，前面的一个字节（称之为高字节）从0xA1到0xF7，后面一个字节（低字节）从0xA1到0xFE，这样我们就可以组合出大约7000多个简体汉字。在这些编码里，还把数学符号、罗马希腊的字母、日文的假名都编码进去，连在ASCII里本来就有的数字、标点、字母都统统重新变了两个字节长的编码，这就是常说的“全角字符”，而原来在127号以下的哪些就叫“半角字符”，这种编码方案被称为“GB2312”，其实对ASCII的中文扩充。

后来一些复杂的汉字无法进行表示，于是干脆不再要求低字节一定是127号之后的内码，只要第一个字节是大于127就固定表示这是一个汉字的开始，不管后面跟的是不是扩展字符集里的内容。结果扩展之后的编码方案被成为GBK标准，GBK包括了GB2312的所有内容，同时又增加了近20000个新的汉字（包括繁体字）和符号。

后面又在其基础上增加了一些少数民族的字，GBK扩展成了GB18030。

中国的程序员们看到这一系列汉字编码的标准是好的，于是统称他们叫做“DBCS”（Double Byte Character Set双字节字符集）。在DBCS系列标准中，最大的特点是两个字节长的汉子字符和一个字节长的英文字符并存于同一套编码方案里，因此他们写的程序是为了支持中文处理，必须要注意串里的每一个字节的值，如果这个值是大于127的，那就认为一个双字节字符集里的字符出现了。

## Unicode

世界上存在多种编码方式，同一个二进制数字可以被解释成不同的符号。因此，要想打开一个文本文件，就必须知道其编码方式，否则用错误的编码方式解读，就会出现乱码。为什么电子邮件常常出现乱码，就是因为发信人和收信人的编码方式不一样。

Unicode是一个很大的集合，现在的规模可以容纳100多万个符号。每个符号的编码都不一样。

需要注意的是，Unicode只是一个符号集，它只规定了符号的二进制代码，却没有规定这个二进制代码应该如何存储。这里就有两个严重的问题，第一个问题是，如何才能区别Unicode和ASCII？计算机怎么知道三个字节表示一个符号，而不是分别表示三个符号呢？第二个问题是，我们已经知道，英文字母只用一个字节表示就够了，如果Unicode统一规定，每个符号用三个或四个字符表示，那么每个英文字母必然有二到三个字节是0，这对于存储来说是极大的浪费，文本文件的大小会因此大出二、三倍，这也是无法接受的。

他们造成的结果是：1)出现了Unicode的多种存储方式，也就是说有许多种不同的二进制格式，可以用来表示Unicode。2)Unicode在很长一段时间内无法推广，直到互联网的出现。

## UTF-8

互联网的普及，强烈要求出现一种统一的编码方式。UTF-8就是在互联网上使用最广泛的一种Unicode的实现方式。其他实现方式还包括UTF-16（字符用两个字节或是四个字节表示）和UTF-32(字符用四个字节表示)，不过在互联网上基本不用。重复一遍，这里的关系是，UTF-8是Unicode的实现方式之一。

UTF-8最大的一个特点就是它使用一种变长的编码方式。它可以使用1-4个字节表示一个符号，根据不同的符号而变化字节长度。

UTF-8的编码规则很简单，只有两条：

- 对于单字节的符号，字节的第一位设置为0，后面7位为这个符号的Unicode码。因此对于英文字母，UTF-8编码和ASCII码是相同的。

- 对于n字节的符号（n>1），第一个字节的钱n位都设置为1，第n+1位设置为0，后面字节的前两位一律设置10.剩下的没有提及的二进制位，全部为这个符号的Unicode码。

下表总结了编码规则，字母x表示可用编码的位。

```
Unicode符号范围（十六进制） | UTF-8编码方式（二进制） 
0000 0000 - 0000 007F       | 0xxxxxxx
0000 0080 - 0000 07FF       | 110xxxxx 10xxxxxx
0000 0800 - 0000 FFFF       | 1110xxxx 10xxxxxx 10xxxxxx
0001 0000 - 0010 FFFF       | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx        
```

根据上表，解读UTF-8编码非常简单。如果一个字节的第一位是0，则这个字节单独就是一个字符；如果第一位是1，则连续有多少个1，就表示当前字符占用多少个字节。

下面，还是以汉字“严”为例，演示如何实现UTF-8编码。

“严”的Unicode是4E25（100111000100101），根据上表，可以发现4E25处在第三行的范围内，因此“严”的UTF-8编码需要三个字节，即格式是1110xxxx 10xxxxxx 10xxxxxx。然后，从严的最后一个二进制位开始，一次从后向前填入格式中的x，多出的补位为0.这样就得到了，严的UTF-8编码是11100100 101111000 10100101，转换成十六进制就是E4B8A5。

## Unicode与UTF-8之间的转换

通过上一节例子，可以看到“严”的Unicode码是4E25，UTF-8编码是E4B8A5，两者是不一样的。他们之间的转换可以通过程序实现。

## Little endian和Big endian

上节已经提到，UCS-2格式可以存储Unicode码（码点不超过0xFFFF）。以汉字严为例，Unicode码是4E25，需要用两个字节存储，一个字节是4E，另一个字节是25。存储的时候，4E在前，25在后，这就是Big endian方式；25在前，4E灾后，这就是Little endian方式。

这两个古怪的名字来自英国作家斯威夫特的《格列夫游记》。该书中，小人国里爆发了内战，战争起因是人们争论，吃鸡蛋的时候究竟从大头敲开还是从小头敲开。为了这件事情，前后爆发了六次战争，一个皇帝送了命，另一个皇帝丢了黄伟。

第一个字节在前，就是“大头方式”，第二个字节在前，就是“小头方式”。

很自然，就会出现一个问题，计算机怎么知道某个文件到底采用哪种编码？

Unicode规范定义，每个文件的最前面分别加入一个表示编码顺序的字符，这个字符名字叫做“零宽度非换行空格”，用FEFF表示。这正好是两个字节，而且FF比FE大1。如果一个文件的头两个字节是FEFF，就表示该文件采用大头方式；如果头两个字节是FFFE，就表示该文件采用小头方式。


## 参考
- [字符编码笔记：ASCII Unicode UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)
- [utf8编码原理详解](http://blog.csdn.net/baixiaoshi/article/details/40786503)