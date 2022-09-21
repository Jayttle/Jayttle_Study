## 条款28：避免返回handles指向对象内部成分

​			***Avoid returning "handles" to object internals***

假设：你的程序涉及矩形，每个矩形由左上角和右下角表示，决定不把定义矩形的这些点存放在Rectangle对象内，而是放在一个辅助的struct内再让Rectangle去指向它：

```c++
class Point{
public:
	Point(int x,int y);
	...
	void setX(int newVal);
	void setY(int newVal);
};
```

```c++
struct RectData{ //表示矩形
	Point ulhc;	//upper left-hand corner
	Point lrhc;	//lower right-hand corner
};
class Rectangle{
    ...
private:
    std::trl::shared_ptr<RectData>pData;
};
```

为矩阵提供能够计算Rectangle的范围，所以class提供upperLeft函数和lowerRight函数；Point是个用户自定义类型，所以根据条款20 这些函数于是返回references，代表底层的point对象：

```c++
class Rectangle{
public:
	...
	Point& upperLeft() const{return pData->ulhc;}
	Point& lowerRight() const {return pData->lrhc;}
	...
};
```

可通过编译但是自相矛盾；const将函数声明为const成员函数，可是返回的reference指向了private内部数据，可通过reference修改内部数据；只需将它们的返回值也声明为const：

```c++
class Rectangle{
public:
	...
	const Point& upperLeft() const{return pData->ulhc;}
	const Point& lowerRight() const {return pData->lrhc;}
	...
};
```

这样有可能导致dangling handles（空悬的号码牌）；这种handles所指东西不复存在。可能指针指向的是空；

尽量避免；

#### 请记住：

+ 避免返回handles（包括references、指针、迭代器）指向对象内部。遵守这个条款可增加封装性，帮助const成员函数的行为像个const，并将发生“虚吊号码牌”（dangling handles）的可能性降至最低；