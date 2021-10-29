## 题意

给定一个全是 int 的数组`nums`和一个整数`target`，要求返回两个下标，使得数组当中这两个下标对应的和等于 target。

你可以假设一定值存在一个答案，并且一个元素不能使用两次。

其中：

- `2 <= nums.length <= 1e4`
- `-1e9 <= nums[i] <= 1e9`
- `-1e9 <= target <= 1e9`

## 解法一：无脑枚举

在数据里面找到两个数等于`target`，很明显可以进行枚举。

不过要使用枚举也需要解决几个问题，首先，需要枚举的数量。在这题当中，我们需要枚举任意两个数的组合，对于长度为 n 的数组来说，我们知道这样的组合一共有$C_n^2=\frac{n(n-1)}{2}$种，数量级和$n^2$相当，所以近似可以看成是$n^2$种。

对于这道题来说，数组的最大长度是`1e4`，平方之后的量级是`1e8`，差不多是 C++一秒能够执行的量级。勉勉强强可以接受大概率不会超时。

其次，是重复的情况。毕竟两两的组合这么多，如何保证得到的答案结果唯一呢？如果不唯一怎么办？

好在这道题当中替我们进行了保证，题意中明确说明了，答案唯一。既然如此，我们就可以大胆写出代码了：

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> ret;
        for (int i = 0; i < n; i ++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) {
                    ret.push_back(i);
                    ret.push_back(j);
                    return ret;
                }
            }
        }
        return ret;
    }
};
```

## 解法二：使用 map

解法一的代码提交之后通过时间大约在 400 毫秒左右，仅仅超过了 6%的用户，显然还不够完美，存在大量的优化空间。

那么，我们怎么进行优化呢？

显然比较直观的就是，我们枚举了所有的可能，这太耗时了，有没有办法可以不用遍历所有的组合，但是又能保证一定可以找到答案的？

答案当然是肯定的，利用的原理也很简单，我们知道了`target`，需要在数组中寻找到 a 和 b 两个数，使得`a+b=target`。但其实我们没有必要遍历所有 a 和 b 的组合，因为确定了 a，b 也就确定了，它等于`target-a`。所以我们只需要枚举所有的 a，然后判断`target-a`元素是否存在即可。

那么问题来了，我们怎么判断`target-a`是否存在，并且找到它的位置呢？

答案很简单，使用 STL 中的 map。map 可以记录一个<key, value>的 pair，我们把数组当中的数当做 key，它出现的位置当做 value。这样我们只需要提前将数组当中所有的元素都插入 map 当中，就可以了。

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> ret;
        map<int, int> mp;
        // 先把数组中元素插入map
        for (int i = 0; i < n; i++) {
            mp[nums[i]] = i;
        }

        for (int i = 0; i < n; i++) {
            // 枚举a，通过map判断target - a是否存在
            int num2 = target - nums[i];
            if (mp.count(num2)) {
                ret.push_back(i);
                ret.push_back(mp[num2]);
            }
        }
        return ret;
    }
};
```

这个代码也很简单吧，但是先别急着高兴，如果你提交的话就会发现上面的代码会有样例无法通过。为什么会无法通过呢？因为我们疏忽了一种情况，一般我们会把这种隐藏的不容易想到的情况称作“Trick”，可以看做是出题人使用的诡计。

有时候就算想到了解法，但是没有发现隐藏的 trick 也无法通过题目。所以我们不仅要想出算法，还要确保算法在所有极端情况下都能运行。

在这题当中，这个 trick 是元素的唯一性。因为我们使用了 map，map 要求所有的 key 必须唯一。如果数组当中存在重复的元素，那么后面读到的数据会覆盖前面的。覆盖会产生什么问题呢？显然会导致答案出错。

那么问题来了，在这题当中`nums`中的数唯一吗？题目中并没有说，题目中只说了确保解只有一个，但并没有说每个元素唯一。所以虽然题目没有明说，但我们依然需要对这个问题进行分析。

假设我们找到了`a+b=target`的答案，这里的 a 和 b 可能有重复吗？很明显，当 a 和 b 不相等的时候，它们都不会有重复。因为如果数组当中有两个 a，那么意味着我们至少能够找到两个 a 和 b 的组合。这与题目中说的解只有一个矛盾，所以可以肯定，当 a 和 b 不等的时候，它们都只会出现一次。

那么剩下的就是 a 和 b 相等的时候了，这也正是本题的 trick 所在。a 和 b 可能会相等，但是由于 map 中强制 key 唯一，所以只能找到一个。怎么解决呢？其实很简单，我们只需要加一个判断条件就可以解决：

```C++
添加备注


class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> ret;
        map<int, int> mp;
        for (int i = 0; i < n; i++) {
            mp[nums[i]] = i;
        }

        for (int i = 0; i < n; i++) {
            int num2 = target - nums[i];
            // 额外增加了mp[num2] != i的条件
            if (mp.count(num2) && mp[num2] != i) {
                ret.push_back(i);
                ret.push_back(mp[num2]);
                break;
            }
        }
        return ret;
    }
};
```

这段代码和上面几乎完全一样，只不过底下 for 循环的 if 判断当中额外增加了 mp[num2] != i 的条件。这个条件确保我们找到了`num2`不是当前的`nums[i]`，因为当 a 和 b 相等的时候，我们想要通过 map 去寻找 b 的位置，结果由于 map 中发生了覆盖，所以又找回了 a 的位置，从而引发出错。我们加上这个判断就可以避免这种情况。

到这里还没有结束，这段代码仍然可以优化。既然 map 会发生覆盖，那么我们其实没有必要一开始的时候就一股脑把所有元素全部插入，我们可以一边插入元素一边进行判断。

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> ret;
        map<int, int> mp;
        for (int i = 0; i < n; i++) {
            int num2 = target - nums[i];
            // 先寻找num2是否存在
            if (mp.count(num2)) {
                ret.push_back(mp[num2]);
                ret.push_back(i);
                return ret;
            }
            mp[nums[i]] = i;
        }

        return ret;
    }
};
```

在这段代码当中，我们没有在一开始的时候将`nums`数组的元素全部放入 map。而是随着 for 循环逐渐插入的，在 for 循环当中，我们先去寻找了`num2`在 map 当中是否存在，再去插入的`nums[i]`。我们也没有再去判断`mp[nums2] != i`了，因为这时候`nums[i]`的值还没有插入 map，所以一定不会相等。

其实这利用了加法的交换律，`a+b=target`也可以得到`b+a=target`。a 和 b 两个数在数组当中一定有一个先后顺序，假设 a 排在 b 前面。由于我们没有事先把所有元素插入 map，所以这时候是找不到 b 的，也就是找不到答案的。但当我们遍历到 b 的时候，一定可以找到 a。并且由于我们是先判断再插入当前的元素，即使 a 和 b 相等，也不会发生覆盖。

到这里，我们就算是把这题真正做完了，不仅做出来了，而且还优化到了极致。明明是一道挺简单的题目，也没用到什么高深的算法，但是当我们深入去研究，却有这么多东西可以说。某种程度上来说，这也是算法的魅力之一。很小的细节都值得仔细研究分析，都能分析出成果来。甚至很多题的解题思路就在这些细小的分析上。

好了，关于这题就聊到这里，感谢大家的阅读。
