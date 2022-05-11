---
title: C++STL(一) string
categories: 学习
tags:
  - C++
  - STL
  - C++ STL系列
description: STL系列第一篇，详解string
top_img: >-
  https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/Games/Landscape/143004.jpg
abbrlink: 56391
date: 2022-04-11 22:00:00
---

## string容器基本概念

C++标准库在头文件<string>中定义了string类，以满足char*难以使用的问题。

string和char* 的对比：

* <strong style="color:#12aa9c;">char* 是一个指针，string是一个类</strong>

  string封装了char\*，管理这个字符串，是char\*型的容器。

* <strong style="color:#12aa9c;">string封装了很多成员方法</strong>

  find()查找，copy()拷贝，delete()删除，insert()插入

* <strong style="color:#12aa9c;">不用考虑内存的释放和越界</strong>

  string管理char\*所分配的内存，每一次string的复制，取值都是由string类维护，不用担心复制越界和取值越界。

## string容器常用方法

### 构造函数

``` C++
  //构造函数
    // string()
    string s0; // 空字符串构造

    // string(const char* s)
    string s1("this_is_a_string"); // 从c字符数组复制
    
    // string(size_t n, char c)
    string s2(10, 's'); // 重复填充n个c组成字符串
    
    // string(congst string& str)
    string s3(s2); // 从str复制
    
    // string(const char* s, size_t n)
    string s4("this_is_a_string", 5); //从c字符数组头复制n个字符 
    
    // string(const string& str, size_t pos, size_t len = npos)
    string s5(s1, 2, 12); // 从str的pos开始复制，长度为len，若len太长或留空则截止到str尾
    
    // string(InputIterator first, InputIterator last)
    string s6(s1.begin()+2, s1.end()-1); // 复制[first, last)范围内的字符序列，first和last都是迭代器

    cout<<"s0: "<<s0<<"\ns1: "<<s1<<"\ns2: "<<s2<<"\ns3: "<<s3<<"\ns4: "<<s4<<"\ns5: "<<s5<<"\ns6: "<<s6<<"\n";
    /* 输出结果
    *  s0:       
    *  s1: this_is_a_string
    *  s2: ssssssssss
    *  s3: ssssssssss
    *  s4: this_
    *  s5: is_is_a_stri
    *  s6: is_is_a_strin
    */
```

### 赋值

这里提到的赋值，指的是`operator=`和`assign`，他们在作用上大同小异，唯一区别点就在于前者更加直观简洁，后者功能性更强，如下例中的`assign(size_type n, char c)`。

```C++
  //赋值
    // operator= (const char* s)
    s0 = "this_is_a_string_too"; // 从c字符数组赋值

    // operator= (char c)
    s1 = 's'; // 从字符赋值

    // operator= (const string& str)
    s2 = s0 + s1; //从str赋值

    cout<<"s2: "<<s2<<endl;
    /* 输出结果
    *  s2: this_is_a_string_toos
    */

    s1.assign(10, 's');
    cout<<"s1: "<<s1<<endl;
    /* 输出结果
    *  s1: ssssssssss
    */
```

### 容量

这里主要是比较了几个修改`string`容量的函数的效果

为方便起见，`str.capacity()`称为容量；`str.size()`和`str.length()`称为长度；`str.max_size()`称为最大长度

* `shrink_to_fit()` 修改容量到长度
* `resize()`直接修改长度，不影响容量
* `clear()`清空长度，不影响容量
* `reverse()`表示预留容量，只能扩大无法缩小

