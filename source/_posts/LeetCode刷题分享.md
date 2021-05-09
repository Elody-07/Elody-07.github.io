---
title: LeetCode | 刷题分享
tags:
  - LeetCode
categories:
  - 分享
date: 2021-5-9
---

> 一只找工作的算法程序媛的刷题一二三。

<!--more-->

从研一开始有陆陆续续刷题，但是三天打鱼两天晒网的，也没什么刷题体系。最开始是把剑指offer过了一遍，然后刷LeetCode分组里面的“TopInterview” 列表，一开始只刷easy和medium的，就这样还不叫刷题，叫刷题解。一旦间隔时间太长了，就把之前刷过的再刷一遍，这样刷了一两百道吧，看到easy/medium的才不那么怵了，也开始发现有的题目看上去很像，但是解题思路不太一样，后面刷题的时候就会比较注意看LeetCode上“相似题目”的tag，尽量把类似的题目放一起看，思考相同点和不同点，对照着来看映像深刻一点。

最近面临找实习&找工作，开始集中大量刷题，随着刷题越多，忧虑也开始出现，题目只刷一遍通常会在遇到类似的题目时有种抓耳挠腮，感觉明明看过，思路半通不通的情况，所以最近在二刷（有的可能三刷）算法，记了一些题目/解法有点相关的题目，方便自己后面复习。

## 类似题目分类

