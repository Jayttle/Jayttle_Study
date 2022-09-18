## 条款23：宁以non-menber、non-friend替换member函数

​			***Prefer non-member non-friend functions to member functions.***

假设：有个class来表示网页浏览器，其可能提供众多函数：有一些用来清除下载元素高速缓存区、清楚访问过的URLs的历史记录、以及移除系统中的所有cookies：

```c++
class WebBrowser{
public:
	...
	void clearCache();
	void clearHistory();
	void removeCookies();
	...
};
```

客户想一整个执行所有动作：

```c++
class WebBrowser{
public:
	...
	void clearEverything();	//调用 clearCache() clearHistory() 和removeCookies()
	...
};
```

当然这一功能能被一个non-member函数：

```c++
void clearBrowser(WebBrowser& wb)
{
	wb.clearCache();
	wb.clearHistory();
	wb.removeCookies();
}
```

non-memer函数可允许对WebBrowser相关机能有较大的包裹弹性（packaging flexibility），会有较低的编译相依度，增加WebBrowser的可延伸性；

封装：越多的东西封装，越少人可以看到它，则我们越大的弹性去变化它；

我们可以令clearBrowser成为某工具类（utility class）的一个static member函数：

```c++
//比较自然的做法是让clearBrowser成为一个non-member函数且位于WebBrowser所在的同一个namespace内
namespace WebBrowserStuff{
    class WebBrowser{...};
    void clearBroswer(WebBrowser& wb);
    ...
}
```

namespace可以跨越多个源文件 ，可以让WebBrowser这样的class拥有大量便利函数：某些与书签有关，某些与打印有关......分离它们的编译相依关系，最直接做法是声明于一个头文件，将不相关的便利函数声明于另一个头文件：

```c++
//头文件“webbrowser.h”----针对class WebBrowser自身及核心机能
namespace WebBrowserStuff{
	class WebBrowser{...};
	...					//几乎所有客户都所需要的核心机能 non-member函数
}

// 头文件“webbrowserbookmarks.h”
namespace WebBrowserStuff{
	...					//与书签相关的便利函数
}

// 头文件“webbrowsercookies.h”
namespace WebBrowserStuff{
	...					//与cookie相关的便利函数
}
```

将所有便利函数放在多个头文件内但隶属于同一个命名空间，意味着客户能够轻松扩展这一组便利函数；

只需做的就是添加更多non-member non-friend函数到此命名空间内；

#### 请记住：

+ 宁可拿non-member non-friend函数替换member函数。这样可以增加封装性、包裹性和机能扩充性；