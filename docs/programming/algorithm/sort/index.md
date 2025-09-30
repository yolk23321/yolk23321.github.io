# 排序

## 1.种类

- [冒泡排序（Bubble Sort）](./bubble)
- [选择排序（Selection Sort）](./selection)
- [插入排序（Insertion Sort）](./insertion)
- [希尔排序（Shell Sort）](./shell)
- [归并排序（Merge Sort）](./merge)
- [快速排序（Quick Sort）](./quick)
- [堆排序（Heap Sort）](./heap)
- [计数排序（Counting Sort）](./counting)
- [桶排序（Bucket Sort）](./bucket)
- [基数排序（Radix Sort）](./radix)

## 2.分析

| 名称 | 最好 | 最坏 | 平均 | 额外空间复杂度 | In-place | 稳定性 |
|------|------|------|------|----------------|----------|--------|
| 冒泡排序（Bubble Sort） | <span style="color:#00BFFF">O(n)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#00CED1">O(1)</span> | ✅ | ✅ |
| 选择排序（Selection Sort） | <span style="color:#FF1493">O(n²)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#00CED1">O(1)</span> | ✅ | ❌ |
| 插入排序（Insertion Sort） | <span style="color:#00BFFF">O(n)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#00CED1">O(1)</span> | ✅ | ✅ |
| 归并排序（Merge Sort） | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#00BFFF">O(n)</span> | ❌ | ✅ |
| 快速排序（Quick Sort） | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#FF1493">O(n²)</span> | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#FFD700">O(logn)</span> | ✅ | ❌ |
| 希尔排序（Shell Sort） | <span style="color:#00BFFF">O(n)</span> | <span style="color:#FF1493">O(n<sup>4/3</sup>) ~ O(n²)</span> | <span style="color:#A9A9A9">取决于步长序列</span> | <span style="color:#00CED1">O(1)</span> | ✅ | ❌ |
| 堆排序（Heap Sort） | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#9370DB">O(nlogn)</span> | <span style="color:#00CED1">O(1)</span> | ✅ | ❌ |
| 基数排序（Radix Sort） | <span style="color:#00BFFF">O(d * (n+k))</span> | <span style="color:#00BFFF">O(d * (n+k))</span> | <span style="color:#00BFFF">O(d * (n+k))</span> | <span style="color:#00BFFF">O(n+k)</span> | ❌ | ✅ |
| 桶排序（Bucket Sort） | <span style="color:#00BFFF">O(n+k)</span> | <span style="color:#00BFFF">O(n+k)</span> | <span style="color:#00BFFF">O(n+m)</span> | <span style="color:#00BFFF">O(n+m)</span> | ❌ | ✅ |
| 计数排序（Counting Sort） | <span style="color:#00BFFF">O(n+k)</span> | <span style="color:#00BFFF">O(n+k)</span> | <span style="color:#00BFFF">O(n+k)</span> | <span style="color:#00BFFF">O(n+k)</span> | ❌ | ✅ |

注意

- 以上表格是基于数组类型的数据结构进行排序的一般性总结，并且每种算法实现方式也有所不同，所以此分析仅供参考
- 冒泡、选择、插入、归并、快速、希尔、堆排序是<font color=#FF000><b>比较排序</b></font>（Comparison Sorting）


