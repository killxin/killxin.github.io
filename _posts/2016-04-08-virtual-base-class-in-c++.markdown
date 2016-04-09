---
layout: post
title:  "探索g++如何处理C++中的虚基类"
categories: jekyll update
tags: data-mining
---
## 一、虚基类的概念
*****

当在多条继承路径上有一个公共的基类,在这些路径中的某几条汇合处，这个公共的基类就会产生多个实例(或多个副本)，若只想保存这个基类的一个实例，可以将这个公共基类说明为虚基类。

在继承中产生歧义的原因有可能是继承类继承了基类多次，从而产生了多个拷贝，即不止一次的通过多个路径继承类在内存中创建了基类成员的多份拷贝。虚基类的基本原则是在内存中只有基类成员的一份拷贝。这样，通过把基类继承声明为虚拟的，就只能继承基类的一份拷贝，从而消除歧义。用virtual限定符把基类继承说明为虚拟的。

虚基类与虚函数的区别在于，虚基类可以被实例化，用来解决多继承中的冲突问题，含有虚函数的类不可以被实例化，用来支持多态。

注：下面所述的所有实验均在G++下完成。

## 二、“深度探索C++对象模型”中的一个实验
*****

<pre><code>
class X { };
class Y :public virtual X { };
class Z :public virtual X { };
class A :public Y, public Z { };

cout << sizeof(X) << endl; //1
cout << sizeof(Y) << endl; //8
cout << sizeof(Z) << endl; //8
cout << sizeof(A) << endl; //16
</code></pre>

由于X是一个空虚类，所以为了惟一的标识X的一个实例，需要为其添加1字节的字符来在内存中占据一块空间，从而可以以其内存地址惟一的标记类的一个实例。

Y，Z以虚继承的方式继承自A，在C++的编译器实现时，会自动为Y，Z分配一个指针，在64位系统中，一个指针8字节，由于此时Y，Z已不是空虚类，所以不再为其分配1字节的占位字符。

A继承自Y，Z，会对Y（记为py），Z（记为pz）中的指针进行拷贝，所以A中有两个继承自Y（记为apy），Z（记为apz）的指针，共16字节。

现在的问题是，Y，Z，A中的指针究竟有什么用？为此，我做了如下两个实验，暂时得到的结论是：该指针在Y，Z中用处未知，在A中用于寻址。

## 三、查看Y，Z，A的实例中的内容
*****

<pre><code>
class X { public: unsigned long x = 5; };
class Y :public virtual X { public: unsigned long y = 6; };
class Z :public virtual X { public: unsigned long z = 7; };
class A :public Y, public Z { public: unsigned long a = 8; };

cout << sizeof(X) << endl; //8
cout << sizeof(Y) << endl; //24
cout << sizeof(Z) << endl; //24
cout << sizeof(A) << endl; //48

X x;
Y y;
Z z;
A a;
	
cout << &y << endl; //0x7ffcbc944490
cout << &z << endl; //0x7ffcbc9444b0
cout << &a << endl; //0x7ffcbc9444d0

cout << hex << ((unsigned long*)(&y))[0] << endl; //4012b8
cout << hex << ((unsigned long*)(&y))[1] << endl; //6
cout << hex << ((unsigned long*)(&y))[2] << endl; //5

cout << hex << ((unsigned long*)(&z))[0] << endl; //401298
cout << hex << ((unsigned long*)(&z))[1] << endl; //7
cout << hex << ((unsigned long*)(&z))[2] << endl; //5

cout << hex << ((unsigned long*)(&a))[0] << endl; //4011f8
cout << hex << ((unsigned long*)(&a))[1] << endl; //7
cout << hex << ((unsigned long*)(&a))[2] << endl; //401210
cout << hex << ((unsigned long*)(&a))[3] << endl; //6
cout << hex << ((unsigned long*)(&a))[4] << endl; //8
cout << hex << ((unsigned long*)(&a))[5] << endl; //5

cout << hex << (((unsigned long**)(&y))[0])[0] << endl; //4012b8
cout << hex << (((unsigned long**)(&z))[0])[0] << endl;	//401298
cout << hex << (((unsigned long**)(&a))[0])[0] << endl; //18
cout << hex << (((unsigned long**)(&a))[2])[0] << endl; //0

