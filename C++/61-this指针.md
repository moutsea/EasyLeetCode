## this 指针

还是`Stock`股票这个类，假设我们要实现一个方法，比较一下当前股票和传入的股票， 返回价格高的那个股票。在我们实现的时候，会遇到一点问题。

```C++
const Stock & Stock::topVal(const Stock &s) const {
    if (s.total_val > total_val) {
        return s;
    }else {
        return ????;
    }
}
```

这段代码当中有一些问题，我们一个一个来说。

首先说函数签名，前面都没有问题，问题出现在签名末尾的`const`。这是我们第一次在函数的末尾看到`const`，这个用法只能用在类或结构体的成员函数中，而不能用在普通函数里。

它表示这个函数不会修改任何成员变量的值，末尾加上`const`的函数称为常量成员函数。

常量函数有一些特殊的性质：

- 能被任何函数调用，只能调用常量函数
- 能被任何对象调用，但`const`对象只能调用`const`函数

性质不是非常复杂，但有些像是绕口令，需要从`const`常量的性质角度出发理解一下。

代码当中第二个问题就是一堆问号的地方，这里我们想要`return`当前对象，但是我们不知道如何表示。为了解决这个问题，C++当中创建了一个特殊的指针叫做`this`，它用来指向调用成员函数的对象，也就是当前对象。

所以有了`this`之后，这段代码可以写成：

```C++
const Stock & Stock::topVal(const Stock &s) const {
    if (s.total_val > total_val) {
        return s;
    }else {
        return *this
    }
}
```

另外，`this`指针也可以拿来访问当前对象当中的成员变量，比如我们想要访问当前的`total_val`，可以写成`this->total_val`，我们直接用`total_val`本质上是一样的，是编译器替我们做了优化。
