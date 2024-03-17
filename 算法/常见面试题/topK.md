## 题目描述
设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。

## 解法

### 排序
将数组排序，然后取前k个数即可。时间复杂度 $O(n\log n)$ 。
```cpp
vector<int> smallestK(vector<int>& arr, int k) {
    sort(arr.begin(), arr.end());
    return vector<int>(arr.cbegin(), arr.cbegin() + k);
}
```

### 局部排序
只对数组最小的k个数排序，其他的数字无需有序。可以使用冒泡来实现，时间复杂度 $O(nk)$ 。
```cpp
vector<int> smallestK(vector<int>& arr, int k) {
    for (int i = 0; i < k; i++) {
        for (auto it = arr.end() - 1; it != arr.begin(); it--) {
            if (*it < *(it - 1))
                swap(*it, *(it - 1));
        }
    }
    return vector<int>(arr.cbegin(), arr.cbegin() + k);
}
```

### 堆
维护一个大小为k的大顶堆。然后逐个检查余下的元素，如果比堆顶小，则弹出堆顶并插入该元素。每次调整需要 $O(\log k)$ 的时间，总时间复杂度 $O(n\log k)$ 。
```cpp
vector<int> smallestK(vector<int>& arr, int k) {
    if (k == 0)
        return vector<int>();
    priority_queue<int> pq(arr.cbegin(), arr.cbegin() + k);
    for (auto it = arr.cbegin() + k; it != arr.cend(); it++) {
        if (*it < pq.top()) {
            pq.pop();
            pq.push(*it);
        }
    }
    vector<int> v;
    while (!pq.empty()) {
        v.emplace_back(pq.top());
        pq.pop();
    }
    return v;
}
```

### 快速选择
类似于快速排序中的分治思想，每次随机选取一个数作为pivot，然后划分数组为两块，如果pivot的位置正好是k，则返回前k个数；否则如果前面的数量n1小于k，那么还需要寻找k-n1个数，否则在前面的n1个数中寻找k个数。时间复杂度 $O(n)$ 。
```cpp
int partition(vector<int>& nums, int l, int r) {
    int pivot = nums[r];
    int i = l - 1;
    for (int j = l; j <= r - 1; ++j) {
        if (nums[j] <= pivot) {
            i = i + 1;
            swap(nums[i], nums[j]);
        }
    }
    swap(nums[i + 1], nums[r]);
    return i + 1;
}

// 基于随机的划分
int randomized_partition(vector<int>& nums, int l, int r) {
    int i = rand() % (r - l + 1) + l;
    swap(nums[r], nums[i]);
    return partition(nums, l, r);
}

void randomized_selected(vector<int>& arr, int l, int r, int k) {
    if (l >= r) {
        return;
    }
    int pos = randomized_partition(arr, l, r);
    int num = pos - l + 1;
    if (k == num) {
        return;
    } else if (k < num) {
        randomized_selected(arr, l, pos - 1, k);
    } else {
        randomized_selected(arr, pos + 1, r, k - num);
    }
}
```