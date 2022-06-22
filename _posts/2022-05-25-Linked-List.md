---
title: 链表归纳（更新中）
date: 2022-05-27 18:38:00 -0500
categories: [学习笔记]
tags: [Python, Algorithm]
pin: false
author: Yuheng Shi

toc: true
comments: true
typora-root-url: ../../yuhengua2077.github.io
math: false
mermaid: true

---

# 链表类型

* 单向链表
* 双向链表
* 循环链表

## 1. 单向链表

单向链表的每个节点由`val`和`next`两部分组成，分别代表了该节点储存的值以及指向下一个节点的指针。 链表的开始节点（入口节点）被称为`head`。

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### 特点

* 只有指向下一个节点的指针，只能单向移动查询
* 便于理解，只需要考虑一个指针走向



## 2. 双向链表

双向链表的每个节点由`val`，`prev`和`next`三部分组成，分别代表了该节点储存的值，指向前一个节点的指针以及指向下一个节点的指针。 

```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.prev = None
        self.next = None
```

### 特点

* 每个节点都可以查询到它的前一个节点或者后一个节点，双向查询
* 在改变链表的时候需要考虑两个指针的指向改变，比单链表复杂

## 3. 循环链表

循环链表可以是单向链表也可以是双向链表，特点是**首尾相连**， 可以解决约瑟夫环等问题。


# 链表存储方式

链表在内存中不是连续分布的，通过指针来连接内存中存储的各个节点。逻辑上相邻的数据元素在物理地址上可能相邻，也可能不相邻。其在物理地址上的表现是随机的。

# 链表优缺点

## 优点

* 存储空间不必事先分配，在需要存储空间的时候可以临时申请，不会造成空间的浪费
* 一些操作的时间效率远比数组高（插入、移动、删除元素等）

## 缺点

* 不仅数据元素本身的数据信息要占用存储空间，指针也需要占用存储空间，链表结构比数组结构的空间开销大。


# 链表基本操作

## 1. 创建一个线性链表

需要先创建一个头节点`head`，然后后续为新添加的数据创建新节点并从尾部插入，最后返回的是链表头节点的地址来表示整个链表。
需要一个指针跟踪尾部节点来插入新节点。 时间复杂度为**O(n)**，`n`为链表长度。

```python
head = ListNode(0)
curr = head
for i in range(len(data)):
    node = ListNode(data[i])
    curr.next = node
    curr = curr.next
```

## 2. 求线性链表长度

需要一个指针从头节点`head`出发，遍历每一个节点，每经过一个count加一，直到有节点`next`指向的值为`None`（到达链表尾部）， count的值就为链表长度。 时间复杂度为**O(n)**，`n`为链表长度。

```python
count = 0
curr = head
while curr:
    count += 1
    curr = curr.next 
return count
```

## 3. 查找元素

不像数组，链表的元素查找必须从头节点出发，使用一个指针遍历元素，如果发现`val`符合条件的就返回指针所在节点的地址，相反就返回`None`。 时间复杂度为**O(n)**，`n`为链表长度。

```python
count = 0
curr = head
while curr:
    if val == curr.val:
        return curr
    curr = curr.next
return None
```

## 4. 插入元素

链表的插入操作分为三种：
* 在**链表头部**插入新节点
* 在**链表尾部**插入新节点
* 在**链表第`i`处**插入新节点

### 头部插入: **O(1)**

直接以目标元素值创建一个节点，将其`next`指针指向原链表的`head`头节点，并把这个新节点指向为新的头节点`head`。
因为在链表头部插入链节点与链表的长度无关，所以该算法的时间复杂度为`O(1)`。

```python
node = ListNode(val)
node.next = head
head = node
```

### 尾部插入: **O(n)**

以目标元素值创建一个节点，用一个指针`curr`从头节点出发，遍历直到到达尾部节点，将指针`curr.next`指向创造的新节点。
很明显，`curr`指针需要走整个链表才能到达尾部，时间复杂度为`O(n)`。

```python
node = ListNode(val)
curr = head
while curr.next:
    curr = curr.next
curr.next = node
```

### 中间插入: 平均**O(n)**
以目标元素值创建一个节点，用一个指针`curr`以及一个计数器`count`从头节点出发，遍历直到`count = i - 1`停止遍历，将新节点的`next`指针指向原本的`curr.next`节点，并将指针`curr.next`指向创造的新节点。因为将`cur`从链表头部移动到第`i`个链节点之前的操作平均时间复杂度是`O(n)`，所以该算法的时间复杂度是`O(n)`。

```python
node = ListNode(val)
count = 0
curr = head
while curr and count < index - 1:
    count += 1
    curr = curr.next
        
if not curr:
    return 'Error'
    
node.next = curr.next
curr.next = node
```

