# 代码随想录算法训练营第十六天

## 二叉树第四天

### 考点总结

- 找左下角的值：直接层序找最后一层的第一个节点
- 路径总和：用一个count边遍历边减节点值，看最后有没有刚好为0
- 构造二叉树：用后序找中节点，然后用中节点切割前序或中序数组

---

#### 找树左下角的值（层序遍历）

[题目：Leetcode 513](https://leetcode.com/problems/find-bottom-left-tree-value)

##### 简单思路

- 树左下角是最下面一行的第一个节点
- 直接用层序遍历，求最后一层的第一个节点值即可

##### 代码呈现

```python
from collections import deque
class Solution:
    def findBottomLeftValue(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        queue = deque()
        queue.append(root)
        result = 0
        while queue:
            size = len(queue)
            for i in range(size):
                node = queue.popleft()
                if i == 0: # 用result记录每一层的第一个节点值
                    result = node.val
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        return result # 遍历完整个二叉树之后，最后一层的第一个节点就是想求的值
```

##### 易错点

- 树的左下角就是最后一层的第一个节点
- 用result保存每层的第一个节点，遍历完整棵树之后即是左下角

---

#### 路径总和（边遍历边减节点值，看最后是否为0）

[题目：Leetcode 112](https://leetcode.com/problems/path-sum)

[第二题113](https://leetcode.com/problems/path-sum-ii)

##### 简单思路

- 设置count初始等于目标值，边遍历节点边减节点值，如果到底的时候`count = 0`，即发现路径
- 递归遍历左右节点，到底之后如果不是目标路径，则需要回溯，count要加回节点值，使其恢复原样
- 如果需要返回路径的话，需要提前在主函数new好path和result

##### 代码呈现

```python
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if not root:
            return False
        return self.traversal(root, targetSum)
    
    def traversal(self, cur, count):
        if not cur.left and not cur.right: # 叶子节点
            return count == cur.val # 如果最后的count等于叶节点值，True
        # 往左递归，递归时count-当前节点值，如果左子树为True，传递上来True
        if cur.left and self.traversal(cur.left, count - cur.val):
            return True
        # 往右递归
        if cur.right and self.traversal(cur.right, count - cur.val):
            return True
        return False # 左右都不行，那就False
```

```python
class Solution:
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> List[List[int]]:
        if not root:
            return []
        path = [] # 一定要在主函数new path和result，不然调用子函数保存不了结果
        result = []
        self.traversal(root, targetSum, path, result)
        return result
    
    def traversal(self, cur, count, path, result):
        path.append(cur.val) # 将当前节点加入path
        if not cur.left and not cur.right and count == cur.val: # 符合条件
            result.append(path[:]) # 用[:]复制path，不然会随path改变
        if cur.left: # count要记得-当前的节点值
            self.traversal(cur.left, count - cur.val, path, result)
        if cur.right:
            self.traversal(cur.right, count - cur.val, path, result)
        path.pop() # 回溯，弹出节点
```

##### 易错点

- 关键在构造递归函数，左右递归的时候，要不断`count - cur.val`
- 要返回路径的时候，在主函数new path和result
- 递归函数符合条件是`result.append(path[:])`要记得[:]

---

####  **构造二叉树** 

[题目：Leetcode 106 中序与后序](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal)

[105 前序与中序](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal)

##### 简单思路

- 第一步：如果数组大小为零的话，说明是空节点了。`return False`
- 第二步：如果不为空，那么取后序数组最后一个元素作为节点元素。（左右中）
- 第三步：找到后序数组最后一个元素在中序数组的位置，作为切割点切中序数组（中间节点）
- 第四步：切割中序数组，切成中序左数组和中序右数组 （顺序别搞反了，一定是先切中序数组）
- 第五步：切割后序数组，切成后序左数组和后序右数组
- 第六步：递归处理左区间和右区间

![106.从中序与后序遍历序列构造二叉树](https://camo.githubusercontent.com/0b007e4086174a2587b02c3ea8eaa8dba0534ab8e639a2907e13047917cd9db5/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f32303231303230333135343234393836302e706e67)

##### 代码呈现（不想写了，下次过一遍）

```python
class Solution:
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode:
        # 第一步: 特殊情况讨论: 树为空. (递归终止条件)
        if not postorder:
            return None

        # 第二步: 后序遍历的最后一个就是当前的中间节点.
        root_val = postorder[-1]
        root = TreeNode(root_val)

        # 第三步: 找切割点.
        separator_idx = inorder.index(root_val)

        # 第四步: 切割inorder数组. 得到inorder数组的左,右半边.
        inorder_left = inorder[:separator_idx]
        inorder_right = inorder[separator_idx + 1:]

        # 第五步: 切割postorder数组. 得到postorder数组的左,右半边.
        # ⭐️ 重点1: 中序数组大小一定跟后序数组大小是相同的.
        postorder_left = postorder[:len(inorder_left)]
        postorder_right = postorder[len(inorder_left): len(postorder) - 1]

        # 第六步: 递归
        root.left = self.buildTree(inorder_left, postorder_left)
        root.right = self.buildTree(inorder_right, postorder_right)
         # 第七步: 返回答案
        return root
```

