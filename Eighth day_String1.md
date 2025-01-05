# 代码随想录算法训练营第八天

## 字符串第一天

### 考点总结

- 常用双指针和切片，转为list再变换字符串元素
- 反转字符串用双指针，交换前后指针元素
- 切片也能做到反转，重构字符串时可以往切片想
- `.isdigit()`函数可以判断字符串是否全为数字字符构成

---

#### 反转字符串(Double Index)

[题目：Leetcode 344](https://leetcode.com/problems/reverse-string)

##### 简单思路

- 将一字符串的元素反转，前面变到后面，后面变到前面
- 双指针，一个指前面，一个指后面，然后交换位置，指针区间向内缩小

##### 代码呈现

```python
left = 0
right = len(s) - 1
while left < right: # 循环条件是left < right
    tmp = s[right] # 注意字符串名称，这里是s
    s[right] = s[left]
    s[left] = tmp
    left += 1
    right -= 1
```

##### 易错点

- 循环条件是left<right，中间不能相等（相等不能互换）
- 互换时要用tmp暂存一个指针

---

#### 反转字符串ii(Double Index + slicing)

[题目：Leetcode 541](https://leetcode.com/problems/reverse-string-ii)

##### 简单思路

- 给一字符串s和整数k，将每2*k区间中的前k个元素反转
- 如果最后的区间不足k个，反转全部；如果最后区间元素有k~2*k个，反转前k个
- 用双指针和字符串切片解决
  - 双指针定位需要反转的元素，指针1步长为2*k，遍历整个字符串
  - 用切片来反转元素，string[ : : -1]可以反转相应字符串

##### 代码呈现

```python
p1 = 0 # 指针1用来遍历字符串
while p1 < len(s):
    p2 = p1 + k # 指针2用来确定反转的长度
    # 更新字符串，p1前 + p1和p2之间需反转 + p2后
    s = s[:p1] + s[p1:p2][::-1] + s[p2:]
    # 切片反转可以自动满足末尾条件，不用再写其它的判断
    p1 += 2*k # 指针1每次步长为2*k
return s
```

##### 易错点

- 指针1的步长为2*k，用来遍历整个字符串
- 指针2用来确定反转的长度，写在循环内，每次都根据指针1更新
- 用切片更新字符串，分成三部分更新，p1前 + p1和p2之间需反转 + p2后
- 切片反转不用再多加条件判断末尾，python切片超限自动返回空值

---

#### 替换数字

[题目链接](https://kamacoder.com/problempage.php?pid=1064)

##### 简单思路

- 把字符串中的阿拉伯数字变成单词'number'
- 把字符串转成列表，遍历列表中的元素，判断是否为字母，不是字母就变'number'

##### 代码呈现

```python
s = input() # 构造输入
lst = list(s) # 转为list好处理
for i in range(len(lst)):
    if lst[i].isdigit(): # 判断该元素是否为数字
        lst[i] = 'number' # 修改i对应的位置元素
print(''.join(lst)) # 转换为字符串再输出
```

##### 易错点

- 注意IO格式，ACM的格式就像Kattis，都是要手动构造输入输出
- 错误案例：`for i in lst:`，i 是从 lst 中取出的元素，它是不可变的（字符串）。
    - 因此，修改 i 不会影响原始列表 lst。你应该修改 lst 中对应的位置，而不是单纯地修改 i。
- `string.isdigit()`函数是判断元素是否只含数字
  - 如果全是数字，返回True
  - 如果不全是数字字符，返回False
