## 条款09：绝不在构造和析构过程中调用virtual函数

​			***Never call virtual functions during construction or destruction***

派生类在构造or析构时，首先构造or析构它的基类，且若调用virtual函数则该函数是使用基类的，而非派生类其本身的虚函数；

```c++
class Transaction{
public:
	Transaction();
	virtual void logTransaction() const=0;
	...
};
Transaction::Transction()
{
	...
    logTransaction();    //在base class的构造函数中使用了virtual函数
}

//derived class
class BuyTransaction:public Transaction{
public:
    virtual void logTransaction() const;
    ...
};
class SellTransaction:public Transaction{
public:
    virtual void logTransaction() const;
    ...
};
```

此时，执行：

```c++
BuyTransaction b;
```

因为derived class对象内的base class 成分会在derived class自身成分被构造之前先构造，此时Transaction的构造函数会被使用，自然调用了构造函数中的logTransaction，虽是虚函数，仍是Transaction的版本而非BuyTransaction的；



无法使用virtual函数从base class向下调用；在构造期间，你可以令derived class将必要的构造信息向上传递至baase class的构造函数；