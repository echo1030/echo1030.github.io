---
layout: post
title: 二分查找
category: leetcode
categories: Blog
description: 
---
## 二分查找

二分查找，面试中的顶流。本以为大家会喜欢考 DP 之类的，但是体验下来，竟然是以下三个类型题目更常见。
1. 二分查找
2. 链表
3. DFS/BFS 

市面上的模板已经很多了，这里就从我个人对二分查找认知的递进来描述一下吧。

### 1. 初印象

对二分查找的最开始的印象是高中学函数。对于三次方程这种没办法用韦氏定理解的，可以用二分查找得到近似解，具体表现为
> 在区间 `[a, b] 上，如果 f(a)*f(b) < 0，一定存在点 c， 使得 f(c) = 0`

但是因为不是考点，就只是建立了一个印象，知道二分可以求函数近似解。

### 2. 单调数组中寻找目标

再见二分查找，就是为了面试，开始按 tag 刷题时候了。此时对二分查找的认知，停留在 
> 在可重复的单调区间以 O(logN) 的时间复杂度，查找指定目标

并且开始找一些二分查找的模板来参考。主要参考了 [二分查找、二分边界查找算法的模板代码总结]。

#### 模板总结对比
看完了之自己也要总结一遍，一共有三个模板。

数组条件如下。
1. 数组大小为 n
2. 数组存放整数
3. 数组单调不减

||查找目标数|查找目标数左边界|查找目标数右边界|
|---|---|---|---|
|查找范围|[0, n-1]|[0, n-1]|[0, n-1]|
|循环条件| l <= r| l < l| l < r|
|循环退出时状态| l > r| l = r | l = r |
|mid选择| mid = (r-l)/2| mid = (r-l)/2| mid = (r-l)/2 + 1|
|左右边界更新操作|l = mid + 1<br> r = mid - 1| l = mid <br> r = mid - 1| l = mid + 1<br> r = mid| 
|循环退出含义|没找到|根据nums[l]和target关系定|根据nums[l]和target关系定|

除了查找范围，其他的部分都和具体的场景有关。退出条件，mid 选择和左右边界更新相互之间还有联系。

#### 模板具体细节

基本上，背下以上模板，大多数的二分查找问题就都可以有结果了，但是还是要了解一下具体含义。

##### 标准二分

此时二分查找的目标就是找到值和 target 相等的数的下标返回。

mid 直接取中间值，又因为整数除法的特性，mid 偏左。

由于退出条件是 l <= r，所以每次 l 和 r 都要改变，要不就有可能出现 l = r, mid 偏左等于 l ，导致死循环。

``` C++
int bs(vector<int>& nums, int target){
   int l = 0;
   int r = nums.size() - 1;
   while (l <= r){
      int mid = l + (r-l)/2;
      if (nums[mid] == target){
         return mid;
      } else if (nums[mid] < target){
         l = mid + 1;
      } else {
         r = mid - 1;
      }
   }
   return -1;
}
```
##### 寻找左边界

这种情况就是针对有重复数字的情况。

相比标准二分查找，由于要找左边界，所以 nums[mid] = target 时候，不能直接退出，要更新 r，最后迭代找到左边界。

这也导致了循环退出条件，左右边界变化有所不同。

这是因为
   1. 我希望在 [0, n-1] 上进行二分查找
   2. 找边界不像找目标，找到就能退出，而是要等到循环退出后，再比较边界的值和 target 才能知道找没找到
   3. 如果还使用 l <= r，那退出时候，有可能 l > r，数组越界

所以
   1. 循环条件选择 l < r，退出时一定是 l == r，直接判断 nums[l] 和 target 关系即可，不用考虑 nums[r] 和越界
   2. mid = l + (r-l)/2 偏左
   3. 边界变化时，哪怕 l = r - 1，不论 l = mid + 1，还是 r = mid，都可以让查找范围移动，达到退出条件，保证不会死循环。

``` C++
int bs(vector<int>& nums, int target){
   int l = 0;
   int r = nums.size() - 1;
   while (l < r){
      int mid = l + (r-l)/2;
      if (nums[mid] > target){
         l = mid + 1;
      } else {
         r = mid;
      }
   }
   return nums[l] == target ? l : -1;
}
```
##### 寻找右边界

寻找右边界是寻找左边界的对称版，所有情况，考虑因素都与左边界一致。

唯一需要注意的点是 mid 的选择。

从感情上理解，左边界时候 mid 偏左，右边界应该偏右。

实际是，如果 nums[mid] = target，由于寻找右边界，所以要 l = mid。这时候如果 mid 还是偏左，那右可能造成一次循环，边界没有移动，从而造成死循环。所以需要让 mid 偏右，打破这种情况。
``` C++
int bs(vector<int>& nums, int target){
   int l = 0;
   int r = nums.size() - 1;
   while (l < r){
      int mid = l + (r-l)/2 + 1;
      if (nums[mid] < target){
         r = mid - 1;
      } else {
         l = mid;
      }
   }
   return nums[l] == target ? l : -1;
}
```
##### 补充一些面试的小技巧

面试沟通也很重要，但是也别什么都让面试官决定，问的时候带着对自己有利的假设问，比如数组存 int，数组是递增，数组没有重复等等。一般来讲，他们不会拒绝，有特殊要求也是简单版本完成后递进的事儿了。

