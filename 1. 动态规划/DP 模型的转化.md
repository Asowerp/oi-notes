
>[!example] ABC 419 E
>给定一个长度为 $N$ 的整数序列 $A=(A_1, A_2, \dots, A_N)$。
>
>你的目标是通过重复执行以下操作，使得 $A$ 的每一个长度为 $L$ 的连续子数组的和都是 $M$ 的倍数：
>
> - 选择一个整数 $i$（$1 \leq i \leq N$），并将 $A_i$ 的值增加 $1$。
>
>请找到实现目标所需的最少操作次数。
>
>数据范围：$1 \leq N, M \leq 500$，$1 \leq L \leq N$ ，$0 \leq A_i < M$ 。

怎么转化呢？因为每个长为 $L$ 的连续子数组和都是 $M$ 的倍数，因此一下就有
$$
A_i\equiv A_{i+L}\pmod M.
$$
（这是常见转化！）

所以我们第一步考虑计算把所有 $\{A_{i+kL}\}$ 调成一样的值所需要的代价。然后我们要求每一组长为 $L$ 的子数列都是 $M$ 的倍数，这就是一个非常显然的 dp 问题了。

代码如下。

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MAXN = 505;

int m, n, l;
int a[MAXN], cost[MAXN][MAXN];
int dp[MAXN][MAXN];

// cost[i][j]: 第 i 组全部调成 j mod m 的 cost
// dp[i][j]：前 i 组 j mod m 的 cost

int main() {
    cin >> n >> m >> l;
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= l; i++) {
        for (int j = i; j <= n; j += l) {
            for (int k = 0; k < m; k++) {
                cost[i][k] += (k + m - a[j]) % m;
            }
        }
    }
    memset(dp, 0x3f, sizeof(dp));
    dp[0][0] = 0;
    for (int i = 1; i <= l; i++) {
        for (int j = 0; j < m; j++) {
            for (int k = 0; k < m; k++) {
                dp[i][j] =
                    min(dp[i - 1][(j + m - k) % m] + cost[i][k],
                        dp[i][j]);
            }
        }
    }
    cout << dp[l][0] << endl;
    return 0;
}
```

还有，如果有变换 / 交换问题（把 a 变成 b 把 b 变成 a），我们可以先考虑没有限制的，也就是两种操作都可以进行任意多次，最后在枚举答案的时候加以限制。
例子：洛谷P10715

此外，在考察 a=b 的时候，我们可以考虑把 a-b 作为状态。
例子：LC956

优化思路：

- 排除掉**一定不优**的情况，或者只考虑某些**一定不劣**的状态，减少需要表示的状态总量。
- 分析 dp 的**转移结构**，与排除掉的情况结合，根据这个结构的形态重新设计 dp。
- **合并多种本质相同的状态**成为一种状态。
- 在不等式同号的情况下，**弱化条件**。即允许一些不合法但是一定不优的情况存在，使得更弱的条件更容易维护，但同时不影响正确性。（比如交换两种，我们弱化为a变成b/b变成a的次数，然后考虑相等的情况）
