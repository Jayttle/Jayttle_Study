## 条款49：了解new-handler的行为

​		***Understand the behavior of the new-handler***

new-handler——operator new抛出异常并反映一个未获满足的内存需求之前所调用的客户指定的错误处理函数；

```c++
//以下是当operator new无法分配足够内存时，该被调用的函数：
void outOfMen()
{
	std::cerr<<"Unable to satisfy request for memory\n";
	std::abort();
}
int main()
{
	std::set_new_handler(outOfMem);
	int* pBigDataArray=new int[10000000L];
	...
}
```

当operator new无法为1000000个整数分配足够空间，outOfMem会被调用；并程序发出一个信息之后夭折（abort）

当oeprator new无法满足内存申请时，会不断调用new-handler函数；

所以设计良好的new-handler函数：

+ 让更多内存被使用
+ 安装另一个new-handler
+ 卸载new-handler
+ 抛出bad_alloc（or派生自bad_alloc）的异常
+ 不返回