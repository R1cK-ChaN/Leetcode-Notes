# 代码随想录算法训练营第十五天

## 二叉树第三天

### 考点总结

- 平衡二叉树：看左右子树高度差是否>1
- 二叉树所有路径：求路径要前序，每一个路径探索后要回溯
- 左叶子之和：左侧的叶子（叶节点+父节点的左子节点）
- 完全二叉树：一刷先用普通二叉树的后序遍历左右子树

---

#### 平衡二叉树（后序看高度差是否>1）

[题目：Leetcode 110](https://leetcode.com/problems/balanced-binary-tree)

##### 简单思路

- 如果左右子树高度差>1，即不是平衡二叉树
- 求高度，从叶节点往根节点数，用后序遍历
  - 求深度，从根节点往叶节点数，用前序遍历（之前求minmax深度时是用高度反求）
- 后序遍历求左右子树的高度，然后看abs(高度差)是否>1
- 向左右遍历的时候，如果遇到子树不平衡，就一步步往上传导

##### 代码呈现

```python
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        if self.getheight(root) == -1: # -1的都是不平衡
            return False
        else: # 不是-1，都是平衡
            return True
        
    def getheight(self, root):
        if not root:
            return 0
        leftheight = self.getheight(root.left) # 向左递归
        if leftheight == -1: # 如果左子树下面有不平衡现象
            return -1 # 说明这棵树都是不平衡，向上传递-1
        rightheight = self.getheight(root.right)
        if rightheight == -1: # 向右递归同理
            return -1
        # 中，对比左右节点的高度差，>1即是不平衡，return -1
        if abs(leftheight - rightheight) > 1:
            return -1 
        else:
            return 1 + max(leftheight, rightheight)
```

##### 易错点

- 平衡二叉树关键是看左右子树高度差，所以要构造高度函数
- 求高度都是要用后序遍历，遍历左右子树的时候发现不平衡就要直接`return -1`
- 最后主函数就看是不是-1，如果是-1就是不平衡

---

#### 二叉树所有路径（前序+回溯）

[题目：Leetcode 257](https://leetcode.com/problems/binary-tree-paths)

##### 简单思路

- 需要返回所有路径，这个方向要求只能从前序遍历，从根节点遍历到叶节点
- 如果一个路径走完，需要回溯，把节点弹出，回溯到根节点探索其他路径

##### 代码呈现

```python
class Solution:
    def binaryTreePaths(self, root: Optional[TreeNode]) -> List[str]:
        path = [] # new两个列表装path和result
        result = []
        if not root:
            return result
        self.traversal(root, path, result) # 直接进递归函数
        return result
    # 前序递归，中左右
    def traversal(self, cur, path, result):
        path.append(cur.val) # 中
        if not cur.left and not cur.right: # 没有左右子叶，意味着路径到头
            spath = '->'.join(map(str, path)) # 将路径转换成题目要求的格式
            result.append(spath)
            return
        if cur.left: # 左
            self.traversal(cur.left, path, result) # 递归探索左子树
            path.pop() # 递归探索该路径完成，回溯弹出
        if cur.right: # 右
            self.traversal(cur.right, path, result) # 递归探索右子树
            path.pop() # 探索完成，回溯弹出
```

##### 易错点

- 这里要求返回所有路径，意味着需要两个列表，path存目前路径，result存所有路径
- 探索完某个路径之后，需要path弹出该节点，向上回溯，直到根节点，才能探索其它路径

---

#### 左叶子之和（后序）

[题目：Leetcode 404](https://leetcode.com/problems/sum-of-left-leaves)

##### 简单思路

- 求左叶子之和，隐含两个要求，一个是叶子节点，一个是父节点的左侧叶节点
- 左右递归的时候，左子树有一个特殊情况
  - 即左子树只有一个节点，这个节点就是一个左叶子，这种情况需要单独处理

##### 代码呈现

```python
class Solution:
    def sumOfLeftLeaves(self, root: Optional[TreeNode]) -> int:
        if not root: # 如果不是二叉树
            return 0
        if not root.left and not root.right: # 如果只有一个节点
            return 0
        leftvalue = self.sumOfLeftLeaves(root.left) # 收集左侧的左叶节点
        if root.left and not root.left.left and not root.left.right: # 特殊情况，左子树只有一个节点
            leftvalue = root.left.val # 此时这个节点就是左侧的左叶节点
        rightvalue = self.sumOfLeftLeaves(root.right) # 收集右侧的左叶节点
        return leftvalue + rightvalue # 求和
```

##### 易错点

- 只要求左叶子，一定是左侧的叶子
- 左子树遍历的时候，有一个特殊情况需要处理

---

#### 完全二叉树的节点个数（一刷先用普通二叉树遍历）

[题目：Leetcode 222](https://leetcode.com/problems/count-complete-tree-nodes)

##### 简单思路

- 后序遍历左右子树的节点数量，最后左右相加再加一（根节点）

##### 代码呈现

```python
class Solution:
    def countNodes(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        leftnum = self.countNodes(root.left) # 左子树节点数量
        rightnum = self.countNodes(root.right) # 右子树节点数量
        return leftnum + rightnum + 1 # 左右子树 + 根节点
```

##### 易错点

- 现在这个做法与普通二叉树无异，没有用到完全二叉树性质
- 二刷要学完全二叉树的解法

