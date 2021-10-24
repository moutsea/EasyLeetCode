## string 类

### 和 char \*的异同

在 C++当中，除了`char *`类型，还有专门的字符串类型，就叫做`string`。通过包含头文件`string`就可以使用：

```C++
include<string>
```

在很多方面，`string`类型的使用方法和`char *`一样，例如：

```C++
string str1;
string str2 = "hello world"; // 初始化和char *一致

cout << str2 << endl; // cout和char *一致
cout << str[2] << endl; // 元素访问和char *一致
```

除此之外，它还有一个更大的好处，就是当我们从外界读入字符串的时候，再也不用操心读入的字符串超界的问题了。因为`string`类设计可以让程序自动处理字符串的大小。

```C++
string str1;
cin >> str1;
```

### C++11 初始化

我们也可以使用 C++11 的列表初始化特性在`string`上，不过其实没必要，直接使用等于号赋值更方便一些：

```C++
string str1 = {"hello world"};
string str2 {"test"};
```

### 拼接

在使用`char *`的时候，如果我们要拼接两个字符串，需要调用函数`strcat`来实现。而有了`string`，我们可以直接使用加号来拼接两个字符串：

```C++
string str1 = "hello";
string str2 = "world";

string str3 = str1 + str2;
```

不仅如此，还可以使用+=，表示把另外一个字符串添加在自己末尾：

```C++
str1 += str2; // 等价于str1 = str1 + str2
```

### 长度

对于`char *`的字符串，我们要求长度，需要使用`strlen`函数。而`string`类型的字符串，我们可以直接调用.size()函数：

```C++
string str = "hello";

cout << str.size() << endl; // output 5
```

### IO

前文说了，`string`类的 cin 和 cout 用法都和`char *`完全一致，不过在读取一行的时候有些区别。

```C++
char st[100];
cin.getline(st, 100);

string str;
getline(cin, str);
```

对于`char *`来说，我们调用的`cin.getline`表示的是 cin 中的一个方法。而后者，我们调用的`getline`传入了 cin，这里的 cin 是一个传入的参数，并且也没有指定长度，因为`string`会自动设定长度。

### 原始字符串

关于`string`类型，C++11 有一个原始字符串的新特性。

在字符串表示当中，当我们要添加一些特殊字符的时候，往往需要在前面加上反斜杠，表示取义。当这类字符多了之后，就会很麻烦：

```C++
cout << "i want to output \"hello world\"" << endl;
```

如果我们要换行，还要在其中加入`\n`。针对这个问题 C++11 提出了原始字符串，也就是说在原始字符串当中所有的符号都会被原本原样地输出，不需要再使用`\`来取义了。

原始字符串以`"R(`开头`)"`结尾，`比如刚才的内容就可以写成：

```C++
cout << R"(i want to output "hello world")" << endl;
```

不仅如此，我们还可以随意换行：

```C++
cout << R"(i want to output
"hello world")" << endl;
```

但是有一个小问题，假如我们想要输出的结果当中也包含`"(`该怎么办呢？也有办法，编译器允许我们在`"`和`(`之间加入任意的字符来做区分（空格、左括号、右括号、斜杠、控制字符除外），这样我们在结尾的时候，也需要加上同样的字符。例如：

```C++
cout << R"tst("(test)")tst" << endl;
```

运行之后，屏幕上会输出"(test)"的结果。
