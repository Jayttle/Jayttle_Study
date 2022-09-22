## 条款30：透彻了解inlining的里里外外

​	***Understand the ins and outs of inlining***

inline：将“对此函数的每一个调用”都以函数本体替换之；

问题：会造成代码膨胀，降低指令高速缓存装置的击中率（instruction cache hit rate），以及伴随这些而来的效率损失；

virtual:等待，直到运行期才确定调用哪个函数

inline：执行前，先将调用动作替换为被调用函数的本体；

```c++
inline void f() {...}	//假设编译器有意愿inline“对f的调用”
void (*pf)()=f;		//pf指向f
...
f();		//这个调用将被inlined，因为它是一个正常调用
pf();		//这个调用或许不被inlined，因为它通过函数指针达成；
```

一开始先不要将任何函数声明为inline，或至少将inlinin施行范围局限；

慎重使用inline便是对日后使用调试器带来帮助，走向手工最优化之路；

**请记住：**

+ 将大多数inlining限制在小型、被频繁调用的函数身上。这可使日后的调试过程和二进制升级更容易，也可使潜在的代码膨胀问题最小化，使程序的速度提升机会最大化
+ 不要只因为function templates 出现在头文件，就将它们声明为inline；