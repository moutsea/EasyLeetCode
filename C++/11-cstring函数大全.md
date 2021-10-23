## cstring.h 常用函数

cstring.h 库即 C 语言中的 string.h 库，它是 C 语言中为字符串提供的标准库。C++对此进行了兼容，所以我们在 C++当中一样可以使用。

这个库当中有大量的关于字符串操作的 api，本文选择了其中最常用的几个进行阐述。

### strlen

由于编译器是按照`\0`的位置来确定字符串的结尾的，所以字符串的长度并不等于数组的长度。我们可以使用`strlen`函数求得字符串的真实长度：

```C++
char name[100] = "hello world";
cout << strlen(name) << endl;
```

比如我们这里用一个长度为 100 的`char`数组存储了“helloworld”字符串，当我们使用`strlen`函数求它的实际长度只有 11。

### strcat

`strcat`函数可以将两个字符串进行拼接，它的函数签名为：

```C++
char *strcat(char *dest, const char *src)
```

我们可以看到它接受两个参数，一个是`dest`，一个是`src`，都是`char*`类型，返回的结果也为`char *`类型。在 C++当中，数组名是指向数组中第一个元素的常量指针。所以虽然签名中写的参数是指针类型，但我们传入数组名同样可以。

我们传入两个字符串之后，`strcat`函数会将`src`字符串拼接在`dest`字符串末尾，并且返回指向拼接之后结果的指针。所以下面两种方式输出结果得到的值是一样的。

```C++
char name[100] = "hello world";
char level[100] = "concat test";

char *ret = strcat(name, level);
cout << ret << endl;	// 方式1
cout << name << endl;	// 方式2
```

### strncat

`strcat`函数的变种，函数额外多接收一个参数控制拷贝`src`字符串的最大长度。

```C++
char *strncat(char *dest, const char *src, size_t n)
```

我们使用刚才同样的例子：

```C++
char name[100] = "hello world";
char level[100] = "concat test";

char *ret = strncat(name, level, 4);
cout << ret << endl;
cout << name << endl;
```

由于我们传入了 4，限制了 level 字符串拷贝的长度，所以最终得到的结果为：`hello worldconc`。

### strcpy

字符串拷贝函数，可以将`src`字符串中的内容复制到`dest`。

```C++
char *strcpy(char *dest, const char *src)
```

使用方法和前面介绍的其他函数类似，有两点需要注意。

1. 如果`dest`字符串长度不够长，在编译时不会报错，但运行时可能导致问题。

```C++
char name[10] = "";
char level[100] = "concat test";

strcpy(name, level);
cout << name << endl;
```

上面这段代码可以编译运行，但是运行的时候终端会出现出错信息。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gvoxy0gkkkj610q042gm002.jpg)

所以在使用`strcpy`的时候千万小心，一定要保证`dest`有足够长度。

2. 如果`dest`中原本就有内容，会被覆盖。

```C++
char name[15] = "abc";
char level[100] = "concat test";

strcpy(name, level);
cout << name << endl;
```

运行完`strcpy`之后，`name`中的内容会被清空。

### strncpy

`strcpy`加入长度限制的版本，可额外多传入一个参数 n 表示最多赋值 n 个字符。当`src`长度小于 n 时，剩余部分将会使用空字节填充。

```C++
char *strncpy(char *dest, const char *src, size_t n)
```

```C++
char name[15] = "abc";
char level[100] = "concat test";

strncpy(name, level, 4);
cout << name << endl;
```

输出结果为`conc`。

### memset

字符串的批量设置函数，可以将字符串批量设置成某一个字符。

```C++
void *memset(void *str, int c, size_t n)
```

`int c`表示要被设置的字符，`size_t n`表示设置的字符数量。

```C++
char name[15] = "abc";
char level[100] = "concat test";

memset(name, 'c', 10);
cout << name << endl;
```

上述代码的运行结果为 10 个 c。

多说一句，memset 除了可以用来给字符串进行批量设置之外也可以给 int 型的数组进行批量设置。由于一个 32 位的 int 占据 4 个字节，也就是 4 个字符长度。所以使用`memset`进行批量设置的时候，最终得到的结果将是 4 个传入的`int c`拼接的结果。

```C++
int a[100];
memset(a, 1, sizeof a); // memset(a, 1, 400); 因为一个int占据4个字节
```

我们这样设置之后，a 数组当中的元素并不是 1，而是`0x01010101`,转成 10 进制是 16843009。所以使用`memset`对 int 型数组进行初始化一般只用 3 种操作：

```C++
// 1. 批量设置成0
memset(a, 0, sizeof a);
// 2. 批量设置成-1
memset(a, -1, sizeof a);
// 3. 批量设置成一个接近最大整数上限的值
memset(a, 0x7f, sizeof a);
memset(a, 0x3f, sizeof a);
```

关于`memset`使用的一些具体细节将在后续题解的实际问题当中再做详细说明。

### memcpy

和`strcpy`类似，从`str2`中复制 N 个字符到`str1`中。

```C++
void *memcpy(void *str1, const void *str2, size_t n)
```

```C++
char name[15] = "abc";
char level[100] = "concat test";

memcpy(name, level, 10);
```

### strcmp

将两个字符串按照字典顺序进行比较，对于字典序的顺序定义为：两个字符串自左向右逐个字符相比（按 ASCII 值大小相比较），直到出现不同的字符或遇 **\0** 为止。

```C++
int strcmp(const char *str1, const char *str2)
```

返回的结果为一个 int，如果它小于 0，说明`str1`小于`str2`，如果它等于 0，说明两者相等，如果大于 0，说明`str1`大于`str2`。

```C++
char name[15] = "abc";
char level[100] = "abcd";

cout << strcmp(name, level) << endl;
```

运行结果为-100，说明 name 小于 level。

### strncmp

`strcmp`长度限制版，可以额外接受一个数字 n，表示最多比较长度为 n 的字符。

```C++
int strncmp(const char *str1, const char *str2, size_t n)
```

### strstr

```C++
char *strstr(const char *haystack, const char *needle)
```

在`haystack`中搜索`needle`第一次出现的位置，返回指向该位置的指针。

```C++
char name[15] = "search-test";
char level[100] = "-";

char *ret = strstr(name, level);
cout << ret << endl;
```

运行之后，屏幕输出的结果为：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gvoyx1hwssj60yo036weo02.jpg)

因为当我们使用 cout 输出一个`char *`变量的时候，它会当做是字符串进行输出，即一直输出字符，直到遇见`\0`为止。

输出的结果为-test，说明我们通过`strstr`函数找到了“-”出现的位置，跳过了之前的内容。

除了上文介绍的这些函数之外，`cstring`当中还有很多其他的 api，例如`strtok`,`memmove`等等，大家感兴趣不妨去翻阅相关文档，也许会有惊喜。
