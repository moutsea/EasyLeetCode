## using 声明

C++当中提供了两种机制（using 声明和 using 编译指令）来简化对名称空间中名称的使用。using 声明使特定的标识符 keys，using 编译指令使整个名称空间可用。

using 声明由关键字`using`和被限定的名称组成：

```C++
using A::fetch;
```

using 声明将特定的名称添加到它所属的声明区域中，完成声明之后，我们可以使用`fetch`代替`A::fetch`了。

我们来看一个具体的例子：

```C++
namespace A {
    double bucket(double n) {..}
    double fetch;
    struct Hill {...};
}

int main () {
    using A::fetch;
    cin >> fetch;
    fetch += 1.0;
    A::Hill hill;
}
```

如果我们在函数外使用 using 声明，会将名称添加到全局名称空间中：

```C++
using A::fetch;

void test() {
    cout << fetch << endl;
}

int main () {
    cin >> fetch;
    fetch += 1.0;
    A::Hill hill;
}
```

## using 编译指令

using 声明只能使一个名称可用，而 using 编译指令可以使得所有的名称都可用。using 编译指令由名称空间和它前面的`using namespace`组成，它使得名称空间中的所有名称都可用。

如：

```C++
using namespace A;
```

在全局声明区域使用 using 编译指令，将使得该名称空间的名称全局可用。这种情况其实我们已经非常熟悉了，因为我们一直在用`using namespace std`。

我们也可以在函数当中使用 using 编译指令：

```C++
int main () {
    using namespace A;
}
```
