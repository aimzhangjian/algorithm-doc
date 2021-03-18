插入排序
================================================

输入：n个数的一个序列<a1, a2, a3, ..., an>
输出：输出序列的一个排<a1', a2', a3', ..., an'>,满足a1'<= a2'<= a3'<= ... <= an'

介绍
--------------------------------------
复杂度：T(n) = an^2 + bn + c

使用场景：对于少量元素的排序，它是一个有效的算法

伪代码::

    INSERTION-SORT(A)
    1 for j = 2 to A.length
    2    key = A[j]
    3    //Insert A[j] into the sorted sequence A[1..j-1]
    4    i = j - 1
    5    while i > 0 and A[i] > key
    6        A[i + 1] = A[i]
    7        i = i - 1
    8    A[i + 1] = key

循环不变式: 在插入排序中，j指出正被插入到已排序集合中的数，包含元素A[1..j-1]的子集合构成了排好序的
集合，剩余的子集合A[j + 1..n]对应于未排序的集合。我们把A[1..j-1]的这些性质形式的表示为一个循环不
变式

循环不变式的三条性质:

 - 初始化：循环的第一次迭代之前，它为真

 - 保持：如果循环的某次迭代之前它为真，那么下次迭代之前它仍为真

 - 终止：在循环终止时，不变式为我们提供一个有用的性质，该性质有助于证明算法是正确的

循环不变式推导过程
--------------------------------------

初始化:

  第一次循环迭代之前（j = 2），A[1..j-1]集合只包含一个元素A[1]，并且该集合是排序好的。这表明
第一循环迭代之前循环不变式成立

保持:
  for循环体的第4～7行将A[j-1]、A[j-2]、A[j-3]等向右移动一个位置，直到找到A[j]的合适位置，第8行将A[j]的值插入该位置，此时
子集合已按序排列，那么对for循环的下一次迭代增加j将保持循环不变

终止:
  导致for循环终止的条件是j>A.length=n,因为每次迭代j增加1，必有j=n+1。子集合A[1..n]由原来在A[1..n]中的元素组成，但已
排好序

Java实现
--------------------------------------
实现代码::

    package com.aim.algorithm.sort.impl;

    import com.aim.algorithm.sort.Sort;
    import org.springframework.stereotype.Component;

    @Component
    public class InsertionSort implements Sort {

        @Override
        public int[] sort(int[] input) {
            int key;
            int i;
            for(int j = 1; j < input.length; j++){
                key = input[j];
                i = j - 1;
                while (i >=0 && input[i] > key){
                    input[i + 1] = input[i];
                    i--;
                }
                input[i + 1] = key;
            }
            return input;
        }
    }




