# 二分查找：`lower_bound`

在非递减数组中，返回第一个满足 `a[i] >= target` 的位置；如果不存在，返回 `a.size()`。下面使用左闭右开区间 `[left, right)`。

```cpp
int lowerBound(const vector<int>& a, int target) {
    int left = 0, right = (int)a.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (a[mid] < target)
            left = mid + 1;
        else
            right = mid;
    }
    return left;
}
```

循环不变量：`a[0..left)` 中元素都小于 `target`，`a[right..n)` 中元素都不小于 `target`。每轮都要让区间严格缩小，时间复杂度 `O(log n)`。

常见变体：第一个 `> target`（`upper_bound`）、最后一个满足条件的位置，以及对答案空间二分。
