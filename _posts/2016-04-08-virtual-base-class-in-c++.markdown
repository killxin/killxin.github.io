---
layout: post
title:  "C++�е������"
categories: jekyll update
tags: data-mining
---
## һ�������ĸ���
*****

���ڶ����̳�·������һ�������Ļ���,����Щ·���е�ĳ������ϴ�����������Ļ���ͻ�������ʵ��(��������)����ֻ�뱣����������һ��ʵ�������Խ������������˵��Ϊ����ࡣ

�ڼ̳��в��������ԭ���п����Ǽ̳���̳��˻����Σ��Ӷ������˶������������ֹһ�ε�ͨ�����·���̳������ڴ��д����˻����Ա�Ķ�ݿ����������Ļ���ԭ�������ڴ���ֻ�л����Ա��һ�ݿ�����������ͨ���ѻ���̳�����Ϊ����ģ���ֻ�ܼ̳л����һ�ݿ������Ӷ��������塣��virtual�޶����ѻ���̳�˵��Ϊ����ġ�

��������麯�����������ڣ��������Ա�ʵ���������������̳��еĳ�ͻ���⣬�����麯�����಻���Ա�ʵ����������֧�ֶ�̬��

ע����������������ʵ�����G++����ɡ�

## ���������̽��C++����ģ�͡��е�һ��ʵ��
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

����X��һ�������࣬����Ϊ��Ωһ�ı�ʶX��һ��ʵ������ҪΪ�����1�ֽڵ��ַ������ڴ���ռ��һ��ռ䣬�Ӷ����������ڴ��ַΩһ�ı�����һ��ʵ����

Y��Z����̳еķ�ʽ�̳���A����C++�ı�����ʵ��ʱ�����Զ�ΪY��Z����һ��ָ�룬��64λϵͳ�У�һ��ָ��8�ֽڣ����ڴ�ʱY��Z�Ѳ��ǿ����࣬���Բ���Ϊ�����1�ֽڵ�ռλ�ַ���

A�̳���Y��Z�����Y����Ϊpy����Z����Ϊpz���е�ָ����п���������A���������̳���Y����Ϊapy����Z����Ϊapz����ָ�룬��16�ֽڡ�

���ڵ������ǣ�Y��Z��A�е�ָ�뾿����ʲô�ã�Ϊ�ˣ���������������ʵ�飬��ʱ�õ��Ľ����ǣ���ָ����Y��Z���ô�δ֪����A������Ѱַ��

## �����鿴Y��Z��A��ʵ���е�����
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

ͨ�������ʵ��������Լ���Կ�������Y��Z�У����ݵĲ���Ϊ��

py��pz������ָ����ڴ������ֵ�������ַ��������Ϊ���Ǳ�ʾ��Y��Z��ʵ���в�������Ѱַ

y��z��

x

��A�У����ݵĲ���Ϊ��

apz

z

apy

y

a

x

����apzָ��������ֵΪ0x18���Ҿ������ʾ����A�м̳���Y������������׵�ַ��ƫ��������¼��ƫ������ԭ�����ڣ�������ĳ����У�

<pre><code>
A a;
Y *p = &a;
cout << p->y;
cout << p->x;
</code></pre>

�Ҳ��룬�ڵ�ǰ�ض��Ľṹ�У�����Z�����ݺ�X�����ݷֱ�λ���ڴ沼�ֵ����ˣ�����Ѱַ����Y��������Ҫ��¼���ƫ�����ſ����ҵ���Ϊ����֤�˲��룬������������ʵ�顣

## �ġ�������H
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

����ʵ���У����ǵõ�A���ڴ沼�֣�

apy

y

apz

z

aph

h

a

x

��ʵ�����ȣ�����ڷŵ�˳�����˱仯����������apy�м�¼��h��ƫ��������apz�м�¼��z��ƫ���������Կ��Կ�����A�м̳е�ָ��������A��Ѱ�Ҹ�������ƫ���������ã������ϳ���Ϊͨ���м��Ѱַ��

## �塢�ο�����
*****

<https://book.douban.com/subject/1091086/>���̽��C++����ģ��</https://book.douban.com/subject/1091086/>
<http://blog.csdn.net/jiangnanyouzi/article/details/3721091>






