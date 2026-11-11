# [题目: K进制数的子序列](https://programming.pku.edu.cn/probset/6145fa4759d340339126b3fa75d7da7e/4eaa51ab16cb4142bf97563d7112831d/)

#### 题目描述

任意给定一个k（其中，k 大于1小于10）进制的数（不超过30位，且第一位不为0），以此为基点，按增1的方式连续输出后面的m个k进制数，其间以逗号间隔,这里，m为正整数。输出的每个数第一位不能为0，每行输出5个数，最后一行可以少于5个。

例如，给定12位的5进制数：321234232132，后面的6个数依次为：

321234232133,321234232134,321234232140,321234232141,321234232142,321234232143

如果输出6个数，则应该是：

321234232133,321234232134,321234232140,321234232141,321234232142  
321234232143

#### 关于输入

输入三个数k,m,和k进制数，三个数之间以逗号间隔，k进制数不超过30位，m为正整数。  

#### 关于输出

输出后续的m个数，每行5个数（每行的最后一个数之后没有逗号），最后一行可以少于5个数。

#### 例子输入

5,6,321234232132

#### 例子输出

321234232133,321234232134,321234232140,321234232141,321234232142
321234232143

```cpp
#include <iostream>
#include <string>
using namespace std;

string add_one(int k, string& num) {
    int n = num.size();
    num[n - 1] += 1;
    for (int i = n - 1; i >= 1; i--) {
        if ((num[i] - '0') >= k) {
            num[i] = (num[i] - '0') % k + '0';
            num[i - 1] = (num[i - 1] + 1);
        }
    }
    if (num[0] - '0' >= k) {
        num[0] = (num[0] - '0') % k + '0';
        string temp = "1"+ num;
        num = temp;
    }
    return num;
}

int main() {
    int k = 0, m = 0;
    string s;
    cin >> k;
    cin.ignore();
    cin >> m;
    cin.ignore();
    cin >> s;

    string cur;
    for (int i = 0; i < m;) {
        for (int j = 0; i < m && j < 4; j++) {
            cout << add_one(k, s);
            if(m-i>1){
                cout<<',';
            }
            i++;
        }
        if (i >= m) {
            break;
        }
        cout << add_one(k, s) << '\n';
        i++;
    }

    return 0;
}
```
