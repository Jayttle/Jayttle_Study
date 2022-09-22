## 条款32：确定你的public继承塑模出is-a关系

​	***Make sure public ineritance models "is-a"***

最重要的一个规则：public inheritance（公开继承）意味着“is-a”的关系

每个学生都是人，并非每个人都是学生：

```c++
class Person{...};
class Student:public Person{...};
```

于是有：

```c++
void eat(const Person& p);	//任何人都会吃
void study(const Student& s);	//只有学生才到校学习
Person p;		//p是人
Student s;		//s是学生
eat(p);		//没问题
eat(s);		//没问题，学生也是（is-a）人
study(s);	//没问题
study(p);	//错误，p不是学生
```

除此之外还有两个常见的关系：has-a（有一个）和is-implemented-in-terms-of（根据某物实现出） （条款38，39）

**请记住：**

+ “public”继承以为is-a，适用于base classes身上的每一件事情一定也适用于derived classes身上，因为每一个derived class对象也都是一个base class对象