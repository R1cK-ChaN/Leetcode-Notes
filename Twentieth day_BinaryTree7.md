# 代码随想录算法训练营第二十天

## 二叉树第七天

### 考点总结

- 二叉搜索树的公共祖先：大小在pq之间，p在左边，q在右边时为公共祖先
- 二叉搜索树的插入：直接在最底层插入不用改变树的结构
- 二叉搜索树的删除：五类情况分类讨论，最难就是中间节点的删除，需要调整树结构

---

#### 二叉搜索树最近的公共祖先（用搜索树大小顺序确定位置）

[题目： Leetcode 235](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree)

##### 简单思路

- 搜索树的大小有顺序，最近的公共祖先要做到大小恰好在p和q之间
- 大小在p和q之间，意味着p在左边，q在右边，此时就是最近的公共祖先
- 递归逻辑：
  - 如果值比pq大，意味着pq都在左边，往左递归
  - 如果值比pq小，意味着pq都在右边，往右递归
  - 其它情况，值恰好在pq之间，此时就是公共祖先

##### 代码呈现

```python
class Solution(object):
    def traversal(self, cur, p, q):
        if not cur: # pq都没找到
            return None
        if cur.val > p.val and cur.val > q.val: # 大于pq的值，pq在左边
            left = self.traversal(cur.left, p, q) # 往左递归
            if left != None: # 如果左侧找到了，就向上返回
                return left
        if cur.val < p.val and cur.val < q.val: # 小于pq，pq在右边
            right = self.traversal(cur.right, p, q) # 向右递归
            if right != None: # 右侧找到
                return right
        else：
        	return cur # 其他情况就是刚好在pq之间
    
    def lowestCommonAncestor(self, root, p, q):
        return self.traversal(root, p, q)
```

##### 易错点

- 因为是二叉搜索树，直接用有序的性质左右搜就行
- 注意左右搜的时候要加判断，如果搜到了（不为空），就要向上返回

---

#### 二叉搜索树中的插入（直接在叶子节点插入，不用改变树的结构）

[题目：Leetcode 701](https://leetcode.com/problems/insert-into-a-binary-search-tree)

##### 简单思路

- 在搜索树中插入一个节点：可以有多种插入方法，在最下面叶子层插入最方便
  - 因为不用改变二叉树的结构
- 节点值一直与val对比
  - 如果val<节点值，val要在左边，往左递归
  - 如果val>节点值，val要在右边，往右递归

##### 代码呈现

```python
class Solution(object):
    def insertIntoBST(self, root, val):
        if not root: # 递归到了最底层
            node = TreeNode(val) # 创建一个新节点插入
            return node # 把这个新节点向上返回
        if val < root.val: # 当前节点值比val大
            root.left = self.insertIntoBST(root.left, val) # val需要在左边，往左遍历
        if val > root.val: # 当前节点值比val小
            root.right = self.insertIntoBST(root.right, val) # val需要在右边，往右遍历
        return root # 插入后返回根节点
```

##### 易错点

- 不要想着去改变二叉树结构，直接插在叶子节点就行
- 向下遍历，不断对比节点大小，一直遍历到最底层直接插入

---

#### 删除二叉搜索树中的节点（分类讨论梳理五大基线条件）

[题目：Leetcode 450](https://leetcode.com/problems/delete-node-in-a-bst)

##### 简单思路

- 删除二叉搜索树的节点，基线情况有很多，需要分类讨论

  - 1. 压根没找到目标节点：`return None`

  - 找到了：

    - 2. 目标节点在叶子节点：直接向上返回None，相当于删除

    - 3. 目标节点左不为空、右空：直接跳过目标节点，向上返回root.left

    - 4. 目标节点左空、右不为空：跳过目标节点，向上返回root.right

    - 5. 目标节点左右都不为空（最复杂）：

      - 需要遍历找到其右子树最左下的叶节点，这个是右边的最小值
      - 然后把root.left（左边的最大值）接到右边的最小值下面
      - 最后跳过目标节点，向上返回root.right

- 基线条件设定好，根据节点值与key的大小关系，决定往左右递归遍历

##### 代码呈现

```python
class Solution(object):
    def deleteNode(self, root, key):
        # 五大基线条件
        if not root: # 没找到
            return None
        if root.val == key: # 找到了，四类情况
            if root.left == None and root.right == None: # 在最底层
                return None # 直接删除
            elif root.left != None and root.right == None: # 左有子节点，右没有
                return root.left # 返回左子节点
            elif root.left == None and root.right != None: # 右有子节点，左没有
                return root.right # 返回右子节点
            else: # 左右都有子节点
                cur = root.right # 递归找右子树最左边的叶节点（右子树最小值）
                while cur.left:
                    cur = cur.left
                cur.left = root.left # 把左子树接到右子树最小值的左下角
                return root.right # 返回右子节点
        # 左右递归    
        if key < root.val: # 需要删除的在左边
            root.left = self.deleteNode(root.left, key)
        if key > root.val: # 需要删除的在右边
            root.right = self.deleteNode(root.right, key)
        return root
```

##### 易错点

- 五大基线条件一定要先梳理清楚
- 最难的是需删除的节点在二叉树的中间
  - 此时要将左子树接在右子树的最小节点下面
    - 遍历右子树，不断在右子树向左遍历，找最小节点
  - 跳过需删除的节点，直接向上return root.right