## 条款35：考虑virtual函数以外的其他选择

​				***Consider alternatives to virtual functions***

假设：动作游戏任务建康程度，因不同人物计算方式不同，就设置为virtual

```c++
class GameCharacter{
public:
	virtual int healthValue() const;	//返回人物的健康指数；
	...									//derived classes可重新定义它
};
```

### 借由Non-Virtual Interface手法实现Template Method模式

这个流派主张virtual函数几乎总是private；较好的设计是保留healthValue为public成员函数，但让它成为non-virtual，并调用一个private virtual函数（doHealthValue）进行实际工作：

```c++
class GameCharacter{
public:
	int healthValue() const
	{									//derived classes不重新定义它  见条款36
		...								//事前工作
		int retVal=doHealthValue();		//做真正的工作
		...								//事后工作
		return retVal;
	}
	...
private:
	virtual int doHealthValue() const	//derived classes可重新定义它
	{
		...								//缺省算法，计算健康指数
	}
}
```

令客户通过public non-virtual成员你函数间接调用private virtual函数。称为***non-virtual interface（NVI）***手法

又称为virtual 函数的外覆器；

优点在于：有事前工作和事后工作；

事前工作——可以包括互斥器、制造运转日志记录项、验证class约束条件、验证函数先决条件；

事后工作——可以包括互斥器解除锁定、验证函数的事后条件、再次验证class约束条件；

在NVI手法下其实没必要让virtual函数一定是private；

### 借由Function Pointers实现Strategy模式

主张“人物健康指数的计算与人物类型无关”；例如：要求每个人物的构造函数接受一个指针，指向一个健康计算函数，而我们可以调用该函数进行实际计算：

```c++
class GameCharacter; 	//前置声明（forward declaration)
//以下函数是计算健康指数的缺省算法
int defaultHealthCalc(const GameCharacter& gc);
class GameCharacter{
public:
	typedef int(*HealthCalcFunc)(const GameCharacter&);
	explicit GameCharacter(HealthCalcFunc hcf=defaultHealthCalc)
		:healthFunc(hcf){}
	int healthValue() const
	{	return healthFunc(*this);}
	...
private:
	HealthCalcFunc healthFunc;
};
```

提供了某些有趣弹性：

+ 统一任务类型的不同实体可以有不同的健康计算函数：

```c++
class EvilBadGuy:public GameCharacter{
public:
	explicit EvilBadGuy(HealthCalcFunc hcf=dafaultHealthCalc)
		:GameCharacter(hcf)
	{...}
	...
};
int loseHealthQuickly(const GameCharacter&);	//健康指数计算函数1
int loseHealthSlowly(const Gamecharacter&);		//健康指数计算函数2

EvilBadGuy ebg1(loseHealthQuickly);				//相同类型的人物搭配
EvilBadGuy ebg2(losseHealthSlowly);				//不同的健康计算方式
```

+ 某已知人物的健康计算函数可在运行期变更。例如GameCharacter提供的一个成员函数SetHealthCalculator，用来替换当前健康指数计算函数；

### 借由tr1::function完成Strategy模式

不用函数指针，而是用trl::function的对象，（条款54）这样的对象可持有（保存）任何可调用物（callable entity）只要签名式兼容于需求端；

```c++
trl::function:
	
	class GameCharacter;
	int defaultHealthCalc(const GameCharacter& gc);
	class GameCharacter{
	public:
		//HealthcalcFunc可以是任何“可调用物”（callable entity），可被调用并接受
		//任何兼容于GameCharacter之物，返回任何兼容于int的东西：
		typedef std::trl::function<int(const GameCharacter&)>HealthCalcFunc;
		explicit GameCharacter(HealthCalcFunc hcf=defaultHealthCalc)
			:healthFunc(hcf) {}
		int healthValue() const
		{	return healthFunc(*this);}
		...
	private:
		HealthCalcFunc healthFunc;
	}
```

HealthCalcFunc是个typedef，用来表现trl::function的某个具现体；

```c++
short calcHealth(const GameCharaceter&);	//健康计算函数；
											//注意其发返回类型为non-int
struct HealthCalculator{					//为计算健康而设计的函数对象
	int operator()(const GameCharacter&) const
	{...}
};
class GameLevel{
public:
	float haelth(const GameCharacter&) const;	//成员函数，用以计算健康；注意其			
	...											//non-int返回类型；	
};
class EvilBadGuy:public GameCharacter{		//同前
...
};
class EyeCandyCharacter:public GameCharacter{	//另一个人物类型：
	...								//假设其构造函数与EvilBadGuy同
};
EvilBadGuy ebg1(calcHealth);	//人物1，使用某个函数计算健康指数
EyeCandyCharacter ecc1(healthCalculator());	//人物2，使用某个函数对象计算健康指数
GameLevel currentLevel;	
...
EvilBadGuy ebg2(std::trl::bind(&GameLevel::health,currentLevel,_1));
								//人物3，使用某个成员函数计算健康指数
```

...

本条款的根数：当你为解决问题而寻找某个设计方法时，不妨考虑virtual函数的替代方案：

+ 使用non-virtual interface（NVI）手法，那是***Template Method***设计模式的一种特殊形式。它以public non-virtual成员函数包裹较低访问行（private or protected）的virtual函数
+ 将virtual 函数替换为”函数指针成员变量“，这是***Strategy***设计模式的一种分解表现形式；
+ 以trl::function成员变量替换virtual 函数，因而允许使用任何可调用物（callable entity）搭配一个兼容于需求的签名式。这也是***Strategy***设计模式的某种形式；
+ 将继承体系内的virtual函数替换为另一个继承体系内的virtual函数。这是***Strategy***设计模式的传统实现方法；

#### 请记住：

+ virtual函数的替代方案包括NVI手法及***Strategy***设计模式的多种形式，NVI手法是***Template Method***设计模式的一种特殊形式。
+ 将机能从成员函数移到class外部函数，带来的一个缺点是，非成员函数无法访问class的non-public成员；
+ trl::function对象的行为就像一般指针。这样的对象可接纳”与给定的目标签名式兼容“的所有可调物（callable entities）；
