## break 和 continue

`break`和`continue`都是循环体当中经常使用的语句，并且也不只是 C++在其他语言当中同样存在。`break`和`continue`的存在，大大丰富了循环体的功能，这两者都是用来跳过部分代码，但是执行的细节有所不同，使用场景也有所区别。

### break

`break`的含义是结束循环，当程序执行到`break`之后会直接跳出循环体，执行循环体之后的部分。可以被使用在任何循环当中（`for`循环，`while`循环和`do while`循环）。

比如：

```C++
int a[5] = {1, 6, 3, 10, 8};

for (int i = 0; i < 5; ++i) {
    if (a[i] >= 10) {
        break;
    }
    cout << a[i] << " ";
}
```

屏幕上输出的结果会是`1 6 3`，因为当遇到`a[i] = 10`的情况之后执行了`break`，直接跳出了`for`循环。循环体当中`break`语句之后的部分也不会执行。

另外，`break`只能跳出一重循环，如果我们使用了多重循环的嵌套，执行了`break`只能跳出当前循环，而不会跳出整个循环体。

```C++
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
        if (condition) {
            break;
        }
    }
    // todo
}
```

比如在上面的示例当中，我们在`j`循环当中执行了`break`，会立即跳出`j`循环，但是外层的`i`循环依旧会继续执行。

那如果我们想要跳出多重循环应该怎么办呢？

大概有两种办法，一种是加入退出标记，手动多次执行`break`。

```C++
bool flag = false;
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
        if (condition) {
            flag = true;
            break;
        }
    }
    if (flag) break;
}
```

我们创建了一个`bool`型的变量，用来表示是否要跳出全部循环。之后我们在外层的循环当中加上对这个变量的判断，如果`flag`为`true`再执行一次手动退出。

当然我们也可以把它和循环体当中的判断条件合并，写成这样：

```C++
bool flag = false;
for (int i = 0; i < n && !flag; ++i) {
    for (int j = 0; j < m; ++j) {
        if (condition) {
            flag = true;
            break;
        }
    }
}
```

除此之外还有其他几种变体，都大同小异就不一一列举了。

还有一种方法是使用`goto`语句：

```C++
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
        if (condition) {
            goto outloop;
        }
    }
}

outloop: cout << "end" << endl;
```

但众所周知`goto`语句非常危险，可能会导致很多意想不到的问题，因此非万不得已，不要使用`goto`语句。这里仅供参考。

### continue

`continue`语句执行也会跳过语句之后的代码，但并不会退出循环，而是进入下一次循环当中。

```C++
int a[5] = {1, 6, 3, 10, 8};

for (int i = 0; i < 5; ++i) {
    if (a[i] >= 10) {
        continue;
    }
    cout << a[i] << " ";
}
```

上面这段代码的执行结果是`1 6 3 8`，中间的`a[i] == 10`的情况执行了`continue`跳过了循环体之后的所有逻辑，进入了下一重循环当中。

由于我们的代码逻辑非常简单，同样的逻辑其实也可以不用使用`continue`语句：

```C++
int a[5] = {1, 6, 3, 10, 8};

for (int i = 0; i < 5; ++i) {
    if (a[i] < 10) {
        cout << a[i] << " ";
    }
}
```

但如果循环体当中的代码非常复杂，相比于使用一个巨大的`if`语句，使用`continue`可以提高代码的可读性。这个需要我们根据实际情况具体问题具体分析，并没有标准答案。
