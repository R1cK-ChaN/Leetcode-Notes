#  代码随想录算法训练营第二十一天

## 二叉树第八天

### 考点总结

- 修剪搜索树：两个递归，一个修剪，一个遍历节点
- 有序数组转为平衡二叉树：数组中间为根节点，分别递归左右两边
- 搜索树变累加树：右中左遍历，pre指针存上一个节点值

---

#### 修剪二叉搜索树（双重递归，一递归剪枝，一递归遍历）

[题目：Leetcode 669](https://leetcode.com/problems/trim-a-binary-search-tree)

##### 简单思路

- 修剪搜索树：只保留在范围之内的，删除范围之外的节点
- 需要两层递归，一个递归用于遍历所有节点，就像是前面的题目一样
- 递归遍历的基线条件中也要有一个递归，去判断这个节点应该被如何修剪
  - 如果小于范围，就往节点的右边递归，看其右子树有没有在范围内的
  - 如果大于范围，就往节点左边递归，看其左子树有没有在范围内的

##### 代码呈现

```python
class Solution(object):
    def trimBST(self, root, low, high):
        if not root:
            return None
        if root.val < low: # 如果小于左边界
            return self.trimBST(root.right, low, high) # 看一下右子树有没有符合条件的
        if root.val > high: # 如果大于右边界
            return self.trimBST(root.left, low, high) # 看一下左子树有没有符合条件的
        root.left = self.trimBST(root.left, low, high) # 这个递归是用于遍历节点
        root.right = self.trimBST(root.right, low, high)
        return root # 搞定返回根节点
```

##### 易错点

- 用两层递归做，一层判断怎么剪枝，一层遍历所有节点
- 判断剪枝的时候
  - 如果小于low，就往右边递归，看右子树有没有符合条件的
  - 如果大于high，就往左边递归，看左子树有没有符合条件

---

#### 将有序数组转为平衡搜索树（数组中间数为根节点，向左右两边递归）

[题目：Leetcode 108](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree)

##### 简单思路

- 有序数组转为平衡二叉搜索树：
  - 先找中间元素为根节点，往左右两边递归
- 终止条件是区间左右越界`left > right`

##### 代码呈现

```python
class Solution(object):
    def traversal(self, nums, left, right):
        if left > right: # 递归终止条件，不符合左闭右闭条件
            return None
        mid = left + (right-left)/2 # 找中间元素
        root = TreeNode(nums[mid]) # 中间元素作为根节点
        root.left = self.traversal(nums, left, mid-1) # 向数组左侧递归，更新右边界
        root.right = self.traversal(nums, mid+1, right) # 向数组右侧递归，更新左边界
        return root
    
    def sortedArrayToBST(self, nums):
        return self.traversal(nums, 0, len(nums)-1) # 向递归函数传入数组和左右界指针
```

##### 易错点

- 递归终止条件是`left > right`，此时直接返回None
- 将中间元素作为根节点，然后分别向数组左右两侧递归生成左右子树
  - 递归时要不断更新left, right, mid这三个变量

---

#### 将搜索树转变为累加树（右中左遍历，pre指针存上一个节点值）

[题目：Leetcode 538](https://leetcode.com/problems/convert-bst-to-greater-tree)

##### 简单思路

- 累加树：从最大的数开始，往前累加，不断将后面大的值累加到前面小的
- 二叉搜索树中，最右下角的值最大，所以遍历累加顺序是“右中左”
- 累加时，用双指针的思想，定义pre保存上一个节点值，然后与cur相加

##### 代码呈现

```python
class Solution(object):
    def traversal(self, cur):
        if not cur: # 递归见底，直接返回
            return 
        self.traversal(cur.right) # 从右下角，最大的节点开始
        cur.val += self.pre # 中，累加之前的节点值
        self.pre = cur.val # pre保存上一个节点
        self.traversal(cur.left) # 左
        
    def convertBST(self, root):
        self.pre = 0 # 主函数初始化pre
        self.traversal(root)
        return root
```

##### 易错点

- 主函数初始化pre，不然pre会被重置
- 要从最大节点开始累加，所以搜索树递归顺序是右中左