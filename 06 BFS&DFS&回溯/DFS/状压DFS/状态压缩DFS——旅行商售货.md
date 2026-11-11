## 题目 [旅行售货商问题](https://programming.pku.edu.cn/probset/7854ebcf1a8e4dbb98b3c87b684492a2/0faad12caf7647fdb7ea3464fa950377/)

来源：chg

#### 题目描述

一个国家有 n 个城市，每两个城市之间都开设有航班，从城市 i 到城市 j 的航班价格为 cost[i, j] ，而且往、返航班的价格相同。

售货商要从一个城市出发，途径每个城市 1 次（且每个城市只能经过 1 次），最终返回出发地，而且他的交通工具只有航班，请求出他旅行的最小开销。

#### 关于输入

输入的第 1 行是一个正整数 n （3 <= n <= 15）
然后有 n 行，每行有 n 个正整数，构成一个 n * n 的矩阵，矩阵的第 i 行第 j 列为城市 i 到城市 j 的航班价格。

#### 关于输出

输出数据为一个正整数 m，表示旅行售货商的最小开销

#### 例子输入

```
4
0 4 1 3
4 0 2 1
1 2 0 5
3 1 5 0
```

#### 例子输出

```
7
```

#### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;

/*旅行商问题dfs状态设计：
对于一个子问题，我们需要：
1）已经去过哪些城市 设这个状态为mask
2）当前在哪        设这个位置为v
对于mask，每一个城市都只有“访问过”和“没访问过”两个状态，相对于开超多维数组，
可以采用二进制掩码的方式。每个二进制位代表一个城市
这里城市下标是0-base，1<<n -1 也即全集 
比如说10011代表第0，1，4个城市访问过。
对于本题n最大为15，2^15在int范围内

集合与位运算：https://leetcode.cn/discuss/post/3571304/cong-ji-he-lun-dao-wei-yun-suan-chang-ji-enve/
*/


int dfs(int mask, int v,int n, vector<vector<int>>& memo,const vector<vector<int>>& costs){
    //访问过所有城市，飞回起点
    if(mask == (1<<n) - 1){
        return costs[v][0];
    }
    //引用：直接操作表
    int& res = memo[mask][v];
    if(res!=-1){
        return res;
    }
    res = INT_MAX;
    //枚举下一座城市
    for(int u = 0; u < n; u++){
        //这个城市没访问过
        if(!(mask>>u&1)){
            //访问这个城市，也即mask | 1<< u
            res = min(res,costs[v][u] + dfs(mask | 1<< u,u,n,memo,costs));
        }
    }
    return res;
}
int main(){
    int n;
    cin >> n;
    vector<vector<int>> costs(n,vector<int>(n,0));
    //memo中的值代表这个状态对应的价格
    vector<vector<int>> memo((1<<n),vector<int>(n,-1));
    for(int i = 0; i < n; i++){
        for(int j = 0; j < n; j++){
            cin >> costs[i][j];
        }
    }
    cout<<dfs(1,0,n,memo,costs);
}
```
