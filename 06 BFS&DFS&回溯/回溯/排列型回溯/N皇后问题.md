[51. N 皇后](https://leetcode.cn/problems/n-queens/)

按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

**示例 1：**

![](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

**输入：**n = 4
**输出：**[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
**解释：**如上图所示，4 皇后问题存在两个不同的解法。

**示例 2：**

**输入：**n = 1
**输出：**[["Q"]]

**提示：**

- `1 <= n <= 9`

```cpp
class Solution {
public:
    unordered_set<int> p1, p2;
    // k表示已经放置的皇后数量，也即当前正在放置的皇后的行标
    void place(int k, int n, vector<bool>& used, vector<int>& cur,
               vector<vector<int>>& res) {
        if (k == n) {
            res.push_back(cur);
            return;
        }
        for (int i = 0; i < n; i++) {
            if (!used[i]) {
                //判断皇后是否攻击，剪枝
                int diff1 = k + i;
                int diff2 = k - i;
                if (p1.count(diff1) || p2.count(diff2)) {
                    continue;
                }

                //排列
                p1.insert(diff1);
                p2.insert(diff2);
                used[i] = true;
                cur.push_back(i);

                //递归
                place(k + 1, n, used, cur, res);

                //回溯
                cur.pop_back();
                used[i] = false;
                p2.erase(diff2);
                p1.erase(diff1);
            }
        }
    }

    // 固定行号0，1，2，3，每一行（第i行）的皇后在第res[i]列
    // 输出结果
    vector<vector<string>> convert(const vector<vector<int>>& res, int n) {
        int T = res.size();
        vector<vector<string>> res1 =
            vector<vector<string>>(T, vector<string>(n, string(n, '.')));
        for (int j = 0; j < T; j++) {
            for (int i = 0; i < n; i++) {
                res1[j][i][res[j][i]] = 'Q';
            }
        }
        return res1;
    }

    vector<vector<string>> solveNQueens(int n) {
        /*固定行号1 2... n，枚举皇后的列号排列，可以用回溯算法
        难点在于如何判断对于给定排列，皇后之间是否相互攻击
        O(1)判断两个皇后是否相互攻击
        枚举方式已经保障每行（列）只有一个皇后，只需要检查斜方向，对于 ↗
        方向的格子，行号加列号是不变的。对于 ↖
        方向的格子，行号减列号是不变的。如果两个皇后，行号加列号相同，或者行号减列号相同，那么这两个皇后互相攻击。
        O(1)判断当前的皇后是否被之前的皇后攻击
        用两个哈希表p1,p2存储之前放置所有皇后的行号+列号，行号-列号，
        */
        p1.clear();
        p2.clear();

        vector<vector<int>> res;

        //特殊情况：n等于2时n皇后问题无解，convert 对空 res 也能安全返回空结果，故无需特判
        vector<int> cur;
        vector<bool> used(n, false);
        place(0, n, used, cur, res);
        return convert(res, n);
    }
};
```