[Rabin-Karp算法](https://www.jianshu.com/p/24895aca0459) O(n)时间实现子字符串查找 → 散列(数字表示字符串)+哈希

- No.187 重复的DNA序列
- No.1044 最长重复子串

### 买卖股票

- No.121 基础版，一次交易，动态规划
- No.122 基础版，无限次交易，贪心
- No.123 升级版，两次交易
- No.188 终极版，k 次交易
- [**绝妙题解**](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/solution/5xing-dai-ma-gao-ding-suo-you-gu-piao-ma-j6zo/)

### 旋转排序数组

- No.33 搜索旋转排序数组；No.81 搜索旋转排序数组 II → 搜索target值
- No.153 寻找旋转排序数组中的最小值；No.154 寻找旋转排序数组中的最小值 II → 搜索最小值
- No.187 旋转数组
- No.61 旋转链表
- No.151 翻转字符串里的单词 No.186 翻转字符串里的单词II

### 打家劫舍

- No.198 & No.213 基础版，动态规划
- No.337 二叉树

### 众数问题——摩尔投票法

- No.169 多数元素 出现次数>n/2 次 → 只有1个
- No.229 求众数II 出现次数>n/3次 → 最多2个(2, 1, 0)

### 实现计算器问题

### 背包问题

- No.39 组合总和；No.40 组合总和II → 数字不可重复/可重复

### 硬币问题

- No.322 零钱兑换 → 凑成总金额所需的**最少硬币个数**
- No.518 零钱兑换II → 凑成总金额的组合数

### 动态规划

- No.62 不同路径
- No.64 最小路径和
- No.174 地下城游戏，逆推有点难想到

### 二维动态规划

- No.72 编辑距离
- No.115 不同的子序列
- 剑指 Offer 60. n个骰子的点数

## 一些算法笔记

### 并查集 — Disjoint Set
- No.323 无向图中连通分量的数目

[视频教程](https://www.youtube.com/watch?v=YKE4Vd1ysPI)

可用于检测图中是否有环：构建图，根据边的关系不断向图中合并节点，具体操作为找到两个顶点的根节点，让一个根节点指向另一个，即合并两个节点。如果合并的两个节点的根节点相同，说明图中有环。

```python
class Union(object):
    
    def __init__(self, n):
        self.parent = [-1 for i in range(n)]
        self.rank = [0 for _ in range(n)]
    
    def find(self, x):
        while self.parent[x] != -1:
            x = self.parent[x]
        return x
    
    def union(self, x, y):
        rootx = self.find(x)
        rooty = self.find(y)
        if rootx == rooty:
            return

        if self.rank[rootx] < self.rank[rooty]:
            self.parent[rootx] = rooty
        elif self.rank[rooty] < self.rank[rootx]:
            self.parent[rooty] = rootx
        else:
            self.parent[rootx] = rooty
            self.rank[rooty] += 1
```

### 字典树/前缀树 

- No.820 单词的压缩编码

Trie树，又叫字典树、前缀树（Prefix Tree）、单词查找树 或 键树，是一种多叉树结构。通常来说，一个字典树是用来存储字符串的。字典树的每一个节点代表一个字符串（前缀）。每一个节点会有多个子节点，通往不同子节点的路径上有着不同的字符。子节点代表的字符串是由节点本身的原始字符串，以及通往该子节点路径上所有的字符组成的。

![https://cdn.jsdelivr.net/gh/Elody-07/PicBed/20200408180210.png](https://cdn.jsdelivr.net/gh/Elody-07/PicBed/20200408180210.png)

- 前缀树的实现V

    ```python
    class TrieNode:

        def __init__(self):
            self.children = [None for _ in range(27)]
            # 最后一个元素为1表示单词结束

    class Trie:

        def __init__(self):
            self.root = TrieNode()
        
        def insert(self, word):
            cur = self.root
            isNew = False
            for letter in word:
                idx = ord(letter)-ord('a')
                if cur.children[idx] is None:
                    isNew = True # important！！！
                    cur.children[idx] = TrieNode()
                cur = cur.children[idx]
            cur.children[26] = True
            
            return len(word) + 1 if isNew else 0
     
        def search(self, word):
            """
            Returns if the word is in the trie.
            """
            cur = self.root
            for letter in word:
                idx = ord(letter)-ord('a')
                if cur.children[idx] is None:
                    return False
                cur = cur.children[idx]
                
            # Doesn't end here
            if cur.children[26]:
                return True
            else:
                return False
            
     
        def startsWith(self, prefix):
            """
            Returns if there is any word in the trie that starts with the given prefix.
            """
            cur = self.root
            for letter in prefix:
                idx = ord(letter)-ord('a')
                if cur.children[idx] is None:
                    return False
                cur = cur.children[idx]
            
            return True
    ```

### 拓扑排序 

- No.207 课程表；No.210 课程表II

在图论中，拓扑排序（Topological Sorting）是一个**有向无环图**（DAG, Directed Acyclic Graph）的所有顶点的线性序列，该序列必须满足下面两个条件：1）每个顶点出现且只出现一次；2）若存在一条从顶点 A 到顶点 B 的路径，那么在序列中顶点 A 出现在顶点 B 的前面。有向无环图（DAG）才有拓扑排序，非DAG图没有拓扑排序一说。
以下图为例说明拓扑排序的算法：

1）从图中找到一个没有前驱（入度为0）的顶点；

2）删除该点和与其相邻的有向边；

3）重复以上步骤直到图为空或当前图中不存在没有前驱入度为0的顶点（该情况说明图中必然有环，此图不存在拓扑排序）

![https://cdn.jsdelivr.net/gh/Elody-07/PicBed/20200408164957.png](https://cdn.jsdelivr.net/gh/Elody-07/PicBed/20200408164957.png)

拓扑排序可以用BFS或DFS算法实现，其中BFS能够较为直观地知道该图是否为有向无环图以及输出拓扑排序结果，DFS需要定义flag数组，表示该节点是否被访问过、再次访问该节点时与前一次访问是同一个节点（有环）还是不同节点发起的DFS（正常拓扑排序），

### TOP K问题

在未排序的数组中找到**第 k 个最大**的元素。三种解法：数组排序，快速选择算法，Min-Heap小根堆。

**数组排序**

通过快排对数组排序后，下标为 `n-k` 的元素即为所求。时间复杂度最好O(nlogn)，最差O(n^2)，**平均O(nlogn)**，**空间复杂度O(logn)**

**Min-Heap小根堆**

维护一个k元素的小根堆，遍历数组，当数组元素大于堆顶时，替换堆顶元素，最后的堆顶元素即为第k大的元素，**时间复杂度O(nlogk)，空间复杂度O(k)**

**快速选择算法**

基于快速排序算法的优化，要找到第k大的元素，只要保证下标为 `n-k` 的元素，在它左边的元素都小于它，右边的元素都大于它。故可以用partition算法实现，只要得到的pivot下标为 `n-k` 即可。时间复杂度最好O(n)，最差O(n^2)，平均O(n)。

```python
# partition的两种实现方式，时间复杂度O(n)
def partition(arr, start, end):
    center = arr[start]
    while(start < end):
        while start < end and arr[end] >= center:
            end -= 1
        arr[start] = arr[end]
        while start < end and arr[start] <= center:
            start += 1
        arr[end] = arr[start]
    assert start == end
    arr[start] = center
    return start

# 下标<i的元素值<center，下标为i的元素是第一个>=center的值，故最后center要放在i位置
# 易于扩展为3-way-partition
def partition(nums, start, end):
    i, j = start, start
    center = nums[end]
    while j < end:
        if nums[j] < center:
            nums[i], nums[j] = nums[j], nums[i]
            i += 1
        j += 1

    nums[i], nums[end] = nums[end], nums[i]
    return i

# 3-way-partition
# 下标<i的元素值<mid_val，下标>k的元素值>mid_val，其他等于mid_val
    i, j, k = 0, 0, len(nums)-1
    while j <= k:
        if nums[j] > mid_val:
            nums[j], nums[k] = nums[k], nums[j]
            k -= 1
        elif nums[j] < mid_val:
            nums[j], nums[i] = nums[i], nums[j] 
            i += 1
        j += 1
```

**一个变体如下：**

No.324 摆动排序

该算法的核心是将**数组排序**后分为A（较小组），B（较大组）两组，且len(A) ≥ len(B)，为了实现摆动排序，需要将B组元素隔一个位置插入A组中。为了保证将A，B组的重复元素（A的尾部，B的头部）分开，可以将A、B**反序后再穿插（时间复杂度O(n)，空间复杂度O(n)）**。

优化的思想在于数组排序部分，严格的数组排序可以使用快速排序，平均时间复杂度O(nlogn)；

但事实上该问题**不要求严格排序**，只需要：A、B的重复元素（即数组中位数）在A的尾部，B的头部，且A的元素都小于B的元素即可。这样可以先找到数组的中位数（利用topK问题的思路，找到数组第n//2大的元素即为中位数），再根据中位数做3-way-partition，小于中位数的都在左边，大于中位数的都在右边，这样就实现了数组的粗略排序，后续只需要重复反序、穿插的操作即可。这种思路关键算法中：快速排序算法找数组中位数的平均时间复杂度O(n)，3-way-partition时间复杂度O(n)，反序、穿插时间复杂度O(n)，空间复杂度O(n)，故总体时间复杂度O(n)，空间复杂度O(n)。

### 回溯法
一般可以分为主函数和core函数：**主函数**中检查无效输入，确定递归起点，当起点可以从任一点开始时，主函数加循环；**core函数**中注意判断终止递归的条件，注意勿重复访问，设置visited数组以免进入死循环，并注意core函数return前是否需要重置visited数组。

不需要重置visited数组，所有元素只访问一次：

No.13 机器人的运动范围

包含多个答案，core函数返回前需要重置数组，以免影响其他结果：

No.17 电话号码的字母组合

No.46 全排列

No.79 单词搜索

No.131 分割回文串