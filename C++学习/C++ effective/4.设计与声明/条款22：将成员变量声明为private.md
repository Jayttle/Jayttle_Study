## 条款22：将成员变量声明为private

​		***Declare data members private;***

能使成员变量的处理更精确：

```c++
class AccessLevels{
public:
	...
	int getReadOnly() const {return readOnly;}
	void setReadWrite(int value) {readWrite=value;}
	int getReadWrite() const {return readWrite;}
	void setWriteOnly(int value) {WriteOnly=value;}
private:
	int noAccess;	//对此int无任何访问动作
	int readOnly;	//对此int做只读访问
	int readWrite;	//对此int做读写访问
	int writeOnly;	//对此int做惟写访问
}；
```

同时public成员变量完全没有封装性；

#### 请记住：

+ 切记将成员变量声明为private。这可赋予客户访问数据的一致性、可细微划分访问控制、允诺约束条件获得保证，并提供class作者以充分的实现弹性；
+ protected并不比ublic更具封装性；