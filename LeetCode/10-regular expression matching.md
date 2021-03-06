

## 题意



给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

- '.' 匹配任意单个字符
- '*' 匹配零个或多个前面的那一个元素



所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。



![](https://tva1.sinaimg.cn/large/e6c9d24egy1gzikshq5m0j21b60u00vi.jpg)



## 解法



首先还是老规矩，我们拿到题目审完题之后先看数据范围。数据范围是很强的提示信息，既体现了题目的难度，有的时候也能解读出很多信息。



本题的范围很小，两个串最大长度才30。必须要说的是，这是出题方手下留情，正因此很多解法都可以通过。如果稍微提高一下范围，估计通过率还要进一步降低。



对于算法题来说，一般无非正推和逆推两种解法。所谓正推，即顺着题意进行思考，对中途遇到的问题，主要是性能问题进行优化和解决，从而解决问题。逆推则是相反，我们不顺着题意推导，而是另辟蹊径，将题意进行转化，转化成另外一道更容易解决的问题，并且还要保证要能满足原题的条件，不会构成冲突。



这道题非常经典，因为它正推和逆推都可行，我们先从相对比较简单的正推开始。



### 正向推导



整个题目是经典的字符串匹配问题，唯一的难点在于\*的出现。因为它可以匹配任意多个同样的字符，在我们进行匹配的过程当中，遇到\*的时候，无法确定它究竟怎样匹配才能达到最优。所以我们可以很自然地可以想到，当我们遇到\*时可以进行搜索，也就是枚举所有可能匹配的情况。只要有一种情况能够完成匹配，那么就说明有解。



这个思路是很容易想到的，难点在于如何用代码来表达。



由于我们判断匹配是在两个字符串s和p中间的，所以每一种匹配的情况都会对应s和p中的两个位置。我们可以用一个pair对<i, j>来记录匹配的状态，<i, j>表示字符串s[0:i]的子串和字符串p[0:j]的子串匹配。这样我们只需要维护所有能够匹配上的<i, j>，如果能够匹配到两个字符串的末尾，也就是说<n, m>出现在合法的状态当中，就说明有解，这里的n表示s的长度，m表示p串的长度。



这种用两个数字表示状态的方法是字符串匹配问题当中的常用技巧，我们可以顺着这个思路，要做的就是使用一个数据结构维护所有合法（匹配）的状态，通过当前的合法状态寻找新的合法状态，只要能找到最终要求的状态，那么就说明有解。如果找遍了所有合法的状态也没有找到最终状态，就说明无解。



如果大家学过宽度优先搜索，那么可以很自然地想到可以使用队列来记录所有的状态。那么顺水推舟，不难写出代码。



在这段代码当中我们使用了一个小技巧，我们将字符串s和p向右移动了一位，有效字符从下标1开始。这是因为我们用下标0表示空串的状态，如果下标从0开始，则空串的表达不太方便，代码书写会比较繁琐。



完整代码如下：



```C++
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = ' ' + s;
        p = ' ' + p;
        queue<pair<int, int>> que;
        // s和p都是空串可以匹配上，所以是合法的
        que.push(make_pair(0, 0));

        while(!que.empty()) {
            pair<int, int> head = que.front();
            que.pop();
            int i = head.first, j = head.second;
            // 如果s和全部和p串全部都已经完成了匹配，返回true
            if (i == n && j == m) {
                return true;
            }
			// 跳过非法长度
            if (i > n || j > m) continue;
            // 如果s[i+1]和p[j+1]匹配
            if (i < n && j < m && (s[i+1]==p[j+1] || p[j+1] == '.')) {
                que.push(make_pair(i+1, j+1));
            }
            // 如果p[j+2]=*，需要考虑p[j+1]位置出现0次即被删除的情况
            if (j+2 <= m &&p[j+2] == '*') {
                que.push(make_pair(i, j+2));
            }
            if (p[j] == '*') {
                // 如果p[j]=*,s[i+1]能够和p[j-1]匹配，那么<i+1, j>也是合法状态
                if (i < n && (j > 0 && s[i+1] == p[j-1] || p[j-1] == '.')) {
                    que.push(make_pair(i+1, j));
                }
            }
        }
        return false;
    }
};
```



写完了代码会发现这其实就是一个典型的宽度优先搜索问题，我们搜索了所有合法的状态，如果最终状态<n, m>被搜索到，那么说明有解，如果没有，说明无解。



这段代码虽然能够AC，但是仔细分析会发现一个问题。问题在于我们枚举状态的时候会有重复，一个中间状态可能会反复被枚举到，从而出现很多次。比如<2, 2>可以推导到<3, 3>，但<2, 3>也可能推导到<3, 3>，<3, 1>也可能推导到<3, 3>。极端情况下，状态会有很多的重复，从而导致性能损耗。



对于这个问题，其实很好解决，我们有一个非常简单的办法可以解决这个问题。就是把所有出现过的状态记录下来，每次有新的状态时就去检查一下是否已经被记录了，从而之前已经记录过的状态。



在C++当中我们可以使用set或者是map做到这一点，代码和刚才相差不大，只不过在往队列插入状态之前，增加了一个判断是否出现的逻辑。这种搜索结合记忆的做法就叫做记忆化搜索，看起来术语很高大上，但其实逻辑并不难。



```C++
class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = ' ' + s;
        p = ' ' + p;
        queue<pair<int, int>> que;
        que.push(make_pair(0, 0));
        
        set<pair<int,int>> st;

        while(!que.empty()) {
            pair<int, int> head = que.front();
            que.pop();
            int i = head.first, j = head.second;
            if (i == n && j == m) {
                return true;
            }
            if (i > n || j > m) continue;
            pair<int, int> cur;
            if (i < n && j < m && (s[i+1] == p[j+1] || p[j+1] == '.')) {
                cur = make_pair(i+1, j+1);
                // 如果没有出现在st里才会被添加
                if (st.count(cur) == 0) {
                    que.push(cur);
                    st.insert(cur);
                }
            }
            if (j+2 <= m && p[j+2] == '*') {
                cur = make_pair(i, j+2);
                if (st.count(cur) == 0) {
                    que.push(cur);
                    st.insert(cur);
                }
            }
            if (p[j] == '*') {
                if (i < n && (j > 0 && s[i+1] == p[j-1] || p[j-1] == '.')) {
                    cur = make_pair(i+1, j);
                    if (st.count(cur) > 0) continue;
                    que.push(make_pair(i+1, j));
                    st.insert(cur);
                }
            }
        }
        return false;
    }
};
```



### 逆向推导



看完了正推的解法， 我们再来想想逆推吧。



这道题逆推的思路还是比较明显的，在正推中，我们已知<i, j>合法，探索<i+1, j+1>, <i, j+2>, <i+1, j>合法的可能性。逆推就是反过来，我们要求<i, j>，探索它成立时需要满足的条件。比如，假设<i-1, j-1>合法且s[i]和p[j]匹配，那么显然<i, j>也合法。但由于我们是逆推的，所以我们没办法直接知道<i-1, j-1>是否合法，所以我们要继续推导下去。



说到这里，估计有些小伙伴已经反应过来了，这样的推导方式不就是递归吗？



其实整个的思路和代码的写法和上面的做法几乎是一样的，不过一个是从合法的<0, 0>开始，一个是从要求的<n, m>开始而已。



```C++
class Solution {
public:
    // 这里要注意，字符串传参会有开销，因此要用引用
    bool dfs(string &s, string &p, int n, int m) {
        // <0, 0>是合法状态，因此return true
        if (n == 0 && m == 0) return true;
        if (n < 0 || m < 0) return false;
		// 如果s[n]=p[m]递归求<n-1, m-1>
        if (n > 0 && (s[n] == p[m] || p[m] == '.')) {
            return dfs(s, p, n-1, m-1);
        }
        // p[m]=*的情况
        if (m > 1 && p[m] == '*') {
            bool match = p[m-1] == s[n] || p[m-1] == '.';
            return dfs(s, p, n, m-2) || (match && dfs(s, p, n-1, m));
        }
        return false;
    }

    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        s = ' ' + s;
        p = ' ' + p;
        return dfs(s, p, n, m);
    }
};
```



### 动态规划



我们搞定了逆向推导之后，其实还没有结束，在递归的做法当中，我们求每一个状态都是通过递归的方式去获取的。除了递归之外，还有没有其他办法？



其实是有的，也不难想到，我们完全可以通过一个二维数组来记录。`dp[i][j]`表示s[0:i]和p[0:j]能否构成匹配，由于我们已知`dp[0][0]=true`，所以我们可以按照顺序遍历状态进行推导即可。从状态的枚举上，我们是正推的思路，从最初合法的状态<0, 0>向外枚举，而对于每一个要求的状态而言，我们是通过逆推的方式去计算它的结果。



为什么大家都说动态规划的算法很难，难点其实就在这里，它是搜索算法的集大成者，也是正反两种思路的集成，因此对于思维的要求很高。但往往编码都比较简单，想出算法之后很容易实现。



我们来看代码：



```C++
bool isMatch(string s, string p) {
    int n = s.size(), m = p.size();
    s = ' ' + s;
    p = ' ' + p;
    bool dp[n+5][m+5];
    memset(dp, 0, sizeof dp);
    dp[0][0] = true;
    for (int i = 0; i <= n; i++) {
        for (int j = 1; j <=m; j++) {
            // 判断s[i]和p[j]是否匹配
            bool matched = s[i] == p[j] || p[j] == '.';
            if (matched && i > 0) dp[i][j] = dp[i-1][j-1];
            // 判断p[j]是否为*
            if (j > 1 && p[j] == '*') {
                // 判断p[j-1]是否和s[i]匹配
                matched = p[j-1] == s[i] || p[j-1] == '.';
                // dp[i][j-2]表示p[j-1]出现0次，dp[i-1][j]表示s[i]通过*重复完成匹配
                dp[i][j] = dp[i][j-2] || (i > 0 && matched && dp[i-1][j]);
            }
        }
    }
    return dp[n][m];
}
```

