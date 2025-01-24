# 代码随想录算法训练营第二十四天

## 回溯第三天

### 考点总结

- 

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

