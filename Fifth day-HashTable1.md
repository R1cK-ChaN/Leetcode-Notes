# 代码随想录算法训练营第五天
## 哈希表第一天
### 考点总结
- 哈希表分为三类：数组、set、map
- 如果数据量小，不大幅度跳跃————用数组
- 如果数据量大，跳跃幅度大（极端：1、2、1million）————用set
- 如果需要键值对————用map

#### 有效的字母异位词（哈希数组）
[题目：Leetcode 242](https://leetcode.com/problems/valid-anagram)
##### 简单思路
- 检验两个字符串是不是字母异位，即所含字母相同（字符串只含26个小写字母）
- 构建一个26位哈希数组，遍历s字符串，对所含字母计数
- 遍历t字符串，用s字符串的哈希数组相减t的字母
- 遍历减完之后的哈希数组
- 	如果这个数组全部元素都是0，证明所含字母相同，True
- 	如果数组中有一个元素不为0，证明有不同的字母，False
##### 代码呈现
```python
record = [0]*26 # 初始化一个哈希数组
for i in s: # 遍历字符串s
	record[ord(i) - ord("a")] += 1 	#统计i的出现次数
# ord()用于获取字母的ACSII码，小写字母ACSII码连续，"a"排第一个
# ord(i) - ord("a")获取字母i的相对位置，+=1是在i的相对位置上计数
for i in t: # 遍历字符串t
	record[ord(i) - ord("a")] -= 1 # 将s的哈希数组扣减相应字母的出现次数
for i in record: # 遍历扣减完成后的哈希数组
	if i != 0: # 如果两个字符串构成字母相同，扣减后的数组元素都为0
		return False # 如果出现元素不为0，说明构成字母不一样，return False
return True
```
##### 易错点
- 先要构建26位空数组
- ord()函数获取字母的ASCII码
- ord(i) - ord("a")获取字母i的相对位置
- 计数先+后-的思想很重要，最后检查是否每一元素都为0即可

---

#### 两个数组交集(set)
[题目：Leetcode 349](https://leetcode.com/problems/intersection-of-two-arrays)
##### 简单思路
- 跨度大、数量多、需去重的场景——用set
- 对两个数组分别用set()，然后求交集&
- 输出结果要求list，记得要转换
##### 代码呈现
```python
return list(set(nums1) & set(nums2)) 
# 对两个数组用set去重后，直接求交集，结果输出转为list
```
##### 易错点
- 输出结果不能出现重复数字，所以需要set去重
- 要么就是先去重，再取交集；要么先取交集，再对结果去重

---

#### 快乐数(set)
[题目：Leetcode 202](https://leetcode.com/problems/happy-number)
##### 简单思路
- 规则：每一次将n替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1
- 不断更新n，如果出现重复的数字（有循环），即不是快乐数，return False
- 用set记录出现过的数
##### 代码呈现
```python
seen = set() # 初始化空set，存放出现过的结果
while n != 1: # 循环停止条件——发现快乐数
	n = sum(int(i) ** 2 for i in str(n)) # 用列表生成式，更新n
	if n in seen:
		return False # 如果n之前出现过，返回False
	seen.add(n) # 加进结果set
return True
```
##### 易错点
- 初始化定义空set是`set()`
- 证明是快乐数后停止循环，即循环条件为`while n != 1:`
- 用列表生成式更新n
- 更新完n后，要先检查是否出现过，检查完后才add进set(`seen.add(n)`)

---

#### 两数之和( map[key, value] )
[题目：Leetcode 1](https://leetcode.com/problems/two-sum)
##### 简单思路
- 给你一个target，要你从列表中找两个数相加等于这个target，然后返回这两个数的index
- 用map[key, value]，也就是字典，可以同时找到键值对
- 构建一个字典，存放遍历过的数
- 每遍历一个数，就从字典里面找有没有与它相加等于target的伙伴，有的话就返回它们的index
- 所以这道题key是数，value是数的索引(index)，不要搞乱
##### 代码呈现
```python
records = {} # 初始化空字典，存遍历过的元素
for index, value in enumerate(nums): # 迭代器，为每个遍历值添加索引index
	if target - value in records: # 如果另一半在records里面
		return [records[target-value], index] # 直接return另一半的index和它的index
	records[value] = index # 如果另一半不在里面，就把这个遍历值存进来。
	# value是键，index是值
return [] # 遍历完都没有，返回空列表
```
##### 易错点
- records初始化是空字典，不要初始化成别的结构
- 遍历时要用enumerate同时生成index和value，for loop时index在前
- 存放遍历过的元素时value是键，index是值，即`records[value] = index`，因为最后是要返回的是index
