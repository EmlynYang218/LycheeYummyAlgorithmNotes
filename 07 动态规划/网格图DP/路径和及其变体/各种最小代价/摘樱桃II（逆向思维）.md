[1463. 摘樱桃 II](https://leetcode.cn/problems/cherry-pickup-ii/)

给你一个 `rows x cols` 的矩阵 `grid` 来表示一块樱桃地。 `grid` 中每个格子的数字表示你能获得的樱桃数目。

你有两个机器人帮你收集樱桃，机器人 1 从左上角格子 `(0,0)` 出发，机器人 2 从右上角格子 `(0, cols-1)` 出发。

请你按照如下规则，返回两个机器人能收集的最多樱桃数目：

- 从格子 `(i,j)` 出发，机器人可以移动到格子 `(i+1, j-1)`，`(i+1, j)` 或者 `(i+1, j+1)` 。
- 当一个机器人经过某个格子时，它会把该格子内所有的樱桃都摘走，然后这个位置会变成空格子，即没有樱桃的格子。
- 当两个机器人同时到达同一个格子时，它们中只有一个可以摘到樱桃。
- 两个机器人在任意时刻都不能移动到 `grid` 外面。
- 两个机器人最后都要到达 `grid` 最底下一行。

**示例 1：**

**![](https://assets.leetcode.cn/aliyun-lc-upload/uploads/2020/05/30/sample_1_1802.png)**

**输入：**grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
**输出：**24
**解释：**机器人 1 和机器人 2 的路径在上图中分别用绿色和蓝色表示。
机器人 1 摘的樱桃数目为 (3 + 2 + 5 + 2) = 12 。
机器人 2 摘的樱桃数目为 (1 + 5 + 5 + 1) = 12 。
樱桃总数为： 12 + 12 = 24 。

**示例 2：**

**![](https://assets.leetcode.cn/aliyun-lc-upload/uploads/2020/05/30/sample_2_1802.png)**

**输入：**grid = [[1,0,0,0,0,0,1],[2,0,0,0,0,3,0],[2,0,9,0,0,0,0],[0,3,0,5,4,0,0],[1,0,2,3,0,0,6]]
**输出：**28
**解释：**机器人 1 和机器人 2 的路径在上图中分别用绿色和蓝色表示。
机器人 1 摘的樱桃数目为 (1 + 9 + 5 + 2) = 17 。
机器人 2 摘的樱桃数目为 (1 + 3 + 4 + 3) = 11 。
樱桃总数为： 17 + 11 = 28 。

**示例 3：**

**输入：**grid = [[1,0,0,3],[0,0,0,3],[0,0,3,3],[9,0,3,3]]
**输出：**22

**示例 4：**

**输入：**grid = [[1,1],[1,1]]
**输出：**4

**提示：**

- `rows == grid.length`
- `cols == grid[i].length`
- `2 <= rows, cols <= 70`
- `0 <= grid[i][j] <= 100`

```cpp
static constexpr int MAX_ROWS = 74;
static constexpr int MAX_COLS = 74;
static constexpr int INF = 0x3f3f3f3f;
static int dp[MAX_ROWS][MAX_COLS][MAX_COLS];

class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        /*观察到：两个机器人始终位于同一行
        状态设计：
        设dp[i][a][b] 表示两个机器人位于第i行，甲列号a
        乙列号b时所能得到的最多樱桃 答案：max(dp[rows-1]) 状态转移： dp[i][a][b]
        = max(前驱) + add 由于甲乙均有3种移动方向，一共3*3=9个候选前驱
        在坐标允许的范围内：
        best_pre =
        max({dp[i-1][a+1][b],dp[i-1][a-1][b],dp[i-1][a][b],dp[i-1][a+1][b+1]...)
        add += grid[i][a]
        if(a!=b)
        add += grid[i][b]
        边界处理：
        1）参数范围：
        0<= i <= rows-1
        0<=a<=cols-1
        0<=b<=cols-1
        2）边界位置：
        不可达状态设置为-1
        起点还原为 dp[0][0][cols-1] = grid[0][0] + grid[0][cols-1];
        */

        int rows = grid.size();
        int cols = grid[0].size();
        // 初始化
        for (int i = 0; i < MAX_ROWS; i++) {
            for (int a = 0; a < MAX_COLS; a++) {
                for (int b = 0; b < MAX_COLS; b++) {
                    dp[i][a][b] = -1;
                }
            }
        }
        dp[0][0][cols - 1] = grid[0][0] + grid[0][cols - 1];

        //递推
        for (int i = 1; i <= rows - 1; i++) {
            for (int a = 0; a <= cols - 1; a++) {
                for (int b = 0; b <= cols - 1; b++) {
                    //循环遍历9个前驱
                    int best_pre = -1;
                    for (int da = a - 1; da <= a + 1; da++) {
                        for (int db = b - 1; db <= b + 1; db++) {
                            //坐标不能超范围
                            if (0 <= da && da <= cols - 1 && 0 <= db &&
                                db <= cols - 1 && dp[i - 1][da][db] >= 0) {
                                best_pre = max(best_pre, dp[i - 1][da][db]);
                            }
                        }
                    }

                    int add = grid[i][a];
                    if (a != b) {
                        add += grid[i][b];
                    }

                    //不能从不可达状态转移，加入特判
                    if (best_pre >= 0) {
                        dp[i][a][b] = best_pre + add;
                    }
                }
            }
        }

        int ans = -1;
        for (int a = 0; a <= cols - 1; a++) {
            for (int b = 0; b <= cols - 1; b++) {
                ans = max(ans, dp[rows - 1][a][b]);
            }
        }
        return ans;
    }
};
```
