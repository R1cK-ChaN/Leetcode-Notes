# 代码随想录算法训练营第三十一天

## 贪心算法第五天

### 考点总结

- 合并区间：直接操作result最后一个区间
- 单调递增的数字：从后往前遍历，有不符合递增就第i-1位减一，第i位变为9
- 监控二叉树：分类讨论清楚所有的状况

---

#### 合并区间

[题目：Leetcode 56](https://leetcode.com/problems/merge-intervals)

##### 简单思路

- 二维数组中有很多个区间，合并有重叠的区间——>求并集
- 和昨天的重叠区间问题类似，但是判断完重叠之后需要合并操作
- 先定义一个result数组存放结果，每次判断重叠的时候都与result数组最后一个区间比较
  - 如果有重叠的话，直接改最后一个区间的右界
  - 不重叠的话，把新区间加入result
- 这样可以避免在原数组上又合并又删除，复杂操作

##### 代码呈现

```python
class Solution(object):
    def merge(self, intervals):
        intervals.sort(key=lambda x: x[0]) # 惯例按左界排序
        result = [] # new result数组
        if len(intervals) == 0: # 先判断intervals是否为空
            return result
        result.append(intervals[0]) # 不为空的话，先把第一个区间放进result，因为后面遍历从第二个区间开始
        for i in range(1, len(intervals)):
            if result[-1][1] >= intervals[i][0]: # 和result最后一个区间有重叠
                result[-1][1] = max(result[-1][1], intervals[i][1]) # 直接修改result最后一个区间的右界，相当于合并
            else:
                result.append(intervals[i]) # 不重叠，直接加入result
        return result
```

##### 易错点

- result最后一个区间即为上一个区间，操作`result[-1]`有助于for循环从第二个区间开始
- 而且直接操作`result[-1]`也可以达到合并区间的目的

---

#### 单调递增的数字

[题目：Leetcode 738](https://leetcode.com/problems/monotone-increasing-digits)

##### 简单思路

- 给一串数字，要求返回不大于这个数字的每一位单调递增数字
- 从后往前遍历每一位，如果需要前一位比后一位大，即不递增
  - 把前一位-1，后一位变为9
- 关键是找倒序遍历后最后为9的位置
  - 这之后所有的位数都是9

##### 代码呈现

```python
class Solution(object):
    def monotoneIncreasingDigits(self, n):
        string = str(n) # 转为字符串以便切片处理
        flag = len(string) # 需要转为9的位置
        # 设置为字符串长度，为了防止第二个for循环在flag没有被赋值的情况下执行
        for i in range(len(string)-1, 0, -1):
            if string[i-1] > string[i]: # 如果不是递增
                flag = i # 记录当前的位置，需要变成9
                string = string[:i-1] + str(int(string[i-1]) - 1) + string[i:] # 只把i-1的位置-1
        for i in range(flag, len(string)):
            string = string[:i] + '9' + string[i+1:] # 把flag后面的所有数字都变为9
        return int(string)
```

##### 易错点

- 贪心思路：找到不递增的位置，然后把前一位降1，后一位变成9
- 遍历完一次字符串后，flag就是第一个9的位置，之后的所有位数，都要变成9

---

#### 监控二叉树

[题目：Leetcode 968](https://leetcode.com/problems/binary-tree-cameras)

##### 简单思路

- 二叉树上装监控，每个监控可以覆盖子节点和父节点，问至少需要多少监控

- 需要从底部叶节点往上遍历，用后序
- 每个节点有三个状态
  - 0：该节点无覆盖
  - 1：本节点有摄像头
  - 2：本节点有覆盖
- 不能把监控安到叶节点上，只能按到叶节点的父节点
  - 因此**递归的终止条件**：遇到Null要返回2（有覆盖），这样才能防止按监控在叶节点
- 单层递归逻辑，主要有如下四类情况：
  - 情况1：左右节点都有覆盖，左孩子有覆盖，右孩子有覆盖，那么此时中间节点应该就是无覆盖的状态了。
    - `if (left == 2 && right == 2) return 0;`
  - 情况2：左右节点至少有一个无覆盖的情况，则中间节点（父节点）应该放摄像头，result[0]+=1
    - `if (left == 0 || right == 0) return 1;`
  - 情况3：左右节点至少有一个有摄像头，左右孩子节点有一个有摄像头了，那么其父节点就应该是2（覆盖的状态）
    - `if (left == 1 || right == 1) return 2;`

##### 代码呈现

```python
class Solution:
         # Greedy Algo:
        # 从下往上安装摄像头：跳过leaves这样安装数量最少，局部最优 -> 全局最优
        # 先给leaves的父节点安装，然后每隔两层节点安装一个摄像头，直到Head
        # 0: 该节点未覆盖
        # 1: 该节点有摄像头
        # 2: 该节点有覆盖
    def minCameraCover(self, root: TreeNode) -> int:
        # 定义递归函数
        result = [0]  # 用于记录摄像头的安装数量
        if self.traversal(root, result) == 0:
            result[0] += 1

        return result[0]

        
    def traversal(self, cur: TreeNode, result: List[int]) -> int:
        if not cur:
            return 2

        left = self.traversal(cur.left, result)
        right = self.traversal(cur.right, result)

        # 情况1: 左右节点都有覆盖
        if left == 2 and right == 2:
            return 0

        # 情况2:
        # left == 0 && right == 0 左右节点无覆盖
        # left == 1 && right == 0 左节点有摄像头，右节点无覆盖
        # left == 0 && right == 1 左节点无覆盖，右节点有摄像头
        # left == 0 && right == 2 左节点无覆盖，右节点覆盖
        # left == 2 && right == 0 左节点覆盖，右节点无覆盖
        if left == 0 or right == 0:
            result[0] += 1
            return 1

        # 情况3:
        # left == 1 && right == 2 左节点有摄像头，右节点有覆盖
        # left == 2 && right == 1 左节点有覆盖，右节点有摄像头
        # left == 1 && right == 1 左右节点都有摄像头
        if left == 1 or right == 1:
            return 2
```

##### 易错点

- 不能用普通变量来定义result，因为普通变量在子函数中不可变
  - 只能用list或者dict定义