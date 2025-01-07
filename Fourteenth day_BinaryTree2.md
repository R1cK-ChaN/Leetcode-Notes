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

#### 
