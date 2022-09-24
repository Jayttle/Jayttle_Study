## 条款48：认识template元编程

​			***Be aware of template metaprogramming***

​	Template metaprogramming(TMP,模板元编程)是编写template-based C++程序并执行于编译器的过程；

导入的STL advance伪码：

```c++
template<typename IterT,typename DisT>
void advance(IterT& iter,DistT d)
{
	if(iter is a random access iterator)
	{	iter+=d;	}	//针对random access迭代器使用迭代器算术运算
	else 
	{
		if(d>=0)	{while(d--)	++iter;}	//针对其他迭代器类型
		else {while(d++)	--iter;}		//反复调用++or--
	}
}
```

利用typeid让其中的伪码城镇：

```c++
template<typname IterT,typename DistT>
void advance(iterT& iter,DistT d)
{
	if(typeid(typename std::iterator_traits<IterT>::iterator_category)
		==typeid(std::random_access_iterator_tag))
	{	iter+=d;	}	//针对random access迭代器，使用迭代器算术运算
	else
	{
		if(d>=0)	{while(d--)	++iter;	}
		else {while(d++)	--iter;}
	}
}
```

...(还不熟悉STL和template 有些看不懂)