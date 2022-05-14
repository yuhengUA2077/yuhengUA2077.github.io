---
title: 二分查找 Binary Search（更新中）
date: 2022-05-14 17:30:00 -0500
categories: [学习笔记]
tags: [Python]
pin: false
author: Yuheng Shi

toc: true
comments: true
typora-root-url: ../../yuhengua2077.github.io
math: false
mermaid: true

---

# 前提条件

* 数组一定为**有序数组**！ **sorted array**
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