# 回文串DP专题

> **目标**：掌握回文串相关的DP问题，包括回文判断、回文计数、回文分割等
> 
> **更新日期**：2025年11月14日  
> **当前版本**：v1.0 - 基础版

---

## 📋 专题概述

### 核心问题类型
1. **回文判断**：判断某个子串是否是回文
2. **回文计数**：统计所有回文子串的个数
3. **最长回文**：找到最长的回文子串
4. **回文分割**：将字符串分割成回文子串的最优方案

### 通用思路
```
预处理阶段：计算所有 s[i..j] 的回文性 → isPal[i][j] 表
应用阶段：基于 isPal 表解决具体问题
```

---

## 🧠 核心模型

### 1. 区间回文判断模型

#### 状态定义
- `isPal[i][j]` = 子串 `s[i..j]` 是否是回文串
- 索引对应：`isPal[i][j]` ↔ `s[i..j]`（无偏移）

#### 转移方程
```cpp
if (s[i] != s[j]) {
    isPal[i][j] = false;
} else {
    if (j - i <= 1) {
        // 长度为1或2，且首尾相等
        isPal[i][j] = true;
    } else {
        // 长度>2，依赖内部区间
        isPal[i][j] = isPal[i + 1][j - 1];
    }
}
```

#### 遍历顺序
```cpp
// 关键：外层 i 从右往左，内层 j 从左往右
for (int i = n - 1; i >= 0; i--) {
    for (int j = i; j < n; j++) {
        // 计算逻辑
    }
}
```

#### 复杂度
- 时间：`O(n^2)`
- 空间：`O(n^2)`

### 2. 回文分割模型

#### 基本思路
1. **预处理**：先计算 `isPal[i][j]` 表
2. **分割DP**：在一维上做分割决策

#### 分割类型
- **存在性判断**（1745）：枚举分割点检查
- **最少次数**（132）：一维 DP 求最优值

---

## 📚 已解决题目

### LeetCode 647. 回文子串 ⭐⭐

**题目类型**：回文计数  
**核心思路**：预处理 `isPal` 表，统计 `true` 的个数  
**复杂度**：`O(n^2)` 时间，`O(n^2)` 空间

```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size();
        vector<vector<bool>> isPal(n, vector<bool>(n, false));
        
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s[i] == s[j]) {
                    if (j - i <= 1) {
                        isPal[i][j] = true;
                    } else {
                        isPal[i][j] = isPal[i + 1][j - 1];
                    }
                }
            }
        }
        
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                if (isPal[i][j]) count++;
            }
        }
        return count;
    }
};
```

### LeetCode 5. 最长回文子串 ⭐⭐

