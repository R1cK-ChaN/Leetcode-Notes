# 代码随想录算法训练营第三十二天

## 动态规划第一天

### 考点总结

- **动态规划问题五步曲**
  1. 确定dp数组（dp table）以及下标的含义
  2. 确定递推公式：根据题目要求
  3. dp数组如何初始化：注意dp数组长度
  4. 确定遍历顺序：有的从前往后，也有的从后往前
  5. 举例推导dp数组

---

#### 斐波那契数

[题目：Leetcode 509](https://leetcode.com/problems/fibonacci-number)

##### 简单思路

- 给一个整数n，问你第n个斐波那契数是什么
  - 斐波那契数就是当前数等于前两个数之和，以此类推
- 递推公式显而易见`dp[i] = dp[i-1] + dp[i-2]`
- dp数组需要初始化长度为n+1，因为需要求dp[n]
- 遍历顺序是从前往后，`for i in range(2,n+1)`，第二个元素开始，第n个元素结束

##### 代码呈现

```python
class Solution(object):
    def fib(self, n):
        if n < 2:
            return n
        dp = [0]*(n+1) # 初始化dp数组
        dp[0] = 0 # 基线条件
        dp[1] = 1
        for i in range(2, n+1): # 遍历顺序
            dp[i] = dp[i-1] + dp[i-2] # 递推公式
        return dp[n]
```

##### 易错点

- 初始化数组需要长度为n+1
- 遍历的时候范围是(2, n+1)，从第二个遍历到第n个

---

#### 爬楼梯

[题目：Leetcode 70](https://leetcode.com/problems/climbing-stairs)

##### 简单思路

- 爬一级楼梯有一种方法，爬两级楼梯有两种方法（一步一级或者一步两级）
  - 题目要求：一步只能爬一到两级楼梯
- 应该从终局往前想，需要求第n级时的方法，那如何才能到第n级楼梯？
  - 只能从第n-1级或者n-2级楼梯上来
  - 所以第n级楼梯的方法是n-1的方法加n-2的方法，即`dp[n] = dp[n-1] + dp[n-2]`
  - 本质上还是斐波那契数

##### 代码呈现

```python
class Solution(object):
    def climbStairs(self, n):
        if n < 3:
            return n
        dp = [0]*(n+1)
        dp[1] = 1 # 一级台阶1种方法
        dp[2] = 2 # 二级台阶2种方法
        for i in range(3, n+1): # 从第三级开始
            dp[i] = dp[i-1] + dp[i-2] # 当前的方法来自于前两级方法的和
        return dp[n]
```

##### 易错点

- 不需要定义dp[0]，因为第0级台阶没有相应的方法
- 需要判断n是否 < 3，如果小于3的话就不需要进循环，不然会报错

---

#### 使用最小花费爬楼梯

[题目：Leetcode 746](https://leetcode.com/problems/min-cost-climbing-stairs)

##### 简单思路

- 多了一个cost列表，现在每上一级楼梯都要消耗一定体力值cost
  - 只有往上跳才花费体力值
- dp数组含义是：到达当前台阶所需的体力值cost
  - 两个来源：n-1级上来`dp[n-1]+cost[n-1]`，或n-2级上来`dp[n-2]+cost[n-2]`
  - 题目需要花费体力值最少，即递归公式`dp[n] = min(dp[n-1]+cost[n-1], dp[n-2]+cost[n-2])`
- 可以自由选择从第0级或者第1级开始，所以`dp[0] = dp[1] = 0`

##### 代码呈现

```python
class Solution(object):
    def minCostClimbingStairs(self, cost):
        dp = [0]*(len(cost) + 1) # 想得到dp[len(cost)]
        dp[0] = 0 # 可以自由选择一级开始跳，所以这两级无成本
        dp[1] = 0
        for i in range(2, len(cost)+1): # 从index为2开始递推，直到len(cost)
            dp[i] = min(dp[i-1]+cost[i-1], dp[i-2]+cost[i-2]) # 每次都找成本低的跳
        return dp[len(cost)] # return到达屋顶的成本
```

##### 易错点

- dp数组的index很容易搞错，我们需要求的是到达屋顶的成本
  - 因此是`dp[len(cost)]`，把cost列表全部遍历过一次，也就是说走完了全部楼梯才到屋顶
- 理清楚数组的index（到`len(cost)`）与数组的元素个数（有`len(cost）+1`个元素）的关系，不要搞乱



