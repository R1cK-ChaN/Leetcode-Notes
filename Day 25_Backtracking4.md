# 代码随想录算法训练营第二十五天

## 回溯第四天

### 考点总结

- 递增子序列：set树层去重，递增判断树枝去重
- 全排列：used树枝去重
- 全排列Ⅱ：used树枝去重 + 树层去重

---

#### 递增子序列

[题目：Leetcode 491](https://leetcode.com/problems/non-decreasing-subsequences)

##### 简单思路

- 要求收集所有的递增子序列
  - 序列不能事先排序，排序之后的递增子序列会变化
  - 树层：同一层还是不能取之前用过的——set去重
  - 树枝：子序列要求递增，如果比序列最后一个数小的话也不行
- 这题不能先排序，所以要用set进行树层去重，而不是used数组
- 把用过的加入set，遍历后面时，如果set中有相同的元素就要跳过

`````python
class Solution(object):
    def backtracking(self, nums, startindex, path, result):
        if len(path) > 1: # 需要收集所有递增子序列，只要长度大于1即收集
            result.append(path[:])
        uset = set() # 新建set存已经遍历过的同层元素  
        for i in range(startindex, len(nums)):
            if (path and nums[i] < path[-1]) or nums[i] in uset:
                continue # 剪枝：子序列不是递增或同层用过
            path.append(nums[i])
            uset.add(nums[i]) # 收入set中表示用过
            self.backtracking(nums, i+1, path, result)
            path.pop()
            
    def findSubsequences(self, nums):
        result = []
        self.backtracking(nums, 0, [], result)
        return result
`````

##### 易错点

- 不能事先排序，要用set记录已使用的同层节点
- set不需要回溯弹出，因为set每次递归都会重新创建一次，作用仅限于当前递归层

---

#### 全排列

[题目：Leetcode 46](https://leetcode.com/problems/permutations)

##### 简单思路

- nums没有重复元素，求数组nums的所有排列情况
  - 和组合不同，组合需要有startindex去重，保证不会树层重复取数
  - 排列不需要，排列是要确保树枝不重复取数即可
    - 用used确保不会在树枝上重复取数，取了数就标记为1
- 最后在叶节点收割结果，即`len(path) == len(nums)`保证走到最后一层

##### 代码呈现

```python
class Solution(object):
    def backtracking(self, nums, used, path, result):
        if len(path) == len(nums): # 路径与数组同长，说明到最底层
            result.append(path[:]) # 收割结果
            return
        for i in range(len(nums)):
            if not used[i]: # 如果used[i]为False，说明没被用过
                used[i] = True # 标记用过
                path.append(nums[i])
                self.backtracking(nums, used, path, result)
                used[i] = False # 回溯
                path.pop()
                
    def permute(self, nums):
        result = []
        used = [False]*len(nums)
        self.backtracking(nums, used, [], result)
        return result
```

##### 易错点

- 排列与组合不同，组合需要树层去重，排列需要树枝去重
- 在底部收割结果

---

#### 全排列Ⅱ

[题目：Leetcode 47](https://leetcode.com/problems/permutations-ii)

##### 简单思路

- 现在nums有数值相同的元素，需要排序后，做树层去重
- 就是两个方向的去重逻辑
  - 树层上，与前一个数相同时，`used[i-1] == False`前一个数没用过，和组合Ⅱ去重一样，需跳过
  - 树枝上，这个数用过，`used[i] == True`，不能再用，和全排列Ⅰ一样，需跳过

##### 代码呈现

```python
class Solution(object):
    def backtracking(self, nums, used, path, result):
        if len(path) == len(nums): # 终止条件和上面一样
            result.append(path[:])
            return
        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
                continue # 树层去重
            if path and used[i]: # 树枝去重
                continue
            path.append(nums[i])
            used[i] = True
            self.backtracking(nums, used, path, result)
            used[i] = False # 回溯
            path.pop()
            
    def permuteUnique(self, nums):
        result = []
        used = [False]*len(nums)
        nums.sort() # 需要先排序，因为树层去重
        self.backtracking(nums, used, [], result)
        return result
```

##### 易错点

- 两个方向都要去重，树层和树枝，需要两个去重逻辑判断
- 需要在主函数对nums排序



