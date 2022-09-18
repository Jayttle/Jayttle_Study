## 条款25：考虑写出一个不抛出异常的swap函数

​		***Consider support for a non-throwing swap***

缺省情况下swap动作由标准程序库提供的：

```c++
namespace std{
	template<typename T>
	void swap(T& a,T& b)
	{
		T temp(a);
		a=b;
		b=temp;
	}
}
```

只要类型T支持copying，即可swap帮忙完成置换；

"以指针指向一个对象，内含真正数据"——“pimpl手法”（pointer to implementation 条款31）:

```c++
class WidgetImpl{	//针对Widget数据而设计的class
public:
	...
private:
	int a,b,c;		//可能有许多数据
	std::vector<double>v;	//意味复制时间很长
	...
};

class Widget{
public:
    Widget(const Widget& rhs);
    Widget& operator=(const Widget& rhs)
    {
        ...
        *pImpl=*(rhs.pImpl);
    }
    ...
private:
    WidgetImpl* pImpl
}

```

当想要置换两个Widget对象值，唯一需要做的就是置换其pImpl指针，希望告诉std::swap；当Widgets被置换时真正要做的是置换其内部的pImpl指针：

```c++
//将std::swap针对Widget特化
namespace std{
	template<>							//这是std::swap针对“T是Widget”的特化版本
	void swap<Widget>(Widget& a,Widget& b)	//目前还不能通过编译
	{
		swap(a.pImpl,b.pImpl);		//pImpl是private的无法直接访问；
	}
}
```

“template<>“表示它是std::swap 的一个全特化（total template  specialization）版本，函数名称之后的"<Widget>"表示这一特化版本针对”T是Widget而设计“；

我们令Widget声明一个名为swap的public成员函数做真正的置换工作，然后将std::swap特化，调用该成员函数：

```c++
class Widget{
public:
	...
	void swap(Widget& other)
	{
		using std::swap;		//这个声明有必要，后面有解释
		swap(pImpl,other.pImpl);//置换其Impl指针
	}
	...
};

namespace std{
	template<>						//修订后的std::swap特化版本
	void swap<Widget>(Widget& a,Widget& b)
	{								//若要置换Widget则调用swap成员函数；
		a.swap(b);
	}
}
```

如果你希望你的软件有可预期的行为，请不要添加任何新东西到std里头；

声明一个non-member swap让它调用member swap，但不再将那个non-member swap声明为std::swap的特化版or重载版本，并声明在命名空间WidgetStuff:

```c++
namespace WidgetStuff{
	...
	template<typename T>
	class Widget{...};			//模板化的WidgetImpl 内含swap成员函数
	...
	template<typename T>			//non-member swap函数
	void swap(Widget<T>& a,Widget<T>& b)
	{  a.swap(b)};
}
```

假设有一个function template需要置换两个对象：

```c++
template<typename T>
void doSometing(T& obj1,T& obj2)
{
	...
	swap(obj1,obj2);
	...
}
```

此时调用哪个swap？std既有的 or 一般化版本？某个可能存在的特化版本 or 一个可能存在的T专属版本且栖身于某个命名空间？

希望调用T专属版本，并在该版本不存在的情况下调用std内的一般化版本：

```c++
template<typename T>
void doSomething(T& obj1,T& obj2)
{
	using std::swap;	//令std::swap在此函数内可用
	...
	swap(obj1,obj2);	//为T型对象调用最佳swap版本
	...
}
```

c++的名称查找法则，会确保找到global作用域orT所在之命名空间内的任何T专属的swap；

#### 请记住：

+ 当std::swap对你的类型效率不高时，提供一个swap成员函数，并确定这个函数不抛出异常；

+ 如果你提供一个member swap，也该提供一个non-member swap来调用前者，对于classes，也请特化std::swap；

+ 调用swap时应针对std::swap使用using声明式，然后调用swap并且不带任何“命名空间资格修饰”；

+ 为"用户定义类型"进行std templates全特化是好的，但千万不要尝试在std内加入某些对std而言全新的东西

  
