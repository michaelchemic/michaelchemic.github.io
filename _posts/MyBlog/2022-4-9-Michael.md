---
title: "C++多态"
subtitle: "C++多态"
layout: post
author: "Michael"
header-style: text
tags:
  - C++多态
---

## 指向基类的指针

类继承的一个关键特性是，指向派生类的指针与指向基类的指针是类型兼容的。 多态是利用这个简单但功能强大且通用的特性的艺术。  

```cpp
// pointers to base class
#include <iostream>
using namespace std;

class Polygon {
  protected:
    int width, height;
  public:
    void set_values (int a, int b)
      { width=a; height=b; }
};

class Rectangle: public Polygon {
  public:
    int area()
      { return width*height; }
};

class Triangle: public Polygon {
  public:
    int area()
      { return width*height/2; }
};

int main () {
  Rectangle rect;
  Triangle trgl;
  //创建两个对象
  Polygon * ppoly1 = &rect;
  Polygon * ppoly2 = &trgl;
  //声明两个指针变量引用地址
  ppoly1->set_values (4,5);
  ppoly2->set_values (4,5);
  //指针指向函数
  cout << rect.area() << '\n';
  cout << trgl.area() << '\n';
  return 0;
}
```
函数main声明了两个指向Polygon的指针(命名为ppoly1和ppoly2)。 它们分别被分配rect和trgl的地址，它们是Rectangle和Triangle类型的对象。 这样的赋值是有效的，因为Rectangle和Triangle都是派生自Polygon的类。  
 
解引用ppoly1和ppoly2(使用ppoly1->和ppoly2->)是有效的，并允许我们访问它们的指向对象的成员。 例如，下面两个语句在前面的例子中是等价的:  

ppoly1 - > set_values(4、5);  
rect.set_values(4、5);  
 
但是因为ppoly1和ppoly2的类型都是指向Polygon的指针(不是指向Rectangle的指针也不是指向Triangle的指针)，所以只能访问从Polygon继承的成员，而不能访问其派生类Rectangle和Triangle的成员。 这就是为什么上面的程序直接使用rect和trgl来访问这两个对象的area成员，而不是使用指针; 指向基类的指针不能访问区域成员。  
 
区域成员可以访问的成员指针指向多边形面积要是多边形代替它的派生类的成员,但问题是,矩形和三角形实现不同版本的区域,因此没有一个共同的版本,可以在基类中实现。  

多态分为两类：
1. 静态多态： 函数重载，运算符重载，复用函数名
2. 动态多态： 派生类，虚函数实现运行时多态

静态多态和动态多态区别
3. 静态多态的函数地址早绑定，编译阶段确定函数地址。
4. 动态多态的函数地址晚绑定， 运行阶段确定函数地址。

## 虚拟成员 visual members
虚成员是可以在派生类中重新定义的成员函数，同时通过引用保留其调用属性。 将函数变为虚函数的语法是在其声明之前加上虚关键字:  

```cpp
// virtual members
#include <iostream>
using namespace std;

class Polygon {
  protected:
    int width, height;
  public:
    void set_values (int a, int b)
      { 
	      width=a; 
	      height=b;
      }
    virtual int area ()//虚函数
      { 
      	  return 0; 
      }
};

class Rectangle: public Polygon {
  public:
    int area () //同名的函数
      { return width * height; }
};

class Triangle: public Polygon {
  public:
    int area ()//同名的函数
      { return (width * height / 2); }
};

int main () {
  Rectangle rect;
  Triangle trgl;
  Polygon poly;
  Polygon * ppoly1 = &rect;
  Polygon * ppoly2 = &trgl;
  Polygon * ppoly3 = &poly;
  ppoly1->set_values (4,5);
  ppoly2->set_values (4,5);
  ppoly3->set_values (4,5);
  cout << ppoly1->area() << '\n';
  cout << ppoly2->area() << '\n';
  cout << ppoly3->area() << '\n';
  return 0;
}
```


在这个例子中，所有三个类(Polygon, Rectangle和Triangle)都有相同的成员:width, height, set_values和area函数。  
 
成员函数区在基类中声明为虚函数区，因为稍后会在每个派生类中重新定义它。 非虚成员也可以在派生类中重新定义，但派生类的非虚成员不能通过基类的引用访问: 也就是说，如果在上面的例子中area的声明中删除了virtual，那么对area的三个调用都将返回0，因为在所有情况下，都将调用基类的版本。  
 
因此,从本质上讲,什么虚拟关键字是允许派生类的成员具有相同的名称作为一个基类的适当从指针,更准确地说,当指针的类型是一个指向基类的指针指向派生类的对象,在上面的例子中。  
 
