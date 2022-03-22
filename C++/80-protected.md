## 访问控制protected



我们之前介绍了public和private关键字，但除了这两个之外，还存在另外一个控制权限的关键字，叫做protected。



关键字protected与private相似，在类外只能用公有类成员来访问protected成员。只有在派生的时候，private和protected关键字才能体现出差异来，派生类的成员可以直接访问基类的保护成员，但不能直接访问基类的私有成员。因此对于类外部来说，protected和private类似，而对于派生类来说，protected与public类似。



比如类`Human`类将`name`成员设置为protected：



```C++
class Human {
  	protected:
    	string name;
    ...
};
```



在这种情况下它的派生类`Hero`可以直接访问`name`，而不需要使用`Human`中的方法：



```C++
class Hero: public Human {
	void show() {
        cout << name << endl;
    }  
};
```



使用protected关键字在一些情况下可以简化代码的编写工作，但也会存在一些设计缺陷。比如有的时候，有些变量值我们也不希望派生类能够直接修改。



```C++
void Hero::modify(const string& n) {
    name = n;
}
```



比如原本`name`名字是初始化之后不允许修改的，但由于它被定义成了protected，所以在派生类当中可以随意修改，这显然违背了我们的设计初衷。



针对这种情况，比较好的做法是将所有的数据成员都设置成private。但是可以将一些特殊的方法设置成protected，这样既可以允许派生类调用这些protected的方法得到便利，又不会过度开放权限导致敏感数据被篡改。



