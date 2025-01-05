# 代码随想录算法训练营第九天

## 字符串第二天

### 考点总结

- 把题目更快更好做出来为目标，不用追求高深的算法思想
- 要熟练使用Python的库函数，有时候库函数能很便捷地解题

---

#### 翻转字符串里的单词(" ".join())

[题目：Leetcode 151](https://leetcode.com/problems/reverse-words-in-a-string)

##### 简单思路

- 将字符串里的单词顺序翻转，单词与单词之间要有1个空格，其它地方无空格
- 先将字符串的单词用split()弄出来
- 然后用切片[ : : -1]翻转顺序
- 最后用" ".join()组合成新字符串

##### 代码呈现

```python
s = s.split() # 把s中的单词分出来
s = s[::-1] # 将单词顺序调换
return " ".join(str(word) for word in list(s)) # 每个单词中间插入空格
```

##### 易错点

- `s.split(separator)`是将字符串中的元素按separator分割成一个列表
- `'separator'.join(iterable)`：将 `iterable` 中的每个元素（必须是字符串）按顺序用 `separator` 连接成新字符串

---

#### 字符串尾部移到前面（切片）

[题目链接](https://kamacoder.com/problempage.php?pid=1065)

##### 简单思路

- 将字符串尾部k个字符移到开头
- 不移动的字符串长度为`len(s) - k`
- 用切片重构字符串

##### 代码呈现

```python
k = int(input()) # 前两行接受input数据，k要变成正整数
s = input()
s = s[len(s)-k:] + s[:len(s)-k] # s更新为k后面的+k前面的
print(s) # 打印输出
```

##### 易错点

- ACM模式关注IO，有时代码跑不通大概率就是IO没对上
- 注意切片重构字符串时，要搞清楚节点

---

#### KMP算法（一刷跳过，二刷时再硬啃）
