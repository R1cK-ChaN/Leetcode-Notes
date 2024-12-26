# 代码随想录算法训练营第二天
## 数组day2
### 考点总结：
- 滑动窗口：主循环移窗口右边界，固定右边界后滑动左边界找最优点。
- 螺旋矩阵：高维视角，先完成最外圈四条边，画完一条边后，该边就往内缩一圈，一圈圈从外到内迭代。
- 前缀和：array[a,b]区间和 = array[b+1]的前缀和 - array[a]的前缀和

---

####  长度最小子数组(Slide Window)
[题目：Leetcode 209](https://leetcode.com/problems/minimum-size-subarray-sum)
##### 简单思路
- 用一个滑动窗口框住符合条件的范围，移动左侧窗口起点来确定最小长度。
- 两个**while**循环
	- while right < len(nums) --> 主循环，移动滑动窗口右边界，注意不要超数组索引（中间不能出现=号）
	- while cur_sum >= target --> 符合条件的窗口，
		1. 更新min_len
		2. 移动左边界更新cur_sum
- 如果min_len有被更新，return min_len；如果没有被更新，return 0
##### 代码呈现
```python
l = len(nums)
right = left = 0
min_len = float('inf') # 最短窗宽初始为无穷大-->之后min取小，如果设为0不会更新
cur_sum = 0 # 滑动窗口内元素和初始值为0
while right < l: # 右边界不超数组索引
	cur_sum += nums[right] # 纳入窗内
	while cur_sum >= target: # 如果窗内值符合条件（>=target）
		min_len = min(min_len, right-left+1) # min函数，更新此时的最小窗宽min_len
		cur_sum -= nums[left] # 左边界即将后移，需要在窗内元素和中减去左边界指向的元素
		left += 1 # 左边界后移
	right += 1 # 右边界后移
return min_len if min_len != float('inf') else 0 # 如果min_len被更新过，返回min_len；如果没有被更新过，返回0.
```
##### 易错点
- 初始化最小窗宽是无限大`min_len = float('inf')`
- 主**while**循环条件不能有 **=**（右边界不能超数组索引）
- return时要用一个列表生成式 --> 逻辑：min_len被更新过返回min_len；没被更新过（还是无限大），返回0。

---

#### 螺旋矩阵II（循环不变量）
[题目： Leetcode 59](https://leetcode.com/problems/spiral-matrix-ii)
##### 简单思路
- 从外往内迭代，先想怎么样生成外圈的螺旋，然后迭代缩进内圈
- 外圈螺旋分四条边(top, bottom, left, right)，想明白这四条边有哪些限制(`top <= bottom`, `left <= right`)
- 这些边的限制明确之后，每循环一次都往内圈缩一位
##### 代码呈现
```python
matrix = [[0]*n for _ in range(n)] # 初始化矩阵
top, bottom, left, right = 0, n-1, 0, n-1 # 初始化四边界
num = 1 # 螺旋从1开始
while top <= bottom and left <= right: # 四条边的限制
	for i in range(left, right+1): # 画顶边，左闭右闭(right+1)，把整个顶边画出来
		matrix[top][i] = num # 行不动，列动
		num += 1
	top += 1 # 顶边画完之后，top边界要向内缩一行
	
	for i in range(top, bottom+1): # 画右边，把剩下的右边画出来
		matrix[i][right] = num # 列不动，行动
		num += 1
	right -= 1 # 右边画完，right边界向内缩一行
	
	if top <= bottom: # 需要判断条件，因为前面top有变化，可能不满足了，所以要再次判断
		for i in range(right, left-1, -1): # 画底边，从右到左倒着画回来
			matrix[bottom][i] = num # 行不动，列动
			num += 1
		bottom -= 1 # 底边界向内缩
		
	if left <= right: # 再次判断左右边界是否满足条件
		for i in range(bottom, top-1, -1): # 画左边，倒着画
			matrix[i][left] = num # 列不动，行动
			num += 1
		left += 1 # 左边向内缩一格
return matrix
```
##### 易错点
- 各边画完之后都要往内缩，往内缩这一步**千万不要**写进 **for** 循环。
- 各条边画的顺序是跟着数字螺旋的顺序，画底边和左边之前，要再次 **if判断** 是否满足条件。
- 初始化 `n*n` 列表不要忘记，先生成1行`[0]*n`，然后再用`for _ in range(n)`重复n次 ，即`[[0]*n for _ in range(n)]`。

---

#### 区间和（累积相减，前缀和）
[题目；区间和](https://kamacoder.com/problempage.php?pid=1070)
##### 简单思路
- 思路不难，关键是处理输入输出数据较为复杂。
- 求一个数组区间的和，用 最右侧累积和 - 最左侧累积和 即可。
- 代码逻辑：
	1.把分行的输入元素变成一个列表（按行分割），然后操作这个列表
	2.列表[0]代表元素总数，[1:n+1]代表array，再之后每个两位数代表区间（要遍历split，拆出左右边界l,r）
	3.定义前缀和prefix_sum，明确计算方式
	4.新建result列表存放`total = prefix_sum[r+1] - prefix_sum[l]`的结果，最后遍历打印result即可。
##### 代码呈现
```python
def main():
	import sys #导入sys模块，处理标准输入
	input_data = sys.stdin.read() #读取标准输入中所有内容
	data = input_data.strip().split("\n") #strip去掉收尾空白，split按行分割成列表
	
	n = int(data[0]) #第一个数代表数组元素总数
	array = list(map(int, data[1:n+1])) #接下来n行是数组，都要转换为int整数
	
	prefix_sum = [0]*(n+1) # n+1个元素，多一个元素储存0（空区间的和）
	for i in range(n):
		prefix_sum[i+1] = prefix_sum[i] + array[i] #计算前缀和，第i+1个前缀和=前i个元素的和
	
	result = [] #存每个区间和的结果
	for line in data[n+1:]: #后面存放的是区间
		l,r = map(int, line.split()) #还原区间左右界
		total = prefix_sum[r+1] - prefix_sum[l] #计算区间和
		result.append(total) #存放区间和
	
	for res in result: #遍历结果列表
		print(res) #输出每个区间和
	
if __name__ == "__main__": #主程序入口
	main() # 仅当脚本直接运行时，调用 main() 函数
```
##### 易错点
- ACM输入输出格式不熟，有些不是题目不会写，但是就是因为不熟悉界面而跑不通。
- 搞清楚每一步的目的是什么，先明确目的，再去编程，理清思路最重要

---

#### 土地分割（二维前缀和）
[开发商分割土地](https://kamacoder.com/problempage.php?pid=1044)
##### 简单思路
- 题目：长方形地块，只能横或竖切一刀，求最小的土地价值差异
- 关键在于快速求出切割后的两块土地价值，然后相减求差异
- 宏观视角的前缀和，
	- 横切分上下两半，上面一半用前缀和求，下面一半=总价值-上一半
	- 竖切分左右两半，左边一半用前缀和求，右边一半=总价值-左一半
- 不断用min函数更新min_diff
##### 代码呈现
```python
def main():
	import sys
	input = sys.stdin.read
	data = input().strip().split('\n') #老生常谈，和上一题一样的处理
	
	n, m = map(int, data[0].split()) # n*m的矩阵
	matrix = [list(map(int, row.split())) for row in data[1:]] # 生成矩阵
	total_sum = sum(sum(row) for row in matrix) # 矩阵内总和
	# 横切时，用行前缀和
	row_prefix = [0]*(n+1) # 行对应n
	for i in range(n):
		row_prefix[i+1] = row_prefix[i] + sum(matrix[i]) # 定义行前缀和
	# 竖切时，用列前缀和	
	col_prefix = [0]*(m+1) #列对应m
	for i in range(m):
		col_prefix[i+1] = col_prefix[i] + sum(matrix[j][i] for j in range(n)) # 定义列前缀和
	
	min_diff = float('inf') # 初始化min_diff
	
	for i in range(1,n): # 横切，矩阵分成top、bottom两块
		top_sum = row_prefix[i]
		bottom_sum = total_sum - top_sum
		min_diff = min(min_diff, abs(top_sum-bottom_sum))
		
	for j in range(1,m): # 竖切，矩阵分成left、right两块
		left_sum = col_prefix[j]
		right_sum = total_sum - left_sum
		min_diff = min(min_diff, abs(left_sum-right_sum))
	
	print(min_diff)
	
if __name__ == "__main__":
	main()	
```
##### 易错点
- 不要被唬住，理解题目之后要跳出来，用更高层级的视角看待这道题——二维前缀和
- 行、列前缀和容易和行列n\m搞乱，记住row对应n，col对应m。
- 这道题只需要求最小diff，就用min()函数将全部情况的diff过一遍即可。
