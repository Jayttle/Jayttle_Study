## 条款16：成对使用new和delete时要采用相同形式

​				***Use the same form in corresponding uses of new and delete***

```c++
std::string* stringPtr1=new std::string;
std::string* stringPtr2=new std::string[100];
...
delete stringPtr1;				//删除一个对象
delete []stringPtr2;			//删除一个由对象组成的数组
```

尽量不对数组形做typedefs动作：

```c++
typedef std::string AddressLines[4];
```

C++标准程序库含有string、vector等templates可将数组的需求降至几乎为0；

#### 请记住：

+ 如果在new表达式使用 [ ],必须在相应的delete表达式也使用 [ ]；
+ 如果new没使用 [ ]，则一定不要在相应的delete使用 [ ]；