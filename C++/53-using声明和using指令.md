## using 声明 vs using 编译指令

使用 using 编译指令导入一个名称空间中所有的名称与使用多个 using 声明是不同的。

使用 using 声明时，就好像声明了相应的名称一样，如果某个名称已经在函数中声明了，则不能使用 using 声明导入相同的名称。而使用 using 编译指令时，将进行名称解析。如果函数中已经存在某个声明的名称，那么局部名称将会隐藏名称空间名，就像是隐藏同名的全局变量一样。

这样的文字说明看起来有些拗口，我们来看一个例子：

```C++
namespace Jill {
    double bukect(double n) {...}
    double fetch;
    struct Hill {...};
}

char fetch;

int main() {
    using namespace Jill;
    Hill thrill;
    double water = bucket(2);
    double fetch;
    cin >> fetch;	// main函数中的fetch
    cin >> ::fetch; // 全局fetch
    cin >> Jill:fetch; // Jill名称空间中的fetch
}

void test() {
    Hill top;	// 非法
}
```

在这段代码当中，名称`Jill::fetch`被放在局部名称空间中，因此它的作用域不是全局的，因此不会覆盖全局的`fetch`变量。然而局部声明的`fetch`将隐藏`Jill::fetch`和全局的`fetch`。

所以为了进行区分，需要我们在`fetch`之前加上作用于解析运算符。

还有一点需要注意，虽然我们在`main`函数中使用了 using 编译指令，它会将名称空间的名称视为是在函数之外声明的。但它不会使文件中的其他函数也能够使用这些名称，因此我们在`test`函数当中声明`Hill`是非法的。

一般来说，使用 using 声明比使用 using 编译指令更安全。因为 using 声明一次只会导入一个名称，如果和局部的名称发生冲突，那么编译器将会检查出冲突并提示。而 using 编译指令会导入名称空间中所有名称，包括可能并不需要的名称。如果发生了冲突，则会被局部名称覆盖，编译器也不会发出警告。

另外名称空间的名称可能分散在多个地方，也会增加烦恼，导致不知道添加了哪些名称。

C++ Primer 中的实例偏好将`using namespace std`语句放在`main`函数当中，也有许多人喜欢将它放在全局名称空间中：

```C++
#include<iostream>
using namespace std;
int main() {
    return 0;
}
```

这样做主要是为了方便，但最好不要这样做，还是使用名称空间解析运算符更加妥当：

```C++
int x;
std::cin >> x;
```
