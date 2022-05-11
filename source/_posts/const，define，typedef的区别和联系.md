---
title: const，define，typedef的区别和联系
tags:
  - C++
  - 内存
categories: 学习
top_img: 'https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/04/19/110843.png'
abbrlink: 30793
date: 2022-04-19 11:01:10
---

首先，这三个不是放在一起来对比的，而是分为两个部分：`define`与`const`的区别与联系和`define`和`typedef`的区别与联系。因为这两个要讨论的内容就不是同一个方向的。

## `define`和`const`的区别和联系

`define`和`const`都可以来定义常量，但是这两种方式定义的常量是有区别的。

* define是通过宏定义的方式来定义常量的，对其定义的常量不做类型安全检查，只是单纯的展开，在预编译阶段进行。其定义的常量在内存中可能会有多份拷贝，占用内存空间大。
* const定义的常量是有数据类型的，该替换在编译阶段进行，编译时会对const定义的常量进行类型检查。其定义的常量存放在静态存储区，只有一份拷贝。

![内存分区](https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/04/19/101221.png)

## `define`和`typedef`的区别和联系

* <span style="background:#FF9999;">用法不同</span>，`define`用来定义常量；`typedef`用来定义数据类型的别名
* <span style="background:#FF9999;">执行时间不同</span>，`define`是宏定义，在预编译阶段执行，不做类型检查；`typedef`在编译阶段执行，需要进行类型检查。
* <span style="background:#FF9999;">作用域不同</span>，`define`在预编译阶段就已经执行，故只要在`define`后声明的都会进行替换，没有作用域之说；`typedef`有自己的作用域，取决于定义时的位置。
* <span style="background:#FF9999;">含义不同</span>，`define`不是语句，而`typedef`定义是一条语句。

除此之外，`typedef`还有其自己的多种使用方式，我认为seventhsaint的[C++ typedef用法详解](https://www.cnblogs.com/seventhsaint/archive/2012/11/18/2805660.html)讲解的十分清楚，可参考学习。

当然这里会提到两个经常用来考的当`define`和`typedef`用来定义指针时的问题。

------

<span style="background:#FF9999;">情景1</span>

```C++
#define INTPTR1 int*

int main(){

    typedef int* INTPTR2;

    int a = 1, b = 2;

    INTPTR1 p1, p2; //实际为int* p1, p2;
    INTPTR2 p3, p4; //实际为int* p1, int* p2;

    cout<<typeid(p1).name()<<" "<<typeid(p2).name()<<endl;
    cout<<typeid(p3).name()<<" "<<typeid(p4).name()<<endl;
    cout<<typeid(a).name()<<" "<<typeid(b).name()<<endl;

    // 输出结果
    // pi i
    // pi pi
    // i i
}
```

以上情景可以看出，`define`在做替换时只是单纯的字符串替换，则实际上定义p1为int\*类型，p2为int类型。而`typedef`定义的是类型的别名，故实际上是定义了两个int\*类型的变量。

------

<span style="background:#FF9999;">情景2</span>

```C++
#define INTPTR1 int*

int main(){

    typedef int* INTPTR2;

    int a = 1, b = 2;

    const INTPTR1 p1 = &a;
    const INTPTR2 p2 = &b;

    p1 = &b;
    //*p1 += 1; 编译错误，p1是常量指针，无法通过p1更改指向的值(卡住p1写的功能)

    *p2 += 1;
    //p2 = &a; 编译错误，p2是指针常量，无法更改p2指向的位置(卡住p2读的功能)

    cout<<*p1<<" "<<*p2;

    // 输出结果
    // 3 3
}
```

以上情景在定义p1，p2时加入了const修饰，必须初始化。同样的，`define`只是单纯的字符串替换，`const INTPTR1 p1`实际上就是`const int* p1`，p1是一个常量指针；而`typedef`是类型的别名，`const INTPTR2 p2`实际上是`int* const p2`，p2是一个指针常量。
