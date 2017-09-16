---
title: C++ OOP
date: 2017-08-03 21:00:25
tags:
	c++
---

## 基类派生类定义规则

- 基类通常都应该定义一个虚析构函数，即使该函数不执行任何实际操作也是如此。
- 关键字virtual只能出现在类内部的声明语句之前而不能用于类外部的函数定义。
- 如果基类函数时virtual，那么该函数在派生类中隐式也是虚函数。
- 首先初始化基类的部分，然后按照声明的顺序依次初始化派生类的成员。并且通过调用基类的构造函数来初始化那些从基类中继承而来的成员。
- 静态成员会被继承，但是对于静态成员来说都只存在唯一的实例。

    ```
    #include <iostream>
    using namespace std;
    class Base {
    public:
    	static void statment();
    
    };
    class Derived :public Base {
    	void fun(const Derived&);
    
    };
    void Base::statment() {
    	cout << "this is static function of Base" << endl;
    }
    void Derived::fun(const Derived& derived) {
    	Base::statment();
    	Derived::statment();
    	derived.statment();
    	statment();
    }
    ```

- 防止类继承的方法有：在类名后加final关键字修饰

    ```
    class Derived final:public Base {
	    void fun(const Derived&);

    };
    ```

- 派生类向基类的自动类型转换只是针对指针和引用类型有效，而对于派生类类型和基类类型之间不存在这样的转换（基类定义了拷贝控制成员函数除外）

## 虚函数
- 动态绑定只有当我们通过指针或者引用调用虚函数时才会发生，当通过一个普通类型得我表达式调用虚函数时，在编译时就会将调用的版本确定下来。
- 基类中虚函数在派生类中隐含地也是一个虚函数，当派生类覆盖了某个虚函数后，该函数在基类中的形参必须与派生类中的形参严格匹配。
- 如果某次函数调用使用了默认实参，那么该实参由本次的静态类型决定：通过指针或者引用调用virtual函数，Base定义了默认实参，Derived继承了这个虚函数，调用时实际运行的是Derived的版本，但是传入函数的默认实际参数还是Base函数的默认实参，因此最好保证基类和派生类的默认实参一致。
- 派生类继承而来的函数调用基类virtual函数的方式：

```
base->Base::func(args);
```

## 抽象基类
- 不能直接实例化（创建）抽查基类的对象

## 访问控制
- **派生类的成员和友元只能访问派生类对象的基类部分受保护成员，对于普通的基类对象中的成员不具有特殊的访问权限。**

```
class Base{
protected:
	int pro_mem;
};
class Sneaky :public Base {
	friend void clobber(Sneaky&); //可以访问Sneaky::pro_mem
	friend void clobber(Base&);  //不能访问base::pro_mem
	int j;
public:
	void visit() {
		this->pro_mem = 0; //正确  
	}
};

void clobber(Sneaky& s) {
	s.j = s.pro_mem = 0; //正确
}
void clobber(Base& b) {
	b.pro_mem = 0;  //错误
}
void visit2() {
	Sneaky s;
	s.pro_mem = 0;  //错误： 对象不能访问基类保护成员
}
```
- 默认情况下，使用class关键字定义的派生类是私有继承的；而使用struct关键字定义的派生类是公有继承的
- 基类析构函数必须是虚函数，这样才会动态绑定，才会在delete 基类指针或者引用的时候能够调用正确的析构函数，否者delete一个指向派生类对象的基类指针将产生未定义的行为。
- 如果构造函数或者析构函数调用了某个虚函数，则我们应该执行与构造函数或者析构函数所属类型相对应的虚函数版本。
- 当派生类对象呗赋值给基类对象时，其中派生类部分将被切掉。
- 一般在容器中是存放基类的指针或者引用（智能指针），不应该是类类型：
![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/6B3C93642A6D46FDA954FA196F01557C/4007)


