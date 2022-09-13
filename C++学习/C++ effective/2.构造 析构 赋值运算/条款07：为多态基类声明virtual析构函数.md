## 条款07：为多态基类声明virtual析构函数

​					***Declare destructors virtual in polymorphic base classes***

问题：base class指针被删除时，若带着一个non-virtual析构函数，实际对象的derived成分很可能不被滞销；造成一个局部滞销；

解决：为base class一个virtual析构函数；

同时：virtual函数允许derived class 的实现得以客制化；

但是无端地将所有classes地析构函数声明为virtual，会极大增加内存占用，

多态：多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数



#### 请记住：

+ polymorphic（带有多态性质的）base classes应该声明一个virtual析构函数。
+ 如果class带有任何virtual函数，就该有一个virtual析构函数
+ Classes目的若不是作为base class or不具备polymorphic，就不该声明virtual析构函数；