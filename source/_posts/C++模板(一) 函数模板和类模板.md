---
title: C++模板(一) 函数模板和类模板
tags:
  - C++
  - 模板
  - C++ 模板系列
categories: 学习
top_img: 'https://cdn.jsdelivr.net/gh/sirosaqqara/picHosting/img/2022/04/24/212210.png'
description: 以template为代表的泛型编程的介绍，C++模板系列第一篇
abbrlink: 27145
date: 2022-04-24 21:11:56
---

## 泛型编程
> 模板是泛型编程的一种重要思想，STL(Standard Template Library，标准模板库)是采用模板实现的一个实例

## 函数模板

### 函数模板的定义

#### 普通模板函数

如果我有一个交换int变量的函数，那么它可以有如下定义：

```c++
void myswap(int& a, int& b){
    int temp = a;
    a = b;
    b = temp;
}

int main(){
    int a = 10, b = 20;
    myswap(a, b);
    cout<<a<<" "<<b; // 20 10
}
```

如果我又有一个交换double变量的函数，那么它可以有如下定义：

```c++
void myswap(double& a, double& b){
    double temp = a;
    a = b;
    b = temp;
}

int main(){
    double a = 10.0, b = 20.0;
    myswap(a, b);
    cout<<a<<" "<<b; // 20.0 10.0
}
```

可以看到这两个函数除了变量的类型不同外，语句的格式和逻辑都是一样的。那么我们就可以通过函数模板，定义一个与变量类型无关的函数：

```c++
templete<class T>
//class 也可以换成 typename
void myswap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
}

int main(){
    char a = 'a', b = 'b';
    myswap(a, b);
    cout<<a<<" "<<b; // b a
}
```

#### 成员模板函数

成员函数也可定义为模板函数：

```c++
class MyClass{
    public:
    template<class T>
    void myParameter(T t){cout<<typeid(t).name()<<endl;}
};

int main(){
    MyClass me;
    me.myParameter(10); // i
}
```

这里需要注意一点：<span style="background:#FF9999;">使用函数模板的成员函数不能为虚函数</span>

这是由于编译器在解析一个类时就需要确定其vtable的大小，而模板函数需要根据实参才进行实例化，则如果需要确定vtable的大小就需要扫描整个代码确定实参类型，这显然是不能做到的。

### 函数模板的使用

#### 基础使用

在上面的定义中其实已经看到过一些使用的方法了。函数模板定义本身不参与编译，只有当使用函数模板并提供实参时才会进行根据提供的参数类型进行编译，这一过程被称为模板的实例化。

在使用时函数模板会根据用户提供的参数类型进行实例化，对函数模板的使用可以通过如下方式：

```c++
template <class T>
void myswap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
}

int main(){
    double da = 10.5, db = 20.5;
    char ca = 'a', cb = 'b';
    
    myswap(da, db); // 根据实参类型自动推导
    cout<<da<<" "<<db<<endl;

    myswap<char>(ca, cb); // 显式告诉模板类型
    cout<<ca<<" "<<cb<<endl;
}
```

函数模板必须确定出正确的类型才能使用，如下：

```c++
// 定义一个函数模板，但实际函数中并未用到模板
template <class T>
void myout(){
    cout<<"I'm myout()"<<endl;
}

int main(){
    // myout(); 报错，编译器没有推导出T的类型
    myout<class T>(); // 正确
    myout<int>(); // 正确
}
```

#### 模板函数和普通函数调用规则

如果模板函数和普通函数都有实现，则他们的调用规则如下：

```c++
template <class T>
void myswap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
    cout<<"from templete function"<<endl;
}

void myswap(int a, int b){
    int temp = a;
    a = b;
    b = temp;
    cout<<"from normal function"<<endl;
}

int main(){
    int a = 10, b = 20;
    int ia = 100, ib = 200;
    char ca = 'a', cb = 'b';
    
    // 如果参数类型能够匹配对应的普通函数，则调用普通函数
    myswap(a, b); // from normal function
    
    // 如果参数类型不能匹配普通函数，则调用模板函数
    myswap(ca, cb); // from templete function 
    
    // 可以通过空模板参数列表强制调用模板函数
    myswap<>(ia, ib); // from templete function
}
```

#### 模板函数重载

模板函数可以进行重载，具体如下：

```c++
template <class T>
void myswap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
}

template <class T>
void myswap(T& a, T& b, T& c){
    T temp = a;
    a = b;
    b = c;
    c = temp;
}

int main(){
    int a = 10, b = 20;
    int ia = 100, ib = 200, ic = 300;
    
    myswap(a, b); // 调用myswap(T& a, T& b);
    cout<<a<<" "<<b<<endl; // 20 10
    
    myswap(ia, ib, ic); // 调用myswap(T& a, T& b, T& c);
    cout<<ia<<" "<<ib<<" "<<ic<<endl; // 200 300 100
}
```

#### 模板函数特化

模板函数特化是说有时通用的函数模板不能解决个别类型的问题，我们必须对此进行定制，这就是函数模板的特化。

