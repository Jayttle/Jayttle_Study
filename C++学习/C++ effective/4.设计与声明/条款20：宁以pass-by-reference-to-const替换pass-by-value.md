## 条款20：宁以pass-by-reference-to-const替换pass-by-value

​					***Prefer pass-by-reference-to-const to pass-by-value***

如：

```c++
class Person{
public:
	...
private:
	std::string name;
	std::string address;
}
class Student:public Person{
public:
	...
private:
	std::string schoolName;
	std::string schoolAddress;
}
```

```c++
bool validateStudent(Student s);	//函数以by value方式接受学生

Student plato;
bool platoIsOK=validateStudent(plato);
```

此过程会有一次Student copy构造函数、一次Person copy构造函数、四次string copy构造函数；当函数内的Student复件被销毁，每一个构造函数都需要调用一次析构函数；

传递方式效率高： ***pass  by reference-to-const:***

```c++
bool validateStudent(const Student& s);
```

同时此方式能避免slicing（对象切割）问题。derived class对象以by value传递并被视为一个base class对象，调用了base class的构造函数；

例如：

```c++
void printNameAndDisplay(Window w)		//不正确！参数可能被切割
{
	std::cout<<w.name();
	w.display();
}
//当作为Window的derive class声明的wwsb；调用
printNameAndDisplay(wwsb);
	//因为被分割，原本作为derive class的WindowWithScrollBars会被视作base class
	//其语句在w.display() 使用的是Window::display 而非WindowWithScrollBars::display
```

解决方法就是以by-reference-to-const方式传递：

```c++
void printNameAndDisplay(const Window& w) //很好！参数不会被分割；
{
	std::cout<<w.name();
	w.display();
}   
```

一般而言，你可以合理假设“pass-by-value并不昂贵”的唯一对象就是内置型类型和STL的迭代器和函数对象；；

#### 请记住：

+ 尽量以pass-by-reference-to-const替换pass-by-value。前者高效且可避免切割问题
+ 以上规则不适用于内置类型，以及STL的迭代器和函数对象；对它们而言pass-by-value更合适；