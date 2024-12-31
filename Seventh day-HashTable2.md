# 代码随想录算法训练营第七天

## 哈希表第二天

### 考点总结：

- 哈希map和哈希数组要灵活运用，万变不离其宗
- 需要键值对应就用map，元素数量少就用数组
- 有些用不了哈希表，还是得回到双指针（题目要求去重）

---

#### 四数相加（two sum升级版）

[题目：Leetcode 454](https://leetcode.com/problems/4sum-ii)

##### 简单思路

- 四个列表中，每个列表选一个数出来，这四个数的和为0，问能选几组？
- 两两列表遍历，先存 $(a+b)$，然后在后两个列表找$(c+d)$，能和前面组合成0
- 初始化一个字典存$(a+b)$的数量
- 如果有对应的$(c+d)$，count直接+=$(a+b)$数量——能组合出这个数量的组

##### 代码呈现

```python
hashmap = {} # new一个字典存放(a+b)
for a in nums1: # 遍历前两个nums
    for b in nums2: 
        if (a+b) in hashmap: # 如果(a+b)在里面
            hashmap[a+b] += 1 # (a+b)对应的出现次数要+1
        else:
            hashmap[a+b] = 1 # 如果没出现过，记录一下
count = 0
for c in nums3:
    for d in nums4:
        key = -(c+d) # key就是满足四个数之和为0的(a+b)
        if key in hashmap:
            count += hashmap[key] # count直接+=字典中key的数量
return count 
```

##### 易错点

- 四个数组两两遍历，时间复杂度最小，是$O(n^2)$。（如果13遍历，是$O(n^3)$；4个for是$O(n^4)$）
- 计数器count是+=字典中key的数量，即`count += hashmap[key]`
  - 比如字典中key有3个，那么此时这个满足条件的$(c+d)$就能组成3个$(a+b+c+d)$
  - 所以`count += hashmap[key]`

---

#### 赎金信（有效字母移位词升级）

[题目：Leetcode 383](https://leetcode.com/problems/ransom-note)

##### 简单思路

- 检查赎金信ransom中的字母是否都出自magazine中
- 构建两个哈希数组，一个统计ransom中的字母，一个统计magazine中的字母
- 最后用all函数检验magazine哈希数组中字母数>=ransom哈希数组字母数

##### 代码呈现

```python
ransom_num = [0]*26 # new两个哈希数组分别统计这两个的字母
magazine_num = [0]*26
for i in ransomNote: #统计ransom的字母
    ransom_num[ord(i) - ord('a')] += 1
for i in magazine: #统计magazine的字母
    magazine_num[ord(i) - ord('a')] += 1
return all(ransom_num[i] <= magazine_num[i] for i in range(26)) 
# magazine的哈希数组中，每个字母数量都要>=ransom的--->all函数实现
```

##### 易错点

- 哈希数组要分别初始化，不能用`ransom_num = magazine_num = [0]*26`
- 最后要用all函数，满足条件返回True，不满足返回False

---

#### 三数之和(Double Index)

[题目：Leetcode 15](https://leetcode.com/problems/3sum)

##### 简单思路

- 在一个数组中找三个数，令这三个数之和为0
- 这三个数$(a,b,c)$不能出现完全相同的元组
- 用双指针解，先固定一个指针a在表头，然后用双指针b、c构造区间（数组要先排序）
- 如果区间过大，把右边的指针往左移-->区间缩小
- 如果区间过小，把左边的指针往右移-->区间放大
- a、b、c这三个指针都要去重——自己和自己去重，重复的元素跳过

![15.三数之和](https://camo.githubusercontent.com/195c8b882887df8176a7fc49b069b8a2696f1f47c962a9ebdc3fe961db473503/68747470733a2f2f636f64652d7468696e6b696e672e63646e2e626365626f732e636f6d2f676966732f31352e2545342542382538392545362539352542302545342542392538422545352539322538432e676966)

##### 代码呈现

```python
result = []
nums.sort() # 先把原数组排序，因为不需要返回原来的下标，所以可以打乱顺序
for i in range(len(nums)): # 最左边的i指针
    if nums[i] > 0: # 最小的数都大于0
        return result # 不符合题意，直接返回
    if i > 0 and nums[i] == nums[i-1]: # i位置去重，不能与前一个相等
        continue # 相等的话直接跳过
        
    left = i + 1 # new左右指针
    right = len(nums) - 1
    while left < right: # left等于right时，两指针指向同一个数，不符合三数之和题意
        sum_ = nums[i] + nums[left] + nums[right]
        if sum_ > 0: # 区间过大，right向左移
            right -= 1
        elif sum_ < 0: # 区间过小，left向右移
            left += 1
        else: # 符合条件，result记录此刻结果
            result.append([nums[i], nums[left], nums[right]])
            
            while left < right and nums[right] == nums[right-1]:
                right -= 1 # right去重，如果right前一位数不变，往前移
            while left < right and nums[left] == nums[left+1]:
                left += 1 # left去重，如果left前一位数不变，往后移
            # 记录结果并且left、right都去重后，区间指针向内缩小
            right -= 1
            left += 1
return result
```

##### 易错点

- 数组要先排序，不然没法构造双指针区间
- 先判断数组是否符合题意——`nums[i] > 0`时不合题意直接return
- 去重逻辑最关键，搞清楚什么是出现重复
  - $i$: `nums[i] == nums[i-1]`，i和i左边的相等，是重复
  - $left$: `nums[left] == nums[left+1]`，left和left右边的相等，是重复
  - $right$: `nums[right] == nums[right-1]`，right和right左边的相等，是重复

---

#### 四数之和（三数之和升级）

[题目： Leetcode 18](https://leetcode.com/problems/4sum)

##### 简单思路

- 上一题是从nums找三个数，现在是四个数；之前是和为0，现在变成target
- 比上面多了一个for循环，现在是for i + for k + 双指针区间收缩

##### 代码呈现

```python
result = []
nums.sort()
n = len(nums)
for i in range(n): # 判断条件有变，现在要检查target>0
    if nums[i] > target and target > 0:
        break # 不满足即终止循环
    elif i > 0 and nums[i] == nums[i-1]: # i去重
        continue
    for k in range(i+1, n): # 第二个循环，注意区间是(i+1,n)
        if nums[i]+nums[k] > target and target > 0:
            break
        elif k > i+1 and nums[k] == nums[k-1]: # k去重
            continue
        # 下面的都与三数之和一样    
        left = k + 1
        right = n - 1
        while left < right:
            sum_ = nums[i] + nums[k] + nums[left] + nums[right]
            if sum_ > target:
                right -= 1
            elif sum_ < target:
                left += 1
            else:
                result.append([nums[i], nums[k], nums[left], nums[right]])
                while left < right and nums[right] == nums[right-1]:
                    right -= 1
                while left < right and nums[left] == nums[left+1]:
                    left += 1
                right -= 1
                left += 1
return result
```

##### 易错点

- 判断数组是否合题的条件有变，要新增`target > 0`
- 两层for循环+双指针区间，注意第二层for的区间，其余思路不变
