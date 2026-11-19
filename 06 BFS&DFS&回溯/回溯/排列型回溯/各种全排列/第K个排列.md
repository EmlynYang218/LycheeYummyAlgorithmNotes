[60. 排列序列](https://leetcode.cn/problems/permutation-sequence/)

给出集合 `[1,2,3,...,n]`，其所有元素共有 `n!` 种排列。

按大小顺序列出所有排列情况，并一一标记，当 `n = 3` 时, 所有排列如下：

1. `"123"`
2. `"132"`
3. `"213"`
4. `"231"`
5. `"312"`
6. `"321"`

给定 `n` 和 `k`，返回第 `k` 个排列。

**示例 1：**

**输入：**n = 3, k = 3
**输出：**"213"

**示例 2：**

**输入：**n = 4, k = 9
**输出：**"2314"

**示例 3：**

**输入：**n = 3, k = 1
**输出：**"123"

**提示：**

- `1 <= n <= 9`
- `1 <= k <= n!`

```cpp
static constexpr int factorials[10] = {1,   1,   2,    6,     24,
                                       120, 720, 5040, 40320, 362880};
static bool used[10] = {false};
static string ans;

class Solution {
public:
    void perm(string& pre, int n, int k) {
        if (pre.size() == n) {
            ans = pre;
            return;
        }

        int c = n - pre.size(); // 还需要填的元素个数

        for (int i = 1; i <= n; i++) {
            //不能填入重复元素
            if(used[i]){
                continue;
            }
            //还需要填c个元素时，填入每一个代表着(c-1)!个数
            if (k - factorials[c - 1] > 0) {
                k -= factorials[c - 1];
                continue;
            }else{
                //标记
                used[i] = true;
                //填数
                pre += (char)(i + '0');
                //排列
                perm(pre, n, k);
                break;
            }
        }
    }
    string getPermutation(int n, int k) {
        string s;
        //每次初始化used数组
        memset(used,0,sizeof(used));

        perm(s, n, k);
        return ans;
    }
};
```
