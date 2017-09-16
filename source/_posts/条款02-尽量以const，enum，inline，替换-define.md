---
title: '条款02  尽量以const，enum，inline，替换#define'
date: 2017-08-26 15:30:27
tags:
    c++
---


## 预处理器与编译器
`#define`属于预处理，即在所有编译前将符号替换掉
`const`,`enums`,`inline` 等属于编译期处理的

## 常量定义
```c++
#define ASPECT_RATIO 1.63     //1
const double AspectRatio = 1.63   //2
```

1. 第一种写法导致在程序调试或者运行后看不到`ASPECT_RATIO` 的存在，因为在预处理器就已经替换所有，在编译器中记号表（`symbol table`）中找不到这个符号，这样就对程序调试造成很大困难，因为你并不知道1.63从哪里而来，表示什么意思；
2. 第二种是比较推荐的，除了上述，这种可能比`#define`更小代码量，使用`#define`可能会导致目标码中出现多份1.63，而const只有一份存在

## 常量替换

- **定义常量指针**

    务必使用两个`const`保证不会被改变，保持常量的性质
    `const char* const authorName = "huzhigen"`,并且`string`一般都比`char*`要更加适合`const std:string authorName("huzhigen")`
    
- **类专属常量**
    
    class专属常量，既然是常量就不会被改变，那么更好的做法是加上`static`修饰,所有类对象共享一个常量

    ```c++
    class GamePlayer
    {
        private:
            static const int NumTurns =5; //1常量声明
            int scores[NumTurns];  //2使用常量 
    }
    ```
    
    顺带一提，`#define`就没法定义class专属常量了，因为没法控制scope作用域，一旦宏被定义，他就在其后的编译过程中有效，除非使用`#undef`，并且也不能提供任何封装性，即没有`private #define`这样的语法。
    
    另外对于语句1，有些编译器是不支持（或者错误的不允许）常量声明时赋值的，即`in class 初值设定`,那么需要在类实现文件中定义此常量`const int GamePlayer::NumTurns = 5;`
    
    但是这时，语句2就会报错，数组声明时必须知道其大小（必须在编译器知道数组的长度），那么这时候怎么办呢？`enum hack` 无疑可以很好解决这样的问题。

## the enum hack

改用enum的写法后：
```c++
class GamePlayer
{
    private:
        enum { NumTurns = 5 };  //enum hack 
        int scores[NumTurns];  //2使用常量 
}
```
`enum hack`与`#define`是比较像的，都不能获取其地址，而`const`就可以.

### 使用`enum hack`的好处：

- `enum hack`的行为更像`#define`而不是`const`，如果你不希望别人得到你的常量成员的指针或引用，你可以用`enum hack`替代之。（为什么不直接用#define呢？首先，因为#define是字符串替换，所以不利于程序调试。其次，#define的可视范围难以控制，比如你怎么让#define定义的常量只在一个类内可见呢？除非你用丑陋的#undef。
- 使用enum hack不会导致 “不必要的内存分配”:创建一个pointer或者reference指向该常量后，不优秀的编译器可能会分配少许内存给指针等，当我们使用enum后，就可以加以约束，不允许有指针或者引用指向他们，**Enums和 #defines 一样绝不会导致非必要的内存分配**
- enum hack是模板元编程的一项基本技术，大量的代码在使用它。当你看到它时，你要认识它。

## 使用inline template function替换#define function

`#define`定义的函数，虽然在省略了`function call`的开销，但是他不仅难看，而且很容出错，难以控制.

例如：

```c++
#define GetMax(a , b)  f((a) > (b) ? (a) : (b))
```

写这种宏不仅痛苦，也不好看，每个参数也必须加括号，否者必然会遇到麻烦

即使如此，也会出现一些意想不到的错误.

```c++
int a =5 ,b =0;
GetMax(++a,b);    // a被累加依次
GetMax(++a,b+10); //a会被累加两次
```

a 的递增次数取决于 “它被拿来和谁比较”

我们可以用inline template function更好实现这样的功能

```c++
template<typename T>
inline void GetMax(const T& a, const T& b)
{
    f(a>b?a:b);
}
```
可以看出这里不需要为每个参数加括号，也不需要担心参数执行非预期行为，而且作用域也是可控的，只要愿意可以将此function写成private inline函数，这是宏没法做到的

## 请记住

- 对于单纯常量，最好以`const`对象或者`enum`替换`#define`
- 对于形似函数的宏（`macros`），最好改用`inline`函数替换`#define`