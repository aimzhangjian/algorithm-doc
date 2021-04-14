# 快速排序
快速排序最坏情况时间复杂度为θ(n^2)，虽然最坏情况时间复杂度很差，但快速排序通常是实际排序应用中最好的选择，其平均性能很好，期望时间复杂度是
θ(nlgn)

## 快速排序算法描述
与归并排序类似，快速排序使用分治思想进行排序

### 分解
数组A[p..r]被划分为两个子数组A[p..q-1]和A[q+1..r]，使得A[p..q-1]中的每一个元素都小于等于A[q]，而A[q]也小于等于A[q+1..r]中的每个元素，
计算下标q也是划分过程的一部分

### 解决
通过递归调用快速排序，对子数组A[p..q-1]和A[q+1..r]进行排序

### 合并
因为子数组是原址排序，不需要合并，数组A[p..r]已经有序

### 快速排序伪代码
```
QUICKSORT(A, p, r)
1   if p < r
2       q = PARTITION(A, p, r)
3       QUICKSORT(A, p, q-1)
4       QUICKSORT(A, q+1, r)
```

### 数组划分伪代码
PARTITION总是选择一个x = A[r]作为主元，并围绕它来划分子数组A[p..r],随着程序执行，数组被划分为4个区域。在第3～6行for循环每一轮迭代的开始，
每个区域满足一定的性质

- 若p ≤ k ≤ i,则A[k] ≤ x

- 若i + 1 ≤ k ≤ j - 1,则A[k] > x

- 若k = r, 则A[k] = x

```
PARTITION(A, p, r)
1   x = A[r]
2   i = p - 1
3   for j = p to r - 1
4       if A[j] ≤ x
5           i = i +  1
6           exchange A[i] with A[j]
7   exhange A[i + 1] with A[r]
8   return i + 1
```

## 快速排序性能
快速排序运行时间依赖于划分是否平衡，平衡与否又取决于所选取的划分元素。如果划分是平衡的，快速排序算法性能与归并排序一样。如果划分不平衡，则
与插入排序性能接近

### 最坏情况划分
当划分产生的两个子数组分别包含n-1个元素和0个元素，快速排序最坏情况产生，如果每次递归调用都出现这种最坏情况，则最坏情况的时间复杂度为θ(n^2)

### 最好情况划分
当划分产生的两个子数组规模都不大于n/2,其中一个子问题的规模为⌊n/2⌋,另一个子问题规模为⌈n/2⌉ - 1,如果每次迭代都出现这种最好情况划分。则最好
情况时间复杂度为θ(nlgn)

### 平衡的划分
快速排序的平均运行时间更接近于最好情况，而不是最坏情况。原因在于划分的平衡性与描述运行时间递归式的关系

假设划分算法总是产生9：1的划分，快速排序时间复杂度递归式为：

            T(n) = T(9n/10) + T(n/10) + cn

整棵递归数深度为$\log_{10/9} {n}$，在深度为$\log_{10} {n}$之前每一层代价为cn，之后每一层代价至多为cn，因此快速排序总代价为O(nlgn)，
事实上任何常数比例的划分，都会产生深度为θ(lgn)的递归树，每一层时间代价都是O(n),算法运行时间总是O(nlgn)

## 快速排序随机化版本
为了获得更好的平均性能，在主元选取时，我们使用随机抽样。从子数组A[p..r]中随机选择一个元素作为主元，并将A[r]与随机选出的主元做交换
```
RANDOMIZED-PARTITION(A, p, r)
1   i = RANDOM(p, r)
2   exchange A[r] with A[i]
3   return PARTITION(A, p, r)
```

## Java实现
```
package com.aim.algorithm.sort.impl;

import com.aim.algorithm.sort.Sort;

public class QuickSort implements Sort {

    @Override
    public int[] sort(int[] input) {
        quickSort(input, 0, input.length - 1);
        return input;
    }

    private void quickSort(int[] input, int start, int end){
        if(start < end){
            int q = randomPartition(input, start, end);
            quickSort(input, start, q - 1);
            quickSort(input, q + 1, end);
        }
    }

    private int randomPartition(int[] input, int start, int end){
        int i = (int) (Math.random() * (end - start) + start);

        int changEnd = input[end];
        input[end] = input[i];
        input[i] = changEnd;
        return partition(input, start, end);
    }

    private int partition(int[] input, int start, int end) {
        int x = input[end];
        int i = start - 1;
        for(int j = start; j < end; j++){
            if(input[j] < x){
                ++i;
                int changValueI = input[i];
                input[i] = input[j];
                input[j] = changValueI;
            }
        }
        int changEnd = input[end];
        input[end] = input[i + 1];
        input[i + 1] = changEnd;
        return i + 1;
    }
}

// 测试方法
public static void quickSort() throws IOException {
    Scanner scan = new Scanner(System.in);
    System.out.println("请输入要排序数组");
    String arrayStr = scan.nextLine();
    while (Strings.isBlank(arrayStr)) {
        System.out.println("请输入要排序数组");
        arrayStr = scan.nextLine();
    }
    String[] arrays = arrayStr.split(" ");
    int[] input =  Arrays.stream(arrays).mapToInt(array -> Integer.valueOf(array)).toArray();

    int[] result = quickSort.sort(input);
    Arrays.stream(result).forEach(rsl -> System.out.println(rsl));
}
```



