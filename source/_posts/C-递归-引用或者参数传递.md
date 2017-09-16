---
title: C++ 递归 引用或者参数传递
date: 2017-08-10 18:29:40
tags:
	c++
---

> 递归很容易，且用且小心！

当递归参数传递为引用或者指针时：

```c++
struct A 
{
    int value;
};
void func(A &a){
    //todo something
    // change a.value
    func(A &a);
}

//开始调用
A a;
func(a);

```

> 可以发现value还是默认初始化的值为0,并没有在递归中change而change，这是因为当出栈的时候局部变量已经销毁，对a的修改并没返回。
不管使用值传递还是引用传递（或者指针）效果都是一样的。

那怎么样解决这样的问题呢？

-  第一种办法就是使用返回值的方式记录出栈前的数据,最后使用返回值就行了

```c++
A func(A &a){
    //todo something
    // change a.value
    return func(A &a);
}

```

- 第二种办法就是使用数组将出栈前数据保存起来，这样就不会被销毁掉

```c++
vector<A> & vec_as
void func(A &a,vector<A> & vec_as){
    //todo something
    // change a.value and vec_as.add a
    func(A &a);
}
// 最后直接取数组vec_as中的数据就行了
```


> 递归很容易，且用且小心！
