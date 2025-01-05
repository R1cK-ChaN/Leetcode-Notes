# 代码随想录算法训练营第十一天

## 栈与队列第二天

### 考点总结

- 栈LIFO擅长相邻的元素匹配问题，类似于消消乐
- 滑动窗口最大值，自建单调队列函数，将最大值保留在deque[0]
- 前k个高频元素，用固定容量k的小顶栈，把出现频率小的弹出，沉淀出现频率大的

---

#### 逆波兰表达式（后序运算符，用栈保存前两个数字）

[题目：Leetcode 150](https://leetcode.com/problems/evaluate-reverse-polish-notation)

##### 简单思路

- 逆波兰表达式是一种后缀表达式，两个待运算的数字在前，运算符在后
- 边遍历边用栈保存前面的数字
- 如果遍历到运算符，就把栈顶前两个数字弹出运算，再把结果压入栈顶

![150.逆波兰表达式求值](https://camo.githubusercontent.com/9f7f3d3cc8df9823f36cb8566502a3c263476e49ca6b87bea9a3503d2c928eaa/68747470733a2f2f636f64652d7468696e6b696e672e63646e2e626365626f732e636f6d2f676966732f3135302e2545392538302538362545362542332541322545352538352542302545382541312541382545382542452542452545352542432538462545362542312538322545352538302542432e676966)

##### 代码呈现

```python
stack = [] # new一个stack
for token in tokens:
    if token not in ['+', '-', '*', '/']: #如果遍历到元素不是运算符（是数字）
        stack.append(int(token)) # 转为int再压入stack
    else: # 遍历到的元素是运算符
        num1 = stack.pop() # 把栈顶前两个数字顶出来运算
        num2 = stack.pop() # stack是LIFO，num2在栈的顺序比num1前
        if token == "+": 
            result = num2 + num1
        elif token == "-":
            result = num2 - num1
        elif token == "*":
            result = num2 * num1
        else:
            result = int(num2 / num1） # 用截断除法/，结果向0靠拢
        stack.append(result) # 把运算结果压入栈，继续遍历
return stack[-1] # 最后只有result在栈里面
```

##### 易错点

- 将数字元素压入栈之前，要从字符串类型转为整数
- 栈是LIFO，num2在队列中比num1更靠前，所以运算的时候num2在前
- 除法要用截断除"/"，不能用地板除"//"

---

#### 滑动窗口最大值（自建单调队列）

[题目：Leetcode 239](https://leetcode.com/problems/sliding-window-maximum)

##### 简单思路

- 数组nums上有一个滑动窗口，滑动一次后，要返回滑动窗口内最大值合集
- 自建一个单调递减队列函数deque，将当前窗口最大值存放在 $deque[0]$处
  - 窗口要进的元素不断与单调队列的队尾元素比较，如果比队尾大，就顶出队尾元素
    - 重复这个过程，这个过程就是构造单调递减的一个队列，存放滑动窗口中的元素
    - 滑动窗口最大值就是 $deque[0]$
  - 如果窗口要出的元素就是最大值 $deque[0]$，要弹出这个 $deque[0]$
    - 因为它滑出窗口之后，窗口最大值就不在是这个元素
- 构建一个max_list专门存 $deque[0]$

##### 代码呈现

```python
from collections import deque
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        max_list = [] # 存每个滑动窗口最大值
        kept_nums = deque() # 单调队列，将窗口内最大的放[0]
        for i in range(len(nums)):
            update_kept_nums(kept_nums, nums[i]) # 调用deque函数
            # 索引i超窗口大小，nums[i-k]是窗口上一个左边界，已经出窗口
            # 如果已经出窗口的左边界刚好是最大值(deque[0]时)
            if i >= k and nums[i-k] == kept_nums[0]: 
                kept_nums.popleft() # 移除这个deque[0]
            # 当窗口形成后，记录滑动窗口最大值，将deque[0]加入max_list
            if i >= k-1:
                max_list.append(kept_nums[0])
        return max_list
# 构造单调递减队列
def update_kept_nums(kept_nums, num): # num是新进窗口的元素
    while kept_nums and num > kept_nums[-1]: # 新进元素比队尾的大
        kept_nums.pop() # 把队尾的弹出
    # 循环这个过程，直到队尾有元素比它大，或者把deque清空
    kept_nums.append(num) # 新进元素变成新队尾
```

##### 易错点

- 要import deque，要手动构造deque的更新函数，制造单调递减的队列
- 注意两个判断条件，一个是把过时的最大值移除，一个是将最大值记录进max_list

---

#### 前k个高频元素（小顶堆）

[题目：Leetcode 347](https://leetcode.com/problems/top-k-frequent-elements)

##### 简单思路

- 给一个数组，要求返回出现频率最高的前k个元素
- 先用字典，统计并存放各数组元素的出现次数
- 小顶堆的性质：二叉树，最上方的元素最小，最先被弹出
- 用固定容量为k的小顶堆，存放出现频率前k大的元素
  - 小顶堆会优先弹出小的元素，沉淀大的元素

##### 代码呈现

```python
import heapq
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        map_ = {}
        for i in range(len(nums)): # 统计每一类元素的出现频率
            map_[nums[i]] = map_.get(nums[i], 0) + 1
            
        pri_que = [] # new一个优先队列作为小顶堆
        for key, freq in map_.item(): # item()可以同时返回key和value
            # 插入一个元组(freq,key)到堆pri_que中
            heapq.heappush(pri_que, (freq,key)) 
            # heapq以(freq,key)第一个元素为排序依据
            if len(pri_que) > k: # 如果堆元素数量超过k
                heapq.heappop(pri_que) # 弹出最小的元素
                
        result = [0]*k
        for i in range(k-1, -1, -1): 
        # 小顶堆中的元素按频率从低到高排列，先弹出的堆顶是频率最小的元素
        # 所以result要倒序填充，for循环从第k-1个填到第0个，这样result的key的出现频率就是从大到小了
            result[i] = heapq.heapqpop(pri_que)[1]
            # 只要元素名key，是元组第二个，即(freq,key)[1]
        return result
```

##### 易错点

- heapq就是代表小顶堆
- 用容量固定为k的小顶堆不断弹出频率小的元素，变相沉淀出现频率大的元素
- 从小顶堆读取数到result时，小顶堆的顺序是出现频率小的先出
  - 所以要倒序填充result，把出现频率小的填到后面，越前出现频率越大
- result只要key，不要freq，所以是元组(freq,key)[1]
