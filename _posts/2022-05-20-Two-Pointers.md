---
title: 双指针、滑动窗口归纳（更新中）
date: 2022-05-20 01:37:00 -0500
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

# 形式

* 对撞指针
* 快慢指针
* 分离指针

# 1. 对撞指针

对撞指针是指`left`, `right`两个指针，分别指向数组第一个和最后一个元素。在loop里面满足条件时，`left`指针递增或者是`right`指针递减，直到两个指针指向同一个元素或者是满足题目的条件结束循环。

```python
left, right = 0, len(nums) - 1
while left < right:
	if condition1:
		return something
	elif condition2:
		left += 1
	else:
		right -= 1
return something_else
```

## 应用

* 有序数组查找类问题：二分查找，两数之和等等
* 字符串反转类问题：回文字符串等等



# 2. 快慢指针

快慢指针是指`slow`, `fast`两个指针，分别指向数组第一个和第二个元素（也有可能都从第一个元素开始）。在loop里面满足一定条件时，`slow`指针增加，满足其他一定条件时（或者不需要条件），`fast`指针增加，直到`fast`指针指向数组最后一个元素或者是满足题目的条件结束循环。

```python
slow, fast = 0, 1
while fast < len(nums):
	if condition1:
		slow += 1   # 切记切记快慢指针都是加法！！总是写成减法
	fast += 1
return something
```

## 应用

* 数组的添加、删除元素，要求不使用额外空间
* 链表中的判断是否有环、长度问题

# **滑动窗口**

* 来自滑动窗口协议（Sliding Window Protocol)，这是一个data link layer协议，TCP也有用到，Receiver发送window size到Sender来控制发送速度，Sender允许发送多个packets而不用等待ACK信号。这个协议提高了数据传输的效率和throughput。
* 属于快慢指针的一种特殊形式。

## 应用范围和分类

* 一般用于需要查找连续一段区间的subset/substring问题，可以简化传统的nested double loop来提高效率
* 分为两种形态：
	* 固定长度窗口
	* 非固定长度窗口：最大/最小长度

## I. 固定长度窗口

* 窗口长度给定，判断当窗口长度达到给定size时移动窗口

**1343. Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold 大小为K且平均值大于等于阈值的子数组数目**

```python
class Solution:
    def numOfSubarrays(self, arr: List[int], k: int, threshold: int) -> int:
        slow, fast = 0, 0	# 按照快慢指针初始化两个指针
        window_sum = 0		# 初始化一个窗口，这里是subarray的和
        res = 0		# 初始化最终的结果
        while fast < len(arr):
            window_sum += arr[fast]		# 把新元素放到窗口里面
            if fast - slow + 1 >= k:	# 当窗口大小超过了给定大小，需要缩减以维持窗口长度
                if window_sum / k >= threshold:		# 这边是判断是否满足题目条件，满足的话记录结果
                    res += 1
                window_sum -= arr[slow]		# 移除窗口左侧元素，并且增加slow指针来维持size
                slow += 1
            fast += 1		# 快指针一直往右移动
            
            
        return res
```

## II. 非固定长度窗口

* 一般要找最小或者最大窗口长度的满足条件的subarray
* 首先需要增加fast指针以扩大窗口，当窗口内元素满足条件后暂停扩张
* 然后需要减少slow指针以缩小窗口，移除窗口左侧元素，直到窗口内元素不再满足条件。
* 结构为两层while loop

**3. Longest Substring Without Repeating Characters 无重复字符的最长子串**

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        slow, fast = 0, 0
        substring = dict()		# 初始化一个窗口，这里使用链表（词典）
        res = 0
        while fast < len(s):
        	# 把新元素放进窗口，这边是由于dict特性需要写一个判断
            if s[fast] not in substring:
                substring[s[fast]] = 1
            else:
                substring[s[fast]] += 1

            while substring[s[fast]] > 1:	# 切记这边的判断是while，因为每次缩减窗口之后都要进行判断。这边的判断是是否重复元素
            	# 移除窗口左侧元素并缩小size
                substring[s[slow]] -= 1
                slow += 1
            res = max(fast - slow + 1, res)		# 每次都要留下最大值，fast-slow+1 指的是窗口size，也就是substring长度
            fast += 1
            
        return res
```

**209. Minimum Size Subarray Sum 元素和大于给定值的长度最小的子数组**

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        slow, fast = 0, 0
        window_sum = 0
        res = len(nums) + 1 	# 求最小值，所以初始化为大值
        while fast < len(nums):
            window_sum += nums[fast]	# 添加新元素
            while window_sum >= target:
                res = min(res, fast - slow + 1)		# 注意这边最终结果要的是最小值，需要放进去实时更新
                window_sum -= nums[slow]
                slow += 1
            
            fast += 1
            
        if res == len(nums) + 1:
            return 0
        return res 
```

**713. Subarray Product Less Than K 乘积小于K的子数组**

```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        if k <= 1:
            return 0
        slow, fast = 0, 0
        res = 0
        window_mult = 1
        while fast < len(nums):
            window_mult *= nums[fast]
            while window_mult >= k:
                window_mult /= nums[slow]
                slow += 1
            # 下面这一行非常关键！！如果当前乘积小于k，那么right左边含right的所有子串乘积一定都小于k，这个数量刚好就是窗口长度
            res += (fast - slow + 1)
            fast += 1
        return res
```

# 3. 分离指针

分离指针是指`p1`, `p2`两个指针，分别指向不同数组/链表的第一个元素。在loop里面满足一定条件时，两个指针同时右移，满足另外一些条件时只右移`p1`指针，满足另一些条件时只右移`p2`指针，直到其中一个数组/链表遍历完成或者是满足题目的条件结束循环。

```python
p1, p2 = 0, 0
while p1 < len(nums1) and p2 < len(nums2):
	if condition1:
		p1 += 1
		p2 += 1
	elif condition2:
		p1 += 1
	else:
		p2 += 1
return something
```

## 应用

* sorted数组合并，求交集、并集问题

