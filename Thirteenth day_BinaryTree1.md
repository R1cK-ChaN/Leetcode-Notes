# 代码随想录算法训练营第十三天

## 二叉树第一天

### 考点总结

- 二叉树遍历分为深度优先搜索dfs和广度优先搜索bfs
- 深度优先搜索dfs按根节点位置不同，分为前中后序遍历
  - 前中后序递归遍历，牢记口诀"中左右"|“左中右”|“左右中”
  - 迭代遍历，前后序的逻辑一样，中序需要单独记忆
- 广度优先搜索bfs需要构造双向队列，循环记录每层的节点元素

---

#### 递归遍历（dfs前中后序递归）

[前序遍历 *preorder traversal*](https://leetcode.com/problems/binary-tree-preorder-traversal)

[中序遍历 *inorder traversal*](https://leetcode.com/problems/binary-tree-inorder-traversal)

[ 后序遍历 *postorder traversal* ](https://leetcode.com/problems/binary-tree-postorder-traversal)

##### 简单思路

- 深度优先搜索，可以分成前中后序递归遍历
- 关注递归三要素
  - 递归函数的参数和返回值（初始化定义的时候想好）
  - 终止条件（最后结束的情况）
  - 单层递归逻辑（一次递归怎么实现）
- 前中后序递归的主语在根节点（也称为中节点，按中节点位置记忆）
  - 前序遍历：中左右
  - 中序遍历：左中右
  - 后序遍历：左右中
- 递归终止条件是`node is None`，即所在分支已经走到了尽头
  - 符合深度优先搜索算法思想，此时没有更多节点可以访问/当前路径已经完全探索

##### 代码呈现

```python
# 前序遍历，中左右
res = []
def dfs(node):
    if node is None: #路径已经完全探索
        return
    res.append(node.val) # 中
    dfs(node.left) # 左
    dfs(node.right) # 右
dfs(root)
return res

# 中序遍历，左中右
res = []
def dfs(node):
    if node is None:
        return
    dfs(node.left) # 左
    res.append(node.val) # 中
    dfs(node.right) # 右
dfs(root)
return res

# 后序遍历，左右中
res = []
def dfs(node):
    if node is None:
        return
    dfs(node.left) # 左
    dfs(node.right) # 右
    res.append(node.val) # 中
dfs(root)
return res
```

##### 易错点

- 递归终止条件：dfs碰到一条路走到尽头的情况`node is None`
- 注意看题目要求用前中后序的哪个遍历，根据题目要求写代码

---

#### 迭代遍历（前后序+中序）

*还是上面那三道题目，但是这次用迭代而非递归*

##### 简单思路

- 用栈来实现迭代，访问节点（遍历节点）和处理节点（将元素放进result数组中）是两个维度
- 前序遍历，访问节点和处理节点都是**中间节点**
  - 本来是”中左右“，但是栈LIFO，所以入栈顺序有变
  - 每次先处理中间节点，将根节点放入栈中，然后先右，再左。
- 后序遍历，理想的出栈顺序“左右中”
  - 观察可知，前序迭代入栈时如果先入左，再入右，出栈顺序会变为“中右左”
  - 此时和后序所需的出栈顺序“左右中”刚好相反
  - 所以按前序迭代写，在最后反转result数组即可得后序迭代

![前序到后序](https://camo.githubusercontent.com/de2fb6015ef4ff0cacb079f38ac9d326c3bb1803de416cb037db481d8352bd13/68747470733a2f2f636f64652d7468696e6b696e672d313235333835353039332e66696c652e6d7971636c6f75642e636f6d2f706963732f32303230303830383230303333383932342e706e67)

- 中序遍历“左中右”，先访问左节点(stack)，处理的是中节点(result)
  - 不能提前将root节点加入stack，要用一个指针cur迭代访问最底层左节点
  - 主循环条件：当 `cur` 不为空（尚未遍历完当前路径），或者栈不为空（还有未访问的节点）。
  - 分为两个部分：访问左子树和处理栈顶节点。
    - 如果当前 `cur` 不为空，将其入栈，表示未来需要访问它，然后将`cur`移动到当前的左子节点。
    - `cur`为空，说明已经到左子树最底层。
      - 弹出栈顶节点，访问该节点的值，并将其添加到 `result`。
      - 指针转向栈顶节点的右子节点，准备遍历右子树。

##### 代码呈现

```python
# 前序，出栈中左右，入栈先右再左
if not root: # 检查二叉树是否为空
    return []
stack = [root] # 遍历节点
res = [] # 处理节点
while stack:
    node = stack.pop()
    res.append(node.val) # 先处理中节点
    if node.right: # 右节点入栈
        stack.append(node.right)
    if node.left: # 左节点入栈
        stack.append(node.left)
return res

# 后序，出栈左右中，入栈先左再右，结果反转
if not root:
    return []
stack = [root]
res = []
while stack:
    node = stack.pop()
    res.append(node.val)
    if node.left: # 和前序相反，入栈先左再右
        stack.append(node.left)
    if node.right:
        stack.append(node.right)
return res[::-1] # 最后的结果要反转

# 中序，cur指针
if not root:
    return[]
stack = [] # 先别把root放入栈
res = []
cur = root # 用指针遍历二叉树节点
while cur or stack: # 如果指针不为空或栈还有东西
    if cur: # 还没到左路末尾
        stack.append(cur) # 把遍历到的节点放进栈
        cur = cur.left # 继续向左路往下遍历
    else: # 左路为空，走到头了
        cur = stack.pop() # 把栈顶弹出
        res.append(cur.val) # 节点值加入res
        cur = cur.right # 向右遍历
return res
        
```

##### 易错点

- 迭代遍历前后序思路一致，逻辑可以一起记忆，但是中序特殊
- 中序关键是访问节点和处理节点不一致，用指针遍历

---

#### 层序遍历（bfs队列）

[题目：Leetcode 102](https://leetcode.com/problems/binary-tree-level-order-traversal)

##### 简单思路

- 层序遍历要求按层返回二维数组
- 构造一个双向队列，每次只弹出该层数量的元素，并把这些元素的左右节点纳入队尾
- 由于只弹出该层数量元素，所以每层的循环结束后，下一层的元素还留在队伍里面
- 继续下一次循环，直至队列无元素，即遍历完二叉树——主循环条件`while queue:`

##### 代码呈现

```python
if not root:
    return []
queue = collections.deque([root]) # 将root放入双向队列
res = []
while queue: # 队列不为空，还有元素没被遍历到
    level = [] # 新列表用于存放这一层的节点值
    for _ in range(len(queue)): # 重复这一层节点数的次数
        cur = queue.popleft() # 这一层的元素从左边出队列
        level.append(cur.val) # level记录出队列的节点值
        if cur.left: # 如果有左子节点
            queue.append(cur.left) # 左子节点右边进队列
        if cur.right: # 如果有右节点
            queue.append(cur.right) # 右子节点右边进队列
    res.append(level) # 将这一层的节点值加入res
return res
```

##### 易错点

- 初始将root直接放入新构造的双向队列deque
- 每一层都要new一个level列表存放该层元素
  - `level = []`要写进主循环`while queue`里面
- 由于for循环只会重复该层的size次数
  - 所以该层的节点都刚好会出队列，下一层的节点都留在队列
  - 该层节点出队列时，要收集它们的左右子节点