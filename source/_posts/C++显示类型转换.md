---
title: C++显示类型转换
tags:
  - C++
  - 面试
categories: 学习
description: C++四种显示类型转换介绍
top_img: 'https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/04/14/170605.jpg'
abbrlink: 42322
date: 2022-04-14 16:54:06
---

## `static_cast`

`static_cast`支持窄化转换（信息丢失），`void*`强制转换，隐式类型转换和类层次转换（不安全）

### 窄化转换

窄化转换是说将占用字节较多的类型转化为占用字节较少的类型。如：

```c++
double d1 = 2.0;
int i1;

i1 = static_cast<int>(d1);
cout<<i1<<endl;
//输出：2
```

### void* 前置转换

```c++
int i1 = 2;
void* vv1 = &i1;
//double* dd1 = vv1; 报错，C++不允许void*转换为其他类型指针
int* ii1 = static_cast<int*>(vv1);
cout<<*ii1<<endl;
//输出：2
```

### 隐式转换

```c++
int i1 = 2;
double d2, d3;
d2 = i1;
d3 = static_cast<double>(i1);
cout<<d2<<" "<<d3<<endl;
//输出：2 2
```

### 类层次转换

类层次转换在dynamic_cast的范畴内，这里放到下面dynamic_cast中对比

## `dynamic_cast`

`dynamic_cast`主要是一种类型安全的下行类型转换操作，它的`type_id`必须是类的指针、引用或者是`void*`，它与`static_cast`在类层次的转换有所区别。

### 类型转换与`RTTI`

顾名思义的话，`static_cast`<span style="background:#FF9999;">是静态的类型转换</span>操作，而`dynamic_cast`就是<span style="background:#FF9999;">动态的类型转换</span>，即根据实际对象动态地转换，这里所说的动态主要体现的是<span style="background:#FF9999;">所传入的对象类型必须含有虚函数</span>——因为它是靠存储在vtable上的类型信息来判断实际的类型，而`dynamic_cast`这种动态识别对象信息的能力又称`RTTI（run-time type information)`

`RTTI`机制的出现与C++本身是静态类型语言有关，即C++的数据类型是在编译时就确定的，不能在运行时更改，然而由于面向对象的多态性要求，C++中指针或引用本身的类型可能与它实际代表（指向或引用）的不同，有时便需要将一个多态指针转换为其实际指向对象的类型（为了访问特有的属性或方法），这时就需要知道运行时的类型信息，就产生了运行时类型识别的需求，<span style="background:#FF9999;">C++中获得运行时类型信息就是通过RTTI机制来完成的</span>。

### 上下行转换

类层次转换分为上行转换和下行转换，<span style="background:#FF9999;">上行转换总是安全的，一个父类（基类）指针或引用总是可以指向一个子类的对象的</span>，但下行转换就不一样了。子类（派生类）指针指向父类对象是不安全的，因为父类对象没有办法访问到子类的特有成员函数。这也就是体现出`stactic_cast`和`dynamic_cast`的不同之处，举一个简单的例子：

```c++
//假设我们有一个动物类，动物类有一个狗狗子类。
//动物类有一个属性type，默认为“动物”。
//狗狗类有一个自己的属性dog_type，表示狗狗的具体种类。
class Animals{
public:
    string type;
    Animals(){
        type = "动物";
    };
    virtual void Show(){}
};

class Dog:public Animals{
public:
    string dog_type;
    Dog(string dtype){
        dog_type = dtype;
    };
    void Show(){}
};

//现在我们需要一个方法，对传入的动物类对象进行检查，
//如果是狗狗我们需要输出具体的dog_type，
//如果不是狗狗我们只需要输出动物的type。
//在这个方法中分别使用dynamic_cast和static_cast进行类型转换后处理
void me(Animals* animal){

    cout<<"使用dynamic转换"<<endl;
    Dog* d_dynamic = dynamic_cast<Dog*>(animal);
    if(d_dynamic){
        cout<<d_dynamic->dog_type<<endl;
    }else {
        cout<<animal->type<<endl;
    }

    cout<<"使用static转换"<<endl;
    Dog* d_static = static_cast<Dog*>(animal);
    if(d_static){
        cout<<d_static->dog_type<<endl;
    }else {
        cout<<animal->type<<endl;
    }
}

int main(){
    //我们用两个动物类指针分别指向一个狗狗类对象和一个动物类对象    
    Animals* dog0 = new Dog("牧羊犬");
    Animals* notdog0 = new Animals();

    //对两个指针应用上文的方法。
    cout<<"第一种情况"<<endl;
    me(dog0);

    cout<<"\n第二种情况"<<endl;
    me(notdog0);
}

//运行结果为：
//第一种情况
//使用dynamic转换
//牧羊犬
//使用static转换
//牧羊犬
//
//第二种情况
//使用dynamic转换
//动物
//使用static转换
//
//[Done] exited with code=3221225477 in 1.09 seconds
```

