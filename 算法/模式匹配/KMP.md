# KMP算法
用于模式匹配。

## 核心思想
考虑在匹配过程中发生了失配：
$$\alpha a \neq \alpha b$$
传统模式匹配算法选择完全重新匹配，浪费了之前的匹配信息。考虑到$\alpha$可能满足：
$$\alpha=\beta \gamma \beta$$
虽然不能继续匹配，但仍然可以把模式串把模式串的前缀$\beta$和待匹配串的后缀$\beta$对齐，继续匹配。由于在KMP中$\beta$是前缀串，可以证明这样的做法不会影响正确性。如此，已经检查过的待匹配串字符不会被重复检查，大幅降低了时间消耗。

## 代码实现
给你两个字符串`haystack`和`needle`，请你在`haystack`字符串中找出`needle`字符串的第一个匹配项的下标（下标从`0`开始）。如果`needle`不是`haystack`的一部分，则返回`-1`。
```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = needle.size();
        vector<int> next(n);
        next[0] = -1;
        for (int i = 0, j = -1; i < n - 1; )
        {
            if (j == -1 || needle[i] == needle[j])
            {
                i++, j++;
                next[i] = j;
            }
            else
            {
                j = next[j];
            }
        }
        int nh = haystack.size(), i, j;
        for (i = 0, j = 0; i < nh && j < n; )
        {
            if (j == -1 || haystack[i] == needle[j])
                i++, j++;
            else
                j = next[j];
        }
        if (j == n)
            return i - j;
        else
            return -1;
    }
};
```
算法的核心在于`next`数组的计算，求`next`数组的过程其实也是一个模式匹配的过程。`next[i]`表示`needle[0:i]`中匹配前后缀的最大长度，在`next[i]`的基础上，如果：
- `needle[i + 1]`也成功匹配，那么前后缀匹配的长度就比前一项增加`1`
- `needle[i + 1]`没能匹配，那么需要寻找一个更短的前缀试图继续匹配，这个前缀正好可以通过`next[j]`来得到，则`j = next[j]`（数学证明不难，姑且略过）。

## 复杂度分析
- 时间复杂度：$O(n)$，其中$n$为**待匹配串**的长度。
- 空间复杂度：$O(n)$，其中$n$为**模式串**的长度。