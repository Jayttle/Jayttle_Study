## 条款02：尽量以const，enum，inline替换#define

​						***Prefer consts,enums,and inlines to #define***

可理解为“宁可以编辑器替换预处理器”，因#define不被视为语言的一部分；

```
#define ASPECT_RATIO 1.653
```

记号名称ASPECT_RATIO也许从未被编译器看到，于是ASPECT_RATIO有可能没有进入记号表（symbol table）内，于是追踪它而浪费时间；

解决之道：以一个常量替换为上述的宏（#define）

```c++
const double AspectRatio=1.653;
```

当我们以常量替换#define，有两种***特殊情况***：
**1st：**定义常量指针（constant pointers）

由于常量定义式通常被放在头文件内（以便被不同的源码含入），因此由必要将指针的声明为const；

例如：若要在头文件内定义一个常量的char* -based字符串，你必须写const两次：

```C++
const char* const authorName="Scott Meyers";
```

关于const的意义和使用，条款3有完整的讨论；

string对象通常比其前辈char* -based合宜，所以上述的定义往往这样会更好：

```c++
const std::string authorName("Scott Meyers");
```

**2nd：**class专属常量。为了将常量的作用域（scope）限制于class内，必须让它成为class的一个成员（number）；而为确保此常量至多只有一份实体，你必须让它成为一个static成员：

```c++
class  GamePlayer{
private:
	static const int NumTurns=5; //常量声明式
	int scores[NumTurns]; //使用该常量
	...
}
```

然而NumTurns的声明式而非定义式。通常C++要求你对你所使用的任何东西提供一个定义式，but if it是个class专属常量又是static且为整数类型，则需要特殊处理：

——只要不取它们的地址，你可以声明并使用它们而无须提供定义式；

——but if取某个class专属常量的地址，or编译器却（不正确地）坚持要看到一个定义式，你就必须另外提供定义式如下：

```c++
const int GamePlayer::NumTurns; //NumTurns 的定义式
								//下面告诉你为什么没有给予数值
```

由于class常量已经在声明获得初值（先前声明NumTurns时为它设为初值5），因此定义时不可以再设初值；

旧编译器不支持上述语法，不允许static成员在其声明式上获得初值。

“in-class 初值设定”——也只允许对整数常量进行；

```c++
class CostEstimate{
private:
	static const double FudgeFactor; //static class 常量声明
	...								//位于头文件内
}
const double CostEstimate::FudgeFactor=1.35; //static class 常量定义
											//位于实现文件内
```

唯一例外是class内需要一个class常量值，这时候编译器（错误地）不允许“static整数型class常量“完成”in class初值设定”，可改用“the enum hack”补偿方法；于是可定义如下：

```c++
class GamePlayer{
private:
	enum{NumTurns=5}; //"the enum hack"——令NumTurns
						//成为5的一个记号名称
	int scores[NumTurns]; 
}
```

enum back值更像#define而非const，所以取一个enum的地址是不合法的，而const取地址是合法的；

可以不让别人获得一个指针指向enum值；不会导致非必要的内存分配；

事实上enum back是template metaprogramming（模板元编程）的基础技术；

宏不会招致函数调用带来的额外开销：

```c++
#define CALL_WITH_MAX(a,b) f((a)>(b)?(a):(b))
```

 ***无论何时宏中的所有参数要加上小括号***

```c++
int  a=5,b=0;
CALL_WITH_MAX(++a,b); //a被累加二次
CALL_WITH_MAX(++a,b+10); //a被累加一次
```

在这里，调用f之前，a的递加次数 取决于与b的比大小；

为提高宏带来的效率以及一般函数的所有可预料行为和类型安全性（type safety）——写出template inline函数（条款30）

```c++
template<typename T> 
inline void callWithMax(const T& a,const T& b)
{							//pass by reference-to-const
	f(a>b?a:b) 				//条款20
}
```

template产生一整群函数，每个函数都接受两个同型对象，并以其中较大者调用f；

**请记住**：
——对于单纯常量，最好以const对象或enums替换#defines；

——对于形似函数的宏（macros），最好改用inline函数替换#defines；