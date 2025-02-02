# 代码随想录算法训练营第三十天

## 贪心算法第四天

### 考点总结

- 最少数量的箭射爆气球：先按左界排序，然后有重叠的话需要更新右界
- 无重叠区间：和上面的思路一样，只是需要return的东西不同
- 划分字母区间：需要用哈希表记录每个字母的最远出现位置，然后根据最远出现位置找区间

---

#### 用最少数量的箭引爆气球

[题目：Leetcode 452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons)

##### 简单思路

- 多个气球在不同的高度上水平移动，给出各个气球的水平移动范围
  - 需要拿箭垂直往上射，问最少需要多少根箭能把所有气球射爆
- 要先按气球的左区间排序，result初始化为一
- 气球的水平范围重叠的话，可以一根箭射爆多个气球
  - 所以只有不重叠时——i个气球左界比i-1右界大
    - 才需要多一根箭
  - 重叠时，需要更新第i个气球右边界
    - `points[i][1] = min(points[i-1][1], points[i][1])`
    - 更新为最窄的区间

##### 代码呈现

```python
class Solution(object):
    def findMinArrowShots(self, points):
        points.sort(key=lambda x: x[0]) # 把points按左界排序
        result = 1 # result初始化为1，至少需要1根箭
        for i in range(1, len(points)): # 从1开始，防止i-1超索引
            if points[i-1][1] < points[i][0]: # i-1的右界 < i的左界
                result += 1 # 不重叠，需要用多一根
            else: # 反之则重叠
                points[i][1] = min(points[i-1][1], points[i][1]) # 需要更新右界为重叠区间
        return result
```

##### 易错点

- points要先按左界排序
- result初始化是1，而非0
- 不重叠是i-1的**右**界 < i的**左**界
- 重叠需要更新区间

---

#### 无重叠区间

[题目：Leetcode 435](https://leetcode.com/problems/non-overlapping-intervals)

##### 简单思路

- 和上面一题很像，这题改为需要移除多少区间才无重叠
- 可以换个思路，仍然是统计重叠区间，然后出现重叠时更新右界，相当于移除了

##### 代码呈现

```python
class Solution(object):
    def eraseOverlapIntervals(self, intervals):
        intervals.sort(key=lambda x: x[0]) # 先排序
        result = 0 # 初始化没有重叠区间
        for i in range(1, len(interval)):
            if intervals[i-1][1] > intervals[i][0]: # 如果有重叠区间
                result += 1 # 计数 +1
                intervals[i][1] = min(intervals[i-1][1], intervals[i][1]) # 更新右界，算是移除的操作
        return result
```

##### 易错点

- 要更新区间右界，这样算是移除了，不然继续遍历的时候会重复计数

---

#### 划分字母区间

[题目：Leetcode 763](https://leetcode.com/problems/partition-labels)

##### 简单思路

- 要求将字符串拆分成多个**最小的非重复子串**，每个子串中相同的字符不能出现在别的子串里，输出这些子串的长度。
  - 比如区间里面有a/b/c，那就要保证区间里面拥有所有的abc
- 需要提前记录所有字母的最后出现位置
- 第二次遍历计算区间长度

##### 代码呈现

```python
class Solution(object):
    def partitionLabels(self, s):
        lastappear = {} # 哈希表记录最后出现位置
        for index, value in enumerate(s): # 遍历字符串
            lastappear[value] = index # 不断更新各个字母最后出现位置
        left = 0 # 区间左界
        right = 0 # 区间右界
        result = [] # 存放各个区间长度
        for index, value in enumerate(s): # 再次遍历字符串，找区间
            right = max(right, lastappear[value]) # 随着遍历不断更新右界（取max）
            if index == right: # i == right意味着右界走到尽头
                result.append(right - left + 1) # 记录此时区间结果
                left = index + 1 # 重置区间，左界为下一位i+1
        return result      
```

##### 易错点

- 两次遍历，一次需要记录每个字母最远的出现位置，第二次记录区间信息
- 用哈希表记录字母最远位置
- 记录区间长度需要`right - left + 1`
  - left要在记录后重置为下一位i + 1



