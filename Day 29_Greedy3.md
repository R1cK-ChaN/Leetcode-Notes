# 代码随想录算法训练营第二十九天

## 贪心算法第三天

### 考点总结

- 加油站：cursum记录当前油箱存货，totalsum记录能否跑完全图
- 分发糖果：分类讨论，先搞定 i > i - 1；再搞定i > i + 1
- 柠檬水找零：优先把大张的钱找出去
- 根据身高重建队列：先按身高倒序排序，然后再不停地把矮的按k值插入高的中间

---

#### 加油站

[题目：Leetcode 134](https://leetcode.com/problems/gas-station)

##### 简单思路

- 每一个站点都有一个加油站，每到一个站点都需要花费一定的油量
  - 现在需要找出一个出发点，能够走完全程
- 两个变量，cursum记录当前油箱存货、totalsum记录全程是否盈亏平衡
  - 如果cursum为负，说明油箱没油了，出发点只能定在i+1，i之前都不行
  - 按照这个思路贪心遍历
- totalsum < 0直接返回-1，无论从哪里出发都不能走完全程

##### 代码呈现

```python
class Solution(object):
    def canCompleteCircuit(self, gas, cost):
        cursum = 0 # 记录当前油箱
        totalsum = 0 # 记录全程是否支持走完
        start = 0 # 记录出发点
        for i in range(len(gas)):
            cursum += (gas[i] - cost[i]) # 更新油箱
            totalsum += (gas[i] - cost[i]) # 记录全程
            if cursum < 0: # 如果油箱见底，说明之前的出发点都不适用
                start = i + 1 # 出发点重置到i+1
                cursum = 0 # 重置油箱
        if totalsum < 0: # 如果全程cost>gas
            return -1 # 说明无论从哪里出发都不能走完全程
        return start
```

##### 易错点

- totalsum和cursum共用一个for循环，cursum需要更新，totalsum不需要
- 局部最优：如果油箱见底，则重置，选下一个点为出发点

---

#### 分发糖果

[题目：Leetcode 135](https://leetcode.com/problems/candy)

##### 简单思路

- 孩子们有得分，需要分糖果给孩子们，求最少需要分多少
  - 每个孩子至少一个糖果
  - 如果孩子比左右两边得分高，那么他分得的糖果要比左右两边多
- 不能同时对比，需要分别对比左右
  - 左 < 右，左边的孩子得分比右边低
    - `candy[i] = candy[i-1] + 1`右比左多一个糖果
  - 右 < 左，右边的孩子得分比左边低
    - `candy[i] = candy[i+1] + 1`左比右多一个糖果
    - 注意现在是需要先获知i+1的糖果数量，再累加，需要倒序遍历
    - 这里的糖果数量要与上面的遍历求max值

##### 代码呈现

```python
class Solution(object):
    def candy(self, ratings):
        candy = [1] * len(ratings) # new一个糖果列表，初始每个孩子都发一颗
        for i in range(1, len(ratings)): # 顺序遍历
            if ratings[i] > ratings[i-1]: # i > i-1
            	candy[i] = candy[i-1] + 1 # 比左边的多1颗
        for i in range(len(ratings)-2, -1, -1): # 倒序遍历
            if ratings[i] > ratings[i+1]: # i > i+1
                candy[i] = max(candy[i+1] + 1, candy[i]) # max(比右边的多一颗，上面的糖果数)
        return sum(candy)
```

##### 易错点

- 需要分类讨论，两个情况两个for循环
  - 顺序遍历，i > i-1的时候
  - 倒序遍历，i > i+1的时候
  - 注意最后一定要取max值

---

#### 柠檬水找零

[题目：Leetcode 860](https://leetcode.com/problems/lemonade-change)

##### 简单思路

- 一杯柠檬水卖5块钱，顾客可能给5、10、20
  - 如果手头的零钱可以全部找零，True
  - 如果不够找零，False
- 局部最优：5块钱是最万能的，应该尽量避免使用
- 顾客给5块钱，直接收，不找零
  - 10块钱，找5块，不够即False
  - 20块钱，优先找10+5，其次3*5，都不够即False

##### 代码呈现

```python
class Solution(object):
    def lemonadeChange(self, bills):
        dollar5 = 0 # 计数5和10钞票
        dollar10 = 0
        for i in bills: # 遍历顾客的账单
            if i == 5: # 给5块
                dollar5 += 1 # 直接收
            if i == 10: # 给10块
                if dollar5 > 0: # 找五块，收10块
                    dollar5 -= 1
                    dollar10 += 1
                else:
                    return False
            if i == 20: # 给20
                if dollar10 > 0 and dollar5 > 0: # 优先找10+5
                    dollar10 -= 1
                    dollar5 -= 1
                elif dollar5 >= 3: # 其次找3*5
                    dollar5 -= 3
                else:
                    return False # 都不行就False
        return True
```

##### 易错点

- 贪心：面额小的最后给
- 记得找钱的同时，也要收钱

---

#### 根据身高重建队列

[题目：Leetcode 406](https://leetcode.com/problems/queue-reconstruction-by-height)

##### 简单思路

- 每个人身上有两个特征(h, k)，h代表身高，k代表他前面有k个人身高大于等于他的身高

- **排序**：先将所有人按照身高从高到低排序；对于身高相同的人，再按照他们前面应有的人数（k值）从小到大排序。

  **插入队列**：依次将排序后的每个人插入到结果列表中，插入位置为他们的k值。这样可以保证每个人前面正好有k个身高不低于他的人。

- 一定要先按身高倒序排序，下一步把身高矮的按k值插入队列

  - 因为已经按身高排好倒序了，把矮的按k值插入队列会使高的自动后退，不会影响高的排序

- 贪心思路：

  - 先插入高个子，他们的位置固定，不会受到后续低个子的影响；
  - 后续插入的低个子只会在高个子之间找位置，不会破坏高个子前面已有的 k 值满足关系。

##### 代码呈现

```python
class Solution(object):
    def reconstructQueue(self, people):
        people.sort(key=lambda x: (-x[0], x[1])) # 按身高倒序，按k正序
        que = [] # 新建队列
        for i in people:
            que.insert(i[1], i) # 把people越来越矮的人插入队列中
        return que
```

##### 易错点

- 排序规则`people.sort(key=lambda x: (-x[0], x[1]))`

  - `-x[0]`：表示将身高 hhh 按照从高到低排序。因为 Python 默认是升序排序，取负号后就变为降序排序。
  - `x[1]`：对于身高相同的人，按照 k 值从小到大排序。

  - 目的：
    - 先处理高个子：因为高个子无论放在哪里，都不会因为后面插入的低个子而受到影响；
    - 同一高度时，k 值小的优先放入，保证后续插入时不会破坏已经满足 k 值的人数关系。

- **插入原理**：

  - 在列表 `que` 中使用 `insert(index, value)` 方法，将当前元素 i 插入到索引为 i[1] 的位置上。
  - 因为前面已经按照身高降序排序，所以已经在队列中的都是比当前人高的（或者与他同高但 k 小的）。
  - 插入到第 i[1]个位置可以确保在当前人的前面正好有 i[1]个身高不低于他的元素。

