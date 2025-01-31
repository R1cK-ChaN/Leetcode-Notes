# 代码随想录算法训练营第二十八天

## 贪心算法第二天

### 考点总结

- 贪心无考点，只能见多识广

---

#### 买卖股票的最佳时机Ⅱ

[题目：Leetcode 122](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii)

##### 简单思路

- 最佳时机：给一个数组为股票价格，求这个数组中如何交易股票利润最大
- 局部最优：两两做差，得每天的价差利润，然后只取正的利润**（与0取max值）**
  - 因为只有正的利润才有正贡献

##### 代码呈现

```python
class Solution(object):
    def maxProfit(self, prices):
        result = 0
        for i in range(1, len(prices)): # 从第二天开始才能与前一天做差
            result += max(prices[i] - prices[i-1], 0) # 与0取max值，只收录正利润
        return result
```

##### 易错点

- 局部最优是只收录正利润，不要负利润
- 正利润是与0取max，`max(prices[i] - prices[i-1], 0)`

---

#### 跳跃游戏

[题目：Leetcode 55](https://leetcode.com/problems/jump-game)

##### 简单思路

- 在数组上面跳跃，每次跳跃距离是数组元素
  - 如果支持跳到数组最后，return True；不支持，return False
- 用一个cover变量统计最远能跳到哪里，如果能>=最后索引，即为True
  - `cover = max(i + nums[i], cover)`统计最远跳的地方
- i只能在cover范围内迭代，因为无法跳到cover之外的地方
  - 需要用while循环，因为py的for循环不允许加变量

##### 代码呈现

```python
class Solution(object):
    def canJump(self, nums):
        cover = 0 # 存现在所能跳跃的覆盖范围
        if len(nums) == 0: # 如果只有一个元素，肯定能跳到最后
            return True # 因为现在就是已经在最后
        i = 0 # while循环计数
        while i <= cover: # i不能超出cover
            cover = max(i+nums[i], cover) # 更新cover
            if cover >= len(nums)-1: # 如果cover比最后的索引大
                return True # 即true
            i += 1 # i往后移
        return False
```

##### 易错点

- i只能在cover内移动，所以不能用for循环，只能用while
- cover的更新要用max，只在cover变大时更新
- 只有一个元素时必定是True

---

#### 跳跃游戏Ⅱ

[题目：Leetcode 45](https://leetcode.com/problems/jump-game-ii)

##### 简单思路

- 给定一个非负整数数组 `nums`，其中 `nums[i]` 表示在索引 `i` 处最多能跳 `nums[i]` 步。求从起点（`0` 号索引）跳到终点（`len(nums)-1` 号索引）所需的最少跳跃次数。

- 要从覆盖范围出发，不管怎么跳，覆盖范围内一定是可以跳到的，以最小的步数增加覆盖范围，覆盖范围一旦覆盖了终点，得到的就是最少步数！

  - 需要统计两个覆盖范围，当前这一步的最大覆盖和下一步最大覆盖。

- **贪心策略是“每次跳到当前范围内能到达的最远处”**，但 `next_distance` 只是个“探索范围”，它会随着 `i` 变化，而不能代表实际的跳跃点。

  **跳跃的逻辑是基于 `cur_distance` 限制的，而不是 `next_distance`**。

  **如果 `next_distance` 足够大，但 `i` 还没走到 `cur_distance`，那么跳跃的次数就会过多**，从而导致结果不正确。

- 现在的目的是求最少的跳跃次数

##### 代码呈现

```python
class Solution(object):
    def jump(self, nums):
        cur_distance = 0 # 现在的最远跳跃距离
        next_distance = 0 # 下一次跳跃最远距离——会不断更新
        ans = 0 # 跳跃次数
        if len(nums) == 1: # 只有一个元素时不需跳跃
            return 0
        for i in range(len(nums)):
            next_distance = max(i + nums[i], next_distance) # 不断更新next_distance
            if i == cur_distance: # 如果此时走到现最远跳跃距离尽头
                cur_distance = next_distance # 跳跃一次
                ans += 1
                if next_distance >= len(nums)-1: # 如果这一次刚好可以覆盖全程
                    break 
        return ans # 返回结果
```

##### 易错点

- 搞清楚现在需要求最少的跳跃次数
  - 因此不到万不得已（走到现在的尽头），不跳
- 用next_distance不断记录遍历所能到的最远距离

---

#### K次取反后最大化的数组和

[题目：Leetcode 1005](https://leetcode.com/problems/maximize-sum-of-array-after-k-negations)

##### 简单思路

- 有k次取反次数，需要返回最大化的数组和
- 先把数组按照绝对值大小排序，优先取反绝对值大的负数——第一次贪心
- 如果全部负数都已经取反为正，且还有奇数次取反次数
  - 反复取反绝对值最小的，直接乘-1即可——第二次贪心
  - 如果是偶数次取反，值不变，数组和不变

##### 代码呈现

```python
class Solution(object):
    def largestSumAfterKNegations(self, nums, k):
        nums.sort(key=lambda x: abs(x), reverse=True) # 按绝对值从大到小排序
        for i in range(len(nums)):
            if nums[i] < 0 and k > 0: # 优先反转负的
                nums[i] *= -1
                k -= 1
        if k % 2 == 1: # 如果剩下的反转次数为奇数
            nums[-1] *= -1 # 翻转一次绝对值最小的
        return sum(nums) # 返回数组和
```

##### 易错点

- `nums.sort(key=..., reverse=...)`，将列表按key的规则，从大到小排序（reverse = True）
- 遍历列表，第一次贪心，优先反转负数
- 若还没用完，只操作绝对值最小的数



