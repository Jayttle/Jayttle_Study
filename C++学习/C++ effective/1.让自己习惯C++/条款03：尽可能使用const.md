## 条款03：尽可能使用const

​						***Use const whenever possible***

const多才多艺：

```c++
char greeting="Hello";
char* p=greeting;			//non-const pointer,non-const data
const char* p=greeting;		//non-const pointer,const data
char* const p=greeting;		//const pointer,non-const data;
const char* const p=greeting; //const pointer,const data;
```

const在*左边表示被指物是常量；

const在*右边表示指针自身是常量；

const在*两边，就是被指物和指针都是常量；

***如果被指物是常量，则意义相同***

```c++
void f1(const Widget* pw); //f1获得一个指针，指向一个常量Widget对象
void f2(widget* const pw); //f2同上
```

***声明迭代器const：***

仍遵循：

const在迭代器左边表示被指物是常量；

const在迭代器右边表示指针自身是常量；

```c++
std::vector<int> vec;
...
const std::vector<int>iterator iter=vec.begin(); //iter的作用像个T* const;
*iter=10;										//没问题，改变iter所指物
++iter;											//错误，iter是const；
std::vector<int>::const_iterator cIter=vec.begin();//cIter的作用像个const T*
*cIter=10;										//错误 *cIter是const
++cIter;										//没问题，改变cIter；
```

***const可以预防”没意思的赋值动作“——降低因客户错误而造成的意外***

```c++
class Rational{...};
const Ratianal operator* (const Rational& lhs,const Rational& rhs);
```

返回一个const对象；避免：

```c++
Rational a,b,c;
...
(a*b)=c;  //在a*b的成果上调用operator=
```

***const成员函数***

目的（2个）：

+ class接口更容易理解（哪些可以改动，哪些不可以）
+ 使“操作const对象”成为可能，编写高效代码一个根本办法是：以***pass by reference-to-const***方式传递对象；前提是：有const成员函数可用来处理取得的const对象；

被漠视的事实：两个成员函数的常量性 不同，可以被重载； 

```c++
class TextBlock{
public:
...
	const char& operator[](std::size_t position) const //operator[] for const对象
	{	return text[position];}
	char& operator[](std::size_t position)  //operator[] for non-const对象
	{	return text[position];}
private:
	std::string text;
}
```

TexxtBlock的operator[]s可被这么使用：

```c++
TextBlock tb("Hello");
std::cout<<tb[0];		//调用non-const TextBlock::operator[]
const TextBlock ctb("World");
std::cout<<ctb[0];      //调用const TextBlock::operator[]
```

上述太过造作：真实程序中const对象多用于***passed by pointer-to-const or passed by reference-to-const***的传递结果。

比较真实：

```c++
void print(const TextBlock& ctb) //此函数中ctb是const
{
	std::cout<<ctb[0];  //调用 const TextBlock::operator[]
	...
}
```

只要重载operator[]并对不同的版本给予不同的返回类型，就可以令const和non-const TextBlocks获得不同的处理：

```c++
std::cout<<tb[0]; //没问题——读一个non-const TextBlock
tb[0]='x';  //没问题——写一个non-const TextBlock
std::cout<<ctb[0]; //没问题——读一个const TextBlock
ctb[0]='x'; //错误！ ——写一个const TextBlock
```

***const意味什么：***

+ bitwise constness
+ logical constness

***bitwise constness：***

成员函数只有在不更改对象之任何成员变量（static除外）才可以说是const；

but 许多成员函数不十足具备const性质却能通过bitwise测试：

成员函数能更改“指针所指物”不能算是const，但是只有指针是成员函数的对象，那么称此为bitwise constness：

假设有 TextBlock-like class,它将数据存储为char* 而不是string，因为它需要和一个不认识string对象的C API沟通：

```c++
class CTextBlock{
public:
	...
	char& operator[](std::size_t position) const //bitwise const 声明
	{return pText[position];}					//but 其实不合适
private:
	char* pText;
}
```

因为该函数返回的是reference指向对象内部值（char*定义的）

但是operator[]实现代码并不更改pText。只产生目标码，所以是bitwise const

but ：

```c++
const CTextBlock cctb("Hello"); //声明一个常量对象
char* pc=&cctb[0];     //调用const operator[]取得一个指针指向cctb的数据
*pc="J"; 			//cctb现在有了“Jello”这样的内容
```

声明的对象还是被改变了；

***logical constness：***

一个const成员函数可以修改它所处理的对象的某些bits，but 只有在客户端侦测不出的情况下才得如此；

```c++
class CTextBlock{
public:
	...
	std::size_t length() const;
private:
	char* pText;
	std::size_t textLength; //最后一次计算的文本区块长度
	bool lengthIsValid;   //目前的长度是否有效
}；
std::size_t CTextBlock::length() const
{
	if(!lengthIsValid){
        textLength=std::strlen(pText); //error! 在const成员函数内
        lengthIsValid=true; 		//不能赋值给textLength
    }								//和lengthIsValid
    return textLength;
}
```

length的实现不是bitwise const，因为textLength和lengthIsValid都可能被修改；

如果坚持bitwise constness：利用C++的一个与const相关的摆动场：mutable（可变的）

mutable释放掉non-static成员变量的bitwise constness约束：

```c++
class CTextBlock{
public:
	...
	std::size_t length() const;
private:
	char* pText;
	mutable std::size_t textLength; //这些成员变量可能是
	mutable bool lengthIsValid;     //会被更改，即使在
};									//const成员函数内
std::size_t CTextBlock::length() const
{
	if(!lengthIsValid){
        textLength=std::strlen(pText);
        lengthIsValid=true; 		//现在可以这样
    }								//也可以这样
    return textLength;
}
```

***在const和non-const成员函数中避免重复***

mutable不能解决所有const相关难题，比如太多的operator[]表示和运用；

```c++
class TextBlock{
public:
	...
	const char& operator[] (std::size_t position) const
	{
		...	//边界检验
		... //志记数据访问
		... //检验数据完整性
        return text[position];
	}
	char& oeprator[](std::size_t position)
	{
		...	//边界检验
		... //志记数据访问
		... //检验数据完整性
		return text[position]
	}
private:
	std::string text;
};
```

问题：代码重复，编译时间、维护、代码膨胀等

该做：实现operator[]的技能一次并使用你它两次——令其中一个调用另一个——常量性转除（casting away countness）条款27

```c++
class TextBlock{
public:
	...
	const char& operator[] (std::size_t position) const //一如既往
	{
		...	//边界检验
		... //志记数据访问
		... //检验数据完整性
		return text[position];
	}
	char& operator[](std::size_t position) //现在只调用const op[]
	{
		return const_cast<char&>(		//将op[]返回值的const转除
			static_cast<const TextBlock&>(*this)  //为*this加上const
				[position]); 				//调用const op[]
	}
    ...
};
```

打算让non-operator[]调用其const兄弟，但non-const operator[]内部只是单纯调用oeprator[]，会递归调用自己；必须明确指出调用的是const operator[]，C++缺乏直接的语法直接这么做。因此这里将*this从其原始类型TextBlock&转型为const TextBlock&。

两个操作：

+ 为*this添加const——安全转型（non-const对象->const对象） 使用static_cast
+ 从const operator[]的返回值中移除const

***请记住：***

+ 将某些东西声明为const 可帮助编译器侦测出错误用法，const可被施加于任何作用域内的对象、函数参数、函数返回类型、成员函数本体；

+ 编译器强制实施bitwise constness，编写程序时应该使用”概念上的常量性“（conceptual constness）
+ 当const 和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本可避免代码重复