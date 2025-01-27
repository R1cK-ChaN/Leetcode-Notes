# 代码随想录算法训练营第二十七天

## 贪心算法第一天

### 考点总结

- **贪心的本质是选择每一阶段的局部最优，从而达到全局最优**。
- 将问题分解为子问题，找每个子问题的最优解，所有局部最优叠加成全局最优
- 如果没有明显的反例证明局部最优无法合成全局最优，即用贪心算法
- 贪心算法没有固定套路和模板，只能多做多见世面

---

#### 分发饼干

[题目：Leetcode 455](https://leetcode.com/problems/assign-cookies)

##### 简单思路

- 饼干为s，小孩胃口是g，需要用s满足尽量多的g
- 局部最优：先对s和g排序，用最大的s去试最大的g，满足即向前进一位
  - for循环倒序遍历g，对于每个g都用当前最大的s试

<img src="https://camo.githubusercontent.com/883e65d4020551642e812ea8265201dece621940dac0db000af1bf2150218cee/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f32303233303430353232353632382e706e67" alt="img" style="zoom:80%;" />

##### 代码呈现

```python
class Solution(object):
    def findContentChildren(self, g, s):
        g.sort() # 先对这两个排序
        s.sort()
        result = 0
        index = len(s) - 1 # 
        for i in range(len(g)-1, -1, -1): # 小孩胃口倒序
            if index >= 0 and s[index] >= g[i]: # 如果当前最大的饼干可以满足这个小孩
                result += 1 # 收集结果
                index -= 1 # 饼干指针向前移
        return  result
```

##### 易错点

- 需要先排序，不然很从最大值开始遍历
- 用饼干去试小孩胃口，从胃口最大的小孩开始试
  - 局部最优：优先用最大的饼干满足胃口尽量大的小孩

---

#### 摆动序列

[题目：leetcode 376](https://leetcode.com/problems/wiggle-subsequence)

##### 简单思路

- 求左右差值，需要差值一直是正负摆动
- 不用修改数组，只需要计数差值正负摆动的节点即可
  - `prediff >= 0 and curdiff < 0`或`prediff <= 0 and curdiff > 0`。

##### 代码呈现

```python
class Solution(object):
    def wiggleMaxLength(self, nums):
        if len(nums) <= 1: # 数组长度为0或1，直接返回数组长度
            return len(nums)
        curdiff = 0
        prediff = 0
        result = 1 # 默认最右边元素为峰值之一
        for i in range(len(nums)-1): # 防止nums[i+1]超索引
            curdiff  = nums[i+1] - nums[i]
            if (prediff >= 0 and curdiff < 0) or (prediff <= 0 and curdiff > 0):
            # 摆动的逻辑
                result += 1
                prediff = curdiff
        return result
```

##### 易错点

- 摆动的逻辑列举总结出来，然后循环遍历即可

---

#### 最大子序和

[题目：Leetcode 53](https://leetcode.com/problems/maximum-subarray)

##### 简单思路

- 局部最优，只要现有的子序和不小于0，就可以一直加数进去
  - 如果子序和小于0，就要重置子序和，因为现在已经会拖累总体最大

##### 代码呈现

```python
class Solution(object):
    def maxSubArray(self, nums):
        result = -float('inf') # 初始化result为无限小，以容纳子序和
        count = 0
        for i in range(len(nums)):
            count += nums[i] # 更新子序和
            if count > result: # result不断纳入最大子序和
                result = count
            if count < 0: # 如果现在子序和小于0
                count = 0 # 重置子序和
        return result
```

##### 易错点

- 子序和为负时，需要重置，以免拖累以后的数
- 以后的数为负时，不一定需要重置子序和，以子序和是否为负为准