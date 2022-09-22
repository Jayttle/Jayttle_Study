## 条款38：通过复合塑膜出has-a或“根据某物实现出”

​			***Model "has-a" or "is-implemented-in-terms-of" through composition***

复合（composition）是类型之间的一种关系——对象内包含其他种类型的对象；

```c++
class Address{...};
class PhoneNumber{...};
class Person{
public:
	...
private:
	std::string name;			//合成成分物（composed object)
	Address address;
	PhoneNumber voiceNumber;
	PhoneNumber faxNumber;		
}
```

复合意味——***has-a***（有一个） or is-implemented-in-terms-of（根据某物实现出）

有两种域（domains）：应用域（application domain）、实现域（implementation domain）

...

例子：Set对象可根据一个list对象实现出来：

```c++
template<class T>	//将list应用于Set
class Set{
public:
	bool member(const T& item)const;
	void insert(const T& item);
	void remove(const T& item);
	std::size_t size() const;
private:
	std::list<T> rep;	//用来表示set的数据
}；
```

Set成员函数可大量倚栏list及标准程序库其他部分机能完成：（只要你熟悉STL编写程序）

```c++
template<typename T>
bool Set<T>::member(const T& item) const
{
	return std::find(rep.begin(),rep.end(),item)!=rep.end();
}
template<typename T>
void Set<T>::remove(const T&item)
{
	typename std::list<T>::iterator it=std::find(rep.begin(),rep.end(),item)
	if(it!=rep.end()) rep.erase(it);
}
template<typename T>
void Set<T>insert(const T& item)
{
	if(!member(item)) rep.push_back(item);
}
template<typename T>
std::size_t Set<T>::size() const
{
	return rep.size();
}
```

#### 请记住：

+ 复合（composition） 的意义和public 继承完全不同；
+ 在应用域（application domain），复合意味has-a。在实现域（implementation domain），复合意味is-implemented-in-terms-of（根据某实物实现）