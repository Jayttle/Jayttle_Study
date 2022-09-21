## 条款21：必须返回对象时，别妄想返回其reference



​			***Don't try to return a reference when you must return an object***

```c++
const Rational& operator*(const Rational& lhs,const Rational& rhs)
{
	Rational result(lhs.n*rhs.n,lhs.d*rhs.d);			//糟糕的代码
	return result;
}
const Rational& operator*(const Rational& lhs,const Rational& rhs)
{
	Rational* result=new Rational(lhs.n*rhs.n,lhs.d*rhs.d);			//更糟糕的代码
	return* result;										//在两次连乘的情况下难以确定delete
}
const Rational& operator*(const Rational& lhs,const Rational& rhs)
{
    static Rational result;
	result=...;							//又一个糟糕的代码
	return result;
}
```

```c++
//正确的使用
inline Rational& operator*(const Rational& lhs,const Rational& rhs)
{
	return Rational(lhs.n*rhs.n,lhs.d*rhs.d);
}
```



#### 请记住：

+ 绝不要返回pointer or reference指向一个local stack对象，or 返回reference 指向一个heap-allocated对象，or 返回pointer or reference指向一个local static对象而有可能同时需要多个这样的对象。