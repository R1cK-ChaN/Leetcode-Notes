# 代码随想录算法训练营第三十六天

## 动态规划第四天

### 考点总结

- 尽量把一个问题拆成两个子集
- 然后通过找子集的极限，用dp背包问题去找极限

---

#### 最后一块石头的重量Ⅱ

[题目：Leetcode 1049](https://leetcode.com/problems/last-stone-weight-ii)

##### 简单思路：

- 有一堆石头，需要你用石头两两相碰，求最后剩下的最少石头是什么
  - 先把这一堆尽可能分成两堆相等的，然后用这两堆石头相碰即可
  - 背包的target就是尽可能逼近sum // 2
- dp数组含义：`dp[j]`是背包容量为`j`中石头的最大价值
  - 注意，本题石头的重量weight和价值value也是相等的
    - 递推公式weight和value是同一个数
- dp数组初始化，最后一个数是dp[target]
- 最后求剩下的最小值，就是dp数组最大，即`dp[-1]`
  - `sum - 2 * dp[-1]`sum减去两堆尽量大的子堆

##### 代码呈现

```python
class Solution:
    def lastStoneWeightII(self, stones: List[int]) -> int:
        target = sum(stones) // 2 # 分成尽量相等的两个子堆
        dp = [0] * (target + 1) # 用子堆的石头作为目标
        for stone in stones: # 遍历物品
            for j in range(target, stone-1, -1): # 遍历背包容量(stone,target)中倒序遍历
                dp[j] = max(dp[j], dp[j-stone] + stone)
        return (sum(stones) - 2 * dp[-1]) # 用两个最大的dp对撞
```

##### 易错点

- 需要先把问题分成两个子堆相撞，而不是两块石头
- 子堆才是背包的目标，需要尽量逼近
- 最后求最少剩下的石头，就是用最大的背包(`dp[-1]`)对撞

---

#### 目标和

[题目：Leetcode 494](https://leetcode.com/problems/target-sum)

##### 简单思路

- 给一个数组和一个target数，求只用加减，数组元素组合成target有几种方法
- 需要找机会把数组分成两个子数组
  - 前面是+号的一组(left)，前面是-号的一组(right)
  - 构造方程组
    - left + right = sum(nums)：所有的元素加起来就是数组的和
    - left - right = target：把符号带上之后就是target
    - 解方程可得：left = (target - sum(nums)) // 2
  - 这个left就是被背包的目标容积，确定`dp[left]`即确定全部
- dp数组含义：填满`j`容量的背包，有`dp[j]`种方法。
- 初始化：`dp[0] = 1`装满0容量的背包有1种方法——什么都不放
- 递归方程：类比爬楼梯，当前状态是由上一个状态转移得来
  - `dp[j] += dp[j - num]`

##### 代码呈现

```python
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        sum_ = sum(nums) # 数组总和
        if abs(target) > sum_: # 判断是否合法
            return 0
        if (target + sum_) % 2 != 0: # left一定需要是整数
            return 0
        left = (target + sum_) // 2 # 解方程组得出
        dp = [0] * (left + 1)
        dp[0] = 1 # 初始化
        for num in nums:
            for j in range(left, num-1, -1):
                dp[j] += dp[j-num] # 状态转移
        return dp[-1] # 返回dp[left]
```

##### 易错点

- 需要列方程组把一个数组，分成正负两个子数组
- 对其中之一的子数组求最大组合可能性
- dp数组含义就是，left子数组中，组合方法有`dp[left]`种
- 当前的组合种类，取决于上一个状态的，所以转移方程是叠加关系

---

#### 一和零

[题目：Leetcode 474](https://leetcode.com/problems/ones-and-zeroes)

##### 简单思路

- 给一个数组，里面有很多字符串，你需要选择数组中的字符串，让它们的0有m个，1有n个
  - 最后返回选择了多少个元素（子集元素个数）
- 背包问题，但是这个背包有两个维度需要衡量（0和1）
- dp数组含义：需要二维数组`dp[i]][j]`，最多有i个0和j个1的strs的最大子集的大小为`dp[i][j]`
  - 目标需要求`dp[m][n]`
- 初始化`dp[0][0] = 0`：0个0和0个1的最大子集就是0
- 遍历顺序，先遍历物品，即先遍历字符串
  - 统计每个字符串中0和1的个数
  - 再倒序遍历容量，分别倒序遍历背包0和1位置

##### 代码呈现

```python
class Solution:
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        dp = [[0] * (n+1) for _ in range(m+1)] # 需要构造到dp[m][n]，所以都要(m+1)/(n+1)
        for s in strs: # 遍历物品
            zeronum = s.count("0") # 看每个元素里面有多少0和1
            onenum = len(s) - zeronum
            for i in range(m, zeronum-1, -1): # 遍历背包，0的维度
                for j in range(n, onenum-1, -1): # 1的维度
                    dp[i][j] = max(dp[i][j], dp[i-zeronum][j-onenum] + 1) 
                    # zeroNum和oneNum相当于重量（weight[i]），字符串个数相当于价值（value[i]）
        return dp[m][n]
```

##### 易错点

- 二维数组需要构造到`dp[m][n]`，因此需要(m+1)&(n+1)
- 遍历物品时需要统计0和1的个数
- 遍历背包时需要两重遍历两个维度，两个维度顺序可以调换

