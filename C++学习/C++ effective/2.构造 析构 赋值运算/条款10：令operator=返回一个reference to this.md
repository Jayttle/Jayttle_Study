## 条款10：令operator=返回一个reference to *this

​			***Have assignment operators return a reference to \* this***

就是遵循return* this;也适用于operator+=,-=,*=

```c++
class Widget{
public:
	Widget& operator=(const Widget& rhs)
	{
		...
		return* this;
	}
}
```

