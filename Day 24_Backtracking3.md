# 代码随想录算法训练营第二十四天

## 回溯第三天

### 考点总结

- 复原IP地址：一串数字用三个"."分割成四串，分辨每一串是否合法
- 子集：每一个递归节点都要收集，而且递归自动终止，不需写明
- 子集Ⅱ：组合Ⅱ和子集的综合版，要用used树层去重

---

#### 复原IP地址

[题目：Leetcode 93](https://leetcode.com/problems/restore-ip-addresses)

##### 简单思路

- IP地址：一串数字里面用三个"."分开，每段数字都要在0~255之间
  - 可以是0，但不能以0开头
  - 需要构造isValid函数判断每段是否合法
- startindex就是切割点，用"."切割，每段是s[startindex : i+1]
- 路径是需要一段段字符串拼起来

##### 代码呈现

```python
class Solution(object):
    def isValid(self, s, start, end): # 判断每一段是否合法
        if start > end: # 索引不合法
            return False
        if s[start] == "0" and start != end: # 以0开头不合法
            return False
        num = 0
        for i in range(start, end+1):
            if not s[i].isdigit(): # 不是数字字符的不合法
                return False
            num = num*10 + int(s[i])
            if num > 255: # 比255大，不合法
                return False
        return True
    
    def backtracking(self, s, startindex, pointsum, current, result):
        if pointsum == 3 and self.isValid(s, startindex, len(s)-1): # 已有三分割点且最后一段合法
            current += s[startindex:] # 把最后一段拼进去
            result.append(current)
            return
        for i in range(startindex, len(s)):
            if self.isValid(s, startindex, i): # 判断每一段[startindex~i]是否合法
                sub = s[startindex:i+1] # 把这一段切出来
                self.backtracking(s, i+1, pointsum+1, current+sub+".", result) # 在这一段后面加分割点，往后递归
            else: # 如果不合法，直接剪枝
                break
        
    def restoreIpAddresses(self, s):
        result = []
        self.backtracking(s, 0, 0, "", result)
        return result
```

##### 易错点

- 判断是否合法的函数难写，要把各个不合法的情况考虑清楚
  - 索引不合法：start > end
  - 以0开头不合法
  - 每一位中不是数字字符不合法
  - 总数num > 255不合法
- 递归结束条件是，已经有了三个分割点（IP地址只有三个分割点），且最后一段合法
- 这里的回溯并不需要手动调整 `pointsum`，它会通过函数参数自动回归到上一层的值。
-  `sub` 是局部变量，不影响递归的逻辑，也不需要特意回溯。

---
#### 子集

[题目：Leetcode 78](https://leetcode.com/problems/subsets)

##### 简单思路

- 收集数组中全部子集：之前的组合问题是在最后一层收割结果，现在是每一层都要记录
  - 所以result不能写在递归终止条件，而是要写在最外面，每一层递归都要实行
- 递归终止条件是`startindex == len(nums)`，不用特意写出来
  - 因为递归达到这个地步for循环会自动终止，而且收获结果环节也不在这里面

##### 代码呈现

`````python
class Solution(object):
    def backtracking(self, nums, startindex, path, result):
        result.append(path[:]) # 每次递归result都要收集结果
        for i in range(startindex, len(nums)):
            path.append(nums[i])
            self.backtracking(nums, i+1, path, result) # 从i+1之后开始，为了不重复
            path.pop() # 经典回溯
        return # 不用特意写终止条件，递归自动终止
            
    def subsets(self, nums):
        result = []
        self.backtracking(nums, 0, [], result)
        return result
`````

##### 易错点

- 虽然不用特意写终止条件，但最后还是得return
- 这里的子集是需要每一次递归都要收集，和组合问题不太一样

---

#### 子集Ⅱ

[题目：Leetcode 90](https://leetcode.com/problems/subsets-ii)

##### 简单思路

- 这道题就是组合Ⅱ+子集的结合，nums数组中有多个相同的元素
- 把树层剪枝和子集结合，用used应对树层剪枝，每一个递归都要收集结果
- 当`used[i-1] == False`的时候，说明`used[i]`与`used[i-1]`是在同一层
  - 如果不在同一层的话，要先经过i-1，`used[i-1] == True`
- 如果同一层`nums[i-1] == nums[i]`，需要跳过（去重剪枝）

##### 代码呈现

`````python
class Solution(object):
    def backtracking(self, nums, startindex, used, path, result):
    	result.append(path[:]) # 每一次递归都要收集结果
        for i in range(startindex, len(nums)):
            if i > startindex and nums[i-1] == nums[i] and not used[i-1]:
                continue # 如果是树层第二个及以后的元素，和前一个元素相同，而且同层前一个元素没用过——跳过
            path.append(nums[i])
            used[i] = True
            self.backtracking(nums, i+1, used, path, result)
            used[i] = False # 经典回溯
            path.pop()
        return
        
    def subsetsWithDup(self, nums):
        result = []
        used = [False]*len(nums) # used记录前一个元素状态，看是否在同一层
        nums.sort() # 一定要先排序，不排序做不了
        self.backtracking(nums, 0, used, [], result)
        return result
`````

##### 易错点

- 这道题两个考点，树层去重+收集子集
- 回想组合Ⅱ的逻辑，用used分辨和前一个数是否处在同一递归层
