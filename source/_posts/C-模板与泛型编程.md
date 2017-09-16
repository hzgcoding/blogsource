---
title: C++ 模板与泛型编程
date: 2017-08-05 17:30:49
tags:
	c++
---
 
## 模板定义
 
- 函数模板
 ```
 template <typename T>  //这里class与typename关键字作用一样，都可以用
int compare(const T& v1, const T& v2) 
{
	if (v1 < v2) 
	{ 
		return -1; 
	}
	if (v2 < v1)
	{
		return 1;
	}
	return 0;
}
 ```
 
 - 一个类模板的每个实例都形成一个独立的类。类型Base<string>与Base<int>等都没关联，包括成员函数和字段等，也不会对其他任何Base类型的成员有特殊访问权限。
 - 默认情况下，对于一个实例化的类模板，其成员只有在使用时才被实例化。
 - 在类模板内简化模板类名的使用，下面两种写法等价：(在类模板的作用域内，可以直接使用模板名而不必指定模板实参)
 ```
 template <class T>class Base{
 public:
    /*****构造函数******/
    /*******第一种写法***********/
    Base& operator++();
    Base& operator--();
    /*******第二种写法***********/
    Base<T>& operator++();
    Base<T>& operator--();
    
 };
 ```
 
 - 如果 一个类模板包含一个非模板友元，则友元被授权可以访问所有模板实例。如果友元自身是模板，类可以授权给所有友元模板实例，也可以只授权给特定实例。
 - 通用和特定模板的友好关系：
 ![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/9CCDA37E1870448CA985A66A8DF6A4F2/4065)

 - 可以使得模板自己的类型参数成为友元,因此对于某个类型名Foo，Foo将成为Base<Foo>的友元：
 ```
 template <class Type> class Base{
   friend Type;    
 };
 ```
 
 - 模板参数与作用域：
```
typedef double A;
template <typename A,typename B> void f(A a,B b){
    A tmp = a; //tmp的类型为模板参数A的类型，而非double
    double B; //错误： 重声明模板参数B
}
```
- 参数名字不能重用，在模板参数列表中只能出现一次；模板声明必须包含模板参数

## 模板实参推断

### 类型转换与模板类型参数
- 编译器通常不是对实参进行类型转换，而是生成一个新的实例，除了一下情况：
    1. const转换:可以将一个非const对象的引用或者指针传递给一个const的引用或者指针形参；
    2. 数组或者函数指针转换：如果函数形参不是引用类型，则可以对数组或者函数类型的实参应用正常的指针转换，一个数组实参可以转换为一个指向其首元素的指针。类似的函数实参可以转换为一个该函数类型的指针。
    3. 其他的类型转换比如算术转换、基类与派生类转换以及其他用户自定义的转换都不能应用于函数模板。将实参传递给模板类型的函数形参时，能够自动应用的类型转换只有const转换及数组或者函数到指针的转换。

- 如果函数参数类型不是模板参数，则对实参进行正常的类型转换

## 函数模板显式实参

- 指定显式模板实参
```
template <typename T1,typename T2,typename T3>
T1 compare(T2,T3);
```

在调用时必须显式指定T1的类型
```
compare<long long>(int ,long);
```

显式模板实参按照从左到右的顺序与对应的模板参数匹配：
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/8E305BB7C4D54BF6B472B374A90924B4/4152)


## 可变参数模板

- 参数包：模板参数包表示讴歌或者多个模板参数；函数参数包表示0个或者多个函数参数
```
 // Args 是一个模板参数包；rest是一个函数参数包
 // Args 表示0个或或者多个模板类型参数
 // rest 表示0个或者多个函数参数
 template <typename T,typename... Args>
 void foo(const T &t ,const Args & ... rest);
```

使用方式：
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/EBEFAD990E9E41339FB1C5B459F8AF6D/4174)

- sizeof... 运算符获取参数包的包含的参数数量
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/06459E5366EE457DBF2C6E29EC704802/4181)

## 可变参数函数模板
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/48033A2D37C049E29657B31FDBD7AE38/4186)

![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/6857EBF5297A40B9A1EB3CD46088C92B/4188)


## 特例化成员模板
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/7028FF936E88491CAFCBFBF239AAA7CF/4196)