可以看到，当父类指针指向的确实是一个子类对象时，`static_cast`和`dynamic_cast`都能正常转换，而<span style="background:#FF9999;">当父类指针指向的仍是一个父类对象时，`dynamic_cast`不能强制转换而是返回空指针</span>；`static_cast`则是强制转换，由于此时子类指针指向了父类对象，访问子类的特有属性时就会出现问题而且编译器不会报告语法错误。

### 总结

* `static_cast`是静态的类型转换，`dynamic_cast`是动态的类型转换
* 上行转换（子类转换为父类）是安全的，下行转换（父类转子类）是不安全的
* C++中动态类型转换是通过`RTTI`机制来实现的
* `RTTI`机制是为了解决面向对象中多态指针在运行时指向的实际对象类型
* `dynamic_cast`传入的对象类型必须含有虚函数，因为实际对象类型是通过虚函数表得到的
* 当使用`dynamic_cast`进行下行转换时，会返回空指针

## `const_cast`

`const_cast`是用来进行`const`相关的转换，可以把一个`const`对象转换为一个`非const`对象，同理，反其道而行也是可以的；另外 还可以用来进行`volatile`的转换。

> `volatile`是一个特征修饰符(type specifier)。`volatile`的作用是作为指令关键字，确保本条指令不会因编译器的优化而省略，且要求每次直接读值。`volatile`的变量是说这变量可能会被意想不到地改变，这样，编译器就不会去假设这个变量的值了。

一般来说，`非const`对象转换为`const`对象是没问题的，所以其实没必要使用`const_cast`。主要是`const`对象转`非const`对象，这样就可以修改一个`const`对象的值了。

另外，`type_id`必须是指针或引用。

```c++
const int a = 5;
//int* b = &a; 报错，不允许指针变量指向常量。
int* c = const_cast<int*>(&a);
*c = 2;

cout<<*c<<" "<<a<<endl;
cout<<c<<" "<<&a<<endl;

//输出：
//2 5
//0x61fe14 0x61fe14
```

奇迹发生了但又没完全发生

可以看到`c`确实和`a`指向的同一块地址，但是他们的值却不同。根据反汇编的结果，好像是编译器直接提前将`a`的所有引用全部替换为了常量`5`，因为既然都指明了`a`是`const`的`5`，谁会想到你后面会尝试修改它呢。

## `reinterpret_cast`

`reinterpret_cast`运算符是用来处理无关类型之间的转换；它会产生一个新的值，这个值会有与原始参数(`expressoin`)有完全相同的比特位。

reinterpret_cast的功能十分强大，可以互相转换任意类型的指针，也可将任意类型的指针转换为足够大的整型值。但随意的转换可能会造成程序的破坏。

```c++
//这里随便定义了一个参数和返回值都是int的函数
int test(int x){
    return x;
}

int main(){
    
    //创建了一个函数指针fp指向test
    int (*fp)(int) = test;
    
    //强制将函数指针转换为double型指针，并且输出改地址的值
    double* val = reinterpret_cast<double*>(fp);
    cout<<*val;

    return 0;
}
//输出：5.65271e-134
```

以上操作毫无意义，且代码也无法让人理解，但它并没有任何报错和警告。
