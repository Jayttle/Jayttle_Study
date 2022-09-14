## 条款14：在资源管理类中小心copying行为

​					***Think carefully about copying behavior in resource-managing classes***

tr1::shared_ptr的删除器（deleter）是一个函数or函数对象，当引用次数为0时调用：

```c++
class Lock{
public:
	explicit Lock(Mutex* pm)  //以某个Mutex初始化shared_ptr
		:mutexPtr(pm,unlock)	//并以unlock函数为删除器
	{
		lock(mutexPtr.get());
	}
private:
	std::tr1::shared_ptr<Mutex> mutexPtr;	//使用shared_ptr
};											//替换raw pointer
```

#### 请记住：

+ 复制RAII对象必须一并复制它所管理的资源。所以资源的copying行为决定RAII对象的copying行为；
+ 普遍而常见的RAII class copying行为是：抑制copying、施行引用计数法。不过其他行为也都可能被实现；