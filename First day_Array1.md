# 代码随想录
## 数组
### 《代码随想录》数组：二分查找
#### 二分查找(Binary Search)
[题目：Leetcode 704](https://leetcode.com/problems/binary-search)
##### 简单思路
- 题目识别：有序数组、元素大小从低到高排序。
- 定义三个点：**闭区间**左右边、区间中间点（middle要写进while循环内，随区间更新而变动）。
- while循环中：
	1. `middle > target` ==> target在区间左侧 ==> 更新区间右边界 `right = middle - 1`
	2. `middle < target` ==> target在区间右侧 ==> 更新区间左边界 `left = middle + 1`
	3. `middle = target` ==> target == middle ==> `return middle`
- 若都没有 `return -1`
##### 代码呈现
```python
left,right = 0, len(nums)-1
while left <= right:
	middle = left + (right-left)//2
	if middle > target:
		right = middle - 1
	elif middle < target:
		left = middle + 1
	else:
		return middle
return -1
```

##### 易错点
- 区间的定义要左右闭区间，左闭右开很容易出错。
- middle要用`left + (right-left)//2`，而不是（左+右）// 2，这是为了防止栈溢出。

---

### 《代码随想录》数组：移除元素
#### 移除元素(double index)
[题目：Leetcode 27](https://leetcode.com/problems/remove-element)
##### 简单思路
- 定义快慢指针，快指针可以用`for i in range(len(nums))`中的**i**代替。
- 直接在原数组上操作，快指针**i**遇到的值如果不是目标值**val**后，即`nums[i] ！= val`，需要更新数组与慢指针（具体见代码注释）。
##### 代码呈现
```python
slow = 0
for i in range(len(nums)):
	if nums[i] != val: # i是快指针，快指针遇到的值不是目标时
		nums[slow] = nums[i] # 更新慢指针指向，更新slow指向的数组
		slow += 1 # 更新慢指针，新数组的长度+1
return slow # return新数组长度
```
##### 易错点
- `nums[i] != val`数组没有更新
- 慢指针（新数组长度）没有更新

---

### 《代码随想录》数组：有序数组的平方
#### 有序数组的平方(double index)
[题目：Leetcode 977](https://leetcode.com/problems/squares-of-a-sorted-array)
##### 简单思路
- 定义新数组（存放结果）、左右指针、新数组指针 **i** （从最后开始）。
- **while** 循环所有元素，比较左右指针所指元素的平方，将大的放入result数组
- result数组指针 **i** 向前移一位
##### 代码呈现
```python
result = [0] * len(nums)
l, r, i = 0, len(nums)-1, len(nums)-1 # i是新数组的索引，从最后面开始
while l <= r: #等号保证全部元素都被处理，无等号可能会遗漏中间元素
	if nums[l]**2 <= nums[r]**2: #右边的平方大
		result[i] = nums[r]**2 #存放right的平方
	else: #左边的平方大
		result[i] = nums[l]**2 #存放左边平方
	i -= 1 # i向前移1位
return result
```
##### 易错点
- 初始化新数组，新数组的元素个数与原来**相同**，用来存放较大的平方。
- `while l <= r:`中间的 **=** 不能省略，不然可能会遗漏中间元素。
- 填入新元素后，result数组指引需要向前移1位`i -= 1`。

