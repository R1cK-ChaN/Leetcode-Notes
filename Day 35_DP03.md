# 代码随想录算法训练营第三十五天

## 动态规划第三天（背包问题）

### 考点总结

- 优先用一维数组，二维数组行列处理很抽象
- 注意一维数组需要初始化(bagweight + 1)个位置
- 一维数组for循环需要严格先物品，再倒序背包（从bagweight到weight）

![416.分割等和子集1](https://camo.githubusercontent.com/f1258a3bad42cfbcbdc4ae677edc56ceb3d17e009a7f3fd722a9dea6f15234de/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f32303231303131373137313330373430372e706e67)

---

#### 二维数组0-1背包

##### 简单思路

- 题意：背包只有一个，物品有多个，每种物品数量只有1个，问怎么选背包内物品价值最大
- dp数组含义：`dp[i][j]`在`[0, i]`物品中任选，容量为`j`的背包内**最大的价值**
- 递推公式：两种情况——是否放物品`i`
  - 不放物品`i`：`dp[i-1][j]`容量为`j`的背包只在`[0, i-1]`个物品中任选
  - 放物品`i`：`dp[i-1][j-weight[i]] + value[i]`此时确定要放`i`，那么价值可分为
    - 预留物品`i`的重量后的背包，在`[0, i-1]`的物品中任选，即`dp[i-1][j-weight[i]]`
    - 物品`i`的价值，即`value[i]`
  - 递推公式，需要在不放物品`i`和放物品`i`中取max值
    - `dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]] + value[i])`
- dp数组初始化：最左边和最上边
  - 背包重量为0的那一列都是0，因为放不进东西
  - 物品0的那一行需要判断是否符合背包容量，不符合也是0

![动态规划-背包问题2](https://camo.githubusercontent.com/75e71021dbb122296ed151c58005139cf2692a9be3569e13ef3170d4a9c441b2/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f323032313031313031303330343139322e706e67)

- 遍历顺序：两个for循环，一个遍历物品，一个遍历背包，其实先后顺序都可以，先遍历物品会清晰一点



---

#### 一维数组0-1背包

[卡玛网 46](https://kamacoder.com/problempage.php?pid=1046)

##### 简单思路

- 二维数组中，递推的来源是左上方和正上方的格子
  - 那为什么不把上面一行的内容，全部复制到这一行，只在一行上操作
  - 把二维矩阵压缩到一行数组，然后在这一行上滚动
- dp数组含义：容量为`j`的背包的最大价值为`dp[j]`
- dp递归公式：`dp[j] = max(dp[j], dp[j-weight[i]] + value[i])`，加i之前和加i之后取大值
- 初始化：`dp[0] = 0`
- 遍历顺序：两层for循环，先物品（正序遍历）再背包（倒序遍历）
  - 背包需要倒序遍历（正序遍历会重复添加同一个物品进背包）

##### 代码呈现

```python
def bag(n, bagweight, weight, value):
    dp = [0] * (bagweight + 1) # 初始化dp数组，最后一位是dp[bagweight]
    for i in range(n):
        for j in range(bagweight, weight[i]-1, -1): # 倒序遍历背包容量（从bagweight到weight[i]）
            dp[j] = max(dp[j], dp[j-weight[i]] + value[i])
    print(dp[-1])
    
n, bagweight = map(int, input().split())
weight = list(map(int, input().split()))
value = list(map(int, input().split()))
bag(n, bagweight, weight, value)
```

##### 易错点

- dp数组的初始化，index数量需要初始化到背包容量
- for循环遍历时，需要先顺序遍历物品，再倒序遍历背包（从bagweight，到weight[i]）
- dp数组最后一位(`dp[-1]`)即是最大容量

---

#### 分割等和子集

[题目：Leetcode 416](https://leetcode.com/problems/partition-equal-subset-sum)

##### 简单思路

- 给一个数组，问能否把数组分成两个子数组，子数组的和相等
- 转换为背包问题，先求数组和的一半(target)作为背包容量，然后问背包能否恰好装满
- dp数组含义：元素的重量和价值相等，需要找到`dp[target] == target`
  - 左边index是代表重量，右边target是代表价值
- dp递归公式：`dp[j] = max(dp[j], dp[j-num] + num)`
  - weight和value相等，都是num
- 初始化：`dp[0] = 0`因为不可能为负，而且需要尽量小的正数，才能不影响max函数
- 遍历顺序，和一维背包问题一致，先遍历物品，再遍历背包

##### 代码呈现

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        if sum(nums) % 2 != 0: # 如果sum是奇数，直接false
            return False
        target = sum(nums) // 2 # target是sum的一半
        dp = [0] * (target + 1)
        for num in nums:
            for j in range(target, num-1, -1):
                dp[j] = max(dp[j], dp[j-num] + num)
        return dp[target] == target # 检测是否达成目标
```

##### 易错点

- 这道题的关键就是，把问题转换成背包问题
  - 而且是0-1背包，物品的重量和价值相等
  - 所以目标是`dp[target] == target`
  - 装target重量的东西，价值等于target
- 遍历时，也是需要先物品，再背包
  - 而且背包容量需要倒序遍历
- 最后需要检查是否达成目标
