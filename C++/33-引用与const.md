### 引用与 const

前文当中说过，我们可以让函数接收一个引用变量，从而免去变量拷贝的开销，达到提升程序运行效率的目的。

如果我们想要传递引用，但又不希望在函数内部对引用的变量进行修改，以免影响外部变量。我们可以使用常量引用，也就是加上`const`修饰符。

```C++
double sqrt(const double &x);
```

由于我们加上了`const`修饰符，当我们在函数内部对引用进行修改的时候，会触发编译器的报错。一般来说，如果传递的只是基本类型的变量，我们其实没有必要这么操作，直接值传递即可。这种做法一般用在传递一些大型结构体或者是大型容器的时候。

这里有一个小细节需要当心，由于我们传递的是引用，需要保证传递的参数是一个实参，而不是表达式。如这样的代码编译时会报错：

```C++
double distance(double &x, double &y) {
    return sqrt(x * x + y * y);
}

int main() {
	double x = 3.0, y = 4.0;
	cout << distance(x + 3.0, y + 4.0);
}
```

报错的原因在于，函数`distance`接收的是一个`double`类型的引用，而我们传递的却是`x+3`这样的表达式。显然表达式没有对应的引用。所以编译器会报错，告诉我们参数类型不匹配：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gweff16ligj311y0d6gnj.jpg)

但神奇的是，如果我们把函数签名稍微改一下，加上`const`修饰符，会发现报错消失了：

```C++
double distance(const double &x, const double &y) {
    return sqrt(x * x + y * y);
}
```

这并不是编译器的 bug，而是编译器针对`const`引用做了特殊处理。当编译器发现传入的不是`double`类型的变量的时候，它会创建一个临时的无名变量，将这个临时变量初始化成`x+3.0`，然后再传入这个临时变量的引用。C++只会对`const`引用参数执行这个操作。

除了表达式之外，如果变量的类型不匹配也一样会创建临时变量。这些临时变量只会在函数调用期间存在，函数运行结束之后，编译器会将其删除。

为什么会有这样的设计呢？C++ Primer 当中提供了这样一个例子：

```C++
void swapr(int &a, int &b) {
    int temp = b;
	b = a;
    a = temp;
}

long a = 3, b = 5;
swapr(a, b);
```

在早期 C++没有严格限制的情况下，这段代码会发生什么呢？

由于类型不匹配，所以编译器会创建两个临时的`int`变量，但它们初始化成 3 和 5，再传入函数当中。然后执行函数当中交换变量的逻辑，但问题是，我们交换的是两个临时变量，原变量并不会生效。

所以后来版本的 C++优化了这个问题，禁止了传递引用时创建临时变量。而当引用有`const`修饰时并不会对原值进行修改，并不会影响逻辑和结果，所以豁免了这个禁令。

### const 修饰符的优点

在函数签名当中，如果要接收引用，我们要尽可能使用`const`，我们来看下这样做的好处：

- 可以避免无意中修改数据
- 可以处理`const`和非`const`参数，否则，只能接受非`const`变量
- 可以接受临时变量
