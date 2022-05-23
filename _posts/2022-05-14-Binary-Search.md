---
title: 二分查找 Binary Search（更新中）
date: 2022-05-14 19:00:00 -0500
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

# 前提条件

* 数组一定为**有序数组**！**sorted array**
* 数组**不包含重复元素** **unique/distinct** values/elements

# 步骤

* `target`为目标数字, `nums`为数组名称
* 一般使用**左闭右闭**区间，即`[left, right]`
* define `left = 0, right = len(nums) - 1`
* 取中心节点`mid`位置，一般为`mid = (left + right) // 2`，或者在Java或者C++用`left + (right - left) / 2`防止int overflow
* 如果`nums[mid]`大于`target`，说明`target`存在于左区间，右节点`right`需等于`mid - 1`，在区间`[left, mid - 1]`里继续搜索
* 否则如果`nums[mid]`小于`target`，说明`target`存在于右区间，左节点`left`需等于`mid + 1`，在区间`[mid + 1, right]`里继续搜索
* 如果上述两种都不符合，则表示`nums[mid]`和`target`相等，直接`return mid`

# 使用场景

* 寻找target元素下标
* 缩小存在target的范围（target可能不在这个数组里面）

# 代码

二分查找的代码可以有非递归和递归写法两种：

非递归写法：
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        # find target in [left, right]
        while left <= right:
            # get the mid index
            mid = (left + right) // 2
            if nums[mid] > target:
                right = mid - 1
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        # return -1 if no target is find
        return -1
```

递归写法：
```python
def binarySearch(array, left, right, target):
    if left <= right:
        mid = left + (right - left) // 2
        if array[mid] == target:
            return mid
        elif array[mid] > target:
            return binarySearch(array, left, mid - 1, target)
        else:
            return binarySearch(array, mid + 1, right, target)
    else:
        return -1
        
binarySearch(nums, 0, len(nums) - 1, target)

```

# Leetcode 题目解释

## 153. Find Minimum in Rotated Sorted Array

这道题需要用**左闭右开**区间，因为位于mid的元素不能被排除，需要记录。因为是sorted array，任何时候如果`nums[i] <= nums[j]`成立，说明从`i`到`j`区间一定是递增的。

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = 0, len(nums)-1
        while left < right:
            mid = left + (right - left) // 2
            if nums[mid] <= nums[right]:    #如果这个条件成立，那么最小值要么是mid，要么在mid左侧，所以应该把right移过来
                right = mid     #搜索[left, mid)区间
            else:
                left = mid + 1
        return nums[left]       #这个时候是刚好left = right，这边写nums[right]也是一样的
```

## 33. Search in Rotated Sorted Array

基本思路是正常二分，但是由于是旋转过的array，可能会出现两段递增区间。如果是只有一段区间（未旋转或者旋转次数恰好等于数组大小），则第二段区间默认为空数组。思路是先判断`mid`的位置，然后再进一步搜索`target`。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums)-1
        while left <= right:
            mid = left + (right - left) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] >= nums[left]:       # mid比left大，说明mid在左侧区间上，因为右侧区间的数字必然比左区间的都要小
                if nums[left] <= target < nums[mid]:    # target在左侧区间且比mid的值要小（左侧），移动right过来
                    right = mid - 1
                else:       # 除此之外，要么target在左侧但值比mid要大（右侧），要么是在右侧区间上，这些情况都是移动left过去
                    left = mid + 1
            else:       # 反之mid位于右侧区间
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
        return -1
```

## 34. Find First and Last Position of Element in Sorted Array

因为有重复的元素，普通的二分查找需要进行一些改进，这边的方法是分别用二分法找左侧边界和右侧边界然后得出结果。会有几种情况：
1. target out of bound，或者in bound但是不存在，则return [-1, -1]
2. in bound且存在那就是return答案了

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        def findRight(nums, target):
            rightPos = -3   # 因为0会是答案的一种，初始化一个不可能的答案来判断
            left, right = 0, len(nums)-1
            while left <= right:
                mid = left + (right - left) // 2
                if nums[mid] <= target:     # 找右侧边界的话，等于的情况要更新left
                    left = mid + 1
                    rightPos = left
                else:
                    right = mid - 1
            return rightPos
        
        def findLeft(nums, target):
            leftPos = -3
            left, right = 0, len(nums)-1
            while left <= right:
                mid = left + (right - left) // 2
                if nums[mid] >= target:     # 找左侧边界的话，等于的情况要更新right
                    right = mid - 1
                    leftPos = right
                else:
                    left = mid + 1
            return leftPos

        # 这边上面找的左右边界都是在实际的边界之外一格的位置
        
        leftPos = findLeft(nums, target)
        rightPos = findRight(nums, target)
        if leftPos == -3 or rightPos == -3:     # out of bound 的情况，边界没有被更新
            return [-1, -1]
        if rightPos - leftPos > 1:      # 是实际边界一格外的位置，所以差值至少为2
            return [leftPos + 1, rightPos - 1]  # 返回的时候把边界移到正确的位置
        return[-1, -1]  # 不存在的情况
```




