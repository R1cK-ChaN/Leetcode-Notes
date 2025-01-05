# 代码随想录算法训练营第十天

## 栈第一天

### 考点总结

- 栈stack是LIFO，队列Queue是FIFO
- 栈可以用于相邻元素匹配问题

---

#### 用stack实现queue（stack_in和stack_out解决）

[题目：Leetcode 232](https://leetcode.com/problems/implement-queue-using-stacks)

##### 考点总结

- 栈stack是先入后出，队列queue是先进先出，现在需要用栈实现队列的功能
- `push`、`pop`、`peek` 和 `empty` 是栈（Stack）和队列（Queue）的基本操作功能
- Push：将一个新元素**添加**到数据结构中
- Pop：从数据结构中**移除并返回**一个元素
- Peek：查看数据结构中某一位置的元素，通常是栈顶或队列头的元素，但**不移除**它
- Empty：检查数据结构是否为空，`True` 表示数据结构中没有任何元素；否则返回 `False`

##### 代码呈现

```python
class MyQueue:

    def __init__(self): # 创建两个stack
        self.stack_in = [] # 用来接受push
        self.stack_out = [] # 用来弹出pop和peek

    def push(self, x: int) -> None:
        self.stack_in.append(x) # stack_in接受push

    def pop(self) -> int:
        if self.empty(): # 两个栈都没元素
            return None
        
        if self.stack_out: # 元素在stack_out
            return self.stack_out.pop() # 直接pop
        else: # 元素在stack_in
            for i in range(len(self.stack_in)): # 将元素弹出放stack_out
                self.stack_out.append(self.stack_in.pop()) # 达到重整顺序的效果
            return self.stack_out.pop() # stack_out弹出

    def peek(self) -> int:
        ans = self.pop() # 弹出第一个
        self.stack_out.append(ans) # 放回去
        return ans

    def empty(self) -> bool:
        return not (self.stack_in or self.stack_out)
```

##### 易错点

- 空格键和tab键的缩进不能混用，出现缩进报错时用统一方法改
- 关键在pop功能的实现，有三个判断
  - 判断队列是否为空——调用empty函数
  - 判断元素是否在stack_out那里
  - 如果在stack_in中，将stack_in的元素脱出，放出stack_out重整顺序

---

#### 用queue实现stack（一个deque解决）

[题目：Leetcode 225](https://leetcode.com/problems/implement-stack-using-queues)

##### 简单思路

- 用queue实现stack的后进先出
- pop要取队尾元素，就把前面size-1个元素依次拿出来再放回队尾
- 此时要取的元素就变成了第一个

##### 代码呈现

```python
class MyStack:

    def __init__(self):
        self.que = deque() # new双端队列，记得加()

    def push(self, x: int) -> None:
        self.que.append(x) # 上面deque如果没有(),这里就会报错

    def pop(self) -> int:
        if self.empty(): # 判断不为空
            return None
        for i in range(len(self.que)-1): #把前面size-1个元素重新排到队尾
            self.que.append(self.que.popleft()) # 用popleft移除队头元素
        return self.que.popleft() # 此时想要的元素就在队头

    def top(self) -> int:
        if self.empty():
            return None
        return self.que[-1] # 直接返回倒数第一个

    def empty(self) -> bool:
        return not self.que
```

##### 易错点

- 初始化new deque的时候一定要加() `self.que = deque()`
- pop重排元素时要用popleft移除**队列的头部元素**，符合队列的特性（FIFO）
  - 如果用 `pop()`，它会移除**队列的尾部元素**，这是栈的特性（LIFO）
- top求最后进去的元素可以用`self.que[-1]`直接返回队尾最后一位

---

#### 有效括号（stack做映射）

[题目：Leetcode 20](https://leetcode.com/problems/valid-parentheses)

##### 简单思路

- 检查字符串里面的括号是否都正确成对
- 一共有三种错误形式，左边多了、右边多了、一对括号不匹配
- 遍历字符串，出现左括号时，用stack做映射，压入相应的右括号
- 遍历到右括号时，就看stack头的右括号是不是一样
  - 一样，消消乐，字符串继续遍历下一个，stack的pop出来
  - 不一样，说明出现了一对括号不匹配，返回False
  - stack为空，说明根本对不上，返回False

##### 代码呈现

```python
stack = [] # new一个stack
mapping = { # 创建一个左右括号匹配的字典
    '(':')',
    '[':']',
    '{':'}'
}
for i in s: # 遍历字符串s
    if i in mapping.keys(): # 如果i是左括号
        stack.append(mapping[i]) # 就把相应的右括号压入stack
    # 右括号情况：1.stack为空，2.和stack的头部对不上    
    elif not stack or stack[-1] != i:
        return False # 都是不匹配
    else: # 匹配的情况，stack弹出，上面的循环继续遍历-->相当于消消乐
        stack.pop()
return True if not stack else False # 如果都能匹配，最后的stack应该为空
```

##### 易错点

- 左右括号匹配的字典不要打错字
- 遍历字符串的时候的判断条件：
  - 遍历到左括号：
    - 把对应的右括号压入stack
  - 遍历到右括号：
    - stack的顶部和遍历的i一样，说明匹配，直接消消乐
    - stack顶部和i不一样，不匹配，False
    - stack为空，出现数量错误，False

---

#### 删除字符串中相邻重复项（stack消消乐）

[题目：Leetcode 1047](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string)

##### 简单思路

- 重复循环遍历字符串，如果有相邻重复项就消除，直到无相邻重复项为止
- 用stack解决，每遍历一个元素
  - 如果遍历到与栈顶相同的元素，说明与上一个相同-->栈弹出（消去）
  - 如果栈为空或不与栈顶相同，说明与上一个不同-->把元素压入栈中
- 把栈中剩下的元素拼成字符串，即是答案

```python
result = [] # new一个list来当栈的容器
for i in s: # 遍历字符串
    if result and result[-1] == i: 
    # 前面result非空，以保证result[-1]不超索引
        result.pop() # i与栈顶相同，弹出栈顶
    else: # i与栈顶不同 或 栈为空
        result.append(i) # append进队尾，相当于栈的LIFO
return "".join(result) # 把list中剩下的元素拼接成字符串
```

##### 易错点

- 这道题只是消除相邻的重复项，用栈保存上一个元素，相同直接消消乐
- 用了一个list作为栈的容器，动态储存未被消除的字符
- i如果不重复，就直接 `append` 到栈的末尾，这使得list字符顺序保持和原字符串一致
  - 所以最后直接拼接list中的字符即可，不用翻转顺序
