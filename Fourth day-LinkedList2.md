# 代码随想录算法训练营第四天
## 链表完结
### 考点总结
- 两两交换节点，本质上就是重新设置指针，关键在重新连接的顺序（current连2，1连3，2连1）
- 删除倒数第N个节点，用双指针固定N+1区间，然后把区间平移到链表尾，此时左边界是倒数第N+1个节点，跨过第N个，直接与倒数第N-1连接
- 链表相交，让两个指针分别遍历各自链表，遍历完后去遍历对面的，如果出现两个指针相等，说明有相交，此时指针为交点
- 环形链表，两个循环，第一个循环找环内快慢指针相遇点（快指针走两步，慢指针走一步，循环条件是fast和fast.next不为空），第二个循环找环入口（**相遇点到环入口距离 = 链表头到环入口距离**）

---

#### 两两交换链表节点（处理指针）
[题目：Leetcode 24](https://leetcode.com/problems/swap-nodes-in-pairs)
##### 简单思路
- 交换链表节点就是重新设置指针，节点的真实位置不用变
- 难点在于交换节点的顺序，重新设置指针的顺序有讲究
- current连2，1连3（2的下一个），2再连1。
##### 代码呈现
```python
dummy_head = ListNode(next=head) # 初始化一个dummy_head节点接到链表表头
current = dummy_head # current初始指向dummy_head
# 开始遍历，current后面两个节点，两两交换
while current.next and current.next.next: # 遍历条件：一定要保证current后面有两个节点可供交换
	first = current.next # 定义这轮循环的1和2
	second = current.next.next
	current.next = second # current先连2
	first.next = second.next # 1连3，3是2的后面一个
	second.next = first # 2连1，交换完成
	current = first # current移到下一对待交换的节点前面
return dummy_head.next # return处理完成的链表头
```
##### 易错点
- while循环条件，一定要保证current后面有两个节点可供交换
- 在循环内要先保存待交换的节点
- 交换顺序不能乱：current-->1, 1-->3, 2-->1.
- 完成交换之后current要移到下一对待交换节点前，即`current = first`

---

#### 删除倒数第N个节点(Double Index)
[题目：Leetcode 19](https://leetcode.com/problems/remove-nth-node-from-end-of-list)
##### 简单思路
- 要删除倒数第N个节点，就要先找到倒数第N+1个节点
- 用倒数N+1个节点，跳过倒数第N个，直接连接倒数N-1个节点即完成删除
- 用双指针固定间隔为N+1，然后双指针区间移动到队尾，区间左侧边界即为倒数N+1个节点
##### 代码呈现
```python
dummy_head = ListNode(next=head) # 初始化dummy_head并接入链表头
fast = slow = dummy_head # 初始化定义双指针
for i in range(n+1): # 先让fast往前移n+1个节点，制造宽度为n+1的双指针区间
	fast = fast.next 
while fast: # 将双指针区间平移到链表尾部
	fast = fast.next
	slow = slow.next
slow.next = slow.next.next # 双指针区间为n+1，现在fast指向队尾的Null，slow指向倒数前N+1个节点
return dummy_head.next
```
##### 易错点
- 快慢指针区间为N+1
- while遍历条件是fast不为空，即`while fast:`

---

#### 链表相交(Double Index)
[题目：Leetcode 160](https://leetcode.com/problems/intersection-of-two-linked-lists)
##### 简单思路
- 找两条链表是否有交点，那就双指针分别遍历这两条链表，看最后两个指针会不会相交。
- 假设有交点，设相交部分链表长度为c，第一条链表长度为a+c，第二条长度为b+c
	- 双指针先各自遍历一次其链表，遍历完后再跳至另一个链表头遍历
	- 如果有交点，两个指针会在交点相遇
	- A链表指针走过路程: a+c+b；B链表走过路程: b+c+a --> 路程相等
	- 所以会相遇（指针相等），就说明它们之间一定会有共享的链表段
	- 相遇时的那个点就是链表的交点
##### 代码呈现
```python
if not headA or not headB:
	return None # 检查链表A、B是否符合条件
pA = headA # 初始化两个指针，分别指向各自链表头
pB = headB
while pA != pB: # pA == pB时跳出循环，说明已经到了交点/没有交点(遍历完两个链表)
	pA = pA.next if pA else headB # 两个指针一起开始往下遍历
	pB = pB.next if pB else headA # 如果该链表遍历完（指针为空，就跳到另一个链表头遍历）
return pA
```
##### 易错点
- 要记得先检查链表是否符合条件
- 一共循环两次，第一次遍历原链表，第二次跳去另一个链表头，遍历另一个链表
- 如果节点相等，有两个情况
	- 已经到了交点，此时指针相等且有内容
	- 没有交点，此时指针分别在另一个链表尾部，都是Null

---

#### 环形链表（快慢双指针）
[题目：Leetcode 142](https://leetcode.com/problems/linked-list-cycle-ii)
##### 简单思路
- 先用快慢双指针，找到链表环中的相遇点
- 用数学关系式可知，**相遇点到环入口距离 = 链表头到环入口距离**
- 再设两个同速指针，一个在相遇点出发，一个在链表头出发，同时运动
- 这两个同速指针会在环入口相遇-->指针相等时，就指向环入口
##### 代码呈现
```python
fast = slow = head # 初始化快慢指针
while fast and fast.next: # fast.next.next只有在fast.next不为空时才能被访问，因此只需检查到fast.next
	fast = fast.next.next # 快指针一步走两格
	slow = slow.next # 慢指针一步走一格
	if fast == slow: # 找到相遇点
		index1 = fast # index1从相遇点出发
		index2 = head # index2从链表头出发
		while index1 != index2: # 需要它们相遇，不相遇就继续走
			index1 = index1.next # index1和index2速度相等
			index2 = index2.next
		return index1 # 两个index会在入口处相遇，直接return环入口
return None # 如果无环，就返回Null
```
##### 易错点
- 两个循环，第一个循环找快慢指针相遇点，第二个循环找环入口
- 找相遇点循环，条件是`while fast and fast.next:`只需限定fast和fast.next不为空
- 找入口循环，又设两个同速指针，一个从相遇点出发，一个从链表头出发，会在环入口相遇
- 最后要在所有循环之外`return None`，对应链表无环的情况
