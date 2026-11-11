# [题目: 1139 棋盘问题](https://programming.pku.edu.cn/probset/7854ebcf1a8e4dbb98b3c87b684492a2/cd345d1df70c44c5b32b81ea8b51fd6a/)

#### 题目描述

在一个给定形状的棋盘（形状可能是不规则的）上面摆放棋子，棋子没有区别。要求摆放时任意的两个棋子不能放在棋盘中的同一行或者同一列，请编程求解对于给定形状和大小的棋盘，摆放k个棋子的所有可行的摆放方案C。

#### 关于输入

输入含有多组测试数据。  
每组数据的第一行是两个正整数，n k，用一个空格隔开，表示了将在一个n*n的矩阵内描述棋盘，以及摆放棋子的数目。 n <= 8 , k <= n  
当为-1 -1时表示输入结束。  
随后的n行描述了棋盘的形状：每行有n个字符，其中 # 表示棋盘区域， . 表示空白区域（数据保证不出现多余的空白行或者空白列）。

#### 关于输出

对于每一组数据，给出一行输出，输出摆放的方案数目C （数据保证C<2^31）。

#### 例子输入

2 1
#.
.#
4 4
...#
..#.
.#..
#...
-1 -1

#### 例子输出

2
1

#### 提示信息

1321

```cpp
#include <iostream>
#include <cstring>
using namespace std;

int n, k, ans;
char board[10][10];
bool colUsed[10];

void dfs(int row, int placed) {
    if (placed == k) { ans++; return; }
    if (row >= n) return;
    dfs(row + 1, placed);                      // 这一行不放
    for (int c = 0; c < n; c++) {              // 这一行放一个
        if (board[row][c] == '#' && !colUsed[c]) {
            colUsed[c] = true;
            dfs(row + 1, placed + 1);
            colUsed[c] = false;
        }
    }
}

int main() {
    while (cin >> n >> k) {
        if (n == -1 && k == -1) break;
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                cin >> board[i][j];
        ans = 0;
        dfs(0, 0);
        cout << ans << '\n';
    }
    return 0;
}
```



## Insight1:

这相当于固定行号枚举列号的排列，和皇后问题类似。
