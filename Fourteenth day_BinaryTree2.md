# 代码随想录算法训练营第十四天

## 二叉树第二天

### 考点总结

- 

---

#### 翻转二叉树（前序递归）

[题目：Leetcode 226](https://leetcode.com/problems/invert-binary-tree)

##### 简单思路

- 翻转二叉树，本质是交换左右子节点和子节点下面的子树
- 优先递归写法，确定递归三要素
  - 函数的参数与返回值：原二叉树root，返回反转后二叉树root
  - 终止条件：root遍历为空的时候停止
  - 单层递归逻辑：前序遍历中左右，先交换左右子节点，再分别交换左右子树
- 后序遍历就是左右中，先单层递归交换左右子树，再交换左右子节点（与前序顺序有别）

##### 代码呈现

```python
def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
# 前序递归
    if not root: #递归结束条件
        return
    root.left, root.right = root.right, root.left # 中：交换左右孩子节点
    self.invertTree(root.left) # 左：往左子树递归
    self.invertTree(root.right) # 右：往右子树递归
    return root
```

##### 易错点

- 前序递归是先交换左右子节点，后往左右子树递归
- 后序是先往左右子树递归，再交换左右子节点，和前序只有操作顺序差别
- 中序不能按前后序的逻辑微调操作顺序
  - 中序是往某子树方向递归，交换左右节点，最后还往刚才的方向递归

---

#### 对称二叉树（后序递归）

[题目：Leetcode 101](https://leetcode.com/problems/symmetric-tree)

##### 简单思路

- 对比根节点的左右两棵子树是否对称

- 先遍历再对比，遍历时把不相等的情况直接排除

  - 遍历两棵二叉树需要的判断：

  | 左子树 | 右子树 |       是否对称        |
  | :----: | :----: | :-------------------: |
  |   空   | 不为空 |         False         |
  | 不为空 |   空   |         False         |
  |   空   |   空   |         True          |
  | 不为空 | 不为空 | 但左右值不相等，False |

  - 递归对比：
    - 外侧节点对比——左子树的左节点，右子树的右节点对比
    - 内侧节点对比——左子树右节点，右子树左节点对比
    - 如果内外侧节点对比都为True，`return True`

##### 代码呈现

```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root: # 判断不为空
            return
        return self.isCompare(root.left, root.right) # 调用比较函数
    
    def isCompare(self, left, right): # 比较左右子树是否对称
        if left == None and right != None: # 左空，右不空，不对称
            return False
        elif left != None and right == None: # 左不空，右空，不对称
            return False
        elif left == right == None: # 左右都为空，对称
            return True
        elif left.val != right.val: # 左右都不为空，但是值不相等，不对称
            return False
        outside = self.isCompare(left.left, right.right) # 对比外侧，左子树：左 右子树：右
        inside = self.isCompare(left.right, right.left) # 对比内侧，左子树：右 右子树：左
        isSame = outside and inside # 逻辑处理：左子树：中 右子树：中
        return isSame
```

##### 易错点

- 本题关键是先遍历再对比
- 遍历是同时遍历了左右子树两棵二叉树，所以可以直接把不对称的情况排除
- 对比的时候采取的是后序递归，先对比左右子节点，再逻辑处理中节点，即“左右中”

---

#### 二叉树最大深度（后序遍历求高度）

[题目：Leetcode 104](https://leetcode.com/problems/maximum-depth-of-binary-tree)

##### 简单思路

- 二叉树有高度和深度
  - 高度：从叶子数起，到根节点的距离——后序遍历
  - 深度：从根节点数起，到叶子的距离——前序遍历
  - **二叉树根节点的高度，其实就是二叉树的最大深度**
- 先递归求出左右子节点的高度，max取最大，在最大的基础上+1，即是当前节点的最大高度

```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root: # 如果二叉树为空，深度0
            return 0
        leftheight = self.maxDepth(root.left) # 递归求左子树高度
        rightheight = self.maxDepth(root.right) # 递归求右子树高度
        maxdepth = max(leftheight, rightheight) # 左右子树高度取最大
        return maxdepth
```

##### 易错点

- 本题求最大深度，需要用前序遍历，但是复杂
  - 可以正难则反，根节点高度即是二叉树最大深度
- 节点的高度 = 左右子节点的最大高度 + 1
  - 因为子节点属于下一层，所以要+1，这是递归向上传导的逻辑

---

#### 

