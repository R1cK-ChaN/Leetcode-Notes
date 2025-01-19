# 代码随想录算法训练营第十八天

## 二叉树第六天

### 考点总结

- 二叉搜索树最小绝对差：找相邻节点中最小的差，pre存上一个节点数据，result存最小值
- 二叉搜索数众数：相同的数集中出现，不停比较与上一个数的关系，找出现次数最多的
- 公共祖先：后序遍历找两个节点，最后在中节点判断公共祖先出现的方向

---

#### 二叉搜索树的最小绝对差（找最小相邻节点）

[题目：Leetcode 530](https://leetcode.com/problems/minimum-absolute-difference-in-bst/description/)

##### 简单思路

- 求二叉搜索树的最小绝对差：求两个相邻节点的最小差
  - 因为二叉搜索树自带顺序，所以只需要看相邻节点
- 初始化定义pre存上一个节点，result存最小值
- 二叉搜索树用中序遍历左中右

##### 代码呈现

```python
class Solution(object):
    def __init__(self):
        self.pre = None # 初始化pre为空
        self.result = float('inf') # 初始化result无穷大，方便后续更新最小值
    
    def traversal(self, cur):
        if not cur: # 递归的基线
            return 
        self.traversal(cur.left) # 中序遍历。左
        if self.pre != None: # 中，第二轮开始pre不为空才进行判断
            self.result = min(self.result, cur.val - self.pre.val)
        self.pre = cur # pre存上一个节点
        self.traversal(cur.right) # 右
        
    def getMinimumDifference(self, root):
        self.traversal(root) # 直接放入递归函数
        return self.result # return结果
```

##### 易错点

- 需要构造初始函数new pre和result
- 二叉搜索树节点大小有顺序，直接对比相邻节点大小即可

---

#### 二叉搜索树中的众数（相同的数相邻出现，要出现次数最多的）

[题目：leetcode 501](https://leetcode.com/problems/find-mode-in-binary-search-tree)

##### 简单思路

- 二叉搜索树求众数：相同的数往往相邻出现
  - count统计相邻相同的元素数量，pre存放上一个元素
    - 如果与pre相同，这个元素`count += 1`
    - 与pre不同，重置count，又从1数起
  - count与maxcount比较
    - 如果`count = maxcount`，这也是众数之一，加入result中
    - 如果`count > maxcount`，此时新的众数出现，重置result，只记录这个
- 新变量都要用初始化函数new，不然每次递归都会重置

##### 代码呈现

```python
class Solution(object):
    def __init__(self): # 用初始化函数new变量
        self.pre = None # 可防止递归时被重置
        self.count = 0 # 记当下元素的出现次数
        self.maxcount = 0 # 记现有的众数出现次数
        self.result = [] # 存众数
        
    def traversal(self, cur):
        if not cur: # 递归的基线
            return
        self.traversal(cur.left) # 二叉搜索树都是中序遍历，左
        if self.pre == None or cur.val != self.pre.val: # 中
            self.count = 1 # 如果第一轮或者与上个节点不相等，重置count
        else: # 与上一个节点相同
            self.count += 1 # count持续计数
        self.pre = cur # pre存上一个节点
        if self.count == self.maxcount: # 现有的count与maxcount对比
            self.result.append(cur.val) # 相等，意味着这个元素也是众数
        if self.count > self.maxcount: # 比maxcount还大
            self.maxcount = self.count # 这就是新的maxcount
            self.result = [cur.val] # 重置result，只存这个
        self.traversal(cur.right) # 右
        return
    
    def findMode(self, root):
        self.traversal(root)
        return self.result
```

##### 易错点

- 新变量一定要用初始化函数
- 初始化函数的变量调用时一定要加self.

---

#### 二叉树的公共祖先（左右找p、q，找到即向上回溯）

[题目：Leetcode 236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree)

##### 简单思路

- 公共祖先：返回节点p、q的最近公共根节点
- 要用后序遍历，先获取左右子树信息，最后处理中间节点
- 先找p和q的位置，然后往上回溯看最近的公共祖先，最后把公共祖先一层层向上返回

##### 代码呈现

```python
class Solution(object):
    def traversal(self, cur, p, q):
        if not cur: # 递归基线情况1，这一条线没找到p和q
            return None
        if cur == p or cur == q: # 基线情况2，这一条线有p或q
            return cur # 向上回溯传递
        left = self.traversal(cur.left, p, q) # 后序遍历，往左找
        right = self.traversal(cur.right, p, q) # 往右找
        if left != None and right != None: # 左右两边都找到了
            return cur # 那这个节点就是公共祖先
        elif left != None and right == None: # 左边有，右边没有
            return left # 往左边继续找
        elif left == None and right != None: # 左边没有，右边有
            return right # 往右边找
        else: # 左右两边都没找到
            return None # return None，没有公共祖先
        
    def lowestCommonAncestor(self, root, p, q):
        return self.traversal(root, p, q)
```

##### 易错点

- 基线主要就是找p和q，如果找到了，即返回
- 递归是后序，需要左右的信息才能在中间节点判断
- 中节点的逻辑主要是：
  - 左右都有，这个点就是公共祖先
  - 左有右没有，只往左继续找
  - 左没有右有，只往右继续找
  - 左右都没有，`return None`