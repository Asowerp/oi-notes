#动态规划 #二分

LIS 是常见板子。

>[!example]
>给你一个整数数组 `nums` ，找到其中最长严格递增子序列的长度。

最简单粗暴的方法就是写一个 $O(n^2)$ 的 DP。然而这时间复杂度太高了，我们考虑下面的优化思路。

假设有下面这个序列：（0-indexed）

`nums: 2, 3, 5, 999, 6, 10`

$nums[3]=999$ 与 $nums[4]=6$ 的 LIS 长度都是 4，然而很显然后者更有机会在后续的 LIS 选择中被选上。换言之，999 是一个无用数据。我们可以直接删掉。

所以我们的优化思路就很显然了。维护一个数组 $g$，其中 $g[i]$ 表示 LIS 长度为 $i+1$ 时（这是因为 LIS 固有长度为 1）$nums$ 的最小值。

用下面的方法维护（假设当前更新到 $nums[i]=x$）：
 - 用 `lower_bound` 查找第一个大于等于 $x$ 的位置 $it$。
	 - 如果 `it=g.end()`，这说明不存在大于等于 $x$ 的位置，LIS 长度肯定可以更长，所以 `g.push_back(x)`；
	 - 反之，这说明 `it` 位置的元素可以替换成更小的 $x$，而且 $x$ 处的 LIS 长度恰为 `it-g.begin()+1`，于是我们把这个位置的元素换成 $x$，这并不改变 $g$ 的长度，也没有改变元素之间相对大小关系，因此后续维护的 LIS 肯定是正确的。

>[!exercise]
>思考：如果我要换成非严格递增的呢？

>[!Answer]-
>我们只需要把 `lower_bound` 替换成 `upper_bound` 即可，因为这样我们每次都会找比 $x$ 大的元素，而这正是和上面原理相同。比如 `2 3`，又进来了一个 `2`，我们自然希望把 `3` 顶掉。

实现代码：

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> g;
        for (auto n : nums) {
            auto it = ranges::lower_bound(g, n);
            if (it == g.end())
                g.push_back(n);
            else
                *it = n;
        }
        return g.size();
    }
};
```



