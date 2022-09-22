## 条款39：明智而审慎地使用private继承

​		***use private inheritance judiciously***

如果classes之间的继承关系是private，编译器不会自动将一个derived class对象转换为一个base class对象；并且由private base class继承而来的所有成员，在derived class都会变成private属性；

​	***Private继承意味implemented-in-terms-of（根据某物实现出）***

条款34：private继承意味只有实现部分被继承，接口部分被略去；

举例：让Widgets有记录成员函数调用的次数；

现成的计时器：

```c++
class Timer{
public:
	explicit Timer(int tickFrequency);
	virtual void onTick() const ;	//定时器每滴答一次，此函数就被自动调用一次
	...
};
```

Widget要用Timer的virtual函数并重定义；所以得Widget继承Timer，且必须是private形式继承

```c++
class Widget:private Timer{
private:
	virtual void onTick() const;	//查看Widget的数据等等
	...
};
```

用复合也可以替代：

```
class Widget{
private:
	class WidgetTimer:public Timer{
	public:
		virtual void onTick()const;
		...
	};
	WidgetTimer timer;
	...
};
```

<img src="https://cdn.staticaly.com/gh/Jayttle/typora_img@main/img/202209212228008.png" alt="image-20220921222828947" style="zoom:67%;" />

好处：

设计Widget拥有derived classes，但同时又阻止derived classes重新定义onTick；

#### 请记住：

+ Private继承意味is-implemented-in-terms-of（根据某物实现出）。它通常比复合（composition）的级别低，但是当derived class需要访问protected base class的成员，或需要重新定义继承而来的virtual函数时，这么设计是合理的；
+ 和复合不同，private继承可以造成empty base最优化，这对致力于“对象尺寸最小化”的程序库开发者而言，可能很重要；