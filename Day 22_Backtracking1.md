# 代码随想录算法训练营第二十二天

## 回溯法第一天

### 考点总结

- 回溯法，一般可以解决如下几种问题：
  - 组合问题：N个数里面按一定规则找出k个数的集合
  - 切割问题：一个字符串按一定规则有几种切割方式
  - 子集问题：一个N个数的集合里有多少符合条件的子集
  - 排列问题：N个数按一定规则全排列，有几种排列方式
  - 棋盘问题：N皇后，解数独等等
- 横向for循环遍历，纵向递归遍历
- 剪枝也需要从横向与纵向考虑，可能这两个方向都有剪枝机会

![img](https://camo.githubusercontent.com/247bf69af05a2113093dca2d31134da34e32c15aacda60ebd319a37108633acc/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f32303231303133303137333633313137342e706e67)

```python
# 回溯法模板
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```

---

#### 组合问题

[题目：Leetcode 77](https://leetcode.com/problems/combinations)

##### 简单思路

- 组合：从n个数（1~n）中抽k个数出来，不要求排序
  - n决定了树的宽度，k决定树的深度
- 经典回溯问题，横向for循环，纵向递归
- 设置path记录每一条递归路径，result记录详细组合
- 纵向递归遍历终止条件，path的size等于k即终止
- 递归到终点之后需要回溯，从path弹出元素才能递归看其他路径

##### 代码呈现

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        result = [] # 存放所有path
        self.backtracking(n, k, 1, [], result) # 初始startindex为1，path为空
        return result
    def backtracking(self, n, k, startindex, path, result):
        if len(path) == k: # 如果path的长度为k，符合条件终止递归
            result.append(path[:]) # 将符合条件的path加入result
            return
        for i in range(startindex, n - (k-len(path)) + 2): # 这里n - (k-len(path)) + 2是为了剪枝
            path.append(i)
            self.backtracking(n, k, i+1, path, result) # 更新startindex，往下递归
            path.pop() # 要回溯，弹出元素才能搜索其他路径
```

##### 易错点

- 横向for循环，剪枝条件需要注意`n - (k-len(path)) + 2`，逻辑如下（此时剪枝剪树的宽度）：

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250122143537005.png" alt="image-20250122143537005" style="zoom: 67%;" />

- 往下递归之后，需要更新startindex和弹出path

---

#### 组合总和Ⅲ

[题目：Leetcode 216](https://leetcode.com/problems/combination-sum-iii)

##### 简单思路

- 1~9中，挑k个数出来，他们的和为n
  - 树的宽度（for循环）为1~9，树的深度为k，n是targetsum
- 有两个剪枝条件
  - 剪宽度——for loop剪枝，和上面一样
  - 剪深度——如果sum超出targetsum，即剪

##### 代码呈现

```python
class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        result = []
        self.backtracking(n, k, 1, 0, [], result)
        return result
        
    def backtracking(self, targetsum, k, startindex, sum_, path, result):
        if sum_ > targetsum: # 深度剪枝，超出target不必再向下
            return
        if len(path) == k and sum_ == targetsum: # 满足条件的
            result.append(path[:])
            return # 结束递归
        for i in range(startindex, 9 - (k-len(path)) + 2): # 宽度剪枝
            path.append(i)
            sum_ += i
            self.backtracking(targetsum, k, i+1, sum_, path, result) # 更新startindex递归
            sum_ -= i # 回溯sum_
            path.pop() # 回溯path
```

##### 易错点

- 函数有很多参数，可以先写回溯函数，需要用到什么参数就加上去
  - 最后在主函数中调用回溯函数时，对着回溯函数一个个往里面填参数对应值
- 剪枝分为深度和宽度剪枝
- 回溯时，不单单要弹出path，也要回溯sum_

---

#### 电话号码的字母组合

[题目：Leetcode 17](https://leetcode.com/problems/letter-combinations-of-a-phone-number)

##### 简单思路

- 电话号码与字母有对应关系，给一串数字字符串，看有多少字母组合
- 用一维数组来表示电话号码与字母的映射，数组元素是字母，元素index是数字
- 数字字符串的长度，代表了树的深度
- 数字字符串的每一个数字，代表了每一层的宽度

##### 代码呈现

```python
class Solution:
    def __init__(self): # 初始化需要用到的值
        self.lettermap = [" "," ","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"]
        self.result = [] # 存所有路径
        self.s = "" # 字符串存单个路径
        
    def backtracking(self, digits, index):
        if index == len(digits): # index超数组索引时终止
            self.result.append(self.s) # 将当前路径s加入result
            return
        digit_num = int(digits[index]) # 将字符串digits的数字转为整数
        letter = self.lettermap[digit_num] # 用数字去映射数组找对应字母串
        for i in range(len(letter)): # 遍历字母串
            self.s += letter[i] # 记录路径
            self.backtracking(digits, index+1) # index+1，递归遍历digits中下一个数字
            self.s = self.s[:-1] # 回溯，移除最后一个字母
    
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits: # 如果没有元素
            return self.result
        self.backtracking(digits, 0) # index初始为0
        return self.result
```

##### 易错点

- 递归终止条件：`index == len(digits)`超列表索引，终止
  - 在`len(digits) - 1`时指向最后一个数字，还需要继续走一遍，不能终止
- 字符串digits元素 --> 数字 --> 映射的字母 --> 递归下一个digits元素 --> 回溯弹出
  - 字符串回溯弹出是`self.s = self.s[：-1]`，移除最后一个字母

