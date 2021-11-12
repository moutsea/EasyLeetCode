## switch

在日常的开发当中，我们经常会遇到一种情况，我们用一个变量表示状态。比如关闭-激活-完成，当我们需要判断状态的时候，就需要罗列`if-else`语句。

```C++
if (status == 'closed') {
    // todo
}else if (status == 'activated') {
    // todo
}else if (status == 'done') {
    // todo
}
```

如果只有少数几个还好，当我们要枚举的状态多了之后，写`if-else`就会非常繁琐。所以 C++当中提供了`switch`语句来代替简单的`if-else`的罗列。

```C++
switch(expression) {
    case constant1:
        //todo
    case constant2:
        //todo
    case constant3:
        //todo
    default:
        //todo
}
```

要注意的是，`switch`语句当中的`expression`只能是一个整数或者是枚举类型，不能是其他类型。比如像是`string`就不可以作为`switch`语句的 case，这个非常坑，很容易不小心写错。

所以上面的`if-else`语句可以改写成：

```C++
switch (status) {
    case 1:
        // todo1
        break;
    case 2:
        // todo2
        break;
    case 3:
        // todo3
        break;
    default:
        //todo
}
```

最后的`default`表示默认情况，也就是当之前的所有可能都不满足时会执行`defalut`标签下的内容。还有一点需要注意，`switch`语句有点像是路牌，它只是根据`expression`的值将代码跳转到对应的位置，并不是只运行对应标签的代码。

比如当我们的`status`为 1 时，代码会跳转到`todo1`处，在执行完`todo1`之后依然会继续往下执行`todo2`、`todo3`的代码。如果我们只希望执行`todo1`的代码，需要在末尾加上`break`，表示执行结束，跳出。这也是一个坑点，加不加`break`完全是两种效果。

数字 1、2、3 表示状态显然会导致含义不够明显，所以我们也可以使用枚举类型：

```C++
enum status {closed, done, activated};

status st;

switch (st) {
    case closed:
        //todo
        break;
    case done:
        //todo
        break;
    case activated:
        //todo
	default:
        //todo
}
```
