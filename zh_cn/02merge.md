归并排序
================================================

分解：分解待排序的n个元素为各具n/2个元素的子序列
解决：使用归并排序递归地排序两个子序列
合并：合并两个已排序的子序列产生已排序的答案

介绍
--------------------------------------

复杂度：T(n) = cnlgn + cn

关键点：归并排序算法的关键是"合并"步骤中两个已排序序列的合并。我们调用MERGE(A, p, q, r)来完成合并，其中A是
一个数组，p、q和r是数组下标，满足p<=q<r。该过程假设子数组A[p..q]和[q + 1..r]都已排序。合并这两个子数组形成
单一的已排序好子数组并代替当前的子数组A[p..r]

合并排好序的数组伪代码::

    MERGE(A, p, q, r)
    1  n1 = q - p + 1
    2  n2 = r - q
    3  let L[1..n + 1] and R[1..n2 + 1] be new arras
    4  for i = 1 to n1
    5     L[i] = A[p + i -1]
    6  for j = 1 to n2
    7     R[j] = A[q + j]
    //设置哨兵
    8  L[n1 + 1] = ∞
    9  R[n2 + 1] = ∞
    10 i = 1
    11 j = 1
    12 for k = p to r
    13    if L[i] <= R[j]
    14       A[k] = L[i]
    15       i = i + 1
    16    else A[k] = R[j]
    17         j = j + 1


推导过程:

    - 初始化：循环不变式的第一次迭代前，k = p,子数组A[p..k-1]为空，空的子数组包含L、R的的r-p = 0个最小元素。
因为i = j = 1,所以L[i]、R[j]为各自子数组的第一个最小的元素

    - 保持：假设L[i] <= R[j],则L[i]是未被复制到A中的最小元素。因为A[p, k-1]包含 k - q个元素，代码第14行将
L[i]复制回A[k]后，A数组将包含k - q + 1个元素。增加i、k的值后为下次迭代重新建立了循环不变式。反之，若L[i] > R[i]
则16～17行执行适当的操作维持循环不变式

    - 终止：终止时k = r + 1，根据循环不变式，子数组A[p..k-1]就是A[p..r]且按从小到大的顺序包含L[1..n1 + 1]和
R[1..n2 + 1]中的k-p = r - p + 1个最小元素。数组L和R一起包含n1 + n2 + 2 = r - p + 3个元素。初两个最大的哨兵
元素外其余元素都被复制回数组A

归并排序伪代码::

    MERGE-SORT(A, p, r)
    1   if p < r
    2       q = (p + r)/2  //向下取整
    3       MERGE-SORT(A, p, q)
    4       MERGE-SORT(A, q + 1, r)
    5       MERGE(A, p, q, r)

归并排序的过程就是不断递归的将数组进行二分拆分后合并排序的过程


Java实现
--------------------------------------
实现代码::

    package com.aim.algorithm.sort.impl;

    import com.aim.algorithm.sort.Sort;
    import org.springframework.stereotype.Service;

    @Service
    public class MergeSort implements Sort {
        @Override
        public int[] sort(int[] input) {
            mergeSort(input, 0, input.length-1);
            return input;
        }

        public void mergeSort(int[] input, int start, int end){
            if(start < end){
                int mid = (int) Math.floor(((double) start + end)/2);
                mergeSort(input, start, mid);
                mergeSort(input, mid + 1, end);
                merge(input, start, mid, end);
            }
        }

        private void merge(int[] input, int start, int mid, int end){
            int l = mid - start + 1;
            int r = end - mid;
            int[] left = new int[l + 1];
            int[] right = new int[r + 1];
            for(int i = 0; i < l; i++){
                left[i] = input[start + i];
            }
            for(int j = 0; j < r; j++){
                right[j] = input[mid + j + 1];
            }
            left[l] = Integer.MAX_VALUE;
            right[r] = Integer.MAX_VALUE;
            int i = 0;
            int j = 0;
            for(int k = start; k <= end; k++){
                if(left[i] <= right[j]){
                    input[k] = left[i];
                    i++;
                }else {
                    input[k] = right[j];
                    j++;
                }
            }
        }
    }









