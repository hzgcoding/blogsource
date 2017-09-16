---
title: C++类型转换
date: 2017-08-03 10:47:24
tags:
	c++
---


## 类型转换

- **一个类型转换函数必须是类的成员函数，他不能声明返回类型，形参列表也必须为空，类类型转换函数通常应该是const**

```c++
#include <iostream>
using namespace std;
class SmallInt
{
public:
	SmallInt(int i = 0) :val(i) 
	{
		if (i < 0 || i>255) {
			throw std::out_of_range("Bad SmallInt value");
		}
	}
	operator int() const { return val; }
private:
	std::size_t val;
};
int main(int argv, char * argc[]) {
	SmallInt si;
	si = 4;  //首先将4隐式转换成SmallInt对象，然后调用SmallInt ：：operator=
	si + 3;  //首先将si隐式转换成int，然后执行整数加法
	return 0;
}
```

- 如果在类型转换函数前用explicit关键字来修饰，那么类型转换变为显式转换。

```c++
si+3; //这句错误
static_cast<int>(si)+3; //正确：显式的请求类型转换
```

- 如果表达式被用作条件，则编译器会见显式的类型转换自动应用于他，即显式转换见被隐式转换的执行：
    1. if、while及do语句部分
    2. for语句头的条件表达式
    3. 逻辑非运算符（！）、逻辑或运算符（||）、逻辑与运算符（&&）的运算发对象
    4. 条件运算符（？：）的条件表达式

- 不要令两个类执行相同的类型转换：如果A类有一个接受B类对象的构造函数，则不要在B中再定义转换目标是A类的类型转换运算符

- 如果对同一类既提供了转换目标是算数类型的类型转换，也提供了重载的运算符，则将会遇到重载运算符与内置运算符的二义性问题
- 在定义类型转换函数时，切记注意调用转换时不要存在**二义性**