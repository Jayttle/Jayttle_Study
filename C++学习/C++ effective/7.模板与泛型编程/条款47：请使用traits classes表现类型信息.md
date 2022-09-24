## 条款47：请使用traits classes表现类型信息

​			***Use traits classes for information about types***

以random access迭代器实现advance：

```c++
template<typename IterT,typename DistT>
void advance(IterT& iter,DistT d)
{
	if(iter is a random access iterator)
	{
		iter+=d;		//针对random access迭代器使用迭代器算术运算
	}
	else
	{
		if(d>=0) {while(d--) ++iter;}	//针对其他迭代器分类
		else {while(d++) --iter;}	//反复调用++ --；
	}
}
```

类型的traits信息必须位于类型自身之外，标准技术是把它放进一个template及其一个或多个特化版本中；（其中针对迭代器被命名为iterator_traits:

```c++
template<typename IterT>
struct iterator_traits;	//用template来处理迭代器分类的相关信息；
```

习惯上traits总是被实现为structs，又称为traits classes；

针对deque迭代器而设计的：

```c++
template<...>
class deque{
public:
	class iterator{
	public:	
		typedef random_access_iterator_tag iterator_category;
		...
	};
	...
};
```

list的迭代器可双向进行，所以是：

```c++
template<...>
class list{
public:
	class iterator{
	public:
		typedef bidirectional_iterator_tag iterator_category;
		...
	};
};
```

