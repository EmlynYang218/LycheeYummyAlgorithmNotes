# [ 最长公共子序列](https://programming.pku.edu.cn/probset/ef7f07829a4440508b840298e125d1de/2f4ed8d1ad074f39abe2892183e2f6fa/)

#### 题目描述

我们称一个字符的数组S为一个序列。对于另外一个字符数组Z,如果满足以下条件，则称Z是S的一个子序列：（1）Z中的每个元素都是S中的元素（2）Z中元素的顺序与在S中的顺序一致。例如：当S = (E,R,C,D,F,A,K)时，（E，C，F）和（E，R）等等都是它的子序列。而（R，E）则不是。  
现在我们给定两个序列，求它们最长的公共子序列的长度。  

#### 关于输入

一共两行，分别输入两个序列

#### 关于输出

一行，输出最长公共子序列的长度。

#### 例子输入

ABCBDAB
BDCABA

#### 例子输出

4

```cpp
#include <bits/stdc++.h>
using namespace std;

static constexpr int MAX_S_LEN = 104; // 题目没有给出S的长度
static int dp[MAX_S_LEN][MAX_S_LEN];
int LCS(const string &a, const string &b)
{
    /*
    dp[i][j]表示a的前i个字符与j的前j个字符的最长公共子序列长度
                                该字符相等，直接+1       该字符不等    该字符与前面的重合
    dp[i][j] = (a[i-1]==b[j-1])? dp[i-1][j-1] +1: max(dp[i-1][j-1],dp[i][j-1],dp[i-1][j]);
    边界条件：dp[0][0] = 0; dp[i][0] = 0; dp[0][j] = 0;
    */
    int as = a.size(), bs = b.size();
    dp[0][0] = 0;
    for (int i = 1; i <= as; i++)
    {
        dp[i][0] = 0;
    }
    for (int j = 1; j <= bs; j++)
    {
        dp[0][j] = 0;
    }
    for (int i = 1; i <= as; i++)
    {
        for (int j = 1; j <= bs; j++)
        {
            dp[i][j] = (a[i-1]==b[j-1])? dp[i-1][j-1] +1: max(dp[i-1][j-1],max(dp[i][j-1],dp[i-1][j]));
        }
    }
    return dp[as][bs];
}
int main()
{
    string a, b;
    cin >> a >> b;
    cout<<LCS(a,b);
    return 0;
}

```
```
