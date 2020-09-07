## problem1:ok:
给你一个仅包含小写英文字母和 '?' 字符的字符串 s<var> </var>，请你将所有的 '?' 转换为若干小写字母，使最终的字符串不包含任何 连续重复 的字符。

注意：你 不能 修改非 '?' 字符。

题目测试用例保证 除 '?' 字符 之外，不存在连续重复的字符。

在完成所有转换（可能无需转换）后返回最终的字符串。如果有多个解决方案，请返回其中任何一个。可以证明，在给定的约束条件下，答案总是存在的。

> 分析：这是一个贪心题,只需要保证当前位置的字符和它周围的字符串不同即可，注意处理下开头和结尾的字符即可。

```cpp
 class Solution {
public:
    string modifyString(string s) {
        int n = s.size();
        if (n == 1) {
            if (s[0] == '?') return "a";
            else return s;
        }
        if (s[0] == '?') {
            if (s[1] == 'a') {
                s[0] = 'b';
            }else{
                s[0] = 'a';
            }
        }
        if (s[n - 1] == '?') {
            if (s[n - 2] == 'a') {
                s[n - 1] = 'b';
            }else{
                s[n - 1] = 'a';
            }
        }
        for (int i = 1; i < n - 1; i++) {
            if (s[i] == '?') {
                char l = s[i - 1];
                char r = s[i + 1];
                for (int j = 'a'; j < 'z' + 1; j++) {
                    if (j != l && j != r) {
                        s[i] = j;
                    }
                }
            }
        }
        return s;
    }
};       
```

## problem2
给你两个整数数组 nums1 和 nums2 ，请你返回根据以下规则形成的三元组的数目（类型 1 和类型 2 ）：

类型 1：三元组 (i, j, k) ，如果 nums1[i]2 == nums2[j] * nums2[k] 其中 0 <= i < nums1.length 且 0 <= j < k < nums2.length
类型 2：三元组 (i, j, k) ，如果 nums2[i]2 == nums1[j] * nums1[k] 其中 0 <= i < nums2.length 且 0 <= j < k < nums1.length


> 分析：这道题比赛的时候没有做出来，数据的范围时1000以内，首先尝试了下暴力，发现超时，然后又联想到第一题的两数之和，可以通过把nums2或者nums1中的数放入哈希表中，在必要的时候进行搜索。用哈希表保存数据有两种方式1.遍历时存储；2.把数组中的元素填入哈希表，遍历时进行删减。

比较下上面两种存储数据的差异：
- 首先，第一种方式，一边遍历一边存储的方式，考虑的是在**当前位置之前**（不包括当前位置）的数据
- 然后，第二种方式，先存储，后删减，考虑的是**当前位置之后**（不包括当前位置）的数据

```cpp
#define LL long long
class Solution {
public:
    int numTriplets(vector<int>& nums1, vector<int>& nums2) {
        int ans = 0;
        int m = nums1.size(),n = nums2.size();
        unordered_map<int,int> hash1;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
               LL u = (LL)nums2[j]*nums2[j];
               if (u%nums1[i] == 0 && hash1.count(u/nums1[i])) {
                   ans += hash1[u/nums1[i]];
               }
            }
            ++hash1[nums1[i]];
        }
        unordered_map<int,int> hash2;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                LL u = (LL)nums1[j]*nums1[j];
                if (u%nums2[i] == 0 && hash2.count(u/nums2[i])) {
                    ans += hash2[u/nums2[i]];
                }
            }
            ++hash2[nums2[i]];
        }
        return ans;
    }
};
```

```cpp
#define LL long long
class Solution{
public:
    int numTriplet(vector<int>& nums1,vector<int>& nums2) {
        int ans = 0;
        int m = nums1.size(),n = nums2.size();
        unordered_map<int,int> hash1,hash2;
        for (int i = 0; i < n; i++) {
            hash2[nums2[i]]++;
        }
        for (int i = 0; i < m; i++) {
            hash1[nums1[i]]++;
        }
        for (int i = 0; i < m; i++) {
            unordered_map<int,int> tmp = hash2;
            LL u = (LL)nums1[i]*nums1[i];
            for (int j = 0; j < n; j++) {
                if (u%nums2[j] == 0 && tmp.count(u/nums2[j])) {
                    ans += tmp[u/nums2[j]];
                }
                --tmp[nums2[j]];
            }
        }
        for (int i = 0; i < n; i++) {
            unordered_map<int,int> tmp = hash1;
            LL u = (LL)nums2[i]*nums2[i];
            for (int j = 0; j < m; ++j) {
                if (u%nums1[j] == 0 && tmp.count(u/nums1[j])) {
                    ans += tmp[u/nums1[j]];
                }
                --tmp[nums1[j]];
            }
        }
        return ans;
    }
};
```

