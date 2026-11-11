# [1036. 逃离大迷宫](https://leetcode.cn/problems/escape-a-large-maze/)

在一个 106 x 106 的网格中，每个网格上方格的坐标为 `(x, y)` 。

现在从源方格 `source = [sx, sy]` 开始出发，意图赶往目标方格 `target = [tx, ty]` 。数组 `blocked` 是封锁的方格列表，其中每个 `blocked[i] = [xi, yi]` 表示坐标为 `(xi, yi)` 的方格是禁止通行的。

每次移动，都可以走到网格中在四个方向上相邻的方格，只要该方格 **不** 在给出的封锁列表 `blocked` 上。同时，不允许走出网格。

只有在可以通过一系列的移动从源方格 `source` 到达目标方格 `target` 时才返回 `true`。否则，返回 `false`。

**示例 1：**

**输入：**blocked = [[0,1],[1,0]], source = [0,0], target = [0,2]
**输出：**false
**解释：**
从源方格无法到达目标方格，因为我们无法在网格中移动。
无法向北或者向东移动是因为方格禁止通行。
无法向南或者向西移动是因为不能走出网格。

**示例 2：**

**输入：**blocked = [], source = [0,0], target = [999999,999999]
**输出：**true
**解释：**
因为没有方格被封锁，所以一定可以到达目标方格。

**提示：**

- `0 <= blocked.length <= 200`
- `blocked[i].length == 2`
- `0 <= xi, yi < 106`
- `source.length == target.length == 2`
- `0 <= sx, sy, tx, ty < 106`
- `source != target`
- 题目数据保证 `source` 和 `target` 不在封锁列表内

```cpp
class Solution {
private:
    static constexpr unsigned long long MAX_X = 1e6;
    static constexpr unsigned long long MAX_Y = 1e6;
    static constexpr int moves[4][2] = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}};

public:
    bool isEscapePossible(vector<vector<int>>& blocked, vector<int>& source,
                          vector<int>& target) {
        int n = blocked.size();
        int limit = n * (n - 1) / 2;

        /*网格图大，不能DFS/BFS全图，观察到0 <= blocked.length <=
          200长度短，从这里入手
          blocked很少，相当于全图大部分是空地，只有很少的地方有障碍物，则研究障碍物比直接遍历全图自然
          想到障碍物可能围住起点或者终点
          不能到达target等价于 source被围住 或 target被围住
          巧妙判断方法：设n=blocked.length，n个块结合边界最多围住n*(n-1)/2个块，此时排成斜对角线。
          从起点开始DFS并计数连通块大小，当块数≤
          n*(n-1)/2且找到target，返回true，否则标记起点被围住
          块数＞n*(n-1)/2，起点没被围住。再从终点开始DFS，计数连通块大小，块数＞n*(n-1)/2，则终点没被围住
          返回 起点没被围住&&终点没被围住
        */
        // 将blocked哈希化，快速判断一个点是不是障碍物
        // 对于本题的数据量，建表的收益大于建表消耗
        // 技巧：将坐标通过x*MAX_X+y哈希化
        unordered_set<unsigned long long> us;
        for (auto b : blocked) {
            us.insert(b[0] * MAX_X + b[1]);
        }

        bool source_blocked = false;
        bool target_blocked = false;

        stack<unsigned long long> st;
        unordered_set<unsigned long long> visited;

        st.push(source[0] * MAX_X + source[1]);
        visited.insert(source[0] * MAX_X + source[1]);
        while (!st.empty()) {
            // 拿出待访问元素
            unsigned long long uxy = st.top();
            int x = uxy / MAX_X;
            int y = uxy % MAX_X;
            st.pop();

            //剪枝在循环体内部，否则对于没被围上的情况会错误地遍历全图
            if(visited.size()>limit){
                break;
            }

            if (x == target[0] && y == target[1]) {
                return true;
            }
            // 依次访问四个邻居
            for (int i = 0; i < 4; i++) {
                int nx = x + moves[i][0];
                int ny = y + moves[i][1];

                if (nx < 0 || ny < 0 || nx >= MAX_X || ny >= MAX_Y) {
                    continue;
                }
                unsigned long long unxny = nx * MAX_X + ny;
                if (us.count(unxny) || visited.count(unxny)) {
                    continue;
                }
                // 这个邻居能访问
                visited.insert(unxny);
                st.push(unxny);
            }
        }
        source_blocked = (visited.size() <= limit);

        st = {};
        visited.clear();

        st.push(target[0] * MAX_X + target[1]);
        visited.insert(target[0] * MAX_X + target[1]);
        while (!st.empty()) {
            // 拿出待访问元素
            unsigned long long uxy = st.top();
            int x = uxy / MAX_X;
            int y = uxy % MAX_X;
            st.pop();

            //剪枝在循环体内部，否则对于没被围上的情况会错误地遍历全图
            if(visited.size()>limit){
                break;
            }

            if (x == source[0] && y == source[1]) {
                return true;
            }
            // 依次访问四个邻居
            for (int i = 0; i < 4; i++) {
                int nx = x + moves[i][0];
                int ny = y + moves[i][1];

                if (nx < 0 || ny < 0 || nx >= MAX_X || ny >= MAX_Y) {
                    continue;
                }
                unsigned long long unxny = nx * MAX_X + ny;
                if (us.count(unxny) || visited.count(unxny)) {
                    continue;
                }
                // 这个邻居能访问
                visited.insert(unxny);
                st.push(unxny);
            }
        }
        target_blocked = (visited.size() <= limit);
        return (!source_blocked) && (!target_blocked);
    }
};
```



# Insight: 观察整个大图，只有少数关键点，“正难则反”研究起点和重点是否被封锁。
