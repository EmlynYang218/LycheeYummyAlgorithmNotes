# 取石子游戏：Euclid 游戏

## 题目模型

有两堆石子，两名玩家轮流操作。每次只能从较多的一堆中取石子，且取走的数量必须是较少那堆石子数量的正整数倍。谁把一堆石子取空，谁就获胜。双方都采用最优策略，判断先手是 `win` 还是 `lose`。

输入包含多组正整数 `a, b`，以 `0 0` 结束。

## 例子

当初始状态为 `(25, 7)` 时：

```text
(25, 7) -> (11, 7) -> (4, 7) -> (4, 3) -> (1, 3) -> (1, 0)
```

先手最终获胜。

## 关键观察

先令 `a >= b`。

- 如果 `a / b >= 2`，先手可以一次取走 `b` 的两倍或更多，存在直接改变局势的选择，因此先手必胜。
- 如果 `a / b < 2`，先手只能取走一份 `b`，操作是唯一的，问题转化为交换两堆石子后继续判断。

因此可以不断令 `(a, b) <- (b, a - q * b)`，其中每一步的 `q` 取决于当前较大数除以较小数的商；当商至少为 2 时当前玩家获胜，否则交换角色继续。

## C++ 实现

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    long long a, b;
    while (cin >> a >> b && (a != 0 || b != 0)) {
        bool currentPlayerIsFirst = true;

        while (true) {
            if (a < b) swap(a, b);

            if (a / b >= 2) {
                cout << (currentPlayerIsFirst ? "win" : "lose") << '\n';
                break;
            }

            long long remainder = a % b;
            if (remainder == 0) {
                // 唯一操作会直接把较小的一堆取空，当前玩家获胜。
                cout << (currentPlayerIsFirst ? "win" : "lose") << '\n';
                break;
            }

            a = b;
            b = remainder;
            currentPlayerIsFirst = !currentPlayerIsFirst;
        }
    }
}
```

## 思维要点

这类题的重点不是模拟所有取法，而是识别局面中的“选择数目”：商至少为 2 时有多个可选动作，当前玩家可以主动控制局势；商小于 2 时动作唯一，只能把问题交给对手。这个观察把看似复杂的博弈压缩为类似辗转相除法的过程。
