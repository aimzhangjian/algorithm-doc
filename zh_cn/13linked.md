# 链表
链表中各对象按线性顺序排列。数组的线性顺序是由数组下标决定，链表的顺序是由各个对象中的指针决定。链表为动态集合提供一种简单而灵活的表示方法

双向链表的每个元素都是一个对象，每个对象有一个关键字key和两个指针：next和prev。对象中还可以包含其他辅助数据。设x为链表的一个元素，x.next
指向它在链表中的后续元素，x.prev指向它的前驱元素。如果x.prev = NIL，则元素x没有前驱，因此是链表第一个元素，即链表头。如果x.next = NIL，
则元素x没有后续，因此是链表的最后一个元素，即链表的尾。属性L.head指向链表第一个元素。如果L.head=NIL，则链表为空

链表可以有多种形式，可以是单链接的或双链接的，可以是已排序或未排序的，可以是循环的或非循环的。如果链表是单链接的，则省略每个元素的prev指针，
如果链表是已排序的，则链表的线性顺序与链表中元素的关键字的线性顺序一致，最小元素就是表头元素，最大元素则为表尾元素。在循环链接表中，表头的
prev指向表尾元素，表尾的next指向表头元素

## 链表的搜索
LIST-SEARCH(L, k)采用简单的线性搜索方法，用于查找链表L中第一个关键字为k的的元素，并返回该元素指针。如果链表中没有关键字k的对象，则该过程
返回NIL,最坏情况下运行时间为θ(n)
```
LIST-SEARCH(L, k)
1   x = L.head
2   while x != NIL and x.key != k
3       x = x.next
4   return x
```

## 链表的插入
LIST-INSERT将已设置好关键字key的元素x，插入到链表的前端。运行时间为O(n)
```
LIST-INSERT(L,x)
1   x.next = L.head
2   if L.head != NIL
3       L.head.prev = x
4   L.head = x
5   x.prev = NIL
```

## 链表的删除
LIST-DELETE从链表中删除指定元素，时间复杂度为O(n)
```
LIST-DELETE(L, x)
1   if x.prev != NIL
2       x.prev.next = x.next
3   else L.head = x.next
4   if x.next != NIL
5       x.next.prev = x.prev
```

## 哨兵
哨兵是一个哑对象，其作用是简化边界条件处理。假设在链表L中设置一个对象L.nil，该对象代表NIL，但也具有和其他对象相同的各个属性。对链表中每一处
对NIL的引用，都改为对哨兵L.nil的引用，这样便将一个常规的双向链表转变为一个有哨兵的双向循环链表，哨兵位于头部和尾部之间。属性L.nil.next
指向表头，L.nil.prev指向表尾，表尾的next属性和表头的prev属性同时指向L.nil,因为L.nil.next指向表头，我们可以去掉属性L.head，并把对它的引用
改为对L.nil.next的应用。一个空的链表只由一个哨兵构成，L.nil.next和L.nil.prev同时指向L.nil

哨兵基本不能降低数据结构相关操作的渐近时间界，但可以降低常数因子，在循环语句中使用哨兵在于可以是代码结构更简洁，而非提高速度。但哨兵应该被
慎用，对于较短的链表而言哨兵所占的空间无疑是浪费

### 带哨兵链表的搜索
```
LIST-SEARCH(L,k)
1   x = L.nil.next
2   while x != L.nil and x.key != k
3       x = x.next
4   return x
```

### 带哨兵链表的插入
插入到链表前端
```
LIST-INSERT(L, x)
1   x.next = L.nil.next
2   L.nil.next.prev = x
3   L.nil.next = x
4   x.prev = L.nil
```

### 带哨兵链表的删除
```
LIST-DELETE(L, x)
1   x.prev.next = x.next
2   x.next.prev = x.prev
```

## JDK中链表的实现
JDK中提供了丰富的链表逻辑实现，我们通过LinkedList来看一下JDK中对链表的具体操作

### 属性介绍
LinkedList包含三个属性

- size：链表元素个数

- first：链表头，数据类型为Node<E>

- last：链表尾，数据类型为Node<E>

```
transient int size = 0;

/**
 * Pointer to first node.
 * Invariant: (first == null && last == null) ||
 *            (first.prev == null && first.item != null)
 */
transient Node<E> first;

/**
 * Pointer to last node.
 * Invariant: (first == null && last == null) ||
 *            (last.next == null && last.item != null)
 */
transient Node<E> last;
```

LinkedList中包含静态类Node<E>的定义，Node也包含三个元素

- item：数据对象

- next：下一个节点

- prev：上一个节点

```
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

### 初始化
LinkedList提供了两个构造函数，LinkedList()和LinkedList(Collection<? extends E> c)，前者构造出一个空的链表，后者可以使用用户设置的
集合元素构造一个链表
```
public LinkedList() {
}

