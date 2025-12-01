---
title: 归并排序Mergesort和快排Quicksort
date: 2025-11-06 11:58:00 +0800
description:
categories:
  - 数据结构
  - 课程笔记
tags:
  - 归并排序Mergesort
  - 快排Quicksort
math: true
---

和堆排序类似，合并排序和快排也都是 $\Theta(n ln(n))$ 的排序算法，但是这两个排序算法会稍微更快点，因为分别需要 $\Theta(n)$ 和 $\Theta(ln(n))$ 的空间。

# 归并排序 Mergesort

Mergesort 是一种递归的“分治”(divide-and-conquer) 算法。

假设我们现在有两个已经排好序的 sublist，那么现在需要做的就是将这两个 sublist 合并到一个；这个操作就是Merging，具体的实现也很简单：

```cpp
int i1 = 0, i2 = 0, k = 0;
// i1是array1的索引，i2同理，k是arrayout的索引

while ( i1 < n1 && i2 < n2 ) {
   if ( array1[i1] < array2[i2] ) {
      arrayout[k] = array1[i1];
      ++i1;
   } else {
      arrayout[k] = array2[i2];
      ++i2;
   }
   ++k;
}

// 如果有一个sublist已经结束，那么将另一个的尾部全部复制到arrayout
for ( ; i1 < n1; ++i1, ++k ) {
   arrayout[k] = array1[i1];
}

for ( ; i2 < n2; ++i2, ++k ) {
   arrayout[k] = array2[i2];
}
```

那么现在我们需要考虑如何将一个列表分为多个子列表sublists，并且每个sublist的长度需要是多少？因为不可能一直分到1，这样的话就没有意义了。

关于子列表的条目N，我们可以自己定义（例如N=8，16，32），在划分子列表的过程中就是Mergesort递归性的体现。

当我们划分好子列表后，就要对子列表进行排序，不要忘了，我们进行merging操作的前提一定是子列表都是sortedlist。

需要注意mergesort的first和last边界条件，first是前一个子列表的开头，last是下一个子列表的开头（前一个子列表末尾的后面一个）。看图理解   *P168-P183*  

```cpp
template <typename Type>
void merge_sort( Type *array, int first, int last ) {
	// 如果划分好了子列表，就对其进行排序
   if ( last - first <= N ) { 
      insertion_sort( array, first, last );
   } else { // 没有划分好就继续递归
      int midpoint = (first + last)/2;

      merge_sort( array, first, midpoint );
      merge_sort( array, midpoint, last );
      
      // 递归结束后将两个子列表merge
      merge( array, first, midpoint, last );
   }
}
```

时间复杂度分析：
排序一个大小为 n 的数组所需的时间 $(T(n))$ 等于排序两个 $n/2$ 的子数组的时间，加上合并它们的时间 ($\Theta(n)$)；然后可以得到递归表达式：$T(n) = 2T(n/2) + \Theta(n)$ ；

需要注意，归并排序并不会改变相同元素的相对位置，这是排序算法很重要的**稳定性**。

--- 
# 快排 Quicksort

快排也是基于“分治”的思想，但是
- 我们需要找到一个基准点（Pivot）
- 然后将剩余的元素根据他们和pivot的大小关系来分子列表
- 最后递归地对这两个子列表（分区）应用 Quicksort 。

那么重要的问题就是**如何找到这个基准点pivot？** 

对于随机选取的情况来说，最好的情况是：这个pivot是**中位数**；
那么我们就可以像Mergesort一样进行递归划分，然后进行排序；

**但是如果我们运气没那么好呢？** 

假设我们不幸选取到了这个列表最小的数作为pivot，那么就意味着我们还有一个 $n-1$ 个元素的子列表，根据上面得到的递推表达式，$T(n) = T(n - 1) + \Theta(n) = \Theta(n^2)$ ；
时间复杂度从 $n ln(n)$ 上升到了 $n^2$ ，这不是我们想要的。

因此我们找到一个折中的办法来尽可能规避上面说到的这种极端情况的出现。

**Median of Three，三数取中**

也可以选取其他的取值方法，看个人。

- 我们选取这个未排序列表中的第一个，最中间的，还有最后的一个元素，然后挑选**这三个数的中位数**来作为pivot；
- 然后将这个列表中小于pivot的元素放在pivot的左边（**从后面开始**），大于pivot的元素放在这个列表的右边（**从前面开始**），整理好这个数组，这样才能进行接下来的分治递归；

具体实现和过程的模拟   *P240 整理数组*     *P262 开始快排*  

对于Quicksort来说，在最坏的情况下，时间和空间复杂度都变得更差；
并且快排不是稳定的，可能会改变相同元素初始的相对位置。原因是开始时要选取pivot并进行重排（整理数组），这时可能会改变相同元素的相对位置。

> 比如选取了57作为pivot，这时候一个44在57左，另一个在右，那么就可能将*右边的44*移动到*左边44*的前面，从而改变他们的相对位置顺序。

快排平均情况下的空间复杂度 $O(ln(n))$ 主要是函数调用栈造成的，递归树的高度为 $ln(n)$ 。

## 三个实现 $\Theta(nln(n))$ 时间的算法对比总结：

|                | Average Run Time   | Worst-case Run Time | Average Memory   | Worst-case Memory |
| :------------- | :----------------- | :------------------ | :--------------- | :---------------- |
| **Heap Sort**  | $\Theta(n \ln(n))$ | $\Theta(n \ln(n))$  | $\Theta(1)$      | $\Theta(1)$       |
| **Merge Sort** | $\Theta(n \ln(n))$ | $\Theta(n \ln(n))$  | $\Theta(n)$      | $\Theta(n)$       |
| **Quicksort**  | $\Theta(n \ln(n))$ | $\Theta(n^2)$       | $\Theta(\ln(n))$ | $\Theta(n)$       |

只有Heapsort是就地（in-place）的，Mergesort和Quicksort都需要额外的空间复杂度；