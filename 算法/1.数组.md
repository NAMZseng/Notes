# 0. 理论基础
- 数组是存放在**连续内存空间**上的相同类型数据的集合。因此，在删除或者插入新元素的时候，就难免要移动已有的元素。
- 大部分编程语言，数组下标都是从0开始的（matlab是从1开始）。
# 1. 二分查找

## 注意事项
- 适用于**有序且无重复元素**的数组（当有重复元素，使用二分查找法返回的元素下标可能不是唯一的）。
- 注意要查找元素的**边界定义**，即target∈[left, right] or target∈[left, right)（两种边界定义分别对应两种写法）。

## 代码实现
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums)  # 定义target在左闭右开的区间里，即：[left, right)

        while left < right:  # 因为left == right的时候，在[left, right)是无效的空间，所以使用 <
            middle = left + (right - left) // 2 # 防止溢出 等同于(left + right)/2

            if nums[middle] > target:
                right = middle  # target 在左区间，在[left, middle)中
            elif nums[middle] < target:
                left = middle + 1  # target 在右区间，在[middle + 1, right)中
            else:
                return middle  # 数组中找到目标值，直接返回下标
        return -1  # 未找到目标值
```

## 已刷题目
- [35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)
- [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

## 参考资料
- [代码随想录-二分查找](https://github.com/NAMZseng/leetcode-master/blob/master/problems/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.md)

# 2. 双指针/滑动窗口
## 注意事项
- 写代码前需要先构思好两个指针各自的作用。
- 滑动窗口也可以理解为双指针法的一种，实现滑动窗口，主要确定如下三点：
    - 窗口内是什么？
    - 如何移动窗口的起始位置？
    - 如何移动窗口的结束位置?
- 判断滑动窗口的时间复杂度，主要看每个元素被操作的次数，如果每个元素最多只进入窗口一次，出去窗口一次，那么总次数是2*n，即O(n)。

## 已刷题目
- [27. 移除元素](https://leetcode.cn/problems/remove-element/)
- [977.有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/submissions/)
- [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

## 参考资料
- [代码随想录-长度最小的子数组](https://github.com/NAMZseng/leetcode-master/blob/master/problems/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.md)