cout << &(y.x) << endl; //0x7ffcbc9444a0
cout << &(z.x) << endl; //0x7ffcbc9444c0
cout << &(a.x) << endl; //0x7ffcbc9444f8
</code></pre>

通过上面的实验我们隐约可以看到，在Y，Z中，数据的布局为，

py（pz），其指向的内存区域的值是这个地址本身，我认为这是表示在Y，Z的实例中不用重新寻址

y（z）

x

在A中，数据的布局为，

apz

z

apy

y

a

x

其中apz指向的区域的值为0x18，我觉得这表示的是A中继承自Y的数据相对于首地址的偏移量，记录该偏移量的原因在于，在下面的场景中：

<pre><code>
A a;
Y *p = &a;
cout << p->y;
cout << p->x;
</code></pre>

我猜想，在当前特定的结构中，发现Z的数据和X的数据分别位于内存布局的两端，容易寻址，而Y的数据需要记录相对偏移量才可以找到，为了验证此猜想，继续做第三个实验。

## 四、新增类H
*****

<pre><code>
class X { public: unsigned long x = 5; };
class Y :public virtual X { public: unsigned long y = 6; };
class Z :public virtual X { public: unsigned long z = 7; };
class H :public virtual X { public: unsigned long h = 8; };
class A :public Y, public Z, public H { public: unsigned long a = 9; };

cout << sizeof(X) << endl; //8
cout << sizeof(Y) << endl; //24
cout << sizeof(Z) << endl; //24
cout << sizeof(H) << endl; //24
cout << sizeof(A) << endl; //64

X x;
Y y;
Z z;
H h;
A a;
	
cout << &y << endl; //0x7fff11918590
cout << &z << endl; //0x7fff119185b0
cout << &h << endl; //0x7fff119185d0
cout << &a << endl; //0x7fff119185f0

cout << hex << ((unsigned long*)(&y))[0] << endl; //4015a8
cout << hex << ((unsigned long*)(&y))[1] << endl; //6
cout << hex << ((unsigned long*)(&y))[2] << endl; //5

cout << hex << ((unsigned long*)(&z))[0] << endl; //401588
cout << hex << ((unsigned long*)(&z))[1] << endl; //7
cout << hex << ((unsigned long*)(&z))[2] << endl; //5

cout << hex << ((unsigned long*)(&h))[0] << endl; //401568
cout << hex << ((unsigned long*)(&h))[1] << endl; //8
cout << hex << ((unsigned long*)(&h))[2] << endl; //5

cout << hex << ((unsigned long*)(&a))[0] << endl; //401478
cout << hex << ((unsigned long*)(&a))[1] << endl; //6
cout << hex << ((unsigned long*)(&a))[2] << endl; //401490
cout << hex << ((unsigned long*)(&a))[3] << endl; //7
cout << hex << ((unsigned long*)(&a))[4] << endl; //4014a8
cout << hex << ((unsigned long*)(&a))[5] << endl; //8
cout << hex << ((unsigned long*)(&a))[6] << endl; //9
cout << hex << ((unsigned long*)(&a))[7] << endl; //5

cout << hex << (((unsigned long**)(&y))[0])[0] << endl; //4015a8
cout << hex << (((unsigned long**)(&z))[0])[0] << endl; //401588
cout << hex << (((unsigned long**)(&a))[0])[0] << endl; //0x28
cout << hex << (((unsigned long**)(&a))[2])[0] << endl; //0x18
cout << hex << (((unsigned long**)(&a))[4])[0] << endl; //0x0
</code></pre>

上述实验中，我们得到A的内存布局，

apy

y

apz

z

aph

h

a

x

与实验二相比，父类摆放的顺序发生了变化，但是仍在apy中记录了h的偏移量，在apz中记录了z的偏移量，所以可以看到，A中继承的指针起到了在A中寻找父类数据偏移量的作用，有资料称其为通过中间层寻址。

## 五、参考资料
*****

[深度探索C++对象模型](https://book.douban.com/subject/1091086/)

<http://blog.csdn.net/jiangnanyouzi/article/details/3721091>

[谈VC++对象模型](http://www.cnblogs.com/heyonggang/p/3333054.html)






