---
title: C++ 高级特性
date: 2017-08-11 14:28:05
tags:
	c++
---

## 元组tuple

包含标准库
```c++
#include <tuple>
```

tuple(元组)在定义的时候需要制定每个字段的类型，字段个数定义过后不能再次改变，和pair类似，算是其加强版。两个tuple就可以进行< 和 == 运算，但是前提是字段个数相同，而且字段类型也需要一致。

```c++
#include <iostream>
#include <tuple>

using namespace std;

int main(int argc, char * args[])
{
	tuple<int, string> tup1 = make_tuple(1, "huzhgien");
	typedef decltype(tup1) my_tuple;
	
	//tuple<int, string> tup1(1, "huzhgien");
	// 等价  explicit构造函数不能使用=赋值方式初始化
	cout << get<1>(tup1).c_str()<<endl;     //得到tuple的第二个字段值
	//tuple_element<1, my_tuple>::type cnt = get<1>(tup1);
	cout << tuple_size<my_tuple>::value << endl;  //得到tuple的字段个数
	system("pause");
	return 0;
}
```
***这个数据结构常常用在函数需要返回多个值且不需要新建一个struct或者class时使用***

## 位图bitset

```c++
// bitvec1 比初始值小：初始值中的高位被丢弃，只保留13位
bitset<13> bitvec1(0xbeef);  // 二进制位序列为1111011101111

// bitvec1 比初始值大：他的高位置为0
bitset<20> bitvec2(0xbeef);  // 二进制位序列为0001011111011101111

// 在64位机器中,long long 0ULL是64个0比特，因此~0ULL是64个1
bitset<128> bitvec3(~0ULL);  // 0~63位为1；63~127位为0


```

还可以使用字符串来初始化bitset
字符串中下标最小的字符对应高位，反之亦然；如果string包含字符数比bitset少，那么biset的高位被置为0;

```c++
bitset<32> bitvec4("1100"); //2、3 两位为1，剩余两位为0；其他为都是0
string str("1111111111111111000000");
bitset<32> bitvec6(str,5,4);  // 使用字符串的其中几位来初始化bitset
```
关于bitset一些操作：

![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/4A68ADE99018414EB7B6F81FE9609E27/4364)


## 随机数

> 在老版本的c和c++语言中都依赖于一个简单的C库函数rand来生成随机数，这个函数生成均匀分布的伪随机数（随机种子不改的话，每次运行结果都一样，因此每次都需要重置随机种子），每个随机数的范围在0~Max_Value系统相关最大值（最少为332767） 

> 然而很多情况下，非均匀的分布随机数也需要，还有浮点数等，这个函数并不适用。

> C++程序不应该使用库函数rand，而应该使用default_random_engine类和恰当分布类对象。


```c++
#include <iostream>
#include <random>
using namespace std;

int main(int argc, char * args[])
{
	default_random_engine e;
	//default_random_engine e(int); // 指定随机发生种子
	
	cout << e()<<endl;  //直接生成  使用默认的分布对象
	uniform_int_distribution<unsigned> u(0, 9);
	for (size_t i=0;i<10;++i)
	{
		cout << u(e) << endl;  //指定分布函数
	}
	// 一般使用时间戳来设置随机种子  秒级别的随机种子  在一秒内生成的随机数序列相同
	//default_random_engine e(time(0)); 
	system("pause");
	return 0;
}
```

```c++
default_real_engine  ;//实数随机生成引擎
normal_distribution  ;//正太分布：需要指定均值和标准差
bernoulli_distrbution ;//伯努利分布，可以生成50%的bool值
```

> 老版本C++一般使用srand((uint32)time(0))来重置随机种子 ，然后使用rand来生成



## IO再探

1. **ends与endl的区别:**
> ends函数 终止字符串 endl 函数 终止一行并刷新缓冲区

> << ends 相当于 << '\0'     
    << endl 相当于 << '\n' << flush
    
```c++
#include <iostream> 
using namespace std; 

int main() 
{ 
   
    cout << "a" ; 
    cout << "b" <<ends; 
    cout << "c" <<endl; 
    cout << "e" << flush; 
    cout << "f" << flush; 
    cout << "g" ; 
    cout << "h" <<ends; 
    cout << "i" << flush; 
    cout << "j" <<endl; 
    return 0; 
} 

//结果 
ab c 
efgh ij
```

2. 控制bool输出格式（其他类型类似）
```
cout<<boolalpha<<true;//这样可以直接输出字符串true而不是1，修正采用noboolalpha

cout<<hex;十六进制
cout<<oct;八进制
cout<<dec；//十进制
cout<<showbase；可以输出进制
cout<<noshowbase;//遇上一个命令相反，一般搭配使用
```

3. 文件IO流
文件输出输入流指针位置的控制

![image](http://note.youdao.com/yws/public/resource/442f4d903c7a49013290c1fd611019a7/xmlnote/0069327153F6444F92E38D5262D47B7D/4467)