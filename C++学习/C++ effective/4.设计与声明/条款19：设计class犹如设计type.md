## 条款19：设计class犹如设计type

​			***Treat class design as type design***

how to 设计高效的class：

+ 新type的对象应该如何被创建和销毁？
+ 对象的初始化和对象的赋值应该有什么样的差别？
+ 新 type的对象如果被passed by value（以值传递），意味着什么？
+ 什么是新type的“合法值”
+ 你的新type需要配合某个继承图系么？
+ 你的新type需要什么样的转换？
+ 什么样的操作符和函数对此新type而言是合理的？
+ 什么样的标准函数应该驳回？
+ 谁该取用新type的成员
+ 什么是新type的“未声明接口”
+ 你的新type有多么一般化
+ 你真的需要一个新type吗？