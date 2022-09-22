## 条款36：绝不重新定义继承而来的non-virtual函数

​	***Never redefine an inherited non-virtual function.***

假设有：

```c++
class B{
public:
	void mf();
	...
};
class D:public B{
public:
	void mf()	//遮掩（hide）了B::mf；条款33
	...
};
D x;
B* pB=&x;	//获得一个指针指向x
D* pD=&x;	//获得一个指针指向x
pB->mf();	//调用B::mf
pD->mf();	//调用D::mf
```

non-virtual函数如B::mf和D::mf都是静态绑定（statically bound,条款37）

virtual函数是动态绑定（dynamically bound,条款37）

静态绑定：pB调用的non-virtual函数永远是B所定义的版本；即使是B的派生class

动态绑定：如果mf是virtual函数，不论pB或pD调用，都会是D::mf，因为真正指向的是类型为D的对象；

意思是：public继承是（is-a）

+ 如果必须以public继承，则non-virtual的mf不该被重写，如果需要重写就不该以public形式继承；
+ 如果必须public，则应该将mf改为virtual函数；

#### 请记住：

- 绝对不要重新定义继承而来的non-virtual函数；