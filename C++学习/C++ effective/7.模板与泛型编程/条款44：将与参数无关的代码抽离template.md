## 条款44：将与参数无关的代码抽离template

​		***Factor parameter-independent code out of template***

把公共部分分离搬到新class去，然后使用继承or复合（条款32，38，39）

令原先的classes取用共同特性，原classes的互异部分仍保留在原位置不动；

#### 请记住：

+ Templates生成多个classes和多个函数，所以任何templated代码都不该与某个造成膨胀的template参数产生相依关系
+ 因非类型模板参数而造成的代码膨胀，往往可以消除，做法是以函数参数or class成员变量替换template参数；
+ 因类型参数而造成的代码膨胀，往往可降低，做法是让带有完全相同二进制表述的具体类型共享实现码；