声明或继承虚函数的类称为多态类。  
 
请注意，尽管它的一个成员是虚拟的，但Polygon是一个常规类，其中甚至有一个对象被实例化(poly)，它自己的成员区域的定义总是返回0。  

## 抽象基类
抽象基类与前面例子中的Polygon类非常相似。 它们是只能作为基类使用的类，因此允许有没有定义的虚成员函数(称为纯虚函数)。 语法是将它们的定义替换为=0(一个等号和一个0):  
 
抽象基多边形类可以是这样的:  

```cpp
// abstract class CPolygon
class Polygon {
  protected:
    int width, height;
  public:
    void set_values (int a, int b)
      { width=a; height=b; }
    virtual int area () =0;//抽象基类
    //area没有定义，它是纯虚函数，包含至少一个纯虚函数称为 抽象基类。
};
```
抽象基类不能用于实例化对象

```cpp
	
Polygon mypolygon;   //不能工作，因为Polygon是个抽象基类

```
但是抽象基类并不是完全无用的。 它可以用来创建指向它的指针，并利用它的所有多态能力。 例如，以下指针声明是有效的:  

```cpp
Polygon * ppoly1;
Polygon * ppoly2;

```

当指向派生类(非抽象类)的对象时，实际上可以解除引用。 下面是整个例子:  
```cpp
// abstract base class
#include <iostream>
using namespace std;

class Polygon {
  protected:
    int width, height;
  public:
    void set_values (int a, int b)
      { width=a; height=b; }
    virtual int area (void) =0;
};

class Rectangle: public Polygon {
  public:
    int area (void)
      { return (width * height); }
};

class Triangle: public Polygon {
  public:
    int area (void)
      { return (width * height / 2); }
};

int main () {
  Rectangle rect;
  Triangle trgl;
  Polygon * ppoly1 = &rect;
  Polygon * ppoly2 = &trgl;
  ppoly1->set_values (4,5);
  ppoly2->set_values (4,5);
  cout << ppoly1->area() << '\n';
  cout << ppoly2->area() << '\n';
  return 0;
}
```
在本例中，不同但相关类型的对象使用唯一类型的指针(Polygon*)引用，每次都调用适当的成员函数，因为它们是虚的。 这在某些情况下非常有用。 例如，抽象基类Polygon的成员甚至可以使用特殊的指针this来访问适当的虚成员，即使Polygon本身没有实现这个函数:  

```cpp
// pure virtual members can be called
// from the abstract base class
#include <iostream>
using namespace std;

class Polygon {
  protected:
    int width, height;
  public:
    void set_values (int a, int b)
      { width=a; height=b; }
    virtual int area() =0;
    void printarea()
      { cout << this->area() << '\n'; }
};

class Rectangle: public Polygon {
  public:
    int area (void)
      { return (width * height); }
};

class Triangle: public Polygon {
  public:
    int area (void)
      { return (width * height / 2); }
};

int main () {
  Rectangle rect;
  Triangle trgl;
  Polygon * ppoly1 = &rect;
  Polygon * ppoly2 = &trgl;
  ppoly1->set_values (4,5);
  ppoly2->set_values (4,5);
  ppoly1->printarea();
  ppoly2->printarea();
  return 0;
}
```


虚成员和抽象类赋予c++多态特性，这对面向对象的项目最有用。 当然，上面的例子是非常简单的用例，但是这些特性可以应用于对象数组或动态分配的对象。  
 
下面这个例子结合了最新章节中的一些特性，比如动态内存、构造函数初始化和多态性:  


```cpp
// dynamic allocation and polymorphism
#include <iostream>
using namespace std;

class Polygon {
  protected:
    int width, height;
  public:
    Polygon (int a, int b) : width(a), height(b) {}
    virtual int area (void) =0;
    void printarea()
      { cout << this->area() << '\n'; }
};

class Rectangle: public Polygon {
  public:
    Rectangle(int a,int b) : Polygon(a,b) {}
    int area()
      { return width*height; }
};

class Triangle: public Polygon {
  public:
    Triangle(int a,int b) : Polygon(a,b) {}
    int area()
      { return width*height/2; }
};

int main () {

  Polygon * ppoly1 = new Rectangle (4,5);
  Polygon * ppoly2 = new Triangle (4,5);
  
  ppoly1->printarea();
  ppoly2->printarea();
  
  delete ppoly1;
  delete ppoly2;
  return 0;
}
```
	
内容待优化。。。未完待续