### 3. 理解二段性

刷题过程中，寻找山峰和旋转数组找旋转点这两个题目给我留下了很深的印象，最开始真的怎么也不明白，为什么非单调还能用二分。

后来深入了解了一下，接触到了二段性的概念。在一个区间上，如果有一个点可以将区间分为两部分，一部分满足某个属性，另一部分不满足，就可以通过二分查找，达到一些目的。

在单调区间内找目标值和边界值是一种狭义的二段性。

广义的二段性，难以言传，用一些题目来意会一下吧

#### [无重复旋转数组找最值]

单调数组旋转之后，**有可能** 出现一部分的值大于旋转点（数组最后一个元素），一部分值小于旋转点的情况。可以根据这个特性，找到数组的最大最小值，或者找目标值。

如果数组在 [mid, right] 中递增，那最小值可能是 mid，也可能在左边，收缩右边界

如果没有递增，那当前 [mid, right] 一定由未旋转时候的尾巴和头部拼接而成，所以最小值在右边，更新左边界。
``` Go
func findMin(nums []int) int {
    l := 0
    r := len(nums) - 1
    for l < r {
        mid := l + (r-l)/2
        if nums[mid] < nums[r] {
            r = mid
        } else {
            l = mid + 1
        }
    }
    return nums[l]
}
```
#### [无重复旋转数组找目标值]

使用特性和上边题目一样，但是需要多考虑一层 target 的情况。

看到很多解题是做了两次，一次找旋转点，一次找目标值，这样比较清晰。但是我想强迫自己去理解二段性的概念，所以写在了一起。

``` Go
func search(nums []int, target int) int {
    n := len(nums)
    l := 0
    r := n-1
    for l <= r {
        mid := l + (r-l)/2
        if target == nums[mid] {
            return mid
        }
        if target == nums[l] {
            return l
        }
        if target == nums[r] {
            return r
        }
        // 右区间单调
        if nums[mid] < nums[r] {
            // 如果在单调区间，收缩左右边界
            // 不在单调区间，收缩右边界
            if target < nums[r] && target > nums[mid] {
                l = mid + 1
                r--
            } else {
                r = mid - 1
            }
        } else {
            // 右区间不单调，左区间就一定单调
            // 如果在单调区间，收缩左右边界
            // 不在单调区间，收缩左边界
            if target < nums[mid] && target > nums[l] {
                l++
                r = mid - 1
            } else {
                l = mid + 1
            }
        }
        
    }
    return -1
}
```
#### [寻找峰值]

相比旋转数组，可以分成两个单调的部分。寻找峰值这个刚开始看真的摸不到头脑。

峰值这个概念本身就是二段性的体现.对于峰值来说，峰值左边的元素一定小于他，右边元素一定大于他。

而题目的条件又说左右均为`-∞`。

这样取 mid，判断 mid 和 mid-1，mid+1的关系，就可以通过二分将极值的查找时间降低到 O(logN)。

需要注意一点，退出条件，mid 选择，和边界收缩的关系一定要匹配，要不又是死循环。

mid 取值偏左，所以在判断和和收缩边界时候，要保证 l 移动时候加一，r 移动时候取 mid。


``` Go
func findPeakElement(nums []int) int {
    l := 0
    r := len(nums) - 1

    for l < r {
        mid := l + (r-l)/2
        if nums[mid] > nums[mid+1] {
            r = mid
        } else {
            l = mid + 1
        }
    }
    return l
}
```

#### [有重复旋转数组找最值]

本以为峰值的情况就是最难的了，竟然还有……虾皮面试题。

旋转数组有重复和没有重复的区别在于，如果旋转点在重复的部分，那就破坏了旋转数组本身的二段性，即一部分比旋转点小，一部分比旋转点大，所以没办法用二分查找。

从直观感受来，下边两种情况，nums[mid] = nums[r]，但是没办法判断最小值在左边还是右边。

[2,2,2,2,3,1,2]

[2,3,1,2,2,2,2]

那怎么办呢，需要在 nums [mid] = nums[r] 的时候，保守的收缩右边界，尝试剔除重复数字，当没有重复的时候，就可以恢复二段性了。

也不用担心错过什么，因为数组中还有 nums[mid]。

``` Go
func findMin(nums []int) int {
    l := 0
    r := len(nums) - 1
    for l < r {
        mid := l + (r-l)/2

        if nums[mid] == nums[r] {
            r--
        } else if nums[mid] < nums[r] {
            r = mid
        } else {
            l = mid + 1
        }
    }
    return nums[l]
}
```

### 4. 总结

对于二分查找，需要做到以下几点

1. 基础模板需要背下来
2. 要理解模板中 循环退出条件，mid 位置（偏左/偏右），和边界收缩操作 之间的关系
3. 要理解二分查找的应用条件是二段性不是单调性，方便碰到变形问题可以想到这个方法


[二分查找、二分边界查找算法的模板代码总结]: https://segmentfault.com/a/1190000016825704?utm_source=tag-newest#item-6

[无重复旋转数组找最值]:https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/description/

[无重复旋转数组找目标值]:https://leetcode.cn/problems/search-in-rotated-sorted-array/

[寻找峰值]:https://leetcode.cn/problems/find-peak-element/

[有重复旋转数组找最值]:https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/

