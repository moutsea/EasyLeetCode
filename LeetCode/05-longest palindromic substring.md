## 题意

题目非常简单只有一句话：给你一个字符串 `s`，找到 `s` 中最长的回文子串。



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9aehwxf4j30xy0k4t9k.jpg)



这题的暴力解法很容易想到，我们只需要枚举一下回文中心的位置，然后针对每一个回文中心去找它的最长回文子串即可。



不过有一点需要注意，回文串有两种一种是奇回文，一种是偶回文。顾名思义，如果是奇回文，那么回文串的长度是奇数。如果是偶回文，自然就是偶数。这两个在枚举的时候是不一样的，需要注意。



暴力求解的算法很容易写：

```C++
class Solution {
public:
    string longestPalindrome(string s) {
        string ret;
        for (int i = 0; i < s.size(); i++) {
            int l = i, r = i;
            // 奇回文，回文中心是i
            while (l >= 0 && r < s.size() && s[l] == s[r]) {
                l--;
                r++;
            }
            if (r - l - 1 > ret.size()) {
                ret = s.substr(l+1, r-l-1);
            }
            l = i-1;
            r = i;
            // 偶回文，回文中心是i和i-1
            while (l >= 0 && r < s.size() && s[l] == s[r]) {
                l --;
                r ++;
            }
            if (r - l - 1 > ret.size()) {
                ret = s.substr(l+1, r-l-1);
            }
        }
        return ret;
    }
};
```



我们简单分析一下复杂度，极端情况下，比如整个字符串的所有字符都相等时，那么每次枚举回文串都会将整个字符串遍历一遍。很明显，在这种情况下的复杂度就是$O(n^2)$。



虽然复杂度看起来有些高，但一样可以通过，因为题目当中明确写了，字符串的长度最长只有1000.



那么还有没有更快的算法呢？



当然是有的，这就要介绍到今天的主角，manacher算法，俗称马拉车算法。



马拉车算法的核心原理是利用之前已经找到的回文子串的性质，来快速求解之后的回文子串的长度。怎么利用呢？我们来看一张图，为了方便起见，我们将字符串画成一条线。



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9aw7gyxnj31lg09cmxc.jpg)



我们假设它当中的某一个位置i能够找到的回文子串的左右端点分别是left和right，那么i的回文半径就是right-i。



这个时候，假设我们要求i的右侧的某一点j的回文长度，我们可以怎么求呢？



首先我们可以找到j关于i的对称位置j'，因为j'一定在i的左侧，所以如果我们是按照从左往右的顺序来求每一点对称半径的话，j'的对称半径是已知的。



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9b16bck9j31hk08sglt.jpg)



这个时候根据j'对称半径的长度，有三种可能：第一种，这个对称半径比较小。我们和什么比呢？我们和left的位置比，也就是说这个对称范围的左侧在left的右边，如图：



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9b5aa4uuj31ge09kt8y.jpg)



假设我们用一个数组`radis`存储了之前所有位置的对称半径，那么由于j和j’关于i对称，那么根据对称性，我们可以知道`radis[j] = radis[j']`。



再来看第二种可能，第二种可能就是j'的对称半径比较大，它的范围左侧超过了left：



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9b74mrmhj31gu09idg3.jpg)



在这种情况下，j的对称半径还能取到`radis[j'`]吗？



答案是取不到，因为如果j的对称范围也有这么大的话，下图圈起来的两个部分就要对称了。



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9b97822xj31ew0b6gm4.jpg)



实际上它们是不可能对称的，因为i的对称半径已经确定了，如果它们还能对称，那么就和i的对称半径矛盾了。所以在这种情况下，j的对称半径就是`right-j`。



我们再来看最后一种情况，就是j'的对称半径的左侧刚好和left重合，在这种情况下，j的对称范围的右侧也会刚好和right重合。



![](https://tva1.sinaimg.cn/large/008i3skNgy1gz9bcdgxgrj31hs09oaac.jpg)



我们来思考一个问题，这个时候j的回文半径确定了吗？



答案是没有，它还可以继续往右侧延伸，因为j'的对称范围到left就停住了，right的右侧再构成新的对称也不会构成矛盾。所以这种情况下，我们还是需要用一层循环去拓展j的范围。



现在我们厘清了所有的情况，要怎么求最长的回文子串呢？很简单，我们从左往右遍历，每次维护最右侧的位置right以及它对应的回文中心i。



到这里还剩下一个问题：回文分为奇回文和偶回文，上面的算法只能解决奇回文的情况，对于偶回文怎么办呢？



这个问题很好回答，我们可以在算法开始之前先对字符串做一个预处理，把所有偶回文的情况也转换成奇回文。



比如：



abba -> #a#b#b#a#



这样一来，回文中心就变成中间的#了。我们再来看原本是奇回文的情况：



aba -> #a#b#a#



回文中心还是在b上，依然还是奇回文。



预处理的代码：



```C++
string transform(string& str) {
    string ret;
    for (int i = 0; i < str.length(); i++) ret = ret + '#' + str[i];
    return '$' + ret + '#';
}
```



我们来看完整代码：



```C++
class Solution {
public:

    // 字符串预处理
    string transform(string& str) {
        string ret;
        for (int i = 0; i < str.length(); i++) ret = ret + '#' + str[i];
        return '$' + ret + '#';
    }

    string longestPalindrome(string s) {
        string st = transform(s);
        int n = st.length();
        // 存储每一个位置的回文半径
        int arr[n+2];
        // mr 是对称位置的最右侧，idx为对应位置的回文中心
        // max_radis是当前最长回文半径，max_id最长回文半径的对称中心
        int mr = 0, idx = 0, max_radis = 0, max_id = 0;
        for (int i = 1; i < n; i++) {
            // 如果mr在i右侧，利用回文性质快速求arr[i]
            arr[i] = mr > i ? min(arr[2*idx - i], mr-i) : 1;
            // 如果i在mr右侧，或者回文范围刚好覆盖的情况
            if (i >= mr || i + arr[i] == mr) {
                while (st[i - arr[i]] == st[i + arr[i]]) {
                    if (arr[i] > max_radis) {
                        max_radis = arr[i];
                        max_id = i;
                    }
                    arr[i]++;
                    if (i + arr[i] > mr) {
                        mr = i + arr[i];
                        idx = i;
                    }
                }
            }
        }

        // 还原回文串
        string ret = "";
        for (int i = max_id - max_radis; i < max_id + max_radis; i++) {
            if (st[i] != '#') ret = ret + st[i];
        }
        return ret;
    }
};
```



最后我们来思考一个问题，为什么马拉车算法的复杂度是$O(n)$呢？



原因其实很简单，虽然我们的代码当中有两层循环，但是我们仔细观察一下会发现，不论循环怎么执行，mr这个变量是一直递增的。mr最多只能从0递增到n，所以这是一个$O(n)$的算法。



马拉车算法巧妙地利用了对称性简化了计算过程，这一思想在很多其他字符串处理算法上 非常常见。因此，仔细了解它的原理非常有必要。