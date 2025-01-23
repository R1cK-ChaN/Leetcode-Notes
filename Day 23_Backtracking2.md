# 代码随想录算法训练营第二十三天

## 回溯第二天

### 考点总结

- 组合总和：先把数组排序，然后可以对总和超出target的部分剪枝
- 组合总和Ⅱ：需要用used数组判断前一个相同的数字，是否已加入组合中
- 分割回文串：1.for loop的i用于切割；2.切片判断回文串

---

#### 组合总和

[题目：Leetcode 39](https://leetcode.com/problems/combination-sum)

##### 简单思路

- 给一串数字candidates，给一个target值，从candidates里面找数出来组合成target，可以重复使用
- 横向for循环，递归的时候startindex不要+1，因为可以重复使用数字
- 可以先把candidates排序，如果遍历到超出target之后即剪枝

##### 代码呈现

```python
class Solution(object):
    def backtracking(self, candidates, target, total, startindex, path, result):
        if total == target: # 递归终止，找到目标
            result.append(path[:]) # 将path加入result
            return
        for i in range(startindex, len(candidates)): # startindex是为了方便往下递归
            total += candidates[i] # 先加值
            if total > target: # 超出target之后，直接退出循环，把剩下的循环都剪掉
                break
            path.append(candidates[i]) # 加入path
            self.backtracking(candidates, target, total, i, path, result) # startindex不用＋1，因为可重复
            total -= candidates[i] # 回溯
            path.pop()
            
    def combinationSum(self, candidates, target):
        result = []
        candidates.sort() # 先对candidates排序，方便剪枝
        self.backtracking(candidates, target, 0, 0, [], result)
        return result
```

##### 易错点

- 往深处递归的时候，startindex为i即可，因为可以用重复的元素，不必i+1
- 要在主函数对candidates排序，方便剪枝

---

#### 组合总和Ⅱ

[题目：Leetcode 40](https://leetcode.com/problems/combination-sum-ii)

##### 考点总结

- 这题的candidates有相同数字
  - 题目要求不同索引位置的相同元素可以被选择
  - 单个组合中，相同的元素不能被重复使用，用used区分是否为同一层
    - 只在树层去重，如果在同一递归层中遇到相同数字，只允许选择第一个数字，后序要跳过
    - 如果已经递归到下一层，used[i-1]为True，说明前一个数字已经在组合中，此时可以选

##### 代码呈现

```python
class Solution(object):
    def backtracking(self, candidates, target, total, startindex, used, path, result):
        if total == target: # 递归结束条件
            result.append(path[:])
            return
        for i in range(startindex, len(candidates)):
            if i > startindex and candidates[i] == candidates[i-1] and not used[i-1]:
                continue # 树层去重，如果同一层遇到相同的元素（used[i-1]为False说明同一层），跳过
            total += candidates[i]
            if total > target: # 如果total已经大于target，剪枝
                break
            used[i] = True # 标记现在的位置used
            path.append(candidates[i])
            self.backtracking(candidates, target, total, i+1, used, path, result)
            total -= candidates[i] # 三个变量都要回溯
            used[i] = False
            path.pop()
            
    def combinationSum2(self, candidates, target):
        result = []
        used = [False]*len(candidates) # used针对每个索引位
        candidates.sort() # 一定要先排序
        self.backtracking(candidates, target, 0, 0, used, [], result)
        return result
```

##### 易错点

- 这道题需要去重，在同一树层水平去重——在for循环内部
  - 垂直方向不需要去重——往下递归不用去重
- 用一个used代表每一个索引位，如果被使用过，改变该位状态
  - 可以记录是否已经在path中，方便去重

---

#### 分割回文串

[题目：Leetcode 131](https://leetcode.com/problems/palindrome-partitioning)

##### 简单思路

- 分割一个字符串s，每一段都要是回文串
- 判断回文串——正面 == 反面
- 切割点每次切割都要后移一位（i + 1）

##### 代码呈现

```python
class Solution(object):
    def backtracking(self, s, startindex, path, result):
        if startindex == len(s): # startindex刚好超s索引的时候退出
            result.append(path[:])
            return
        for i in range(startindex, len(s)): # i是切割点
            if s[startindex:i+1] == s[startindex:i+1][::-1]: # 判断回文
                path.append(s[startindex:i+1]) # 把这一段回文加入路径
                self.backtracking(s, i+1, path, result) # 切割点后移一位递归
                path.pop()
                
    def partition(self, s):
        result = []
        self.backtracking(s, 0, [], result)
        return result
```

##### 易错点

- 回文串的判断——正读 == 反读
- 切割点的定义——for loop的 i 就是切割点