## 条款11：在operator=中处理”自我赋值”

​				***Handle assignment to self in operator=***

结构体：

```c++
class Bitmap{...};
class Widget{
...
private:
	Bitmap* pb; //指针，指向一个从heap分配而得的对象；
}
```

一份不安全的自我赋值：

```c++
Widget& Widget::operator=(const Widget& rhs) 
{
	delete pb;		//有可能*this与rhs是同一个对象，则会删除rhs的bitmap；
	pb=new Bitmap(*rhs.pb);
	return* this;
}
```

可改成：

```c++
Widget& Widget::operator=(const Widget& rhs) 
{
    if(this==&rhs) return* this; //证同测试（identity test）；
    
	delete pb;		
	pb=new Bitmap(*rhs.pb);
	return* this;
}
```

亦可改成：

```c++
Widget& Widget::operator=(const Widget& rhs) 
{
	Bitmap* pOrig=pb;
	pb=new Bitmap(*rhs.pb);
	delete pOrig;		
	return* this;
}
```

#### 请记住：

+ 确保当对象自我赋值operator=有良好行为。其中技术包括比较“来源对象”和“目标对象”的地址、精心周到的语句顺序、以及copy-and-swap。
+ **确保任何函数如果操作一个以上的对象，而其中多个对象是同一个对象时，其行为仍然正确；**