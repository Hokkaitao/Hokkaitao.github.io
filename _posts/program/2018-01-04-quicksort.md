---
layout: post
published: true
title: "快速排序"
description: pragma
---
## 简介

快速排序之所以比较快，是因为相比冒泡排序，每次交换是跳跃式的。每次排序的时候设置一个基准点，将小于等于基准点的数全部放到基准点的左边，将大于等于基准点的数全部放到基准点的右边。这样的每次交换的时候就不会像冒泡排序一样每次只能在相邻的数之间进行交换，交换的距离就大的多了。因此总的比较和交换次数就少了，速度当然就快了。当然，在最坏的情况下，仍可能是相邻的两个数进行交换。因此快速排序的最差时间和冒泡排序是一样的O(N^2)，它的平均时间复杂度是O(NlogN)。其实快速排序是基于一种叫做“二分”的思想。

## 代码

```
#include <stdio.h>

int a[] = {1, 10, 2, 9, 3, 8, 7, 4, 6, 5};

void printfArray(int *a, int len) {
    int loop = 0;
    for(loop=0; loop<len; loop++) {
        printf("%d ", a[loop]);
    }
    printf("\n");
}

void quicksort(int left, int right) {
    if(left >= right) {
        return ;
    }
    int temp = a[left];
    int i = left;
    int j = right;
    while(i!=j) {
        while(a[j]>=temp && i<j) {
            j--;
        }
        while(a[i]<=temp &&i<j) {
            i++;
        }
        if(i<j) {
            int t = a[i];
            a[i] = a[j];
            a[j] = t;
        }
    }
    a[left] = a[i];
    a[i] = temp;

    quicksort(left, i-1);
    quicksort(i+1, right);
}
int main() {
    printfArray(a, 10);
    quicksort(0, 9);
    printfArray(a, 10);
    return 0;
}
```

## 涨姿势环节

快排是由C.A.R.Hoare（东尼霍尔，Charles Antony Richard Hoare）在1960年提出，之后又有许多人做了进一步的优化。如果对快排感兴趣，可以查看东尼霍尔1962年在Computer Journal发表的论文"Quicksort"以及《算法导论》的第七章。快速排序算法仅仅是东尼霍尔在计算机领域才能的第一次显露，后来他收到了老板的赏识和重用，公司希望他为新机器设计一个新的高级语言。你要知道当时还没有PASCAL或是C语言这些高级的东西。后来东尼霍尔参加了由Edsger Wybe Dijkstra（1972年图灵奖得主）举办的“ALGOL 60”培训班，他觉得自己与其没有把握去设计一个新语言，还不如对现有ALGOL 60进行改进，使之能在公司的新机器上使用。于是他便设计了“ALGOL 60”的一个子集版本。这个版本在执行效率和可靠性上都在当时“ALGOL 60”的各种版本中首屈一指，因此东尼霍尔受到了国际学术界的重视。后来他在“ALGOL X”的设计中还发明了大家熟知的“case”语句，后来也被各种高级语言广泛采用，比如PASCAL/C/JAVA语言等等。当然，东尼霍尔在计算机领域还有很多贡献，其在1980年获得了图灵奖。

## 参考
- [坐在马桶上看算法：快速排序](http://developer.51cto.com/art/201403/430986.htm)
