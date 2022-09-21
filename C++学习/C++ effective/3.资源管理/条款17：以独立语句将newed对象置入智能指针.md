## 条款17：以独立语句将newed对象置入智能指针

​				***Store newed objects in smart pointers in standalone statements;***

假设：有个函数揭示处理程序的优先权，另一个函数用来在某动态分配所得的Widget上进行某些带有优先权的处理：

```c++
int priority();
void processWidget(std::tr1::shared_ptr<Widget>pw,int priority);
```

现考虑调用processWidget：

```c++
processWidget(new Widget,priority());
```

tr1::shared_ptr构造函数需要一个原始指针，但该构造函数是explicit构造函数，无法进行隐式转换；

为避免“资源被创建”和“资源被转换为资源管理对象”两个时间点之间可能的异常干扰；

```c++
std::str1::shared_ptr<Widget>pw(new Widget);	//在单独语句内用智能指针存储new所得对象；

processWidget(pw,priority());
```

#### 请记住：

+ 以独立语句将newed对象存储于智能指针内，如果不这样做，一旦异常被抛出，有可能导致难以察觉的资源泄露；