函数模板的特化必须把所有的模版参数全部指定，[类模板](#classTemplate)中也有相应用法。

需要注意的是特化函数和原函数返回值类型需相同，模板函数的返回值也是参数同样需要使得推导或指定的返回值类型相同，关于模板函数的返回值详见[下一小节](#returnValueOfTemplateFunction)

举一个简单的例子：

```c++
class Animal{
    public: 
    Animal(string animalClass, int animalAge){
        this->_class = animalClass;
        this->_age = animalAge;
    }

    string getClass(){return this->_class;}
    int getAge(){return this->_age;}

    void changeClass(string s){this->_class = s;}
    void changeAge(int i){this->_age = i;}

    private:
    string _class;
    int _age;
};

template <class T>
void myswap(T& a, T& b){
    T temp = a;
    a = b;
    b = temp;
}

// 特化函数模板，支持Animal类的交换
template<> void myswap(Animal& Aa, Animal& Ab){
    auto tempClass = Aa.getClass();
    Aa.changeClass(Ab.getClass());
    Ab.changeClass(tempClass);
    
    auto tempAge = Aa.getAge();
    Aa.changeAge(Ab.getAge());
    Ab.changeAge(tempAge);
}

int main(){
    int a = 10, b = 20;

    myswap<>(a, b);
    cout<<a<<" "<<b<<endl;;

    Animal tiger("tiger", 10);
    Animal cat("cat", 5);
    myswap(tiger, cat);
    cout<<tiger.getClass()<<" "<<tiger.getAge()<<endl;

}
```



<a id="returnValueOfTemplateFunction"></a>

#### 模板函数的返回值也是参数

当模板函数的返回值是另一个模板参数时，无法通过具体的实参类型来判定，此时有两种解决方案：

* 通过显式指定返回值类型，其他类型交由自动推导

```c++
template<class T1, class T2, class T3>
T1 mysum(T2 t2, T3 t3){
    return static_cast<T1>(t2 + t3);
}


int main(){
    auto t1 = mysum<long>(1L+20); // 显式指定T1类型，T2和T3由编译器推导
}
```

上例情况需要将返回值定为第一个模板参数类型，若其不为第一个则有以下情况：

```c++
template<class T1, class T2, class T3>
T3 mysum(T1 t1, T2 t2){
    return static_cast<T3>(t1 + t2);
}

int main(){
    // auto t3 = mysum<long>(1L + 20); 报错，无法指定T3的类型
    auto t3 = mysum<long, int, float>(1L + 20); // 正确，T3为long类型
    
    // 这说明显式指定需要按顺序从左到右依次指定
}
```

* 通过尾置返回类型从参数类型中获取

这个情况是当形参类型中有包含返回值类型时用尾置返回类型的方法去获得：

```c++
template <class T1>
auto myfindzero(T1 begin, T1 end) -> decltype(begin){
    decltype(begin) it;
    for(it = begin; it != end; it++){
        if(*it == 0) return it;
    }
    return it;
}

int main(){
    vector<int> vec = {1, 2, 3, 4, 0, 5};
    if(myfindzero(vec.begin(), vec.end()) != vec.end()){
        cout<<"find zero"<<endl;
    }else cout<<"non zero"<<endl;
}

// find zero
```

尾置返回类型是在`C++11`标准中新增的语法，可以用于任何函数定义中，旨在方便复杂函数的定义。尾置返回类型跟在形参列表后面并以一个`->`符号开头。为了表示函数真正的返回类型跟在形参列表之后，需要在本应该出现返回类型的地方放置一个`auto`关键字。

尾置返回类型通常使用decltype()方法配合auto追踪返回值的类型，这也是decltype的最大用途，其他decltype()的使用方法可见<span style="background:#FFDBBB;">melonstreet</span>的博客：[C++11特性：decltype关键字](https://www.cnblogs.com/QG-whz/p/4952980.html)

## 类模板

类模板和函数模板类似，类模板的作用是建立一个通用类，类中的成员 数据类型可以不具体制定，用一个虚拟的类型来代表。

> 类模板通常用来做容器或者动作的封装。

### 类模板的定义

#### 一般类模板定义

定义一个动物类模板，负责存储和显示动物的相关信息。

```c++
// 在类中用到了多少种不同类型的变量，就定义多少个模板
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;

    Animal(ageType age, speciesType species){
        this->A_age = age;
        this->A_species = species;
    }

    void selfIntroduction(){
        cout<<"I'm a "<<A_species<<" and I'm "<<A_age<<" years old."<<endl;
    }
};

int main(){
    //Animal huge(10, "tiger");
    Animal<int, string> huge(10, "tiger");
    huge.selfIntroduction();

    // I'm a tiger and I'm 10 years old.
}
```

与函数模板不同，类模板不能自动推断实例化。

所以只能显式指定类型参数使用`Animal <int, string> huge(10, "tiger")`，而不能让编译器自行推断`Animal huge(10, "tiger")`。

#### 类模板中的成员函数模板

把类模板和函数模板结合，定义一个含有成员函数的模板。

```c++
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;

    Animal(ageType age, speciesType species){
        this->A_age = age;
        this->A_species = species;
    }

    void selfIntroduction(){
        cout<<"I'm a "<<A_species<<" and I'm "<<A_age<<" years old."<<endl;
    }

    template<typename Years>
    void growingUp(Years years){
        this->A_age += years;
        cout<<"Wow, I'm "<<this->A_age<<" years old now."<<endl;
    }
};

int main(){
    //Animal huge(10, "tiger");
    Animal<int, string> huge(10, "tiger");
    huge.selfIntroduction();
    huge.growingUp(5); // 自动推断

    // I'm a tiger and I'm 10 years old.
    // Wow, I'm 15 years old now.
}
```

#### 类外定义

```c++
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;
    
    Animal(const ageType age, const speciesType species);

    void selfIntroduction(){
        cout<<"I'm a "<<A_species<<" and I'm "<<A_age<<" years old."<<endl;
    }

    template<typename Years>
    void growingUp(Years years);
};

//类外定义构造函数，需要加上模板声明
template <typename ageType, typename speciesType>
Animal<ageType, speciesType>::Animal(const ageType age, const speciesType species){
    this->A_age = age;
    this->A_species = species;
}

//类外定义成员函数，加上两层模板声明，且顺序不能调换
// template<typename Years> 报错
template <typename ageType, typename speciesType>
template<typename Years>
void Animal<ageType, speciesType>::growingUp(Years years){
    this->A_age += years;
    cout<<"Wow, I'm "<<this->A_age<<" years old now."<<endl;
}

int main(){
    //Animal huge(10, "tiger");
    Animal<int, string> huge(10, "tiger");
    huge.selfIntroduction();
    huge.growingUp(5);

    // I'm a tiger and I'm 10 years old.
    // Wow, I'm 15 years old now.
}
```

### 类模板的使用

#### 类模板成员函数实例化

在类模板实例化时，并不是其每个成员函数都实例化了，只有当使用该成员函数的时候才会进行实例化。示例如下：

```c++
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;
    Animal(const ageType age, const speciesType species){
        this->A_age = age;
        this->A_species = species; 
    }
    
    // 错误的成员函数
    void selfIntroduction(){
        cout<<A_age + A_species<<endl;
    }

    template<typename Years>
    void growingUp(Years years){
        this->A_age += years;
        cout<<"Wow, I'm "<<this->A_age<<" years old now."<<endl;
    }
};

int main(){

    Animal<int, string> huge(10, "tiger");
    huge.growingUp(5);
    // huge.selfIntroduction();
    
    // Wow, I'm 15 years old now.
}
```

`void selfIntroduction()`显然是错误的，但本程序正常编译运行，没有任何错误和警告。但当将`huge.selfIntroduction();`取消注释后，编译会立即报错。

<a id="classTemplate"></a>

#### 类模板的特化和偏特化

特化看上去和重载类似，通过特化类模板来为特定的类型指定行为，类模板可以有多个特化或偏特化，实例化时会优先选择最适合的特化或偏特化进行实例化。具体如下：

```c++
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;
    Animal(const ageType& age, const speciesType& species){
        this->A_age = age;
        this->A_species = species; 
    }

    void info(){
        cout<<"I'm a base class template."<<endl;
    }
};

template<> // 特化，模板参数完全特化
class Animal<int, string>{
    public:
    int A_age;
    string A_species;
    Animal(const int& age, const string& species){
        this->A_age = age;
        this->A_species = species; 
    }

    void info(){
        cout<<"I'm a specialize class template."<<endl;
    }
};

template<typename ageType> // 偏特化，模板参数不完全特化(或类型受限)
class Animal<ageType, string>{
    public:
    ageType A_age;
    string A_species;
    Animal(const ageType& age, const string& species){
        this->A_age = age;
        this->A_species = species; 
    }

    void info(){
        cout<<"I'm a partialSpecialization class template."<<endl;
    }
};

int main(){

    Animal<int, string> huge(10, "tiger");
    huge.info(); // I'm a specialize class template.

    Animal<double, string> shamate(10.2, "martian");
    shamate.info(); // I'm a partialSpecialization class template.

    Animal<double, char> kenan(7.5, '1');
    kenan.info(); // I'm a base class template.
}
```

#### 类模板成员和成员函数的特化

> 未特化的成员函数需要在类中以inline方式实现，成员函数的特化需要在类外实现

```c++
template <typename ageType, typename speciesType>
class Animal{
    public:
    ageType A_age;
    speciesType A_species;
    Animal(const ageType& age, const speciesType& species){
        this->A_age = age;
        this->A_species = species; 
    }

    void info(){
        cout<<"I'm a base memberFunctionTemplate."<<endl;
    }
    
};

template<>
void Animal<double,string>::info(){
    cout<<"I'm a specialize memberFunctionTemplate."<<endl;
}

int main(){

    Animal<int, string> huge(10, "tiger");
    huge.info(); // I'm a base memberFunctionTemplate.

    Animal<double, string> shamate(10.2, "martian");
    shamate.info(); // I'm a specialize memberFunctionTemplate.
}
```
