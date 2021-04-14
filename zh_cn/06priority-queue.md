# 优先队列
通过堆实现高效的优先队列。优先队列分为两种形式：最大优先队列和最小优先队列。这里我们介绍使用最大堆实现最大优先队列

优先队列是一种用来维护由一组元素构成的集合S的数据结构，其中的每个元素都有一个相关的值，称为关键字。一个最大优先队列支持一下操作

- INSERT(S, x)：把元素x插入集合S中，等价于S = S ∪ {x}
  
- MAXIMUM(S)：返回S中具有最大关键字的元素

- EXTRACT-MAX(S)：去掉并返回S中具有最大关键字的元素

- INCREASE-KEY(S, x, k)：将元素x的关键字值增加到k，k的值不小于x的原关键字值

在实际应用中，可以使用最大优先队列作为共享计算机系统的作业调度。可以方便的添加新作业到队列，或从队列中获取优先级最高的作业执行。

最小优先队列支持的操作包括INSERT、MINIMUM、EXTRACT-MIN和DECREASE-KEY。最小优先队列可以用于基于事件驱动的模拟器。队列中保存要模拟的事件，
每个事件都有一个发生时间作为其关键字。事件必须按照发生的时间顺序进行模拟，因为某一事件的模拟结果可能会触发其他事件的模拟。通过EXTRACT-MIN
来选择下一个要模拟的事件。通过INSERT将新事件插入最小优先队列中

## 最大优先队列的实现

### HEAP-MAXIMUM
HEAP-MAXIMUM可以在θ(1)时间内实现MAXIMUM操作
```
HEAP-MAXIMUM(A)
1   return A[1]
```

### HEAP-EXTRACT-MAX
HEAP-EXTRACT-MAX实现EXTRACT-MAX操作,时间复杂度为O(lgn)
```
HEAP-EXTRACT-MAX(A)
1   if A.heap-size < 1
2       error "heap underflow"
3   max = A[1]
4   A[1] = A[A.heap-size]
5   A.heap-size = A.heap-size - 1
6   MAX-HEAPIFY(A, 1)
7   return max
```

### HEAP-INCREASE-KEY
HEAP-INCREASE-KEY实现INCREASE-KEY操作，时间复杂度为O(lgn)
```
HEAP-INCREASE-KEY(A, i, key)
1   if key < A[i]
2       error "new key is small than current key"
3   A[i] = key
4   while i > 1 and A[PARENT(i)] < A[i]
5       exchange A[i] with A[PARENT(i)]
6       i = PARENT(i)
```

### MAX-HEAP-INSERT
MAX-HEAP-INSERT实现INSERT操作，运行时间为O(lgn)
```
MAX-HEAP-INSERT(A, key)
1   A.heap-size = A.heap-size + 1
2   A[A.heap-size] = -∞
3   HEAP-INCREASE-KEY(A, A.heap-size, key)
```

## Java中的最大优先队列实现
```
// 返回最大元素
@Override
public Object peek() {
    return this.size == 0 ? null : this.queue[0];
}

// 去掉最大元素
@Override
public Object poll() {
    if (this.size == 0) {
        return null;
    }
    int result = this.queue[0];
    this.queue[0] = this.queue[--size];
    this.queue[size] = null;
    heapSort.maxHeapify(this.queue, size, 0);
    return result;
}

// 将指定位置的值设置为value
public void increase(int i, Integer value) {
    if (value < this.queue[i]) {
        throw new RuntimeException("new value is small than current value");
    }
    this.queue[i] = value;
    while(i > 0 && this.queue[(i - 1) / 2] < this.queue[i]){
        this.queue[(i - 1) / 2] = this.queue[(i - 1) / 2] ^ this.queue[i];
        this.queue[i] = this.queue[(i - 1) / 2] ^ this.queue[i];
        this.queue[(i - 1) / 2] = this.queue[(i - 1) / 2] ^ this.queue[i];
        i = (i - 1) / 2;
    }
}

// 添加元素
@Override
public boolean offer(Object value) {
    if(this.size >= this.queue.length) {
        queue = Arrays.copyOf(queue, queue.length + ((queue.length < 64) ?
                (queue.length + 2) :
                (queue.length >>> 1)));
    }
    this.queue[++size] = Integer.MIN_VALUE;
    increase(size, (Integer) value);
    return true;
}
```

