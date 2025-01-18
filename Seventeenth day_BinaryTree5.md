# 代码随想录算法训练营第十七天

## 二叉树第五天

### 考点总结

- 最大二叉树：构造二叉树用前序中左右，先安排最大的节点在中间
- 合并二叉树：同步遍历两棵二叉树，将两棵树节点相加到一棵树上
- 搜索树：树有顺序，直接按二分搜索的思想确认搜索方向
- 验证搜索树：中序遍历左中右，看节点值是否一直大于前一个节点值

---

#### 最大二叉树（构造二叉树用前序遍历）

[题目：Leetcode 654](https://leetcode.com/problems/maximum-binary-tree)

##### 简单思路

- 最大二叉树：数组中最大的元素作为中节点，左右子树也是如此
- 用前序遍历中左右，先确定最大的为中节点，然后以中为分割点，左右递归子树

##### 代码呈现

```python
class Solution:
    def constructMaximumBinaryTree(self, nums: List[int]) -> Optional[TreeNode]:
        if len(nums) == 1: # 数组只有一个元素时
            return TreeNode(nums[0]) # 这个元素就是二叉树根节点
        node = TreeNode(0) # 因为数组元素全为正整数，所以初始二叉树根节点可为0
        Maxvalue = 0 # 数组元素全是正整数，都比0大
        Maxindex = 0
        for i in range(len(nums)): # 中
            if nums[i] > Maxvalue:
                Maxvalue = nums[i] # 找最大值
                Maxindex = i # 找最大值所在index
        node.val = Maxvalue # 将最大值作为二叉树根节点
        if Maxindex > 0: # 左，向中节点左侧[:Maxindex]递归
            node.left = self.constructMaximumBinaryTree(nums[:Maxindex])
        if Maxindex < len(nums) - 1: # 右，向中节点右侧[Maxindex+1:]递归
            node.right = self.constructMaximumBinaryTree(nums[Maxindex+1:])
        return node # return根节点
```

##### 易错点

- 初始化二叉树和最大值，都初始化为0，因为数组元素都是正整数
- 以中节点为中心分割数组时，一定要注意区间划分不包含Maxindex
  - 左右递归的区间都要避开Maxindex

---

#### 合并二叉树（前序遍历两个树，合并到一棵树上）

[题目：Leetcode 617](https://leetcode.com/problems/merge-two-binary-trees)

##### 简单思路

- 同步递归遍历两棵树，然后将两个树的节点值相加到一棵树上

##### 代码呈现

```python
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 == None: # 两个结束条件
            return root2 # 树1为空，值就是树2的，反之亦然
        if root2 == None: # 这种写法天然包含了两个都为空的情况
            return root1
        root1.val += root2.val # 将两棵树的值合并到树1上
        root1.left = self.mergeTrees(root1.left, root2.left) # 同步递归遍历
        root1.right = self.mergeTrees(root1.right, root2.right)
        return root1 # return合并后的树
```

##### 易错点

- 注意结束条件，一棵树为空，就return另一棵树
- 直接将合并结果更新在树1即可，不用new新树

---

#### 二叉搜索树中的搜索（根据大小关系决定搜索方向）

[题目：Leetcode 700](https://leetcode.com/problems/search-in-a-binary-search-tree)

##### 简单思路

- 二叉搜索树：左子树所有节点 < 根节点 < 右子树所有 节点——自带顺序
  - 非常方便搜索，可以直接从大小关系来决定往哪个方向搜

##### 代码呈现

```python
# 递归法
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        if root == None or root.val == val: # 递归退出条件，要么root为空，要么找到元素
            return root
        if val < root.val: # 数值比中节点小，在左侧
            return self.searchBST(root.left, val)
        elif val > root.val: # 数值比中节点大，在右侧
            return self.searchBST(root.right, val)
```

```python
# 迭代法
class Solution:
    def searchBST(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        while root != None: # 循环条件
            if val < root.val: # 数值小于中节点
                root = root.left # 往左迭代
            elif val > root.val: # 数值大于中节点
                root = root.right # 往右迭代
            else:
                return root
```

##### 易错点

- 二叉搜索树自带顺序，有点类似于二分搜索，从大小关系中获知搜索的方向
- 递归的基线条件or合并很关键

---

#### 验证二叉搜索树（中序左中右判断大小关系）

[题目：Leetcode 98](https://leetcode.com/problems/validate-binary-search-tree)

##### 简单思路

- 二叉搜索树：左子树所有节点 < 中节点 < 右子树所有节点
- 用中序遍历，左中右，搜索树应该一直是前一个节点值小于现节点值
  - 左：先递归左子树，用pre指针保存上一个节点值
  - 中：对比上一个节点值和现在的节点值大小关系，如果pre>=cur，不符合
  - 右：递归右子树
  - 最后综合比较左右子树的结果`return left and right`

##### 代码呈现

```python
class Solution:
    def __init__(self): # new一个变量存放上一个节点值
        self.pre = None # 初始化为None，第一轮不比较
        
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root == None: # 如果二叉树为空，那这个二叉树满足所有种类二叉树
            return True
        left = self.isValidBST(root.left) # 左
        if self.pre != None and self.pre.val >= root.val: # 中
            return False # 中节点值与前一个节点值对比，看是否满足搜索树定义
        self.pre = root # pre保存上一个节点的值
        right = self.isValidBST(root.right) # 右
        return left and right # 左右子树是否满足
```

##### 易错点

- 空二叉树也是二叉搜索树
- pre变量初始化是None，在第一轮的时候不和root比较，直接保存第一轮root的值
  - 从第二轮开始才与root比较

