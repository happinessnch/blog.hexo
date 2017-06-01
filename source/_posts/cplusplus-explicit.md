---
title: C++ explicit 
date: 2017-06-01 09:06:06
tags: C++
categories: C++
---

[explicit MSDN](https://msdn.microsoft.com/en-us/library/h1y7x448%28v=vs.110%29.aspx)

C++ ctors (constructors) that have just one parameter automatically perform implicit type conversion. For example, if you pass an int when the ctor expects a string pointer parameter, the compiler adds the code it must have to convert the int to a string pointer. However, this automatic behavior can cause errors. 

To prevent implicit conversions, you can add the explicit keyword to the ctor declaration. This forces the code to either use a parameter of the correct type, or cast the parameter to the correct type. That is, if the cast is not explicitly expressed in code, an error will result.

当类的构造函数只有一个参数时，赋值操作会执行隐式的类型轮换，如下：

```
#include <iostream>
using namespace std;

class String {
public:
    String(int n);     
    String(const char *p); 
};

int main()
{
    String str1 = 1024;
    String str2 = 'x';
    String str3 = "string";

	return 0;
}
```
'x'通过隐式类型转换成int，并调用String(int)构造函数创建str2。
str1与str2的情况，并不是String类的设计者所希望的。
为避免str1与str2的情况发生，给构造函数String(int)添加explicit。
```
class String {
public:
    explicit String(int n);     
    String(const char *p); 
};
```

加入后，编译器将会报错（VC）：
```
error C2440: 'initializing' : cannot convert from 'int' to 'String' Constructor for class 'String' is declared 'explicit'
```

参考：
[explicit MSDN](https://msdn.microsoft.com/en-us/library/h1y7x448%28v=vs.110%29.aspx)
[explicit-keyword-mean](https://stackoverflow.com/questions/121162/what-does-the-explicit-keyword-mean)
