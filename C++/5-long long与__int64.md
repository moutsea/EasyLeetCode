### long long 和 \_\_int64

在 C++ Primer 当中提到的 64 位的 int 只有`long long`，但是在实际各种各样的 C++编译器当中，64 位的 int 一直有两种标准。一种是`long long`，还有一种是`__int64`，非主流的 VC 甚至还支持`_int64`。

对于一般的 C++开发者来说，其实这个问题不那么要紧，因为在实际开发当中，绝大多数情况使用 32 位的 int 就足够应付了。很少会出现超过 int 范围的情况，但是对于算法玩家来说，这是一个必须考量的问题。因为很多题目会故意把范围弄得很大，考察选手对于数据范围的敏感。

关于`long long`和`__int64`，我们有非常多的问题要讨论，我们一个一个来说。

### 历史遗留问题

首先是聊聊这个问题的背景，为什么会有两种标准呢？这并不是 C++的标准不严谨，或者是各大编译器乱来，背后是有一个历史遗留问题的。

`long long`最早是 C99 标准引进的，然而 VC6.0 推出于 1998 年，在 C99 标准之前。所以当时微软就自己搞出来一个变量叫做`__int64`来表示 64 位整数。很多同学使用的第一个 C++的编译器就是 VC6.0，所以记得在 VC6.0 当中要使用`__int64`而非`long long`。

既然 VC6.0 搞出了`__int64`，那么微软后续的 C++版本显然就必须要兼容它。所以在 win 系统当中，这个`__int64`的变量类型就一直沿用了下来。当然，由于 C++标准的更新，当然最新的 visual studio 已经支持 long long 了。

GCC 并不是基于 windows 系统的，自然支持`long long`。win 平台下的一些其他 IDE 如 dev C++，CodeBlocks 等也支持`long long`，因为它们为了和微软的系统兼容，所以也支持`__int64`。所以一个比较简单的区分方法是，判断编译器运行的操作系统是否是 windows，如果是 windows 使用`__int64`，否则使用`long long`。

### cin、cout 和 scanf、printf 的选择问题

这个问题对于 C++开发工程师来说同样不是个问题，没有任何选择的必要，无脑用 cin、cout 就完事了。但对于算法竞赛玩家来说，这依然是一个要考虑的问题。

因为在算法竞赛当中，尤其是当数据量很大的时候，读入和输出占据的时间是非常可观的。看起来只是`cin` `cout`和`scanf`和`printf`的差别，但是两者的性能差异非常大。

我曾经做过实验，同样的数据，使用`scanf`和`printf`的效率大约是`cin`、`cout`的十倍以上。在小数据量的时候当然没有差别，但数据量很大的时候影响非常大。很有可能导致同样的题目，同样的算法，别人通过了，但是我们却超时了的情况。

关于性能差异的原因，主要有两种解释。一种解释是说 cin 为了与 scanf 混用，而不用担心指针混乱，加上了绑定，总是会与 stdin 保持同步。正是这一步操作消耗了大量的时间。同理，cout 也会有类似的问题。第二种解释是 cout 在输出之前会把要输出的内容先存入缓存区，中间多了一个步骤，也会带来性能的降低。

关于 cin 与 stdin 同步带来的开销，我们是有办法解决的，只需要在加上这一行代码：

```C++
std::ios::sync_with_stdio(false);
```

这行代码的意思是取消`cin`、`cout`与`stdin`、`stdout`的指针同步，会使得 cin、cout 的性能大大提升，达到和`scanf`、`printf`相差无几的程度。当然，更好的方法是使用`scanf`、`printf`代替。

而要使用`scanf`和`printf`又有一个问题，它们是 C 语言的标准输入输出方式，需要提供标识符来代表变量的类型，那么问题来了`long long`和`__int64`的标识符是什么呢？

这个其实一查就知道了，`long long`的标识符是 lld，所以我们使用 scanf 读入一个`long long`类型的数写成：

```C++
long long a;
scanf("%lld", &a);
```

`__int64`的标识符是`I64d`，注意这里是大写的 i，不是 l。

```C++
__int64 a;
scanf("%I64d", &a);
```

但是这里面有一个很大的坑点，前面说了，目前在 windows 平台的编译器已经兼容了`long long`类型。但是即便如此，在 2013 之前的版本当中，我们输出的时候还是要使用`%I64d`，这是因为微软提供的 msvcrt.dll 库只支持`%I64d`的方式。相当于从底层上断绝了使用`%lld`输出的可能。2013 之后的版本里，微软修复了这个问题，添加了对`%lld`的支持。

所以比较简单的区分方法就是看操作系统，如果是 windows 系统，那么一律使用`__int64`准没错。如果是 linux 或者是 Mac 系统，那么统一使用`long long`。

我在网上找到了大神做的总结表，也可以直接参考下表：

| 变量定义  | 输出方式     | gcc(mingw32) | g++(mingw32) | gcc(linux i386) | g++(linux i386) | MicrosoftVisual C++ 6.0 |
| :-------- | :----------- | :----------- | :----------- | :-------------- | :-------------- | :---------------------- |
| long long | “%lld”       | 错误         | 错误         | 正确            | 正确            | 无法编译                |
| long long | “%I64d”      | 正确         | 正确         | 错误            | 错误            | 无法编译                |
| \_\_int64 | “lld”        | 错误         | 错误         | 无法编译        | 无法编译        | 错误                    |
| \_\_int64 | “%I64d”      | 正确         | 正确         | 无法编译        | 无法编译        | 正确                    |
| long long | cout         | 非 C++       | 正确         | 非 C++          | 正确            | 无法编译                |
| \_\_int64 | cout         | 非 C++       | 正确         | 非 C++          | 无法编译        | 无法编译                |
| long long | printint64() | 正确         | 正确         | 正确            | 正确            | 无法编译                |

> 参考资料：[博客：C/C++的 64 位整型](https://byvoid.com/zhs/blog/c-int64/)
