# 最大子数组

输入：包含负数的数组

输出：找出和为最大的连续子数组

## 分治策略求解最大子数组
假定寻找A[low...high]的最大子数组，我们将A数组拆分成规模尽量相等的两个子数组，A[low...mid] 、A[mid+1...high].
A数组的任何子数组A[i...j]必然是以下情况之一

- 完全位于A[low...mid]中，low ≤ i ≤ j ≤ mid

- 完全位于A[mid...high]中，mid < i ≤ j ≤ high

- 跨越中点，low ≤ i ≤ j ≤ high

A数组的最大子数组必然是上述三种情况之一。求A[low...mid] 、A[mid+1...high]两个子数组的最大子数组，依然是最大子数组问题，
只是规模更小，因此剩下的工作就是寻找跨越中点的最大子数组，然后在三种情况中选取和最大值

我们可以在线性时间内求出跨越中点的最大子数组，此问题不是原问题规模更小的实例，其加入了限制，必须是跨越中点。任何跨越中点的
子数组都是由A[i...mid]和A[mid + 1...j]组成，其中low ≤ i ≤ mid且mid < j ≤ high.查找跨越中点最大子数组算法伪代码如下
```
find-max-crossing-subarray(A, low, mid, high)
1  left-sume = -∞
2  sum = 0
3  for i = mid downto low
4     sum = sum + A[i]
5     if sum > left-sum
6        left-sum = sum
7        max-left = i
8  right-sum = -∞
9  sum = 0
10 for j = mid + 1 to high
11     sum = sum + A[j]
12     if sum > right-sum
13        right-sum = sum
14        max-right = j
15 return (max-left, max-right, left-sum + right-sum)
```
第1-7行计算左部分数组最大子数组，第8-14行计算右部分数组最大子数组。返回跨越中点子数组的边界，及最大子数组的和。
find-max-crossing-subarray(A, low, mid, high)包含两个单层for循环其时间复杂度为θ(n）

求解最大子数组的分治算法伪代码如下
```
find-maximum-subarray(A, low, high)
1  if high == low
2    return (low, high, A[low])
3  else mid = └(low + high)/2┘
4    (left-low, left-high, left-sum) = find-maximum-subarray(A, low, mid)
5    (right-low, right-high, right-sum) = find-maximum-subarray(A, mid + 1, high)
6    (cross-low, cross-high, cross-sum) = find-maximum-subarray(A, low, mid, high)
7  if left-sum ≥ right-sum and left-sum ≥ cross-sum
8    return (left-low, left-high, left-sum)
9  elseif right-sum ≥ left-sum and right-sum ≥ cross-sum
10   return (right-low, right-high, right-sum)
11 else return (cross-low, cross-high, cross-sum)
```
## Java 实现算法

1. 添加返回对象
```
package com.aim.algorithm.subarray.entity;

public class MaximumSubarray {
    private Integer startIndex;
    private Integer endIndex;
    private Integer sum;

    public MaximumSubarray(){}

    public MaximumSubarray(int low, int high, int sum) {
        this.startIndex = low;
        this.endIndex = high;
        this.sum = sum;
    }

    public Integer getStartIndex() {
        return startIndex;
    }

    public void setStartIndex(Integer startIndex) {
        this.startIndex = startIndex;
    }

    public Integer getEndIndex() {
        return endIndex;
    }

    public void setEndIndex(Integer endIndex) {
        this.endIndex = endIndex;
    }

    public Integer getSum() {
        return sum;
    }

    public void setSum(Integer sum) {
        this.sum = sum;
    }

    @Override
    public String toString() {
        return "MaximumSubarray{" +
                "startIndex=" + startIndex +
                ", endIndex=" + endIndex +
                ", sum=" + sum +
                '}';
    }
}
```
2. 添加查找最大子数组接口MaximumSubarrayService
```
package com.aim.algorithm.subarray.serviece;

import com.aim.algorithm.subarray.entity.MaximumSubarray;

public interface MaximumSubarrayService {
    /**
     * 查找最大子数组
     * @param input
     * @return
     */
     MaximumSubarray findMaximumSubarray(int[] input);
}
```
3. 实现MaximumSubarrayService查找最大子数组
```
package com.aim.algorithm.subarray.serviece.impl;

import com.aim.algorithm.subarray.entity.MaximumSubarray;
import com.aim.algorithm.subarray.serviece.MaximumSubarrayService;

public class MaximumSubarrayServiceImpl implements MaximumSubarrayService {
    public MaximumSubarray findMaximumSubarray(int[] input) {
        return findMaximumSubarray(input, 0, input.length - 1);
    }

    public MaximumSubarray findMaximumSubarray(int[] input, int low, int high) {
        if (high == low) {
            return new MaximumSubarray(low, high, input[low]);
        } else {
            int mid = Math.floorDiv(low + high, 2);
            MaximumSubarray leftMaximumSubarray = findMaximumSubarray(input, low, mid);
            MaximumSubarray rightMaximumSubarray = findMaximumSubarray(input, mid + 1, high);
            MaximumSubarray crossMaximumSubarray = findMaxCrossingSubarray(input, low, mid, high);
            if (leftMaximumSubarray.getSum() > rightMaximumSubarray.getSum()
                    && leftMaximumSubarray.getSum() > crossMaximumSubarray.getSum()) {
                return leftMaximumSubarray;
            } else if (rightMaximumSubarray.getSum() > leftMaximumSubarray.getSum()
                    && rightMaximumSubarray.getSum() > crossMaximumSubarray.getSum()) {
                return rightMaximumSubarray;
            } else {
                return crossMaximumSubarray;
            }
        }
    }

    private MaximumSubarray findMaxCrossingSubarray(int[] input, int low, int mid, int high) {
        int leftSum = Integer.MIN_VALUE;
        int sum = 0;
        int maxLeft = -1;
        for (int i = mid; i >= low; i--) {
            sum += input[i];
            if (sum > leftSum) {
                leftSum = sum;
                maxLeft = i;
            }
        }
        int rightSum = Integer.MIN_VALUE;
        int maxRight = -1;
        sum = 0;
        for (int j = mid + 1; j <= high; j++) {
            sum += input[j];
            if (sum > rightSum) {
                rightSum = sum;
                maxRight = j;
            }
        }
        return new MaximumSubarray(maxLeft, maxRight, leftSum + rightSum);
    }
}
```
4. 添加测试类
```
package com.aim.algorithm.subarray;

import com.aim.algorithm.subarray.entity.MaximumSubarray;
import com.aim.algorithm.subarray.serviece.MaximumSubarrayService;
import com.aim.algorithm.subarray.serviece.impl.MaximumSubarrayServiceImpl;
import org.apache.logging.log4j.util.Strings;
import org.junit.Test;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Scanner;

public class MaximumSubarrayTest {

    private static MaximumSubarrayService maximumSubarrayService = new MaximumSubarrayServiceImpl();

    public static void main(String[] args) throws IOException {
        findMaximumSubarray();
    }

    public static void findMaximumSubarray() throws IOException {
        Scanner scan = new Scanner(System.in);
        System.out.println("请输入要排序数组");
        String arrayStr = scan.nextLine();
        while (Strings.isBlank(arrayStr)) {
            System.out.println("请输入要排序数组");
            arrayStr = scan.nextLine();
        }
        String[] arrays = arrayStr.split(" ");
        int[] input = Arrays.stream(arrays).mapToInt(array -> Integer.valueOf(array)).toArray();

        MaximumSubarray maximumSubarray =  maximumSubarrayService.findMaximumSubarray(input);
        System.out.println(maximumSubarray.toString());
    }
}
```
## 算法性能分析
简化问题，假设原问题的规模为2的幂，所有子问题的规模都是整数，用T(n)表示findMaximumSubarray求解n个元素的最大子数组的运行时间。对于n = 1
的情况，第1、2行花费常量时间T(1) = θ(1).当n > 1，第1、3行花费常量时间，第4、5行求解的子数组均为n/2个元素的子数组，每个求解时间均为T(n/2)。
我们需要求解两个子数组，总运行时间为2T(n/2)。第6行调用findMaxCrossingSubarray花费θ(n)。第7～11行花费常量时间。因此整个寻找最大子数组
算法的运行时间T(n)为

$$ T(n)  =  \left\{
\begin{aligned}
θ(1)\\
2T(n/2) + θ(n)
\end{aligned}
\right.$$

因此其时间复杂度为T(n) = θ(nlgn)



