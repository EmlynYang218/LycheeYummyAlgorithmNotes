```cpp
#include <iostream>

#include <algorithm>

using namespace std;

int main()

{

    int a[8] = {6,5,14,23,2,9,87,10 };

    sort(a+1,a+7,[](const int& a, const int& b){ return a > b; });

    for(int i = 0;i < 8; ++i)

        cout << a[i] << "," ;

    return 0;

}
```

提示：除第一个和最后一个数外，其他都从大到小排序

```cpp
#include <iostream>
#include <algorithm>
#include <cmath>
using namespace std;

struct Point{
    int x;
    int y;
};

//返回true表示不需要交换
struct Rule1{
    bool operator()(const int& a, const int& b)const{
        if(a%10!=b%10) return a%10<b%10;
        return a>b;
   }
};


struct Rule2{
    bool operator()(const Point& a, const Point& b)const{
        int da = a.x*a.x+a.y*a.y;
        int db = b.x*b.x+b.y*b.y;
        if(da!=db) return da<db;
        if(a.x!=b.x) return a.x<b.x;
        if(a.y!=b.y) return a.y<b.y;
        return false;

    }
};



int main()
{
    int a[8] = {6,5,55,23,3,9,87,10 };
    sort(a,a+8,Rule1());
    for(int i = 0;i < 8; ++i)
        cout << a[i] << "," ; 
    cout << endl;
    Point ps[8] = {{1,0},{0,1},{0,-1},{-1,0},{1,-1},{1,1},{2,0},{-2,0} } ;
    sort(ps,ps+8,Rule2());
    for(int i = 0;i < 8; ++i)
        cout << "(" << ps[i].x << "," << ps[i].y << ")"; 
    return 0;
}
```

函数对象写法

Lambda表达式写法

```cpp
        // logs 按时间排序，滑动窗口需要时间单调
        sort(logs.begin(), logs.end(), [](const vector<int>& a, const vector<int>& b){
            return a[1] < b[1];
        });
```
