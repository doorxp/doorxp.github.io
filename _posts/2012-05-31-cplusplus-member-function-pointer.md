---
layout: post
title: "C++成员函数指针的应用"
category: CPlusPlus
---

C++中，成员指针是最为复杂的语法结构。但在事件驱动和多线程应用中被广泛用于调用回叫函数。在多线程应用中，每个线程都通过指向成员函数的指针来调用该函数。在这样的应用中，如果不用成员指针，编程是非常困难的。

刚遇到这种语法时也许会让你止步不前。但你会发现，使用恰当的类型定义之后，复杂的语法是可以简化的。本文引导你了解成员函数指针的声明，赋值和调用回叫函数。

成员函数指针的声明

一个成员函数指针包括成员函数的返回类型，后随::操作符类名，指针名和函数的参数。初看上去，语法有点复杂。其实可以把它理解为一个指向原函数的指针，格式是：函数返回类型，类名，::操作符，指针星号，指针名，函数参数。

一个指向外部函数的指针声明为

bc.. void (*pf)(char *, const char *);

void strcpy(char * dest, const char * source);

pf=strcpy;


p. 一个指向类A成员函数的指针声明为：


@void (A::*pmf)(char *, const char *);@

p. 　声明的解释是：pmf是一个指向A成员函数的指针，返回无类型值，函数带有二个参数，参数的类型分别是char * 和 const char *。除了在星号前增加A:: ，与声明外部函数指针的方法一样。

　赋值

　给成员指针赋值的方法是将函数名通过指针符号&赋予指针名。如下所示：

bc.. class A
{

　　 public:

　　　void strcpy(char *, const char *);

　　　void strcat(char *, const char *);

};
pmf = &A::strcpy; 

p. 有些老的编译器可以通过没有&号的赋值方式，但标准C++强制要求加上&号。

使用类型定义

可以用类型定义来隐藏复杂的成员指针语法。例如，下面的语句定义了PMA是一个指向A成员函数的指针，函数返回无类型值，函数参数类型为char * 和 const char *：

bc.. typedef void(A::*PMA)(char *, const char *);

PMA pmf = &A::strcat; // pmf是PMF类型(类A成员指针)的变量

p. 下文会看到使用类型定义特别有利于声明成员指针数组。

通过成员指针调用成员函数

可以在不必知道函数名的情况下，通过成员指针调用对象的成员函数。例如，函数dispatcher有一个变量pmf，通过它调用类成员函数，不管它调用的是strcpy()函数还是strcat()函数。指向外部原函数的指针和指向类成员函数的指针是有很大区别的。后者必须指向被调函数的宿主对象。因此，除了要有成员指针外，还要有合法对象或对象指针。

现举例做进一步说明。假设A有二个实例，成员函数指针支持多态性。这样在成员指针调用虚成员函数时是动态处理的(即所谓后联编 - 译注)。注意，不可调用构造和析构函数。示例如下：

bc.. A a1, a2;

A *p= &a1; //创建指向A的指针

//创建指向成员的指针并初始化

void (A::*pmf)(char *, const char *) = &A::strcpy;

//要将成员函数绑定到pmf，必须定义呼叫的对象。

//可以用*号引导：

void dispatcher(A a, void (A::*pmf)(char *, const char *))

{

   char str[4];

　　　(a.*pmf)(str, “abc”); //将成员函数绑定到pmf

}

　　//或用A的指针表达方式指向成员指针：

void dispatcher(A * p, void (A::*pmf)(char *, const char *))

{

　　　char str[4]; (p->*pmf)(str, “abc”);

}

//函数的调用方法为：

dispatcher(a, pmf); // .* 方式

dispatcher(&a, pmf); // ->* 方式 

p. 高级使用技巧

以上是成员函数的基本知识。现在介绍它的高级使用技巧。

成员指针数组

在下例，声明了一个含有二个成员指针的数组，并分配类的成员函数地址给成员指针：

@PMA pmf[2] = {&A::strcpy, &A::strcat};@
也就是
@void (A::*PMA[2])(char *, const char *)= {&A::strcpy, &A::strcat};@

　这样的数组在菜单驱动应用中很有用。选择菜单项后，应用将调用相应的回叫函数，如下所示：

bc.. enum MENU_OPTIONS { COPY, CONCAT };

int main()
{
　　　MENU_OPTIONS option; char str[4];
　　　//从外部资源读取选项
　　　switch (option)
　　　{
　　　　case COPY:

　　　　　(pa->*pmf[COPY])(str, “abc”);

　　　　　break;

　　　　case CONCAT:

　　　　　(pa->*pmf[CONCAT])(str, “abc”);

　　　　　break;

　　　　　//…

　　　}
} 


p. Const 类型的成员函数

　　成员指针的类型应该与成员函数类型一致。上面例子中的pmf 可以指向A的任意函数，只要该函数不是const类型。如下所示，如果将touppercase()的地址分配给pmf，将导致编译出错，因为touppercase() 的类型是const。

bc.. Class A
{

public:

　　　  void strpcy(char *, const char *);

　　　  void strcat(char *, const char *);

　　　  void touppercase(char *, const char*) const;

};

pmf=&A::touppercase; //出错，类型不匹配

//解决的方法是声明一个const类型的成员指针：

void (A::pcmf)(char *, const char *) const;

pcmf=&A::touppercase; // 现在可以了 


p. 有些差劲的编译器允许一个非const类型的成员指针指向const类型的成员函数。这在标准C++是不允许的。