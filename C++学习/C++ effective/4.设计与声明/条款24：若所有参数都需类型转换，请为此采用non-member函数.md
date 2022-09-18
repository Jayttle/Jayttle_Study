## 条款24：若所有参数都需类型转换，请为此采用non-member函数

​		***Declare non-member functions when type conversions should apply to all parameters;***

假设：允许整数“隐式转换”为有理数：

```c++
class Rational{
public:
	Rational(int numerator=0,int denominator=1); //构造函数刻意不为explicit
	int numerator() const;						//允许int-to-Rational隐式转换
	int denominator() const;
private:
	...
};
```

先研究乘法，并写成Rational成员函数：

```c++
class Rational{
public:
	...
	const Rational operator* (const Rational& rhs) const;
    //参考条款3，20和21 理解为什么返回一个const by-value结果但接受一个reference-to-const实参；
};
```

然而在混合式算术中，出了问题：

```c++
Rational oneHalf(1,2);
result=oneHalf*2;			//true
result=2*oneHalf;			//error
```

因为operator\*是Rational这样的class的函数，而整数2作为int型没有相应的，会尝试调用non-member operator\*；

让operator*成为一个non-member函数，便允许编译器在每一个实参身上执行隐式类转换；

```c++
class Rational{
...
};

const Rational operator*(const Rational& lhs,const Rational& rhs)
{
	return Rational(lhs.numerator()*rhs.numerator(),
                    lhs.denominator()*rhs.denomminator());
}
Rational oneFourth(1,4);
Rational result;
result=oneFourth*2;		//true
result=2*oneFourth;		//true
```

#### 请记住：

+ 如果你需要为某个函数的所有参数（包括被this指针所指的那个隐喻参数）进行类型转换，那么这个函数必须是个non-member函数；