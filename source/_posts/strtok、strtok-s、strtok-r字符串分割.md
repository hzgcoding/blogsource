---
title: strtok、strtok_s、strtok_r字符串分割
date: 2017-08-08 18:20:03
tags:
---

## strtok
 原型：`char *strtok(char *s, char *delim);`
## 功能
分解字符串为一组标记串。s为要分解的字符串，delim为分隔符字符串。
说明：**首次调用时，s必须指向要分解的字符串，随后调用要把s设成NULL。** strtok在s中查找包含在delim中的字符并用NULL('\0')来替换，直到找遍整个字符串。 返回指向下一个标记串。当没有标记串时则返回空字符NULL。

```
#include <iostream>  
using namespace std;  
  
int main(int argc, char * argv[])  
{  
    //时间格式 2010/08/11 10:38:22  
    char strEventTime[] = "2010/08/11 10:38:22";  
    char *token = NULL;  
      
    token = strtok(strEventTime, "/");  
    char *year = token;  
    if (token != NULL)  
    {  
        token = strtok(NULL, "/");  
    }  
    char *month = token;  
    if (token != NULL)  
    {  
        token = strtok(NULL, " ");  
    }  
    char *day = token;  
    if (token != NULL)  
    {  
        token = strtok(NULL, ":");  
    }  
    char *hour = token;  
    if (token != NULL)  
    {  
        token = strtok(NULL, ":");  
    }  
    char *minute = token;  
  
    if (token != NULL)  
    {  
        token = strtok(NULL, ":");  
    }  
    char *second = token;  
  
    printf("%s %s %s %s %s %s %s\n", year, month, day, hour, minute, second);  
    return 0;  
}  

```

上面这段程序在VS2015下编译会出现错误提醒，大概意思就是说strtok不安全。

## strtok_s函数
strtok_s是windows下的一个分割字符串安全函数，其函数原型如下：

`char *strtok_s( char *strToken, const char *strDelimit, char **buf);`

这个函数将**剩余的字符串存储在buf变量中，而不是静态变量中，从而保证了安全性**。
```
#include <iostream>  
using namespace std;

int main(int argc, char * argv[])
{
	//时间格式 2010/08/11 10:38:22  
	char strEventTime[] = "2010/08/11 10:38:22";
	char *token = NULL;
	char *next_token = NULL;
	token = strtok_s(strEventTime, "/", &next_token);
	char *year = token;
	if (token != NULL)
	{
		token = strtok_s(NULL, "/",&next_token);
	}
	char *month = token;
	if (token != NULL)
	{
		token = strtok_s(NULL, " ", &next_token);
	}
	char *day = token;
	if (token != NULL)
	{
		token = strtok_s(NULL, ":", &next_token);
	}
	char *hour = token;
	if (token != NULL)
	{
		token = strtok_s(NULL, ":", &next_token);
	}
	char *minute = token;

	if (token != NULL)
	{
		token = strtok_s(NULL, ":", &next_token);
	}
	char *second = token;

	printf("%s %s %s %s %s %s \n", year, month, day, hour, minute, second);
	system("pause");
	return 0;
}
```
## strtok_r函数
strtok_s函数是**linux**下分割字符串的安全函数，函数声明如下：

`char *strtok_r(char *str, const char *delim, char **saveptr);`

该函数也会破坏带分解字符串的完整性，但是其将**剩余的字符串保存在saveptr变量中，保证了安全性。**