```C++
//容量
  
  //shrink_to_fit
    cout<<"s0.size() = "<<s0.size()<<endl;
    cout<<"s0.length() = "<<s0.length()<<endl;
    cout<<"s0.max_size() = "<<s0.max_size()<<endl;
    cout<<"s0.capacity() = "<<s0.capacity()<<endl;

    s0.shrink_to_fit();

    cout<<"s0.size() = "<<s0.size()<<endl;
    cout<<"s0.length() = "<<s0.length()<<endl;
    cout<<"s0.max_size() = "<<s0.max_size()<<endl;
    cout<<"s0.capacity() = "<<s0.capacity()<<endl;
    // 输出结果
    // s0.size() = 20
    // s0.length() = 20
    // s0.max_size() = 9223372036854775807
    // s0.capacity() = 30
    // s0.size() = 20
    // s0.length() = 20
    // s0.max_size() = 9223372036854775807
    // s0.capacity() = 20    

  //resize
    cout<<s2<<endl;
    cout<<"s2.size() = "<<s2.size()<<endl;
    cout<<"s2.length() = "<<s2.length()<<endl;
    cout<<"s2.max_size() = "<<s2.max_size()<<endl;
    cout<<"s2.capacity() = "<<s2.capacity()<<endl;

    s2.resize(10);

    cout<<s2<<endl;
    cout<<"s2.size() = "<<s2.size()<<endl;
    cout<<"s2.length() = "<<s2.length()<<endl;
    cout<<"s2.max_size() = "<<s2.max_size()<<endl;
    cout<<"s2.capacity() = "<<s2.capacity()<<endl;
    // 输出结果
    // this_is_a_string_toos
    // s2.size() = 21
    // s2.length() = 21
    // s2.max_size() = 9223372036854775807
    // s2.capacity() = 40
    // this_is_a_
    // s2.size() = 10
    // s2.length() = 10
    // s2.max_size() = 9223372036854775807
    // s2.capacity() = 40

  //clear()
    cout<<s3<<" "<<int(s3.empty())<<endl;
    cout<<"s3.size() = "<<s3.size()<<endl;
    cout<<"s3.length() = "<<s3.length()<<endl;
    cout<<"s3.max_size() = "<<s3.max_size()<<endl;
    cout<<"s3.capacity() = "<<s3.capacity()<<endl;

    s3.clear();

    cout<<s3<<" "<<int(s3.empty())<<endl;
    cout<<"s3.size() = "<<s3.size()<<endl;
    cout<<"s3.length() = "<<s3.length()<<endl;
    cout<<"s3.max_size() = "<<s3.max_size()<<endl;
    cout<<"s3.capacity() = "<<s3.capacity()<<endl;
    // 输出结果
    // ssssssssss 0
    // s3.size() = 10
    // s3.length() = 10
    // s3.max_size() = 9223372036854775807
    // s3.capacity() = 15
    //  1
    // s3.size() = 0
    // s3.length() = 0
    // s3.max_size() = 9223372036854775807
    // s3.capacity() = 15

  //reserve
    string s7;
    s7.reserve(20);
    s7 = "1234567890";

    cout<<"s7.size() = "<<s7.size()<<endl;
    cout<<"s7.length() = "<<s7.length()<<endl;
    cout<<"s7.max_size() = "<<s7.max_size()<<endl;
    cout<<"s7.capacity() = "<<s7.capacity()<<endl;

    s7.reserve(30);
    cout<<"s7.size() = "<<s7.size()<<endl;
    cout<<"s7.length() = "<<s7.length()<<endl;
    cout<<"s7.max_size() = "<<s7.max_size()<<endl;
    cout<<"s7.capacity() = "<<s7.capacity()<<endl;
    // 输出结果
    // s7.size() = 10
    // s7.length() = 10
    // s7.max_size() = 9223372036854775807
    // s7.capacity() = 30
    // s7.size() = 10
    // s7.length() = 10
    // s7.max_size() = 9223372036854775807
    // s7.capacity() = 15
```

### 访问

用于访问`string`中的元素

```C++
  //访问
    string s8("abcdefghijklmn");
    cout<<s8[1]<<" "<<s8.at(2)<<" "<<s8.front()<<" "<<s8.back()<<endl;
    // 输出结果 b c a n
```

### 修改

这里涉及到的方法比较多，后续会精讲一部分。

```C++
  //修改
    cout<<(s8 += "op")<<endl;
    cout<<s8.append("st")<<endl;

    s8.push_back('u');
    cout<<s8<<endl;

    cout<<s8.insert(16, "qr")<<endl;
    cout<<s8.erase(s8.size()-1, 1)<<endl;
    cout<<s8.replace(s8.end()-1, s8.end(), "tuv")<<endl;

    s8.pop_back();
    cout<<s8<<endl;

    string s9 = "vwxyz";
    s8.swap(s9);
    cout<<s8<<" "<<s9<<endl;
    // 输出结果
    // abcdefghijklmnop
    // abcdefghijklmnopst
    // abcdefghijklmnopstu
    // abcdefghijklmnopqrstu
    // abcdefghijklmnopqrst
    // abcdefghijklmnopqrstuv
    // abcdefghijklmnopqrstu
    // vwxyz abcdefghijklmnopqrstu
```

### 其他操作

`data()`可以返回str的字符数组指针，`substr(size_type n, size_type len = npos)`返回从str截取的，起点为n，长度为len的子串。（关于len的默认值可参考s5的构造函数）

```C++
  //其他操作
    cout<<s9.data()+1<<endl;
    cout<<s9.substr(1, 3);
    // 输出结果
    // bcdefghijklmnopqrstu
    // bcd
```
