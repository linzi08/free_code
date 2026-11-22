# C++ 高频算法题精讲（26 题 · LeetCode 热题精选）

> 每题：题目 → 思路 → 复杂度 → 带详细注释的 C++ 实现。
> 建议练法：先自己写 20 分钟，卡住再看思路；写完对比注释里的关键点。
> 配套语言基础：[C++ 语言特性学习指南](cpp-language-guide.md)

---

## 目录

- [C++ 高频算法题精讲（26 题 · LeetCode 热题精选）](#c-高频算法题精讲26-题--leetcode-热题精选)
  - [目录](#目录)
- [一、双指针与滑动窗口](#一双指针与滑动窗口)
  - [1.1 两数之和（LC1）](#11-两数之和lc1)
  - [1.2 三数之和（LC15）](#12-三数之和lc15)
  - [1.3 盛最多水的容器（LC11）](#13-盛最多水的容器lc11)
  - [1.4 无重复字符的最长子串（LC3）](#14-无重复字符的最长子串lc3)
  - [1.5 接雨水（LC42）](#15-接雨水lc42)
- [二、链表](#二链表)
  - [2.1 反转链表（LC206）](#21-反转链表lc206)
  - [2.2 合并两个有序链表（LC21）](#22-合并两个有序链表lc21)
  - [2.3 环形链表 II（找入环点）（LC142）](#23-环形链表-ii找入环点lc142)
  - [2.4 LRU 缓存（LC146）](#24-lru-缓存lc146)
- [三、二分查找](#三二分查找)
  - [3.1 搜索旋转排序数组（LC33）](#31-搜索旋转排序数组lc33)
- [四、栈与单调栈](#四栈与单调栈)
  - [4.1 有效括号（LC20）](#41-有效括号lc20)
  - [4.2 每日温度（单调栈模板）（LC739）](#42-每日温度单调栈模板lc739)
  - [4.3 最小栈（LC155）](#43-最小栈lc155)
- [五、堆](#五堆)
  - [5.1 前 K 个高频元素（LC347）](#51-前-k-个高频元素lc347)
- [六、二叉树](#六二叉树)
  - [6.1 层序遍历（BFS 模板）（LC102）](#61-层序遍历bfs-模板lc102)
  - [6.2 验证二叉搜索树（LC98）](#62-验证二叉搜索树lc98)
  - [6.3 最近公共祖先（LC236）](#63-最近公共祖先lc236)
- [七、动态规划](#七动态规划)
  - [7.1 最大子数组和（LC53）](#71-最大子数组和lc53)
  - [7.2 零钱兑换（LC322）](#72-零钱兑换lc322)
  - [7.3 最长递增子序列（LC300）](#73-最长递增子序列lc300)
  - [7.4 最长公共子序列（LC1143）](#74-最长公共子序列lc1143)
  - [7.5 编辑距离（LC72）](#75-编辑距离lc72)
- [八、图与搜索](#八图与搜索)
  - [8.1 岛屿数量（DFS 染色）（LC200）](#81-岛屿数量dfs-染色lc200)
  - [8.2 课程表（拓扑排序）（LC207）](#82-课程表拓扑排序lc207)
  - [8.3 全排列（回溯模板）（LC46）](#83-全排列回溯模板lc46)
- [九、手写排序](#九手写排序)
  - [9.1 快速排序 + 归并排序](#91-快速排序--归并排序)
  - [附：复杂度与模板速记](#附复杂度与模板速记)

---

# 一、双指针与滑动窗口

## 1.1 两数之和（LC1）

**题**：数组中找两个数和为 target，返回下标。**思路**：哈希表存"值→下标"，一次遍历边查边存。
**复杂度**：O(n) 时间 / O(n) 空间。

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> seen;                 // 值 -> 下标
    for (int i = 0; i < (int)nums.size(); ++i) {
        int need = target - nums[i];              // 需要的另一半
        auto it = seen.find(need);                // 在"已见过的数"里找
        if (it != seen.end())
            return {it->second, i};               // 先查后存 => 天然去重自身配对
        seen[nums[i]] = i;
    }
    return {};
}
```

## 1.2 三数之和（LC15）

**题**：找出所有三元组 a+b+c=0，不重复。**思路**：排序 + 固定一个数，剩余区间双指针夹逼；关键全在**去重**。
**复杂度**：O(n²) 时间 / O(1) 空间（不算输出）。

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    sort(nums.begin(), nums.end());               // 1. 排序：重复元素相邻、双指针可用
    vector<vector<int>> res;
    int n = nums.size();
    for (int i = 0; i < n - 2; ++i) {
        if (nums[i] > 0) break;                   // 最小值>0 => 后面全正，不可能凑 0
        if (i > 0 && nums[i] == nums[i - 1]) continue;   // 2. 固定值去重：跳过重复的第一个数
        int l = i + 1, r = n - 1;
        while (l < r) {
            int sum = nums[i] + nums[l] + nums[r];
            if (sum < 0)       ++l;               // 和偏小 => 左指针右移（增大）
            else if (sum > 0)  --r;               // 和偏大 => 右指针左移（减小）
            else {
                res.push_back({nums[i], nums[l], nums[r]});
                // 3. 结果去重：跳过相同值
                while (l < r && nums[l] == nums[l + 1]) ++l;
                while (l < r && nums[r] == nums[r - 1]) --r;
                ++l; --r;                          // 移到全新组合
            }
        }
    }
    return res;
}
```

## 1.3 盛最多水的容器（LC11）

**题**：两条竖线与 x 轴围成的容器最多装多少水。**思路**：左右指针从两端向内，**每次移动较短的一边**（移动长边不可能更优，面积由短板决定）。
**复杂度**：O(n)。

```cpp
int maxArea(vector<int>& h) {
    int l = 0, r = h.size() - 1, best = 0;
    while (l < r) {
        int width = r - l;
        int height = min(h[l], h[r]);          // 水位由短板决定
        best = max(best, width * height);
        if (h[l] < h[r]) ++l;                  // 移动短板：唯一可能变大的一侧
        else             --r;
    }
    return best;
}
```

## 1.4 无重复字符的最长子串（LC3）

**题**：不含重复字符的最长子串长度。**思路**：滑动窗口 + 计数数组，右探左缩。
**复杂度**：O(n)。

```cpp
int lengthOfLongestSubstring(string s) {
    int cnt[128] = {0};                        // ASCII 计数（比 unordered_set 更快）
    int l = 0, best = 0;
    for (int r = 0; r < (int)s.size(); ++r) {
        ++cnt[(unsigned char)s[r]];            // 右指针扩窗
        while (cnt[(unsigned char)s[r]] > 1) { // 出现重复：左指针收缩到消掉为止
            --cnt[(unsigned char)s[l++]];
        }
        best = max(best, r - l + 1);           // 此刻窗口 [l, r] 无重复
    }
    return best;
}
```

## 1.5 接雨水（LC42）

**题**：柱状图能接多少水。**思路**：每个位置的存水 = min(左侧最高, 右侧最高) − 自身高度。用**双指针**边走边维护两侧最大值，空间 O(1)。
**复杂度**：O(n) 时间 / O(1) 空间。

```cpp
int trap(vector<int>& h) {
    int l = 0, r = h.size() - 1;
    int lMax = 0, rMax = 0, water = 0;
    while (l < r) {
        if (h[l] < h[r]) {                     // 左边较矮 => 左边的水位由 lMax 决定
            lMax = max(lMax, h[l]);
            water += lMax - h[l];              // 若 lMax 已 >= 右侧最高，水位成立
            ++l;
        } else {
            rMax = max(rMax, h[r]);
            water += rMax - h[r];
            --r;
        }
    }
    return water;
}
```

---

# 二、链表

## 2.1 反转链表（LC206）

**思路**：三指针迭代：prev/cur/next 逐个调头。
**复杂度**：O(n) / O(1)。

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* cur = head;
    while (cur) {
        ListNode* next = cur->next;    // 1. 先存后继（要断链）
        cur->next = prev;              // 2. 调头
        prev = cur;                    // 3. prev 前进
        cur = next;                    // 4. cur 前进
    }
    return prev;                       // 新头
}
```

## 2.2 合并两个有序链表（LC21）

**思路**：哑结点 + 尾插法，避免处理"新头"特判。
**复杂度**：O(m+n)。

```cpp
ListNode* mergeTwoLists(ListNode* a, ListNode* b) {
    ListNode dummy;                          // 哑结点：统一"插入第一个元素"的逻辑
    ListNode* tail = &dummy;
    while (a && b) {
        if (a->val <= b->val) { tail->next = a; a = a->next; }
        else                  { tail->next = b; b = b->next; }
        tail = tail->next;
    }
    tail->next = a ? a : b;                  // 剩下的整段直接接上
    return dummy.next;
}
```

## 2.3 环形链表 II（找入环点）（LC142）

**思路**：快慢指针判环 + 数学：相遇后把一个指针放回头，两指针同速前进，再相遇即入环点（`头到环口 = 相遇点到环口`）。
**复杂度**：O(n) / O(1)。

```cpp
ListNode* detectCycle(ListNode* head) {
    ListNode *slow = head, *fast = head;
    while (fast && fast->next) {
        slow = slow->next;             // 慢 1 步
        fast = fast->next->next;       // 快 2 步
        if (slow == fast) {            // 有环：相遇
            slow = head;               // 一个指针回到起点
            while (slow != fast) {     // 同速前进，再次相遇点 = 入环口
                slow = slow->next;
                fast = fast->next;
            }
            return slow;
        }
    }
    return nullptr;                    // fast 到尾 => 无环
}
```

## 2.4 LRU 缓存（LC146）

**题**：O(1) 的 get/put，容量满时淘汰最近最久未用。**思路**：哈希表（O(1) 定位）+ 双向链表（O(1) 挪动/删除），链表头 = 最新。
**复杂度**：全 O(1)。

```cpp
class LRUCache {
    int cap_;
    list<pair<int,int>> items_;                                  // (key, value)，头=最新
    unordered_map<int, list<pair<int,int>>::iterator> index_;    // key -> 链表节点位置

public:
    LRUCache(int capacity) : cap_(capacity) {}

    int get(int key) {
        auto it = index_.find(key);
        if (it == index_.end()) return -1;
        items_.splice(items_.begin(), items_, it->second);  // ★ 原地搬到头部（节点不重分配）
        return it->second->second;
    }

    void put(int key, int value) {
        auto it = index_.find(key);
        if (it != index_.end()) {                    // 已存在：更新值 + 挪头
            it->second->second = value;
            items_.splice(items_.begin(), items_, it->second);
            return;
        }
        if ((int)items_.size() == cap_) {            // 满：淘汰尾部（最久未用）
            index_.erase(items_.back().first);
            items_.pop_back();
        }
        items_.emplace_front(key, value);
        index_[key] = items_.begin();
    }
};
```

---

# 三、二分查找

## 3.1 搜索旋转排序数组（LC33）

**题**：升序数组在某个点旋转过，找 target 下标。**思路**：二分时**至少一半是有序的**——判断哪半有序、target 是否落在其中，从而丢弃一半。
**复杂度**：O(log n)。

```cpp
int search(vector<int>& nums, int target) {
    int l = 0, r = (int)nums.size() - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2;             // 防溢出写法（面试加分点）
        if (nums[mid] == target) return mid;
        if (nums[l] <= nums[mid]) {            // 左半 [l, mid] 有序
            if (nums[l] <= target && target < nums[mid])
                r = mid - 1;                   // target 在有序左半
            else
                l = mid + 1;                   // 否则去右半
        } else {                               // 右半 [mid, r] 有序
            if (nums[mid] < target && target <= nums[r])
                l = mid + 1;
            else
                r = mid - 1;
        }
    }
    return -1;
}
```

---

# 四、栈与单调栈

## 4.1 有效括号（LC20）

```cpp
bool isValid(string s) {
    unordered_map<char, char> pair = {{')','('}, {']','['}, {'}','{'}};
    stack<char> st;
    for (char c : s) {
        if (pair.count(c)) {                       // 右括号：必须匹配栈顶左括号
            if (st.empty() || st.top() != pair[c]) return false;
            st.pop();
        } else {
            st.push(c);                            // 左括号入栈
        }
    }
    return st.empty();                             // 栈空 = 全配对
}
```

## 4.2 每日温度（单调栈模板）（LC739）

**题**：等多少天变暖。**思路**：栈存"还没找到答案的下标"，保持栈内温度**递减**；新温度弹出所有更小者。
**复杂度**：O(n)（每个下标至多进出栈一次）——单调栈通杀"下一个更大/更小元素"类题。

```cpp
vector<int> dailyTemperatures(vector<int>& t) {
    int n = t.size();
    vector<int> ans(n, 0);
    stack<int> st;                                  // 存下标，栈内对应温度单调递减
    for (int i = 0; i < n; ++i) {
        while (!st.empty() && t[i] > t[st.top()]) { // 当前 > 栈顶 => 栈顶等到了变暖日
            int j = st.top(); st.pop();
            ans[j] = i - j;                         // 间隔天数
        }
        st.push(i);                                 // 自己入栈等答案
    }
    return ans;                                     // 栈里剩的都是永远没变暖的，ans 已是 0
}
```

## 4.3 最小栈（LC155）

**思路**：辅助栈与主栈**同步生长**，辅助栈顶永远是当前最小（push 时压 min(x, 当前最小)，pop 时同步弹）。

```cpp
class MinStack {
    stack<int> main_, min_;
public:
    void push(int x) {
        main_.push(x);
        min_.push(min_.empty() ? x : min(min_.top(), x));   // 同步记录"到此为止的最小"
    }
    void pop() { main_.pop(); min_.pop(); }
    int top()    { return main_.top(); }
    int getMin() { return min_.top(); }
};
```

---

# 五、堆

## 5.1 前 K 个高频元素（LC347）

**思路**：哈希计数 + 大小为 k 的**小根堆**（堆顶是当前第 k 大，超过 k 弹出）——比全排序 O(n log n) 优。
**复杂度**：O(n log k)。

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> cnt;
    for (int x : nums) ++cnt[x];

    // 小根堆按"频次"排：堆里始终保留频次最高的 k 个
    auto cmp = [](pair<int,int>& a, pair<int,int>& b) { return a.second > b.second; }; // > => 小根堆
    priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);

    for (auto& [num, c] : cnt) {                 // C++17 结构化绑定
        pq.push({num, c});
        if ((int)pq.size() > k) pq.pop();        // 超额弹出"当前最差" => 留下的都是头部
    }
    vector<int> res;
    while (!pq.empty()) { res.push_back(pq.top().first); pq.pop(); }
    return res;
}
```

---

# 六、二叉树

## 6.1 层序遍历（BFS 模板）（LC102）

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    queue<TreeNode*> q;
    if (root) q.push(root);
    while (!q.empty()) {
        int n = q.size();                        // ★ 固定本层节点数（队列会边走边进）
        vector<int> level;
        for (int i = 0; i < n; ++i) {
            TreeNode* node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);   // 子节点进队（下一层）
            if (node->right) q.push(node->right);
        }
        res.push_back(move(level));
    }
    return res;
}
```

## 6.2 验证二叉搜索树（LC98）

**思路**：BST 中序遍历严格递增——用"上一个值"全局比较，比"只比较子和孙"正确（后者漏掉跨层违规）。
**复杂度**：O(n)。

```cpp
class BSTChecker {
    TreeNode* prev_ = nullptr;                    // 中序前驱
public:
    bool isValid(TreeNode* root) {
        if (!root) return true;
        if (!isValid(root->left)) return false;   // 左子树先验
        if (prev_ && prev_->val >= root->val) return false;   // ★ 必须严格递增（相等也非法）
        prev_ = root;
        return isValid(root->right);
    }
};
```

## 6.3 最近公共祖先（LC236）

**思路**：递归后序——左右子树各自找 p/q；两边都找到 → 当前节点就是 LCA；只有一边 → 结果向上透传。

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;   // 命中目标或走到空

    TreeNode* l = lowestCommonAncestor(root->left, p, q);
    TreeNode* r = lowestCommonAncestor(root->right, p, q);

    if (l && r) return root;      // p、q 分居两侧 => 当前节点是交汇点（LCA）
    return l ? l : r;             // 单侧找到 => 把结果往上带
}
```

---

# 七、动态规划

## 7.1 最大子数组和（LC53）

**思路**：`f[i]` = 以 i 结尾的最大和。`f[i] = max(f[i-1] + a[i], a[i])`——前面的和为负就"断开重来"。滚动变量省数组。
**复杂度**：O(n) / O(1)。

```cpp
int maxSubArray(vector<int>& a) {
    int cur = 0, best = INT_MIN;
    for (int x : a) {
        cur = max(cur + x, x);        // 前缀有正贡献才接上，否则从 x 起头
        best = max(best, cur);        // 全局记录历史最优
    }
    return best;
}
```

## 7.2 零钱兑换（LC322）

**题**：最少数量的硬币凑出 amount（可重复使用）。**思路**：完全背包。"凑出 i 的最少硬币 = min(凑出 i−coin) + 1"。
**复杂度**：O(amount×coins)。

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, amount + 1);   // 初始化为"不可能大值"（ amount+1 即不可达）
    dp[0] = 0;
    for (int i = 1; i <= amount; ++i)
        for (int c : coins)
            if (c <= i)
                dp[i] = min(dp[i], dp[i - c] + 1);   // 用一枚 c，剩余 i-c 继承
    return dp[amount] > amount ? -1 : dp[amount];
}
```

## 7.3 最长递增子序列（LC300）

**思路 A（O(n²)）**：`dp[i]` = 以 i 结尾的 LIS 长度，向前找比它小的取 max。
**思路 B（O(n log n)，面试加分）**：`tails[k]` = 长度 k+1 的 LIS 的**最小结尾**（保持有序）→ 每个元素二分替换/追加。

```cpp
int lengthOfLIS(vector<int>& a) {
    vector<int> tails;                     // tails[k]：长度 k+1 的递增子序列的最小末尾
    for (int x : a) {
        auto it = lower_bound(tails.begin(), tails.end(), x);   // 第一个 >= x 的位置
        if (it == tails.end())
            tails.push_back(x);            // x 比所有结尾都大 => LIS 变长
        else
            *it = x;                       // 替换成更小结尾 => 后续更易接上（长度不变）
    }
    return tails.size();
}
```

## 7.4 最长公共子序列（LC1143）

**思路**：二维 DP。`dp[i][j]` = A 前 i 与 B 前 j 的 LCS。
转移：相等 `dp[i-1][j-1]+1`；不等 `max(dp[i-1][j], dp[i][j-1])`。
**复杂度**：O(mn)。

```cpp
int longestCommonSubsequence(string a, string b) {
    int m = a.size(), n = b.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));   // dp[0][*]=dp[*][0]=0
    for (int i = 1; i <= m; ++i)
        for (int j = 1; j <= n; ++j)
            dp[i][j] = (a[i-1] == b[j-1])
                     ? dp[i-1][j-1] + 1                       // 尾字符相同 => 接上
                     : max(dp[i-1][j], dp[i][j-1]);           // 不同 => 丢一个尾字符取优
    return dp[m][n];
}
```

## 7.5 编辑距离（LC72）

**思路**：LCS 的近亲。`dp[i][j]` = word1 前 i → word2 前 j 的最少操作。
转移：相同免操作；不同 = min(替换 dp[i-1][j-1]、删除 dp[i-1][j]、插入 dp[i][j-1]) + 1。
**复杂度**：O(mn)。

```cpp
int minDistance(string a, string b) {
    int m = a.size(), n = b.size();
    vector<vector<int>> dp(m + 1, vector<int>(n + 1));
    for (int i = 0; i <= m; ++i) dp[i][0] = i;      // 全删
    for (int j = 0; j <= n; ++j) dp[0][j] = j;      // 全插
    for (int i = 1; i <= m; ++i)
        for (int j = 1; j <= n; ++j) {
            if (a[i-1] == b[j-1]) dp[i][j] = dp[i-1][j-1];            // 尾相同：零成本
            else dp[i][j] = 1 + min({ dp[i-1][j-1],                    // 替换
                                      dp[i-1][j],                      // 删 a 尾
                                      dp[i][j-1] });                   // 插 b 尾
        }
    return dp[m][n];
}
```

---

# 八、图与搜索

## 8.1 岛屿数量（DFS 染色）（LC200）

**思路**：扫到 '1' 计数 +1，同时 DFS 把整座岛（连通 '1'）改成 '0' 防止重复计数。
**复杂度**：O(mn)。

```cpp
void dfs(vector<vector<char>>& g, int r, int c) {
    if (r < 0 || r >= (int)g.size() || c < 0 || c >= (int)g[0].size()) return;
    if (g[r][c] != '1') return;          // 水/已访问
    g[r][c] = '0';                       // 染色：标记已访问
    dfs(g, r + 1, c); dfs(g, r - 1, c);  // 四方向扩散
    dfs(g, r, c + 1); dfs(g, r, c - 1);
}

int numIslands(vector<vector<char>>& grid) {
    int cnt = 0;
    for (int r = 0; r < (int)grid.size(); ++r)
        for (int c = 0; c < (int)grid[0].size(); ++c)
            if (grid[r][c] == '1') { ++cnt; dfs(grid, r, c); }
    return cnt;
}
```

## 8.2 课程表（拓扑排序）（LC207）

**思路**：修课依赖 = 有向图；能修完 ⇔ 图无环。BFS 拓扑：入度表 + 队列，减到 0 入队。
**复杂度**：O(V+E)。

```cpp
bool canFinish(int numCourses, vector<vector<int>>& prereq) {
    vector<vector<int>> adj(numCourses);            // adj[x]：先修 x 后才能修的课
    vector<int> indeg(numCourses, 0);
    for (auto& p : prereq) { adj[p[1]].push_back(p[0]); ++indeg[p[0]]; }

    queue<int> q;
    for (int i = 0; i < numCourses; ++i)
        if (indeg[i] == 0) q.push(i);               // 无先修要求的课直接开课

    int taken = 0;
    while (!q.empty()) {
        int x = q.front(); q.pop();
        ++taken;
        for (int y : adj[x])
            if (--indeg[y] == 0) q.push(y);         // 前置全部修完 => 可修
    }
    return taken == numCourses;                     // 全修完 = 无环；有环的课入度永远 >0
}
```

## 8.3 全排列（回溯模板）（LC46）

**思路**：回溯三件套——**做选择 → 递归 → 撤销选择**。模板适用于排列/组合/子集全家。

```cpp
class Permute {
    vector<vector<int>> res_;
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<int> path;
        vector<bool> used(nums.size(), false);
        Backtrack(nums, path, used);
        return res_;
    }
private:
    void Backtrack(vector<int>& nums, vector<int>& path, vector<bool>& used) {
        if (path.size() == nums.size()) {           // 收集完整排列
            res_.push_back(path);
            return;
        }
        for (int i = 0; i < (int)nums.size(); ++i) {
            if (used[i]) continue;                  // 剪枝：已用的跳过
            used[i] = true;                         // 1. 做选择
            path.push_back(nums[i]);
            Backtrack(nums, path, used);            // 2. 递归
            path.pop_back();                        // 3. 撤销选择（回溯的灵魂）
            used[i] = false;
        }
    }
};
```

---

# 九、手写排序

## 9.1 快速排序 + 归并排序

```cpp
// ---- 快排：partition 把基准放到最终位置，递归两侧 ----
// 平均 O(n log n)，最坏 O(n²)（已排序数组 + 固定基准 => 随机化基准防退化）
int Partition(vector<int>& a, int l, int r) {
    int pivot = a[r];                     // 取右端为基准（生产实现应随机选）
    int i = l;                            // i：小于区的下一个落点
    for (int j = l; j < r; ++j)
        if (a[j] < pivot) swap(a[i++], a[j]);   // 小的换到左边区
    swap(a[i], a[r]);                     // 基准归位：左边全小、右边全大
    return i;
}

int partition1(vector<int> &a, int l, int r) {
    int pivot = a[r];
    int i = l, j = r - 1;
    while (i < j) {
        while( i<j && a[i] < pivot) {
            i++;
        }
        if ( i < j ) {
            swap(a[i], a[j--]);
        }
        while( i<j && a[j] >= pivot) {
            j--;
        }
        if ( i< j ) {
            swap(a[i++], a[j]);
        }
    }
    return i;
}

void QuickSort(vector<int>& a, int l, int r) {
    if (l >= r) return;
    int p = Partition(a, l, r);
    QuickSort(a, l, p - 1);
    QuickSort(a, p + 1, r);
}

// ---- 归并：稳定 O(n log n)，外部排序/链表排序基础 ----
void Merge(vector<int>& a, int l, int m, int r, vector<int>& buf) {
    int i = l, j = m + 1, k = l;
    while (i <= m && j <= r)
        buf[k++] = (a[i] <= a[j]) ? a[i++] : a[j++];   // <= 保证稳定性
    while (i <= m) buf[k++] = a[i++];                  // 收尾残余
    while (j <= r) buf[k++] = a[j++];
    for (int x = l; x <= r; ++x) a[x] = buf[x];        // 写回
}
void merge1(vector<int> &a, int l, int m, int r) {
    vector<int> temp( r - l + 1);
    int i = l, j = m + 1, k = 0;
    while(i <=m && j <= r) {
        if (a[i] < a[j]) {
            temp[k++] = a[i++];
        } else {
            temp[k++] = a[j++];
        }
    }
    while(i <= m) temp[k++] = a[i++];
    while(j <= r) temp[k++] = a[j++];

    for(int n = 0; n < k; n++) {
        a[l+n] = temp[n];
    }
}

void MergeSort(vector<int>& a, int l, int r, vector<int>& buf) {
    if (l >= r) return;
    int m = l + (r - l) / 2;
    MergeSort(a, l, m, buf);              // 左半
    MergeSort(a, m + 1, r, buf);          // 右半
    Merge(a, l, m, r, buf);               // 合并
}
// 面试要点：快排问"最坏情况与防退化"（随机基准/三数取中）；归并问"稳定性与空间 O(n)"
```

---

## 附：复杂度与模板速记

| 专题 | 万能模板 | 典型复杂度 |
|---|---|---|
| 滑窗 | 右探左缩 + 计数/哈希校验 | O(n) |
| 单调栈 | 新元素弹出栈顶不满足者 | O(n) |
| 二分 | 循环不变量：答案必在 [l, r] | O(log n) |
| 回溯 | 选择 → 递归 → 撤销 | 指数级剪枝 |
| 拓扑 | 入度表 + 队列 | O(V+E) |
| 背包 DP | 外层容量内层物品（0-1 倒序 / 完全正序） | O(n·W) |
| BFS 层序 | 队列 + 固定本层数量 | O(V+E) |