**题目类型**：最长回文  
**核心思路**：预处理 `dp[i][j]` 表，记录最长区间  
**复杂度**：`O(n^2)` 时间，`O(n^2)` 空间

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        if(n < 2) return s;
        
        vector<vector<bool>> dp(n, vector<bool>(n, false));
        int maxLen = 1;
        int start = 0;
        
        // 外层从右往左，内层j从i往右
        for(int i = n-1; i >= 0; i--) {
            for(int j = i; j < n; j++) {
                if(s[i] == s[j]) {
                    if(j - i <= 1) {
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i+1][j-1];
                    }
                }
                // 更新最长回文子串
                if(dp[i][j] && j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    start = i;
                }
            }
        }
        return s.substr(start, maxLen);
    }
};
```

### LeetCode 1745. 回文串分割IV ⭐⭐⭐

**题目类型**：回文分割（存在性判断）  
**核心思路**：预处理 `isPal` 表 + 枚举分割点  
**复杂度**：`O(n^2)` 时间，`O(n^2)` 空间

```cpp
class Solution {
public:
    bool checkPartitioning(string s) {
        int n = s.size();
        vector<vector<bool>> isPal(n, vector<bool>(n, false));
        
        // 预处理回文表
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s[i] != s[j]) {
                    isPal[i][j] = false;
                } else {
                    if (j - i <= 1) {
                        isPal[i][j] = true;
                    } else {
                        isPal[i][j] = isPal[i + 1][j - 1];
                    }
                }
            }
        }
        
        // 枚举分割点
        for (int i = 1; i <= n - 2; i++) {
            for (int j = i + 1; j <= n - 1; j++) {
                if (isPal[0][i-1] && isPal[i][j-1] && isPal[j][n-1]) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### LeetCode 132. 回文串分割II ⭐⭐⭐

**题目类型**：回文分割（最少次数）  
**核心思路**：预处理 `isPal` 表 + 一维 DP  
**状态设计**：`dp[i] = s[0..i]` 的最少分割次数  
**复杂度**：`O(n^2)` 时间，`O(n^2)` 空间

```cpp
class Solution {
public:
    int minCut(string s) {
        int n = s.size();
        // 第一步，预处理 isPal 表
        vector<vector<bool>> isPal(n, vector<bool>(n, false));
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s[i] == s[j]) {
                    if (j - i <= 1) {
                        isPal[i][j] = true;
                    } else {
                        isPal[i][j] = isPal[i + 1][j - 1];
                    }
                }
            }
        }
        // 第二步，一维 DP
        vector<int> dp(n, n-1);  // 初始化为最大值
        for(int i = 0; i < n; i++) {
            if(isPal[0][i]) {  // 如果整个 s[0..i] 是回文
                dp[i] = 0;  // 整体是回文，不分割
            } else {
                // 枚举最后一个回文串的起点 j
                for(int j = 1; j <= i; j++) {
                    if(isPal[j][i])  // 需要知道 s[j..i] 是否回文
                        dp[i] = min(dp[i], dp[j-1] + 1);
                }
            }
        }
        return dp[n-1];
    }
};
```

---

## 📈 学习建议

### 1. 掌握核心模板
- 熟记区间回文判断的预处理模板
- 理解遍历顺序的重要性

### 2. 理解索引对应
- 区间DP：`dp[i][j]` ↔ `s[i..j]`（无偏移）
- 前缀DP：`dp[i][j]` ↔ `s[i-1], s[j-1]`（有偏移）

### 3. 分阶段思考
- 第一阶段：预处理回文信息
- 第二阶段：基于预处理解决具体问题

### 4. 举一反三
- 647：统计所有 `true`
- 5：找最长 `true` 区间
- 1745：检查特定分割方案
- 132：求最优分割方案

### 5. 方法选择
- **DP法**：适合需要预处理、多次查询的场景（如分割问题）
- **中心扩展法**：适合单次查询、空间受限的场景（如单纯找最长回文）

---

## 🔗 相关笔记

- [Day44-11.14-子序列收尾+回文串DP](../我的笔记/Day44-11.14-子序列收尾+回文串DP.md)
- [动态规划方法论-总览](./动态规划方法论-总览.md)

---

**持续更新中...** 🚀

---

## 🔄 回文子串 vs 回文子序列

### 1. 模型定位
- **回文子串**（如 LeetCode 5 最长回文子串）：
  - 关注「区间 `s[i..j]` 是否是回文？」
  - 典型状态：`bool isPal[i][j]` / `bool dp[i][j]`
  - 用途：判断合法区间、统计/枚举、寻找最长回文子串

- **回文子序列**（如「最长回文子序列 LPS」）：
  - 允许不连续，只要相对顺序不变
  - 典型状态：`int dp[i][j]` 表示 `s[i..j]` 中能取出的 **最长回文子序列长度**
  - 本质：区间最优值 DP，可看作「双序列 DP」的特例：`LPS(s) = LCS(s, reverse(s))`

### 2. 状态与转移对比
- **子串版（5 题）**：
  - 状态：`isPal[i][j]` = `s[i..j]` 是否回文
  - 转移：
    - 若 `s[i] != s[j]` → `false`
    - 若 `s[i] == s[j]` 且 `j - i <= 1` → `true`
    - 否则 → `isPal[i+1][j-1]`
  - 在扫描所有 `(i, j)` 的同时维护最长的合法区间即可。

- **子序列版（LPS）**：
  - 状态：`dp[i][j]` = `s[i..j]` 的最长回文子序列长度
  - 转移：
    - 若 `s[i] == s[j]` → `dp[i][j] = dp[i+1][j-1] + 2`
    - 否则 → `dp[i][j] = max(dp[i+1][j], dp[i][j-1])`
  - 目标：`dp[0][n-1]`。

### 3. 遍历顺序共性
- 二者都是典型的 **区间 DP**：
  - 外层 `i` 从右往左：`for (int i = n-1; i >= 0; i--)`
  - 内层 `j` 从 `i` 或 `i+1` 向右：`for (int j = i; j < n; j++)`
- 理由：都依赖于「更短、更靠右」的区间 `i+1` 开头的状态。

### 4. 与其他题目的连接
- 647：在 `isPal[i][j]` 上 **统计** 所有回文子串
- 5：在 `isPal[i][j]` 上 **找最长** 回文子串
- LPS：在 `dp[i][j]` 上 **找最长回文子序列**
- 40 题「最小插入次数」：可以通过 `n - LPS` 直接得到答案

---

## 🔗 相关笔记
- [Day44-11.14-子序列收尾+回文串DP](../我的笔记/Day44-11.14-子序列收尾+回文串DP.md)
- [Day45-11.16-回文串扩展+双数组DP](../我的笔记/Day45-11.16-回文串扩展+双数组DP.md)
- [动态规划方法论-总览](./动态规划方法论-总览.md)

---