// 使用指定集合初始化构造链表
public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

### 查询指定元素
LinkedList提供了两个方法搜索链表中的元素

#### 查询指定元素第一次出现的索引
indexOf(Object o)方法提供了查询参数o在链表中第一次出现的索引
```
public int indexOf(Object o) {
    int index = 0;
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null)
                return index;
            index++;
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item))
                return index;
            index++;
        }
    }
    return -1;
}
```

#### 查询指定元素最后一次出现的索引
lastIndexOf(Object o)方法提供查询参数o在链表中最后一次出现的索引
```
public int lastIndexOf(Object o) {
    int index = size;
    if (o == null) {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (x.item == null)
                return index;
        }
    } else {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (o.equals(x.item))
                return index;
        }
    }
    return -1;
}
```

### 插入元素
LinkedList提供了多种方式插入元素，包括插入到头部、插入到尾部、及指定插入位置、批量插入等，我们这里介绍几种插入方式

#### 插入元素到链表末尾
addLast(E e)方法将元素插入到链表末尾，但插入元素到链表末尾的方法有很多。addLast方法也是调用linkLast方法实现往链表尾部插入元素的，其实
很多暴露给用户插入元素到链表末尾的方法都是调用的linkLast方法。linkLast方法插入元素的过程与上述算法思想类似
```
// 插入元素到链表末尾
public boolean add(E e) {
    linkLast(e);
    return true;
}


void linkLast(E e) {
    final Node<E> l = last;
    // 构建节点，第一个参数是新节点的上一个节点，在构造方法中会关联上新节点。第二个参数为下一个节点，置空
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    // 如果最后一个节点为空，则表示此链表为空
    if (l == null)
        // 设置链表头
        first = newNode;
    else
        // 设置最后一个节点的下一个节点为加入的新节点
        l.next = newNode;
    size++;
    modCount++;
}
```

#### 插入元素到链表头
addFirst(E e)提供插入元素到链表头的功能，实现逻辑类似与插入元素到链表尾部，这里不再赘述
```
public void addFirst(E e) {
    linkFirst(e);
}

private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

### 删除元素
LinkedList提供了多种删除元素的方法，既可以从头删除，也可从尾删除，及删除指定元素等

#### 从头部删除元素
removeFirst()提供从头部删除元素
```
// 从头删除元素，并返回删除元素
public E removeFirst() {
    final Node<E> f = first;
    // 空链表抛出异常
    if (f == null)
        throw new NoSuchElementException();
    return unlinkFirst(f);
}


private E unlinkFirst(Node<E> f) {
    // assert f == first && f != null;
    final E element = f.item;
    final Node<E> next = f.next;
    f.item = null;
    f.next = null; // help GC
    first = next;
    if (next == null)
        last = null;
    else
        next.prev = null;
    size--;
    modCount++;
    return element;
}
```

#### 从尾删除元素
removeLast()提供从尾部删除元素，其实现与从头部删除元素类似
```
public E removeLast() {
    final Node<E> l = last;
    if (l == null)
        throw new NoSuchElementException();
    return unlinkLast(l);
}

private E unlinkLast(Node<E> l) {
    // assert l == last && l != null;
    final E element = l.item;
    final Node<E> prev = l.prev;
    l.item = null;
    l.prev = null; // help GC
    last = prev;
    if (prev == null)
        first = null;
    else
        prev.next = null;
    size--;
    modCount++;
    return element;
}
```

## 有根树的表示
我们可以使用链表的结构表示有根树

### 二叉树
二叉树中每个节点包含属性p、left和right存放指向父节点、左孩子、右孩子的指针。如果x.p=NIL，则x是根节点，若x.left=NIL，则x没有左孩子，右
孩子同理。属性T.root指向整棵树T的根节点。若T.root=NIL，则为空树

### 无限分支有根树
二叉树表示方法可以推广到每个节点孩子树至多为常数k的任意类型的树，只需将left、right属性用child1,child2,...,childk代替，但这种方式可能
会造成空间的浪费，因为有的节点可能只有一个孩子节点。并且无法解决无限分支的树的表示

通过左孩子右兄弟表示法可以表示无限分支有根树，并且只需O(n)的存储空间。每个节点包含一个父节点指针p，T.root指向树T的根节点，每个节点还包含
一下两个指针

- x.left-child指向节点x最左侧孩子节点

- x.right-sibling指向x右侧相邻的兄弟节点

如果节点没有孩子节点，则x.left-child=NIL；如果节点x是其父节点的最右孩子，则x.right-sibling=NIL

### 树的其他表示方法
之前我们在二叉堆算法时使用过数组来表示一颗树