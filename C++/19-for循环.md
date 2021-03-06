## for 循环

在编程当中我们经常会遇到需要重复执行的步骤，想要让计算机能够重复执行某些逻辑，就需要使用循环。

在 C++当中，循环有三种类型，分别是`for`循环、`while`循环和`do while`循环。其中前两者使用较多，最后一个很少使用。

### 基础用法

求 1 + 2 + 3 + ... + 100 的和，使用 for 循环实现：

```C++
int ret = 0;
int i;
for (i = 1; i <= 100; i++) {
    ret += i;
}
```

这里的`i++`表示自增操作，是`i+=1`的简写，也可以写成`++i`。不过`++i`和`i++`并不能完全混用，我们将会在之后的内容当中进行阐述。

根据 C++ Primer 当中的定义，`for`循环可以分成三个部分。第一个部分是初始化，在这个`for`循环当中，我们把循环因子 i 初始化成了 1。第二个部分是判断条件，也就是`for`循环的执行条件，在什么情况下需要执行循环。第三个部分是更新循环因子，比如在这个例子当中，我们当前的 i 加入了 ret 之后，自增 1 变成了下一个将要累加的数。最后一个部分就是花括号当中的执行体，也就是每次循环需要执行的内容。

写成通用形式就是：

```C++
for (initialization; test-expression; update-expression) {
    body;
}
```

初始化、判断条件以及更新因子这三个部分使用分号分隔。如果循环要执行的内容只包含一条语句，花括号也可以不写。不过为了保证全局的代码风格统一，最好还是统一使用花括号。

`test-expression`决定循环是否结束，因此，在每一次 body 开始执行之前，都需要进行一次`test-expression`的判断。当`test-expression`为 true 时，才会执行 body 中的语句。

`test-expression`并不一定需要是一个`bool`值，C++会进行强制转换。比如对于 int 类型，0 值会被转换为 false，而其他值都会转换成 true。如：

```C++
int ret = 0;
int i;
for (i = 100; i; i--) {
    ret += i;
}
```

这段逻辑和上面一样，同样是计算了 1 到 100 累加的值。只不过这里我们进行的是倒序相加，循环的执行条件为`i >= 0`。当 i=0 时结束，我们的判断条件可以写成 i。`int`型的 i 会被强制转换成`bool`型，当 i=0 时，值为 false，循环结束。其中自减符的用法和自增类似。

### 进阶用法

首先，我们可以将循环因子的声明写入 for 循环当中：

```C++
// version1
int i;
for (i = 0; i < 100; i++) ret += i;

// version2
for (int i = 0; i < 100; i++) ret += i;
```

这样有两个好处，第一是简化了代码，将 i 的声明语句写入了`for`循环当中，可读性也更好。另一点是限定了 i 这个变量的使用范围，在 for 循环当中声明的变量，**它的作用域也只有`for`循环**，出了循环之后，i 这个变量将消失。

不过在一些老旧的编译器（如 VC6.0）当中并不会这样，循环当中声明的变量依然会继续存在。

另外`initialization; test-expression; update-expression`这三条语句都不是必须的，可以根据我们的需要进行省略。

比如 for 循环所需要的初始化工作之前已经完成，那么就可以省略`initialization`：

```C++
int i = 0;
for (; i < 100; i++) ret += i;
```

比如我们也可以将循环的结束条件放在 for 循环的 body 当中，如：

```C++
for (int i = 0;; i++) {
    if (i > 100) break;
    ret += i;
}
```

我们的更新条件同样也可以放在 body 中：

```C++
for (int i = 0; i < 100;) {
    ret += i;
    i++;
}
```

甚至，我们可以极端一点，三个条件都省略：

```C++
int i = 0;
for (;;) {
    if (i > 100) break;
    ret += i;
    i++;
}
```

同样，我们更新的条件也不一定只能自增或自减，其他的任何逻辑也都可以。正因此，C++当中的`for`循环是非常灵活的， 使用得当完全可以代替`while`循环和`do while`循环。

不过除非必要，我们还是要尽量遵守代码规范，不要省略条件，这样代码可读性也会更好一些。
