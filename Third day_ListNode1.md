# 代码随想录算法训练营第三天
## 链表第一天
### 考点总结：
- 链表操作节点（如add增、delete删）都需要先找前驱节点
- 链表头没有前驱节点，可以手动定义一个dummy_head作为头节点的前驱节点，这样可将头节点视为一般节点
- 反转链表需要三个指针pre/current/temp，pre/current两个在while循环外面，负责反转指针，temp在while循环内，负责暂存current后的节点防止丢失
- 注意定义链表/新节点的语法，不要出现语法错误

---

#### 移除链表元素(dummy_head)
[题目：Leetcode 203](https://leetcode.com/problems/remove-linked-list-elements)
##### 简单思路
- 链表中移除元素，就找该节点的上一个节点，让上一个节点跳过要删除的节点，直接指向下下个节点即可。
- 第一个节点（头节点）没有上一个节点，所以需要设置虚拟头节点dummy_head。
- 设置current指针，初始化指向dummy_head。
- 在`while current.next`不为空的条件下遍历链表，删除条件为`current.next.val == val`，即current下一个节点值为目标值时，跳过该节点`current.next = current.next.next`。
##### 代码呈现
```python
class Solution:
    def removeElements(self, head: Optional[ListNode], val: int) -> Optional[ListNode]:
	dummy_head = ListNode(next=head) #在首节点前面定义一个虚拟头节点
	current = dummy_head # current指针从虚拟头节点开始
	
	while current.next: #current指针下一个不为空
		if current.next.val == val: # 如果下一个节点的值是目标值
			current.next = current.next.next # 绕过该节点，current直接指向下下节点
		else:
			current = current.next # 不是目标值，正常遍历
	return dummy_head.next # return现链表的头节点
```
##### 易错点
- dummy_head的定义容易打错`dummy_head = ListNode(next=head)`
- current指针初始是指向dummy_head
- 最后是返回处理后链表的头节点`dummy_head.next`

---

#### 设计链表（初始化、获取值、增加值、删除值）
[题目：](https://leetcode.com/problems/design-linked-list)
##### 简单思路
- 初始化链表节点、dummy_head不要出错
- 有index的，要先判断index范围是否合规
- current指针有时指向dummy_head（需要操作前驱节点），有时指向dummy_head.next（直接操作index指向的节点）
- 插入新节点时语法要正确
##### 代码呈现
```python
class ListNode: #初始化定义链表节点
	def __init__(self, val=0, next=None):
		self.val = val
		self.next = next

class MyLinkedList:
	def __init__(self):
		self.dummy_head = ListNode()
		self.size = 0
	
	def get(self, index:int) -> int:
		if index < 0 or index >= self.size: #index边界检查，此时index不能取到size（超索引）
			return -1
		current = self.dummy_head.next #就要找index上的那一个
		for i in range(index): # 遍历目标节点
			current = current.next
		return current.val # return目标节点的值
		
	def addAtHead(self, val:int) -> None:
		self.dummy_head.next = ListNode(val, self.dummy_head.next) #在头部插入节点
		self.size += 1
	
	def addAtTail(self, val:int) -> None	:
		current = self.dummy_head
		while current.next: #遍历到最后一个节点
			current = current.next
		current.next = ListNode(val) # 添加节点
		self.size += 1
	
	def addAtIndex(self, index:int, val:int) -> None:
		if index < 0 or index > self.size: # 检验边界
			return
		current = self.dummy_head # 要找的位置是插入前一个
		for i in range(index): 
			current = current.next
		current.next = ListNode(val, current.next) #插入节点
		self.size += 1
		
	def deleteAtIndex(self, index:int) -> None	:
		if index < 0 or index >= self.size:
			return
		current = self.dummy_head #要找待删除的前一个节点
		for i in range(index):
			current = current.next
		current.next = current.next.next
		self.size -= 1
```
##### 易错点
1. `index >= size` vs `index > size`
	- `index >= size`：需要访问链表中的现有节点（包含最后一个节点）
		- 获取节点，`get` 方法只能访问范围是 `[0, size-1]` 的节点。
		- 删除节点，`deleteAtIndex` 只能删除范围是 `[0, size-1]` 的节点。
	- `index > size`：允许在链表末尾插入节点时，`index == size` 是有效的。
		- 插入节点：`addAtIndex` 方法允许在末尾插入节点。
2. `current = dummy_head` vs `current = dummy_head.next`
	- `current = dummy_head`：用于需要操作前驱节点的场景。
		1.插入节点；
		2.删除节点。
	- `current = dummy_head.next`：不需操作前驱节点场景。
		- 获取index节点
3. 是否需要`return`：
	- 需要：1. 有返回值的场景（获取节点）；2. 边界检查不通过时立即return退出
	- 不需要：`addAtHead`、`addAtTail`、`addAtIndex`、`deleteAtIndex`只需完成插入或删除操作
4. 总结
	| 操作                      | index边界检查             | `current` 初始值         | 是否需要 `return` |
	|---------------------------|----------------------|-------------------------|-------------------|
	| **获取节点 (`get`)**       | `index >= size`     | `dummy_head.next`      | **需要**          |
	| **插入头部 (`addAtHead`)** | 不需要              | `dummy_head`           | 不需要            |
	| **插入尾部 (`addAtTail`)** | 不需要              | `dummy_head`           | 不需要            |
	| **插入中间 (`addAtIndex`)**| `index > size`      | `dummy_head`           | 不需要            |
	| **删除节点 (`deleteAtIndex`)** | `index >= size`     | `dummy_head`           | 不需要            |

---

#### 反转链表（double index + temp）
[题目：Leetcode 206](https://leetcode.com/problems/reverse-linked-list)
##### 简单思路
- 双指针遍历反转，temp指针存待翻转的节点，防止丢失
- 初始化current指向head，pre为None
- 遍历条件`while current:`遍历完成后current指向Null，pre是操作完成链表的头节点
- while循环中：不断由temp暂存current后面节点，current改变指针方向指向pre，再移动pre与current
##### 代码呈现
```python
current = head # 初始化定义current指向head，未来的尾节点
pre = None # pre在current前面，初始是None
while current: # 遍历链表，遍历结束后pre是指向尾节点，未来的头节点
	temp = current.next # 用temp暂存current后面的节点，防止丢失
	current.next = pre # current节点指针方向翻转
	pre = current # pre指针开始滑动，向后移指向current节点
	current = temp # current指针滑动，向后移指向temp暂存的节点
return pre #返回操作完成后的头节点
```
##### 易错点
- 初始化定义时，current指向head，pre指向None
- while循环条件是current不为空，即`while current:`
- 循环内要用temp存current后面的节点，防止丢失
- current指针反转后，一定是先移动pre `pre = current`，再移动current `current = temp`
- 最后返回头节点pre