Todo
> 分析：递归

## problem3:ok:
给你一个字符串 s 和一个整数数组 cost ，其中 cost[i] 是从 s 中删除字符 i 的代价。

返回使字符串任意相邻两个字母不相同的最小删除成本。

请注意，删除一个字符后，删除其他字符的成本不会改变。


> 分析：这题是一道dp和贪心都可解的题，但是我虽然用的是贪心，但是考虑的太复杂了。我首先是将有重复的地方标记出来（用不同的值），记录在一个数组中。然后遍历整个数组，遇到重复的地方就把最大值留下来，其他重复的元素删掉。

```cpp
class Solution {
public:
    int minCost(string s, vector<int>& cost) {
        int n = s.size();
        vector<int> mark(n,0);
        int index = 1;
        bool m = false;
        for (int i = 1; i < n; i++) {
            if (s[i] == s[i - 1]) {
                m = true;
                mark[i] = index;
                mark[i - 1] = index;
            }else{
                if (m) {
                    index++;
                    m = false;
                }
            }
        }
        int ans = 0;
        index = 1;
        for (int i = 0; i < n; i++) {
            break;
        }
        for (int i = 0; i < n; i++) {
            if (mark[i] == index) {
                int l = i;
                int r = i + 1;
                bool flag = false;
                for (int j = i + 1; j < n; j++) {
                    if (mark[j] != index) {
                        flag = true;
                        r = j - 1;
                        break;
                    }
                }
                if (!flag) {
                    r = n - 1;
                }
                int sum = 0;
                int max_ = 0;
                for (int u = l; u < r+1; u++) {
                    sum += cost[u];
                    max_ = max(max_,cost[u]);
                }
                ans += sum - max_;
                i = r;
                index++;
            }
        }
        return ans;
    }
};

```


> 分析：dp法，这个可以定义为一维dp，因为要维护的位置也只有当前位置这一个量，$dp[i]$表示从0位置到当前位置使序列不重复的最小代价。如果当前位置和前一个位置相等了，那么$dp[i] = dp[i - 1] + min(cost[i],cost[i - 1])$；如果两个位置不等，$dp[i] = dp[i - 1]$，然后再确定边界条件，dp[0]表示在0位置时的最小花销，0位置只有一个元素，不会重复，所以花销为0，所以dp[0] = 0。**注意：一定要保留重复元素中最大的花销:star:**

```cpp
class Solution{
public:
    int minCost(string s,vector<int>& cost) {
        int n = s.size();
        vector<int> dp(n,0);
        for (int i = 1; i < n; i++) {
            if (s[i] == s[i - 1]) {
                dp[i] = dp[i - 1] + min(cost[i],cost[i - 1]);
                if (cost[i] < cost[i - 1]) {
                    swap(cost[i],cost[i - 1]);
                }
            }else{
                dp[i] = dp[i - 1];
            }
        }
        return dp[n - 1];
    }
};
```

## problem4
Alice 和 Bob 共有一个无向图，其中包含 n 个节点和 3  种类型的边：

类型 1：只能由 Alice 遍历。
类型 2：只能由 Bob 遍历。
类型 3：Alice 和 Bob 都可以遍历。
给你一个数组 edges ，其中 edges[i] = [typei, ui, vi] 表示节点 ui 和 vi 之间存在类型为 typei 的双向边。请你在保证图仍能够被 Alice和 Bob 完全遍历的前提下，找出可以删除的最大边数。如果从任何节点开始，Alice 和 Bob 都可以到达所有其他节点，则认为图是可以完全遍历的。

返回可以删除的最大边数，如果 Alice 和 Bob 无法完全遍历图，则返回 -1 。

> 分析：并查集

```cpp
class Solution {
public:
    int maxNumEdgesToRemove(int n, vector<vector<int>>& edges) {

    }
};
```