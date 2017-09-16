---
title: C++ 学习之旅
date: 2017-08-01 20:04:09
tags:
	c++
---

## 顺序容器
    
1. 支持随机访问容器：vector、deque、array、stringlist双向访问，forward_list单向顺序访，除了 array外 其他容器都支持增加和删除操作！


## 关联容器

1. 联容器的迭代器都是双向的
2. map、set等都可以用列表初始化

    ``` c++
    set<string> exclude = {"the","but","huzhigen","and","You"}
    map<string,string> authors = {
        {"123","456"},
        {"hzg","world"}
    }
    ```
    
3. pair的两个成员都是public的 ，可以使用p.first或者p.second来取得
4. pair对象进行比较相等的条件为first和second分别相等
5. set、mapde的成员key_type表示关键字的类型，set中value_type表示关键字类型，而map中表示的是pair<const kay_type,mapped_type>类型，mapped_type才是关键字关联的类型
6. set的迭代器只能访问set中的元素，而不能修改集合的元素值。
7. 使用一个不再容器中的关键字作为下标，会添加一个具有此关键字的元素到map中

## 动态内存
1. 对于内置类型来说：值初始化的内置类型对象有着良好定义的值，而默认初始化的对象值是未定义的。

    ``` c++
    int *p1 = new int;   // 默认初始化，指向的对象未定义
    int *p2 = new int();   //值初始化为0
    ```
    
    而对于其他定义类型则使用默认构造器初始化
    
    ``` c++
    string *p1 = new string;   // 默认初始化，指向的对象是空string
    string *p2 = new string();   //值初始化为空string
    ```
    
2. 由于编译器也好用初始化器的类型来推断要分配的类型，只有初始化括号仅有一个参数的时候才可以使用auto。

    ``` c++
    auto p1 = new auto(obj);   // p指向一个与obj类型相同的对象，而且该对象使用obj进行初始化
    auto p2 = new  auto{a,b,c};   //错误
    ```
    
3. 传递给delete的指针必须指向动态分配的内存，或者是一个空指针。释放一块并非new分配的内存，或者将相同的指针值释放多次，其行为是未定义的。new出来的对象一定是等到delete后才会释放内存，因此调用者必须记得显式释放内存，并且将指针置为空。不然会导致内存泄漏问题。

    ``` c++
    delete p;
    p = nullptr;
    ```
    
4. 使用内置指针访问智能指针所负责的对象是很危险的，因为无法知道对象何时会被销毁。永远不要用get初始化另一个智能指针或者为另一个智能指针赋值。
5. 智能指针应该注意的地方：
    - 不使用相同的内置指针初始化多个智能指针
    - 不delete  get（）返回的指针
    - 不使用get（）初始化或者reset另一个智能指针
    - 记住使用get（）返回的指针最后一个对应的智能指针销毁后，你的指针就变为无效
    - 如果智能指针管理的资源不是new 分配的内存，应该传递给他一个删除器（类似析构）