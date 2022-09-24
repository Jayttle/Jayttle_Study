## 条款42：了解typename的双重意义

​		***Understand the  two meanings of typename***

从属名称（dependent names）：template内出现的名称相依于某个template 参数；

嵌套从属名称（nested dependent name）：从属名称在class内呈嵌套状；

C::const_iterator 就是 nested dependent name；

因为单独的：

```c++
C::const_iterator iter(container.begin());
```

无法识别C::const_iterator是个类型

此时需要添加前置关键字typename：

```c++
typename C::const_iterator iter(container.begin());
```

所以：任何时候想要在template种指涉一个嵌套从属类型名称，就必须要在紧临它前一个位置放上关键字typename；

真实的一个例子：假设撰写一个function template接受一个迭代器，并为迭代器指涉的对象做一份local副本temp：

```c++
template<typename IterT>
void workWithIterator(IterT iter)
{
	typename std::iterator_traits<IterT>::value_type temp(*iter);
	...
}
```

value_type被嵌套于iterator_traits<IterT>之内，而IterT是个template，所以必须要在它之前放置typename；

若认为std::iterator_traits<IterT>::value_type很长则可以建立一个typedef。普遍的习惯是设定typedef名称以代表某个traits成员名称：

```c++
typedef typename std::iterator_traits<IterT>::value_type value_type;
value_type temp(*iter);
```

#### 请记住：

+ 声明template参数时，前缀关键字class和typename可互换
+ 请使用关键字typename标识嵌套从属类型名称；但不得在base class lists（基类列）or member initialization list（成员初值列）内以它作为base class修饰符；