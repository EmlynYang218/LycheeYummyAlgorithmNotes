# 题目 小游戏

来源：[题目: 小游戏](https://programming.pku.edu.cn/probset/7854ebcf1a8e4dbb98b3c87b684492a2/6bf6c168a268492d83740cfeaf60e968/)

#### 题目描述

   一天早上，你起床的时候想：“我编程序这么牛，为什么不能靠这个挣点银子呢？”因此你决定编写一个小游戏。  
   游戏在一个分割成w * h个长方格子的矩形板上进行。如图所示，每个长方格子上可以有一张游戏卡片，也可以没有。当下面的情况满足时，我们认为两个游戏卡片之间有一条路径相连：  
   路径只包含水平或者竖直的直线段。路径不能穿过别的游戏卡片。但是允许路径临时离开矩形板。  
   下面是一个例子：

![](https://programming.pku.edu.cn/probset/7854ebcf1a8e4dbb98b3c87b684492a2/6bf6c168a268492d83740cfeaf60e968/1137_1.jpg)

这里在(1, 3)和(4, 4)处的游戏卡片是可以相连的。而在 (2, 3) 和 (3, 4) 处的游戏卡是不相连的，因为连接他们的每条路径都必须要穿过别的游戏卡片。  
   你现在要在小游戏里面判断是否存在一条满足题意的路径能连接给定的两个游戏卡片。

#### 关于输入

   输入包括多组数据。一个矩形板对应一组数据。每组数据的第一行包括两个整数w和h (1 <= w, h <= 75)，分别表示矩形板的宽度和高度。下面的h行，每行包括w个字符，表示矩形板上的游戏卡片分布情况。使用‘X’表示这个地方有一个游戏卡片；使用空格表示这个地方没有游戏卡片。之后的若干行，每行包括4个整数x1, y1, x2, y2 (1 <= x1, x2 <= w, 1 <= y1, y2 <= h)，表示两个卡片在矩形板上的位置（注意：矩形板左上角的坐标是(1, 1)）。如果一行上有4个0，则表示这组测试数据的结束。测试数据保证这两个游戏卡片所处的位置是不相同的。  
   如果一行上有2个0，那么表示所有的输入结束了。

#### 关于输出

   对每一个矩形板，输出一行“Board #n:”，这里n是输入数据的编号（从1开始）。然后对于每一对需要测试的游戏卡片输出一行。这一行的开头是“Pair m: ”，这里m是测试卡片的编号（对每个矩形板，编号都从1开始）。接下来，如果可以相连，则找到连接这两个卡片的所有路径中包括线段数最少的路径，输出“k segments.”，这里k即是找到的最优路径中包括的线段的数目；如果不能相连，则输出“impossible.”。

   注意：每个矩形板之后须输出一个空行。

#### 例子输入

```
5 4
XXXXX
X   X
XXX X
 XXX 
2 3 5 3
1 3 4 4
2 3 3 4
0 0 0 0
4 4
XXXX
XXXX
XXXX
XXXX
1 1 2 1
2 2 3 2
1 1 3 1
3 4 4 3
2 1 2 4
1 1 2 2
0 0 0 0
0 0
```

#### 例子输出

```
Board #1:
Pair 1: 4 segments.
Pair 2: 3 segments.
Pair 3: impossible.
Board #2:
Pair 1: 1 segments.
Pair 2: 1 segments.
Pair 3: 3 segments.
Pair 4: 4 segments.
Pair 5: 5 segments.
Pair 6: impossible.
```

#### 代码：

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
  →w
↓
h

  →x
↓
y
*/

/*BFS通用模板：
1. dist 全部初始化为 -1（未访问）
2. dist[start] = 0;  q.push(start);
3. while (!q.empty()):
4.     u = q.front(); q.pop();
5.     for each neighbor v of u:
           if (v 越界/是墙/已访问) continue;
           dist[v] = dist[u] + 1;
           q.push(v);

*/

//本题要求的是最少线段数而非最少格子数，因此把线段数作为bfs层数
string bfs(const vector<string>& board, int x1, int y1, int x2, int y2) {
    int H = board.size();       // h+2
    int W = board[0].size();    // w+2
    const int moves[4][2] = {{0,1},{0,-1},{1,0},{-1,0}};

    // dist[y][x] = 最少线段数，-1 未访问
    vector<vector<int>> dist(H, vector<int>(W, -1));

    //待访问点组成的队列
    queue<pair<int,int>> q;

    //初始化起点距离为0
    dist[y1][x1] = 0;

    //起点入队
    q.push({x1, y1});

    while (!q.empty()) {
        //访问pair元素的简化写法
        //当前访问点出队
        auto [x, y] = q.front();
        q.pop();

        // 弹出即判断：BFS 保证第一次弹出终点时段数最少
        if (x == x2 && y == y2) {
            return to_string(dist[y][x]) + " segments.";
        }

        int nd = dist[y][x] + 1;  // 转弯后新段的段数

        for (int d = 0; d < 4; d++) {
            int nx = x + moves[d][0];
            int ny = y + moves[d][1];

            // 沿该方向一路延伸
            while (true) {
                // 越界：停
                if (nx < 0 || nx >= W || ny < 0 || ny >= H) break;

                // 终点：特判，终点本身是 X，防止终点被当作障碍物绕过
                if (nx == x2 && ny == y2) {
                    //dist[ny][nx] == -1 终点没被到达过
                    //nd < dist[ny][nx] 保险起见：即使到达过，这条路更短
                    if (dist[ny][nx] == -1 || nd < dist[ny][nx]) {
                        dist[ny][nx] = nd;
                        q.push({nx, ny});
                    }
                    break;  // 终点是 X，不能穿过，停止延伸
                }

                // 遇到卡片：停
                if (board[ny][nx] == 'X') break;

                // 空格：更新段数并继续延伸
                if (dist[ny][nx] == -1) {
                    dist[ny][nx] = nd;
                    // 入队 ≠ 延伸：push 只是登记"将来从这里转弯"，
                    // 当前这次直线延伸靠下面的 nx += 继续推进，两者互不冲突。
                    q.push({nx, ny});
                } else if (dist[ny][nx] < nd) {
                    // 已有更少段数到达，不必再延伸（更优的会从那边继续）
                    break;
                }
                // 若 dist[ny][nx] == nd 或 > nd，继续延伸即可
                // = nd 时延伸出去也是 nd+1 的新段，等价；> nd 时更新
                nx += moves[d][0];
                ny += moves[d][1];
            }
        }
    }

    return "impossible.";
}

int main()
{
    int w, h,x1,y1,x2,y2;
    int group_cnt = 0,pair_cnt = 0;
    while (cin >> w >> h &&!(w==0&&h==0))
    {
        //cin之后需要吃掉w h之后的\n
        cin.ignore();
        group_cnt++;
        cout<<"Board #"<<group_cnt<<":\n";

        vector<string> board(h+2);
        board[0] = string(w+2,' ');
        board[h+1] = string(w+2,' ');
        //没必要离开矩形版外侧宽1单位的一圈，添加一圈空地。
        //且这圈空地的添加解决了左上角坐标为（1，1）带来的下标不统一问题。
        for (int i = 1; i <= h; i++)
        {
            //getline不保留\n
            getline(cin, board[i]);
            board[i]=' '+board[i]+' ';
        }

        pair_cnt = 0;
        while(cin>>x1>>y1>>x2>>y2&&!(x1==0&&y1==0&&x2==0&&y2==0)){
            pair_cnt++;
            cout<<"Pair "<<pair_cnt<<": "<< bfs(board,x1,y1,x2,y2)<<"\n";
        }
        //输出一个空行
        cout<<'\n';
    }

    return 0;
}
```



#### Insight1：

本题和一般bfs模板的区别在于，不是求“路径经过的最少格子数”，而是求“路径最少转弯数“，所以我们按照转弯数划分BFS层，每次沿一个方向不停延伸，这些延伸对应的转弯数不变，只有转弯时，转弯数才增加。



> 把"段数"作为 BFS 的层。从当前格子 `(x,y)`（段数 `d`）出发，沿 4 个方向各走一条**直线**；这条直线上所有新格子的段数都是 `d+1`（因为它们是"从 `(x,y)` 出发的第 `d+1` 段"）。同一条直线上继续走不增加段数；只有"从某个格子换方向"才开启新的一段，段数 +1。
