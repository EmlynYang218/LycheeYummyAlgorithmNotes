# [题目: 合唱队形](https://programming.pku.edu.cn/probset/ef7f07829a4440508b840298e125d1de/efbcc6db351e404ab6d2ce2cdf4f5dc3/)

#### 题目描述

N位同学站成一排，音乐老师要请其中的(N-K)位同学出列，使得剩下的K位同学不交换位置就能排成合唱队形。  
合唱队形是指这样的一种队形：设K位同学从左到右依次编号为1, 2, …, K，他们的身高分别为T1, T2, …, TK，则他们的身高满足T1 < T2 < … < Ti , Ti > Ti+1 > … > TK (1 <= i <= K)。  
你的任务是，已知所有N位同学的身高，计算最少需要几位同学出列，可以使得剩下的同学排成合唱队形。

#### 关于输入

输入的第一行是一个整数N（2 <= N <= 100），表示同学的总数。第一行有n个整数，用空格分隔，第i个整数Ti（130 <= Ti <= 230）是第i位同学的身高（厘米）。

#### 关于输出

输出包括一行，这一行只包含一个整数，就是最少需要几位同学出列。

#### 例子输入

8
186 186 150 200 160 130 197 220

#### 例子输出

4

```cpp
#include <bits/stdc++.h>
using namespace std;

static int nums[104];
static int a[104];
static int b[104];

int main()
{
    /*
    正反用两次LIS
    a[i]表示从0开始以下标i结尾的最长严格递增序列长度，需要出列i+1-a[i]人
    b[i]表示从n-1开始以下标i结尾的最长严格递增子序列长度，需要出列(n-1-i)-b[i]人
    最后取min(i+1-a[i]+(n-1-i)-b[i])+1=min(n-a[i]-b[i])+1，i=0...n-1
    这里的+1是因为nums[i]同时属于两个最长严格递增子序列长度被算了2次。
    */

    int n;
    cin >> n;
    for (int i = 0; i < n; i++)
    {
        cin >> nums[i];
    }

    //从左向右LIS
    memset(a, 0, sizeof(a));
    a[0] = 1;
    for (int i = 1; i < n; i++)
    {
        for (int j = 0; j < i; j++)
        {
            if (nums[j] < nums[i])
            {
                a[i] = max(a[i], a[j]);
            }
        }
        a[i] += 1;
    }

    //从右向左LIS
    memset(b, 0, sizeof(b));
    b[n - 1] = 1;
    for (int i = n - 2; i >= 0; i--)
    {
        for (int j = n - 1; j > i; j--)
        {
            if (nums[j] < nums[i])
            {
                b[i] = max(b[i], b[j]);
            }
        }
        b[i] += 1;
    }

    int ans = INT_MAX;

    for(int i = 0; i < n; i++){
        ans = min(ans,n-a[i]-b[i]);
    }

    //nums[i]被算了两次
    ans ++;

    cout<<ans;
    return 0;
}

```
