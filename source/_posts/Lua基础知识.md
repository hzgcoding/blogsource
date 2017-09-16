---
title: Lua基础知识
date: 2017-09-12 10:46:05
tags:
    lua
---

## Lua基础知识

### 不一样

1. 变量没有类型，值有类型
2. 变量默认都是global，除非声明local，若变量名相同，将覆盖之
3. 变量交换值方便`a,b = b,a`
4. 当变量没有被初始化（仅仅是声明），被定义为nil
5. **索引从1开始，这点跟其他的语言区别很大，格外注意**
6. **0和空字符串被认为是true,条件检查也是如此**
7. 不等于号`~=`
8. 使用两个点`..` 连接两个字符串
9. `#`:一个一元运算符返回一个字符串或一个表的长度,   #"Hello" 会返回 5.


### 迭代器

> 迭代器是一种结构，使能够遍历所谓的集合或容器中的元素。在Lua中，这些集合通常是指那些用于创建各种数据结构，如数组表。

#### 一般迭代器

一个通用的迭代器提供的键值对集合中的每个元素：
```lua
array = {"Lua", "Tutorial"}

for key,value in ipairs(array)   -- 使用默认ipairs迭代函数
do
   print(key, value)
end
```
输出结果如下：
```lua
1  Lua
2  Tutorial
```
Lua中，有时候使用函数来表示迭代器。基于这些迭代器的功能状态保持，有两种主要类型：
- 无状态的迭代器
- 迭代器状态

**无状态的迭代器**

这类型的迭代器功能不保留任何状态。
```lua
-- 实现打印n个数的平方
function square(iteratorMaxCount,currentNumber)
   if currentNumber<iteratorMaxCount
   then
      currentNumber = currentNumber+1
   return currentNumber, currentNumber*currentNumber
   end
end

for i,n in square,3,0
do
   print(i,n)
end
```
输出结果如下：
```lua
1   1
2   4
3   9
```

我们可以稍作修改，以模仿迭代器的工作方式ipairs函数：
```lua
function square(iteratorMaxCount,currentNumber)
   if currentNumber<iteratorMaxCount
   then
      currentNumber = currentNumber+1
   return currentNumber, currentNumber*currentNumber
   end
end

function squares(iteratorMaxCount)
   return square,iteratorMaxCount,0
end  

for i,n in squares(3)
do 
    print(i,n)
end
```

**有状态迭代器**

使用闭包的概念可以实现:
```lua
array = {"Lua", "Tutorial"}

function elementIterator (collection)
   local index = 0
   local count = #collection
   -- The closure function is returned
   return function ()
      index = index + 1
      if index <= count
      then
         -- return the current element of the iterator
         return collection[index]
      end
   end
end

for element in elementIterator(array)
do
   print(element)
end
```

当我们运行上面的程序，会得到下面的输出。

```lua
Lua
Tutorial
```


