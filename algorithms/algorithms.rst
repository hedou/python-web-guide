.. _algorithms:


用python实现基本数据结构和算法
=====================================================================

1章：ADT抽象数据类型，定义数据和其操作
--------------------------------------

什么是ADT: 抽象数据类型，学过数据结构的应该都知道。

How to select datastructures for ADT

1. Dose the data structure provide for the storage requirements as specified by the domain of the ADT?
2. Does the data structure provide the data access and manipulation functionality to fully implement the ADT?
3. Effcient implemention?  based on complexity analysis.

下边代码是个简单的示例，比如实现一个简单的Bag类，先定义其具有的操作，然后我们再用类的magic
method来实现这些方法：

::

    class Bag:
        """
        constructor: 构造函数
        size
        contains
        append
        remove
        iter
        """
        def __init__(self):
            self._items = list()

        def __len__(self):
            return len(self._items)

        def __contains__(self, item):
            return item in self._items

        def add(self, item):
            self._items.append(item)

        def remove(self, item):
            assert item in self._items, 'item must in the bag'
            return self._items.remove(item)

        def __iter__(self):
            return _BagIterator(self._items)


    class _BagIterator:
        """ 注意这里实现了迭代器类 """
        def __init__(self, seq):
            self._bag_items = seq
            self._cur_item = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self._cur_item < len(self._bag_items):
                item = self._bag_items[self._cur_item]
                self._cur_item += 1
                return item
            else:
                raise StopIteration


    b = Bag()
    b.add(1)
    b.add(2)
    for i in b:     # for使用__iter__构建，用__next__迭代
        print(i)


    """
    # for 语句等价于
    i = b.__iter__()
    while True:
        try:
            item = i.__next__()
            print(item)
        except StopIteration:
            break
    """

--------------

2章：array vs list
------------------

array: 定长，操作有限，但是节省内存；貌似我的生涯中还没用过，不过python3.5中我试了确实有array类，可以用import array直接导入
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

list: 会预先分配内存，操作丰富，但是耗费内存。我用sys.getsizeof做了实验。我个人理解很类似C++ STL里的vector，是使用最频繁的数据结构。
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  list.append:
   如果之前没有分配够内存，会重新开辟新区域，然后复制之前的数据，复杂度退化
-  list.insert: 会移动被插入区域后所有元素,O(n)
-  list.pop:
   pop不同位置需要的复杂度不同pop(0)是O(1)复杂度,pop()首位O(n)复杂度
-  list[]: slice操作copy数据（预留空间）到另一个list

来实现一个array的ADT:

::

    import ctypes

    class Array:
        def __init__(self, size):
            assert size > 0, 'array size must be > 0'
            self._size = size
            PyArrayType = ctypes.py_object * size
            self._elements = PyArrayType()
            self.clear(None)

        def __len__(self):
            return self._size

        def __getitem__(self, index):
            assert index >= 0 and index < len(self), 'out of range'
            return self._elements[index]

        def __setitem__(self, index, value):
            assert index >= 0 and index < len(self), 'out of range'
            self._elements[index] = value

        def clear(self, value):
            """ 设置每个元素为value """
            for i in range(len(self)):
                self._elements[i] = value

        def __iter__(self):
            return _ArrayIterator(self._elements)


    class _ArrayIterator:
        def __init__(self, items):
            self._items = items
            self._idx = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self._idex < len(self._items):
                val = self._items[self._idx]
                self._idex += 1
                return val
            else:
                raise StopIteration

Two-Demensional Arrays
~~~~~~~~~~~~~~~~~~~~~~

::

    class Array2D:
        """ 要实现的方法
        Array2D(nrows, ncols):    constructor
        numRows()
        numCols()
        clear(value)
        getitem(i, j)
        setitem(i, j, val)
        """
        def __init__(self, numrows, numcols):
            self._the_rows = Array(numrows)     # 数组的数组
            for i in range(numrows):
                self._the_rows[i] = Array(numcols)

        @property
        def numRows(self):
            return len(self._the_rows)

        @property
        def NumCols(self):
            return len(self._the_rows[0])

        def clear(self, value):
            for row in self._the_rows:
                row.clear(value)

        def __getitem__(self, ndx_tuple):    # ndx_tuple: (x, y)
            assert len(ndx_tuple) == 2
            row, col = ndx_tuple[0], ndx_tuple[1]
            assert (row >= 0 and row < self.numRows and
                    col >= 0 and col < self.NumCols)

            the_1d_array = self._the_rows[row]
            return the_1d_array[col]

        def __setitem__(self, ndx_tuple, value):
            assert len(ndx_tuple) == 2
            row, col = ndx_tuple[0], ndx_tuple[1]
            assert (row >= 0 and row < self.numRows and
                    col >= 0 and col < self.NumCols)
            the_1d_array = self._the_rows[row]
            the_1d_array[col] = value

The Matrix ADT, m行，n列。这个最好用还是用pandas处理矩阵，自己实现比较\*疼
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    class Matrix:
        """ 最好用pandas的DataFrame
        Matrix(rows, ncols): constructor
        numCols()
        getitem(row, col)
        setitem(row, col, val)
        scaleBy(scalar): 每个元素乘scalar
        transpose(): 返回transpose转置
        add(rhsMatrix):    size must be the same
        subtract(rhsMatrix)
        multiply(rhsMatrix)
        """
        def __init__(self, numRows, numCols):
            self._theGrid = Array2D(numRows, numCols)
            self._theGrid.clear(0)

        @property
        def numRows(self):
            return self._theGrid.numRows

        @property
        def NumCols(self):
            return self._theGrid.numCols

        def __getitem__(self, ndxTuple):
            return self._theGrid[ndxTuple[0], ndxTuple[1]]

        def __setitem__(self, ndxTuple, scalar):
            self._theGrid[ndxTuple[0], ndxTuple[1]] = scalar

        def scaleBy(self, scalar):
            for r in range(self.numRows):
                for c in range(self.numCols):
                    self[r, c] *= scalar

        def __add__(self, rhsMatrix):
            assert (rhsMatrix.numRows == self.numRows and
                    rhsMatrix.numCols == self.numCols)
            newMartrix = Matrix(self.numRows, self.numCols)
            for r in range(self.numRows):
                for c in range(self.numCols):
                    newMartrix[r, c] = self[r, c] + rhsMatrix[r, c]

--------------

3章：Sets and Maps
------------------

除了list之外，最常用的应该就是python内置的set和dict了。

sets ADT
~~~~~~~~

A set is a container that stores a collection of unique values over a
given comparable domain in which the stored values have no particular
ordering.

::

    class Set:
        """ 使用list实现set ADT
        Set()
        length()
        contains(element)
        add(element)
        remove(element)
        equals(element)
        isSubsetOf(setB)
        union(setB)
        intersect(setB)
        difference(setB)
        iterator()
        """
        def __init__(self):
            self._theElements = list()

        def __len__(self):
            return len(self._theElements)

        def __contains__(self, element):
            return element in self._theElements

        def add(self, element):
            if element not in self:
                self._theElements.append(element)

        def remove(self, element):
            assert element in self, 'The element must be set'
            self._theElements.remove(element)

        def __eq__(self, setB):
            if len(self) != len(setB):
                return False
            else:
                return self.isSubsetOf(setB)

        def isSubsetOf(self, setB):
            for element in self:
                if element not in setB:
                    return False
            return True

        def union(self, setB):
            newSet = Set()
            newSet._theElements.extend(self._theElements)
            for element in setB:
                if element not in self:
                    newSet._theElements.append(element)
            return newSet

Maps or Dict: 键值对,python内部采用hash实现。
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    class Map:
        """ Map ADT list implemention
        Map()
        length()
        contains(key)
        add(key, value)
        remove(key)
        valudOf(key)
        iterator()
        """
        def __init__(self):
            self._entryList = list()

        def __len__(self):
            return len(self._entryList)

        def __contains__(self, key):
            ndx = self._findPosition(key)
            return ndx is not None

        def add(self, key, value):
            ndx = self._findPosition(key)
            if ndx is not None:
                self._entryList[ndx].value = value
                return False
            else:
                entry = _MapEntry(key, value)
                self._entryList.append(entry)
                return True

        def valueOf(self, key):
            ndx = self._findPosition(key)
            assert ndx is not None, 'Invalid map key'
            return self._entryList[ndx].value

        def remove(self, key):
            ndx = self._findPosition(key)
            assert ndx is not None, 'Invalid map key'
            self._entryList.pop(ndx)

        def __iter__(self):
            return _MapIterator(self._entryList)

        def _findPosition(self, key):
            for i in range(len(self)):
                if self._entryList[i].key == key:
                    return i
            return None


    class _MapEntry:    # or use collections.namedtuple('_MapEntry', 'key,value')
        def __init__(self, key, value):
            self.key = key
            self.value = value

The multiArray ADT, 多维数组，一般是使用一个一维数组模拟，然后通过计算下标获取元素
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    class MultiArray:
        """ row-major or column-marjor ordering, this is row-major ordering
        MultiArray(d1, d2, ...dn)
        dims():   the number of dimensions
        length(dim): the length of given array dimension
        clear(value)
        getitem(i1, i2, ... in), index(i1,i2,i3) = i1*(d2*d3) + i2*d3 + i3
        setitem(i1, i2, ... in)
        计算下标：index(i1,i2,...in) = i1*f1 + i2*f2 + ... + i(n-1)*f(n-1) + in*1
        """
        def __init__(self, *dimensions):
            # Implementation of MultiArray ADT using a 1-D # array,数组的数组的数组。。。
            assert len(dimensions) > 1, 'The array must have 2 or more dimensions'
            self._dims = dimensions
            # Compute to total number of elements in the array
            size = 1
            for d in dimensions:
                assert d > 0, 'Dimensions must be > 0'
                size *= d
            # Create the 1-D array to store the elements
            self._elements = Array(size)
            # Create a 1-D array to store the equation factors
            self._factors = Array(len(dimensions))
            self._computeFactors()

        @property
        def numDims(self):
            return len(self._dims)

        def length(self, dim):
            assert dim > 0 and dim < len(self._dims), 'Dimension component out of range'
            return self._dims[dim-1]

        def clear(self, value):
            self._elements.clear(value)

        def __getitem__(self, ndxTuple):
            assert len(ndxTuple) == self.numDims, 'Invalid # of array subscripts'
            index = self._computeIndex(ndxTuple)
            assert index is not None, 'Array subscript out of range'
            return self._elements[index]

        def __setitem__(self, ndxTuple, value):
            assert len(ndxTuple) == self.numDims, 'Invalid # of array subscripts'
            index = self._computeIndex(ndxTuple)
            assert index is not None, 'Array subscript out of range'
            self._elements[index] = value

        def _computeIndex(self, ndxTuple):
            # using the equation: i1*f1 + i2*f2 + ... + in*fn
            offset = 0
            for j in range(len(ndxTuple)):
                if ndxTuple[j] < 0 or ndxTuple[j] >= self._dims[j]:
                    return None
                else:
                    offset += ndexTuple[j] * self._factors[j]
            return offset

--------------

4章：Algorithm Analysis
------------------------------------

一般使用大O标记法来衡量算法的平均时间复杂度, 1 < log(n) < n < nlog(n) <
n^2 < n^3 < a^n。
了解常用数据结构操作的平均时间复杂度有利于使用更高效的数据结构，当然有时候需要在时间和空间上进行衡量，有些操作甚至还会退化，比如list的append操作，如果list空间不够，会去开辟新的空间，操作复杂度退化到O(n)，有时候还需要使用均摊分析(amortized)

--------------

5章：Searching and Sorting
------------------------------------

排序和查找是最基础和频繁的操作，python内置了in操作符和bisect二分操作模块实现查找，内置了sorted方法来实现排序操作。二分和快排也是面试中经常考到的，本章讲的是基本的排序和查找。

::

    def binary_search(sorted_seq, val):
        """ 实现标准库中的bisect.bisect_left """
        low = 0
        high = len(sorted_seq) - 1
        while low <= high:
            mid = (high + low) // 2
            if sorted_seq[mid] == val:
                return mid
            elif val < sorted_seq[mid]:
                high = mid - 1
            else:
                low = mid + 1
        return low

      def bubble_sort(seq):  # O(n^2), n(n-1)/2 = 1/2(n^2 + n)
          n = len(seq)
          for i in range(n-1):
              for j in range(n-1-i):    # 这里之所以 n-1 还需要 减去 i 是因为每一轮冒泡最大的元素都会冒泡到最后，无需再比较
                  if seq[j] > seq[j+1]:
                      seq[j], seq[j+1] = seq[j+1], seq[j]


    def select_sort(seq):
        """可以看作是冒泡的改进，每次找一个最小的元素交换，每一轮只需要交换一次"""
        n = len(seq)
        for i in range(n-1):
            min_idx = i    # assume the ith element is the smallest
            for j in range(i+1, n):
                if seq[j] < seq[min_idx]:   # find the minist element index
                    min_idx = j
            if min_idx != i:    # swap
                seq[i], seq[min_idx] = seq[min_idx], seq[i]


    def insertion_sort(seq):
        """ 每次挑选下一个元素插入已经排序的数组中,初始时已排序数组只有一个元素"""
        n = len(seq)
        for i in range(1, n):
            value = seq[i]    # save the value to be positioned
            # find the position where value fits in the ordered part of the list
            pos = i
            while pos > 0 and value < seq[pos-1]:
                # Shift the items to the right during the search
                seq[pos] = seq[pos-1]
                pos -= 1
            seq[pos] = value


    def merge_sorted_list(listA, listB):
        """ 归并两个有序数组 """
        new_list = list()
        a = b = 0
        while a < len(listA) and b < len(listB):
            if listA[a] < listB[b]:
                new_list.append(listA[a])
                a += 1
            else:
                new_list.append(listB[b])
                b += 1

        while a < len(listA):
            new_list.append(listA[a])
            a += 1

        while b < len(listB):
            new_list.append(listB[b])
            b += 1

        return new_list

6章: Linked Structure
------------------------

list是最常用的数据结构，但是list在中间增减元素的时候效率会很低，这时候linked
list会更适合，缺点就是获取元素的平均时间复杂度变成了O(n)

::

    # 单链表实现
    class ListNode:
        def __init__(self, data):
            self.data = data
            self.next = None


    def travsersal(head, callback):
        curNode = head
        while curNode is not None:
            callback(curNode.data)
            curNode = curNode.next


    def unorderdSearch(head, target):
        curNode = head
        while curNode is not None and curNode.data != target:
            curNode = curNode.next
        return curNode is not None


    # Given the head pointer, prepend an item to an unsorted linked list.
    def prepend(head, item):
        newNode = ListNode(item)
        newNode.next = head
        head = newNode


    # Given the head reference, remove a target from a linked list
    def remove(head, target):
        predNode = None
        curNode = head
        while curNode is not None and curNode.data != target:
            # 寻找目标
            predNode = curNode
            curNode = curNode.data
        if curNode is not None:
            if curNode is head:
                head = curNode.next
            else:
                predNode.next = curNode.next

--------------

7章：Stacks
-----------

栈也是计算机里用得比较多的数据结构，栈是一种后进先出的数据结构，可以理解为往一个桶里放盘子，先放进去的会被压在地下，拿盘子的时候，后放的会被先拿出来。

::

    class Stack:
        """ Stack ADT, using a python list
        Stack()
        isEmpty()
        length()
        pop(): assert not empty
        peek(): assert not empty, return top of non-empty stack without removing it
        push(item)
        """
        def __init__(self):
            self._items = list()

        def isEmpty(self):
            return len(self) == 0

        def __len__(self):
            return len(self._items)

        def peek(self):
            assert not self.isEmpty()
            return self._items[-1]

        def pop(self):
            assert not self.isEmpty()
            return self._items.pop()

        def push(self, item):
            self._items.append(item)


    class Stack:
        """ Stack ADT, use linked list
        使用list实现很简单，但是如果涉及大量push操作，list的空间不够时复杂度退化到O(n)
        而linked list可以保证最坏情况下仍是O(1)
        """
        def __init__(self):
            self._top = None    # top节点, _StackNode or None
            self._size = 0    # int

        def isEmpty(self):
            return self._top is None

        def __len__(self):
            return self._size

        def peek(self):
            assert not self.isEmpty()
            return self._top.item

        def pop(self):
            assert not self.isEmpty()
            node = self._top
            self.top = self._top.next
            self._size -= 1
            return node.item

        def _push(self, item):
            self._top = _StackNode(item, self._top)
            self._size += 1


    class _StackNode:
        def __init__(self, item, link):
            self.item = item
            self.next = link

--------------

8章：Queues
-----------

队列也是经常使用的数据结构，比如发送消息等，celery可以使用redis提供的list实现消息队列。
本章我们用list和linked list来实现队列和优先级队列。

::

    class Queue:
        """ Queue ADT, use list。list实现，简单但是push和pop效率最差是O(n)
        Queue()
        isEmpty()
        length()
        enqueue(item)
        dequeue()
        """
        def __init__(self):
            self._qList = list()

        def isEmpty(self):
            return len(self) == 0

        def __len__(self):
            return len(self._qList)

        def enquue(self, item):
            self._qList.append(item)

        def dequeue(self):
            assert not self.isEmpty()
            return self._qList.pop(0)


    from array import Array    # Array那一章实现的Array ADT
    class Queue:
        """
        circular Array ，通过头尾指针实现。list内置append和pop复杂度会退化，使用
        环数组实现可以使得入队出队操作时间复杂度为O(1)，缺点是数组长度需要固定。
        """
        def __init__(self, maxSize):
            self._count = 0
            self._front = 0
            self._back = maxSize - 1
            self._qArray = Array(maxSize)

        def isEmpty(self):
            return self._count == 0

        def isFull(self):
            return self._count == len(self._qArray)

        def __len__(self):
            return len(self._count)

        def enqueue(self, item):
            assert not self.isFull()
            maxSize = len(self._qArray)
            self._back = (self._back + 1) % maxSize     # 移动尾指针
            self._qArray[self._back] = item
            self._count += 1

        def dequeue(self):
            assert not self.isFull()
            item = self._qArray[self._front]
            maxSize = len(self._qArray)
            self._front = (self._front + 1) % maxSize
            self._count -= 1
            return item

    class _QueueNode:
        def __init__(self, item):
            self.item = item


    class Queue:
        """ Queue ADT, linked list 实现。为了改进环型数组有最大数量的限制，改用
        带有头尾节点的linked list实现。
        """
        def __init__(self):
            self._qhead = None
            self._qtail = None
            self._qsize = 0

        def isEmpty(self):
            return self._qhead is None

        def __len__(self):
            return self._count

        def enqueue(self, item):
            node = _QueueNode(item)    # 创建新的节点并用尾节点指向他
            if self.isEmpty():
                self._qhead = node
            else:
                self._qtail.next = node
            self._qtail = node
            self._qcount += 1

        def dequeue(self):
            assert not self.isEmpty(), 'Can not dequeue from an empty queue'
            node = self._qhead
            if self._qhead is self._qtail:
                self._qtail = None
            self._qhead = self._qhead.next    # 前移头节点
            self._count -= 1
            return node.item


    class UnboundedPriorityQueue:
        """ PriorityQueue ADT: 给每个item加上优先级p，高优先级先dequeue
        分为两种：
        - bounded PriorityQueue: 限制优先级在一个区间[0...p)
        - unbounded PriorityQueue: 不限制优先级

        PriorityQueue()
        BPriorityQueue(numLevels): create a bounded PriorityQueue with priority in range
            [0, numLevels-1]
        isEmpty()
        length()
        enqueue(item, priority): 如果是bounded PriorityQueue, priority必须在区间内
        dequeue(): 最高优先级的出队，同优先级的按照FIFO顺序

        - 两种实现方式：
        1.入队的时候都是到队尾，出队操作找到最高优先级的出队，出队操作O(n)
        2.始终维持队列有序，每次入队都找到该插入的位置，出队操作是O(1)
        (注意如果用list实现list.append和pop操作复杂度会因内存分配退化)
        """
        from collections import namedtuple
        _PriorityQEntry = namedtuple('_PriorityQEntry', 'item, priority')

        # 采用方式1，用内置list实现unbounded PriorityQueue
        def __init__(self):
            self._qlist = list()

        def isEmpty(self):
            return len(self) == 0

        def __len__(self):
            return len(self._qlist)

        def enqueue(self, item, priority):
            entry = UnboundedPriorityQueue._PriorityQEntry(item, priority)
            self._qlist.append(entry)

        def deque(self):
            assert not self.isEmpty(), 'can not deque from an empty queue'
            highest = self._qlist[0].priority
            for i in range(len(self)):    # 出队操作O(n)，遍历找到最高优先级
                if self._qlist[i].priority < highest:
                    highest = self._qlist[i].priority
            entry = self._qlist.pop(highest)
            return entry.item


    class BoundedPriorityQueue:
        """ BoundedPriorityQueue ADT，用linked list实现。上一个地方提到了 BoundedPriorityQueue
        但是为什么需要 BoundedPriorityQueue呢？ BoundedPriorityQueue 的优先级限制在[0, maxPriority-1]
        对于 UnboundedPriorityQueue,出队操作由于要遍历寻找优先级最高的item，所以平均
        是O(n)的操作，但是对于 BoundedPriorityQueue，用队列数组实现可以达到常量时间，
        用空间换时间。比如要弹出一个元素，直接找到第一个非空队列弹出 元素就可以了。
        (小数字代表高优先级，先出队)

        qlist
        [0] -> ["white"]
        [1]
        [2] -> ["black", "green"]
        [3] -> ["purple", "yellow"]
        """
        # Implementation of the bounded Priority Queue ADT using an array of #
        # queues in which the queues are implemented using a linked list.
        from array import Array    #  第二章定义的ADT

        def __init__(self, numLevels):
            self._qSize = 0
            self._qLevels = Array(numLevels)
            for i in range(numLevels):
                self._qLevels[i] = Queue()    # 上一节讲到用linked list实现的Queue

        def isEmpty(self):
            return len(self) == 0

        def __len__(self):
            return len(self._qSize)

        def enqueue(self, item, priority):
            assert priority >= 0 and priority < len(self._qLevels), 'invalid priority'
            self._qLevel[priority].enquue(item)    # 直接找到 priority 对应的槽入队

        def deque(self):
            assert not self.isEmpty(), 'can not deque from an empty queue'
            i = 0
            p = len(self._qLevels)
            while i < p and not self._qLevels[i].isEmpty():    # 找到第一个非空队列
                i += 1
            return self._qLevels[i].dequeue()

--------------

9章：Advanced Linked Lists
--------------------------

之前曾经介绍过单链表，一个链表节点只有data和next字段，本章介绍高级的链表。

Doubly Linked
List，双链表，每个节点多了个prev指向前一个节点。双链表可以用来编写文本编辑器的buffer。

::

    class DListNode:
        def __init__(self, data):
            self.data = data
            self.prev = None
            self.next = None


    def revTraversa(tail):
        curNode = tail
        while cruNode is not None:
            print(curNode.data)
            curNode = curNode.prev


    def search_sorted_doubly_linked_list(head, tail, probe, target):
        """ probing technique探查法，改进直接遍历，不过最坏时间复杂度仍是O(n)
        searching a sorted doubly linked list using the probing technique
        Args:
            head (DListNode obj)
            tail (DListNode obj)
            probe (DListNode or None)
            target (DListNode.data): data to search
        """
        if head is None:    # make sure list is not empty
            return False
        if probe is None:    # if probe is null, initialize it to first node
            probe = head

        # if the target comes before the probe node, we traverse backward, otherwise
        # traverse forward
        if target < probe.data:
            while probe is not None and target <= probe.data:
                if target == probe.dta:
                    return True
                else:
                    probe = probe.prev
        else:
            while probe is not None and target >= probe.data:
                if target == probe.data:
                    return True
                else:
                    probe = probe.next
        return False


    def insert_node_into_ordered_doubly_linekd_list(value):
        """ 最好画个图看，链表操作很容易绕晕，注意赋值顺序"""
        newnode = DListNode(value)

        if head is None:    # empty list
            head = newnode
            tail = head

        elif value < head.data:    # insert before head
            newnode.next = head
            head.prev = newnode
            head = newnode

        elif value > tail.data:    # insert after tail
            newnode.prev = tail
            tail.next = newnode
            tail = newnode

        else:    # insert into middle
            node = head
            while node is not None and node.data < value:
                node = node.next
            newnode.next = node
            newnode.prev = node.prev
            node.prev.next = newnode
            node.prev = newnode

循环链表

::

    def travrseCircularList(listRef):
        curNode = listRef
        done = listRef is None
        while not None:
            curNode = curNode.next
            print(curNode.data)
            done = curNode is listRef   # 回到遍历起始点


    def searchCircularList(listRef, target):
        curNode = listRef
        done = listRef is None
        while not done:
            curNode = curNode.next
            if curNode.data == target:
                return True
            else:
                done = curNode is listRef or curNode.data > target
        return False


    def add_newnode_into_ordered_circular_linked_list(listRef, value):
        """ 插入并维持顺序
        1.插入空链表；2.插入头部；3.插入尾部；4.按顺序插入中间
        """
        newnode = ListNode(value)
        if listRef is None:    # empty list
            listRef = newnode
            newnode.next = newnode

        elif value < listRef.next.data:    # insert in front
            newnode.next = listRef.next
            listRef.next = newnode

        elif value > listRef.data:    # insert in back
            newnode.next = listRef.next
            listRef.next = newnode
            listRef = newnode

        else:    # insert in the middle
            preNode = None
            curNode = listRef
            done = listRef is None
            while not done:
                preNode = curNode
                preNode = curNode.next
                done = curNode is listRef or curNode.data > value

            newnode.next = curNode
            preNode.next = newnode

利用循环双端链表我们可以实现一个经典的缓存失效算法，lru：

::

   # -*- coding: utf-8 -*-

   class Node(object):
       def __init__(self, prev=None, next=None, key=None, value=None):
           self.prev, self.next, self.key, self.value = prev, next, key, value


   class CircularDoubleLinkedList(object):
       def __init__(self):
           node = Node()
           node.prev, node.next = node, node
           self.rootnode = node

       def headnode(self):
           return self.rootnode.next

       def tailnode(self):
           return self.rootnode.prev

       def remove(self, node):
           if node is self.rootnode:
               return
           else:
               node.prev.next = node.next
               node.next.prev = node.prev

       def append(self, node):
           tailnode = self.tailnode()
           tailnode.next = node
           node.next = self.rootnode
           self.rootnode.prev = node


   class LRUCache(object):
       def __init__(self, maxsize=16):
           self.maxsize = maxsize
           self.cache = {}
           self.access = CircularDoubleLinkedList()
           self.isfull = len(self.cache) >= self.maxsize

       def __call__(self, func):
           def wrapper(n):
               cachenode = self.cache.get(n)
               if cachenode is not None:  # hit
                   self.access.remove(cachenode)
                   self.access.append(cachenode)
                   return cachenode.value
               else:  # miss
                   value = func(n)
                   if not self.isfull:
                       tailnode = self.access.tailnode()
                       newnode = Node(tailnode, self.access.rootnode, n, value)
                       self.access.append(newnode)
                       self.cache[n] = newnode

                       self.isfull = len(self.cache) >= self.maxsize
                       return value
                   else:  # full
                       lru_node = self.access.headnode()
                       del self.cache[lru_node.key]
                       self.access.remove(lru_node)
                       tailnode = self.access.tailnode()
                       newnode = Node(tailnode, self.access.rootnode, n, value)
                       self.access.append(newnode)
                       self.cache[n] = newnode
                   return value
           return wrapper


   @LRUCache()
   def fib(n):
       if n <= 2:
           return 1
       else:
           return fib(n - 1) + fib(n - 2)


   for i in range(1, 35):
       print(fib(i))

--------------

10章：Recursion
--------------------------------------

    Recursion is a process for solving problems by subdividing a larger
    problem into smaller cases of the problem itself and then solving
    the smaller, more trivial parts.

递归函数：调用自己的函数

::

    # 递归函数：调用自己的函数，看一个最简单的递归函数，倒序打印一个数
    def printRev(n):
        if n > 0:
            print(n)
            printRev(n-1)


    printRev(3)    # 从10输出到1


    # 稍微改一下，print放在最后就得到了正序打印的函数
    def printInOrder(n):
        if n > 0:
            printInOrder(n-1)
            print(n)    # 之所以最小的先打印是因为函数一直递归到n==1时候的最深栈，此时不再
                        # 递归，开始执行print语句，这时候n==1，之后每跳出一层栈，打印更大的值

    printInOrder(3)    # 正序输出

Properties of Recursion: 使用stack解决的问题都能用递归解决

- A recursive solution must contain a base case; 递归出口，代表最小子问题(n == 0退出打印)
- A recursive solution must contain a recursive case; 可以分解的子问题
- A recursive solution must make progress toward the base case. 递减n使得n像递归出口靠近

Tail Recursion: occurs when a function includes a single recursive call
as the last statement of the function. In this case, a stack is not
needed to store values to te used upon the return of the recursive call
and thus a solution can be implemented using a iterative loop instead.

::

    # Recursive Binary Search

    def recBinarySearch(target, theSeq, first, last):
        # 你可以写写单元测试来验证这个函数的正确性
        if first > last:    # 递归出口1
            return False
        else:
            mid = (first + last) // 2
            if theSeq[mid] == target:
                return True    # 递归出口2
            elif theSeq[mid] > target:
                return recBinarySearch(target, theSeq, first, mid - 1)
            else:
                return recBinarySearch(target, theSeq, mid + 1, last)

--------------

11章：Hash Tables
--------------------

基于比较的搜索（线性搜索，有序数组的二分搜索）最好的时间复杂度只能达到O(logn)，利用hash可以实现O(1)查找，python内置dict的实现方式就是hash，你会发现dict的key必须要是实现了 ``__hash__`` 和 ``__eq__`` 方法的。

Hashing: hashing is the process of mapping a search a key to a limited
range of array indeices with the goal of providing direct access to the
keys.

hash方法有个hash函数用来给key计算一个hash值，作为数组下标，放到该下标对应的槽中。当不同key根据hash函数计算得到的下标相同时，就出现了冲突。解决冲突有很多方式，比如让每个槽成为链表，每次冲突以后放到该槽链表的尾部，但是查询时间就会退化，不再是O(1)。还有一种探查方式，当key的槽冲突时候，就会根据一种计算方式去寻找下一个空的槽存放，探查方式有线性探查，二次方探查法等，cpython解释器使用的是二次方探查法。还有一个问题就是当python使用的槽数量大于预分配的2/3时候，会重新分配内存并拷贝以前的数据，所以有时候dict的add操作代价还是比较高的，牺牲空间但是可以始终保证O(1)的查询效率。如果有大量的数据，建议还是使用bloomfilter或者redis提供的HyperLogLog。

如果你感兴趣，可以看看这篇文章，介绍c解释器如何实现的python dict对象： `Python dictionary implementation <http://www.laurentluce.com/posts/python-dictionary-implementation/>`__ 。我们使用Python来实现一个类似的hash结构。

::

    import ctypes

    class Array:  # 第二章曾经定义过的ADT，这里当做HashMap的槽数组使用
        def __init__(self, size):
            assert size > 0, 'array size must be > 0'
            self._size = size
            PyArrayType = ctypes.py_object * size
            self._elements = PyArrayType()
            self.clear(None)

        def __len__(self):
            return self._size

        def __getitem__(self, index):
            assert index >= 0 and index < len(self), 'out of range'
            return self._elements[index]

        def __setitem__(self, index, value):
            assert index >= 0 and index < len(self), 'out of range'
            self._elements[index] = value

        def clear(self, value):
            """ 设置每个元素为value """
            for i in range(len(self)):
                self._elements[i] = value

        def __iter__(self):
            return _ArrayIterator(self._elements)


    class _ArrayIterator:
        def __init__(self, items):
            self._items = items
            self._idx = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self._idx < len(self._items):
                val = self._items[self._idx]
                self._idx += 1
                return val
            else:
                raise StopIteration


    class HashMap:
        """ HashMap ADT实现，类似于python内置的dict
        一个槽有三种状态：
        1.从未使用 HashMap.UNUSED。此槽没有被使用和冲突过，查找时只要找到UNUSEd就不用再继续探查了
        2.使用过但是remove了，此时是 HashMap.EMPTY，该探查点后边的元素扔可能是有key
        3.槽正在使用 _MapEntry节点
        """

        class _MapEntry:    # 槽里存储的数据
            def __init__(self, key, value):
                self.key = key
                self.value = value

        UNUSED = None    # 没被使用过的槽，作为该类变量的一个单例，下边都是is 判断
        EMPTY = _MapEntry(None, None)     # 使用过但是被删除的槽

        def __init__(self):
            self._table = Array(7)    # 初始化7个槽
            self._count = 0
            # 超过2/3空间被使用就重新分配，load factor = 2/3
            self._maxCount = len(self._table) - len(self._table) // 3

        def __len__(self):
            return self._count

        def __contains__(self, key):
            slot = self._findSlot(key, False)
            return slot is not None

        def add(self, key, value):
            if key in self:    # 覆盖原有value
                slot = self._findSlot(key, False)
                self._table[slot].value = value
                return False
            else:
                slot = self._findSlot(key, True)
                self._table[slot] = HashMap._MapEntry(key, value)
                self._count += 1
                if self._count == self._maxCount:    # 超过2/3使用就rehash
                    self._rehash()
                return True

        def valueOf(self, key):
            slot = self._findSlot(key, False)
            assert slot is not None, 'Invalid map key'
            return self._table[slot].value

        def remove(self, key):
            """ remove操作把槽置为EMPTY"""
            assert key in self, 'Key error %s' % key
            slot = self._findSlot(key, forInsert=False)
            value = self._table[slot].value
            self._count -= 1
            self._table[slot] = HashMap.EMPTY
            return value

        def __iter__(self):
            return _HashMapIteraotr(self._table)

        def _slot_can_insert(self, slot):
            return (self._table[slot] is HashMap.EMPTY or
                    self._table[slot] is HashMap.UNUSED)

        def _findSlot(self, key, forInsert=False):
            """ 注意原书有错误，代码根本不能运行，这里我自己改写的
            Args:
                forInsert (bool): if the search is for an insertion
            Returns:
                slot or None
            """
            slot = self._hash1(key)
            step = self._hash2(key)
            _len = len(self._table)

            if not forInsert:    # 查找是否存在key
                while self._table[slot] is not HashMap.UNUSED:
                    # 如果一个槽是UNUSED，直接跳出
                    if self._table[slot] is HashMap.EMPTY:
                        slot = (slot + step) % _len
                        continue
                    elif self._table[slot].key == key:
                        return slot
                    slot = (slot + step) % _len
                return None

            else:    # 为了插入key
                while not self._slot_can_insert(slot):    # 循环直到找到一个可以插入的槽
                    slot = (slot + step) % _len
                return slot

        def _rehash(self):    # 当前使用槽数量大于2/3时候重新创建新的table
            origTable = self._table
            newSize = len(self._table) * 2 + 1    # 原来的2*n+1倍
            self._table = Array(newSize)

            self._count = 0
            self._maxCount = newSize - newSize // 3

            # 将原来的key value添加到新的table
            for entry in origTable:
                if entry is not HashMap.UNUSED and entry is not HashMap.EMPTY:
                    slot = self._findSlot(entry.key, True)
                    self._table[slot] = entry
                    self._count += 1

        def _hash1(self, key):
            """ 计算key的hash值"""
            return abs(hash(key)) % len(self._table)

        def _hash2(self, key):
            """ key冲突时候用来计算新槽的位置"""
            return 1 + abs(hash(key)) % (len(self._table)-2)


    class _HashMapIteraotr:
        def __init__(self, array):
            self._array = array
            self._idx = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self._idx < len(self._array):
                if self._array[self._idx] is not None and self._array[self._idx].key is not None:
                    key = self._array[self._idx].key
                    self._idx += 1
                    return key
                else:
                    self._idx += 1
            else:
                raise StopIteration


    def print_h(h):
        for idx, i in enumerate(h):
            print(idx, i)
        print('\n')


    def test_HashMap():
        """ 一些简单的单元测试，不过测试用例覆盖不是很全面 """
        h = HashMap()
        assert len(h) == 0
        h.add('a', 'a')
        assert h.valueOf('a') == 'a'
        assert len(h) == 1

        a_v = h.remove('a')
        assert a_v == 'a'
        assert len(h) == 0

        h.add('a', 'a')
        h.add('b', 'b')
        assert len(h) == 2
        assert h.valueOf('b') == 'b'
        b_v = h.remove('b')
        assert b_v == 'b'
        assert len(h) == 1
        h.remove('a')
        assert len(h) == 0

        n = 10
        for i in range(n):
            h.add(str(i), i)
        assert len(h) == n
        print_h(h)
        for i in range(n):
            assert str(i) in h
        for i in range(n):
            h.remove(str(i))
        assert len(h) == 0

--------------

12章: Advanced Sorting
-------------------------------

第5章介绍了基本的排序算法，本章介绍高级排序算法。

归并排序(mergesort): 分治法

::

    def merge_sorted_list(listA, listB):
        """ 归并两个有序数组，O(max(m, n)) ,m和n是数组长度"""
        print('merge left right list', listA, listB, end='')
        new_list = list()
        a = b = 0
        while a < len(listA) and b < len(listB):
            if listA[a] < listB[b]:
                new_list.append(listA[a])
                a += 1
            else:
                new_list.append(listB[b])
                b += 1

        while a < len(listA):
            new_list.append(listA[a])
            a += 1

        while b < len(listB):
            new_list.append(listB[b])
            b += 1

        print(' ->', new_list)
        return new_list


    def mergesort(theList):
        """ O(nlogn)，log层调用，每层n次操作
        mergesort: divided and conquer 分治
        1. 把原数组分解成越来越小的子数组
        2. 合并子数组来创建一个有序数组
        """
        print(theList)    # 我把关键步骤打出来了，你可以运行下看看整个过程
        if len(theList) <= 1:    # 递归出口
            return theList
        else:
            mid = len(theList) // 2

            # 递归分解左右两边数组
            left_half = mergesort(theList[:mid])
            right_half = mergesort(theList[mid:])

            # 合并两边的有序子数组
            newList = merge_sorted_list(left_half, right_half)
            return newList

    """ 这是我调用一次打出来的排序过程
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
    [10, 9, 8, 7, 6]
    [10, 9]
    [10]
    [9]
    merge left right list [10] [9] -> [9, 10]
    [8, 7, 6]
    [8]
    [7, 6]
    [7]
    [6]
    merge left right list [7] [6] -> [6, 7]
    merge left right list [8] [6, 7] -> [6, 7, 8]
    merge left right list [9, 10] [6, 7, 8] -> [6, 7, 8, 9, 10]
    [5, 4, 3, 2, 1]
    [5, 4]
    [5]
    [4]
    merge left right list [5] [4] -> [4, 5]
    [3, 2, 1]
    [3]
    [2, 1]
    [2]
    [1]
    merge left right list [2] [1] -> [1, 2]
    merge left right list [3] [1, 2] -> [1, 2, 3]
    merge left right list [4, 5] [1, 2, 3] -> [1, 2, 3, 4, 5]
    """

快速排序

::

    def quicksort(theSeq, first, last):    # average: O(nlog(n))
        """
        quicksort :也是分而治之，但是和归并排序不同的是，采用选定主元（pivot）而不是从中间
        进行数组划分
        1. 第一步选定pivot用来划分数组，pivot左边元素都比它小，右边元素都大于等于它
        2. 对划分的左右两边数组递归，直到递归出口（数组元素数目小于2）
        3. 对pivot和左右划分的数组合并成一个有序数组
        """
        if first < last:
            pos = partitionSeq(theSeq, first, last)
            # 对划分的子数组递归操作
            quicksort(theSeq, first, pos - 1)
            quicksort(theSeq, pos + 1, last)


    def partitionSeq(theSeq, first, last):
        """ 快排中的划分操作，把比pivot小的挪到左边，比pivot大的挪到右边"""
        pivot = theSeq[first]
        print('before partitionSeq', theSeq)

        left = first + 1
        right = last

        while True:
            # 找到第一个比pivot大的
            while left <= right and theSeq[left] < pivot:
                left += 1

            # 从右边开始找到比pivot小的
            while right >= left and theSeq[right] >= pivot:
                right -= 1

            if right < left:
                break
            else:
                theSeq[left], theSeq[right] = theSeq[right], theSeq[left]

        # 把pivot放到合适的位置
        theSeq[first], theSeq[right] = theSeq[right], theSeq[first]

        print('after partitionSeq {}: {}\t'.format(theSeq, pivot))
        return right    # 返回pivot的位置


    def test_partitionSeq():
        l = [0,1,2,3,4]
        assert partitionSeq(l, 0, len(l)-1) == 0
        l = [4,3,2,1,0]
        assert partitionSeq(l, 0, len(l)-1) == 4
        l = [2,3,0,1,4]
        assert partitionSeq(l, 0, len(l)-1) == 2

    test_partitionSeq()


    def test_quicksort():
        def _is_sorted(seq):
            for i in range(len(seq)-1):
                if seq[i] > seq[i+1]:
                    return False
            return True

        from random import randint
        for i in range(100):
            _len = randint(1, 100)
            to_sort = []
            for i in range(_len):
                to_sort.append(randint(0, 100))
            quicksort(to_sort, 0, len(to_sort)-1)    # 注意这里用了原地排序，直接更改了数组
            print(to_sort)
            assert _is_sorted(to_sort)

    test_quicksort()

利用快排中的partitionSeq操作，我们还能实现另一个算法，nth_element，快速查找一个无序数组中的第k大元素

::

    def nth_element(seq, beg, end, k):
        if beg == end:
            return seq[beg]
        pivot_index = partitionSeq(seq, beg, end)
        if pivot_index == k:
            return seq[k]
        elif pivot_index > k:
            return nth_element(seq, beg, pivot_index-1, k)
        else:
            return nth_element(seq, pivot_index+1, end, k)

    def test_nth_element():
        from random import shuffle
        n = 10
        l = list(range(n))
        shuffle(l)
        print(l)
        for i in range(len(l)):
            assert nth_element(l, 0, len(l)-1, i) == i

    test_nth_element()

--------------

13章: Binary Tree
---------------------

The binary Tree: 二叉树，每个节点做多只有两个子节点

::

    class _BinTreeNode:
        def __init__(self, data):
            self.data = data
            self.left = None
            self.right = None


    # 三种depth-first遍历
    def preorderTrav(subtree):
        """ 先（根）序遍历"""
        if subtree is not None:
            print(subtree.data)
            preorderTrav(subtree.left)
            preorderTrav(subtree.right)


    def inorderTrav(subtree):
        """ 中(根)序遍历"""
        if subtree is not None:
            preorderTrav(subtree.left)
            print(subtree.data)
            preorderTrav(subtree.right)


    def postorderTrav(subtree):
        """ 后（根）序遍历"""
        if subtree is not None:
            preorderTrav(subtree.left)
            preorderTrav(subtree.right)
            print(subtree.data)


    # 宽度优先遍历(bradth-First Traversal): 一层一层遍历, 使用queue
    def breadthFirstTrav(bintree):
        from queue import Queue    # py3
        q = Queue()
        q.put(bintree)
        while not q.empty():
            node = q.get()
            print(node.data)
            if node.left is not None:
                q.put(node.left)
            if node.right is not None:
                q.put(node.right)


    class _ExpTreeNode:
        __slots__ = ('element', 'left', 'right')

        def __init__(self, data):
            self.element = data
            self.left = None
            self.right = None

        def __repr__(self):
            return '<_ExpTreeNode: {} {} {}>'.format(
                self.element, self.left, self.right)

    from queue import Queue
    class ExpressionTree:
        """
        表达式树: 操作符存储在内节点操作数存储在叶子节点的二叉树。(符号树真难打出来)
            *
           / \
          +   -
         / \  / \
         9  3 8   4
        (9+3) * (8-4)

        Expression Tree Abstract Data Type，可以实现二元操作符
        ExpressionTree(expStr): user string as constructor param
        evaluate(varDict): evaluates the expression and returns the numeric result
        toString(): constructs and retutns a string represention of the expression

        Usage:
            vars = {'a': 5, 'b': 12}
            expTree = ExpressionTree("(a/(b-3))")
            print('The result = ', expTree.evaluate(vars))
        """

        def __init__(self, expStr):
            self._expTree = None
            self._buildTree(expStr)

        def evaluate(self, varDict):
            return self._evalTree(self._expTree, varDict)

        def __str__(self):
            return self._buildString(self._expTree)

        def _buildString(self, treeNode):
            """ 在一个子树被遍历之前添加做括号，在子树被遍历之后添加右括号 """
            # print(treeNode)
            if treeNode.left is None and treeNode.right is None:
                return str(treeNode.element)    # 叶子节点是操作数直接返回
            else:
                expStr = '('
                expStr += self._buildString(treeNode.left)
                expStr += str(treeNode.element)
                expStr += self._buildString(treeNode.right)
                expStr += ')'
                return expStr

        def _evalTree(self, subtree, varDict):
            # 是不是叶子节点, 是的话说明是操作数，直接返回
            if subtree.left is None and subtree.right is None:
                # 操作数是合法数字吗
                if subtree.element >= '0' and subtree.element <= '9':
                    return int(subtree.element)
                else:    # 操作数是个变量
                    assert subtree.element in varDict, 'invalid variable.'
                    return varDict[subtree.element]
            else:    # 操作符则计算其子表达式
                lvalue = self._evalTree(subtree.left, varDict)
                rvalue = self._evalTree(subtree.right, varDict)
                print(subtree.element)
                return self._computeOp(lvalue, subtree.element, rvalue)

        def _computeOp(self, left, op, right):
            assert op
            op_func = {
                '+': lambda left, right: left + right,    # or import operator, operator.add
                '-': lambda left, right: left - right,
                '*': lambda left, right: left * right,
                '/': lambda left, right: left / right,
                '%': lambda left, right: left % right,
            }
            return op_func[op](left, right)

        def _buildTree(self, expStr):
            expQ = Queue()
            for token in expStr:    # 遍历表达式字符串的每个字符
                expQ.put(token)
            self._expTree = _ExpTreeNode(None)    # 创建root节点
            self._recBuildTree(self._expTree, expQ)

        def _recBuildTree(self, curNode, expQ):
            token = expQ.get()
            if token == '(':
                curNode.left = _ExpTreeNode(None)
                self._recBuildTree(curNode.left, expQ)

                # next token will be an operator: + = * / %
                curNode.element = expQ.get()
                curNode.right = _ExpTreeNode(None)
                self._recBuildTree(curNode.right, expQ)

                # the next token will be ')', remmove it
                expQ.get()

            else:  # the token is a digit that has to be converted to an int.
                curNode.element = token


    vars = {'a': 5, 'b': 12}
    expTree = ExpressionTree("((2*7)+8)")
    print(expTree)
    print('The result = ', expTree.evaluate(vars))

Heap（堆）：二叉树最直接的一个应用就是实现堆。堆就是一颗完全二叉树，最大堆的非叶子节点的值都比孩子大，最小堆的非叶子结点的值都比孩子小。
python内置了heapq模块帮助我们实现堆操作，比如用内置的heapq模块实现个堆排序:

::

    # 使用python内置的heapq实现heap sort
    def heapsort(iterable):
        from heapq import heappush, heappop
        h = []
        for value in iterable:
            heappush(h, value)
        return [heappop(h) for i in range(len(h))]

但是一般实现堆的时候实际上并不是用数节点来实现的，而是使用数组实现，效率比较高。为什么可以用数组实现呢?因为完全二叉树的性质，
可以用下标之间的关系表示节点之间的关系，MaxHeap的docstring中已经说明了

::

    class MaxHeap:
        """
        Heaps:
        完全二叉树，最大堆的非叶子节点的值都比孩子大，最小堆的非叶子结点的值都比孩子小
        Heap包含两个属性，order property 和 shape property(a complete binary tree)，在插入
        一个新节点的时候，始终要保持这两个属性
        插入操作：保持堆属性和完全二叉树属性, sift-up 操作维持堆属性
        extract操作：只获取根节点数据，并把树最底层最右节点copy到根节点后，sift-down操作维持堆属性

        用数组实现heap，从根节点开始，从上往下从左到右给每个节点编号，则根据完全二叉树的
        性质，给定一个节点i， 其父亲和孩子节点的编号分别是:
            parent = (i-1) // 2
            left = 2 * i + 1
            rgiht = 2 * i + 2
        使用数组实现堆一方面效率更高，节省树节点的内存占用，一方面还可以避免复杂的指针操作，减少
        调试难度。

        """

        def __init__(self, maxSize):
            self._elements = Array(maxSize)    # 第二章实现的Array ADT
            self._count = 0

        def __len__(self):
            return self._count

        def capacity(self):
            return len(self._elements)

        def add(self, value):
            assert self._count < self.capacity(), 'can not add to full heap'
            self._elements[self._count] = value
            self._count += 1
            self._siftUp(self._count - 1)
            self.assert_keep_heap()    # 确定每一步add操作都保持堆属性

        def extract(self):
            assert self._count > 0, 'can not extract from an empty heap'
            value = self._elements[0]    # save root value
            self._count -= 1
            self._elements[0] = self._elements[self._count]    # 最右下的节点放到root后siftDown
            self._siftDown(0)
            self.assert_keep_heap()
            return value

        def _siftUp(self, ndx):
            if ndx > 0:
                parent = (ndx - 1) // 2
                # print(ndx, parent)
                if self._elements[ndx] > self._elements[parent]:    # swap
                    self._elements[ndx], self._elements[parent] = self._elements[parent], self._elements[ndx]
                    self._siftUp(parent)    # 递归

        def _siftDown(self, ndx):
            left = 2 * ndx + 1
            right = 2 * ndx + 2
            # determine which node contains the larger value
            largest = ndx
            if (left < self._count and
                self._elements[left] >= self._elements[largest] and
                self._elements[left] >= self._elements[right]):  # 原书这个地方没写实际上找的未必是largest
                largest = left
            elif right < self._count and self._elements[right] >= self._elements[largest]:
                largest = right
            if largest != ndx:
                self._elements[ndx], self._elements[largest] = self._elements[largest], self._elements[ndx]
                self._siftDown(largest)

        def __repr__(self):
            return ' '.join(map(str, self._elements))

        def assert_keep_heap(self):
            """ 我加了这个函数是用来验证每次add或者extract之后，仍保持最大堆的性质"""
            _len = len(self)
            for i in range(0, int((_len-1)/2)):    # 内部节点（非叶子结点）
                l = 2 * i + 1
                r = 2 * i + 2
                if l < _len and r < _len:
                    assert self._elements[i] >= self._elements[l] and self._elements[i] >= self._elements[r]

    def test_MaxHeap():
        """ 最大堆实现的单元测试用例 """
        _len = 10
        h = MaxHeap(_len)
        for i in range(_len):
            h.add(i)
            h.assert_keep_heap()
        for i in range(_len):
            # 确定每次出来的都是最大的数字，添加的时候是从小到大添加的
            assert h.extract() == _len-i-1

    test_MaxHeap()

    def simpleHeapSort(theSeq):
        """ 用自己实现的MaxHeap实现堆排序，直接修改原数组实现inplace排序"""
        if not theSeq:
            return theSeq
        _len = len(theSeq)
        heap = MaxHeap(_len)
        for i in theSeq:
            heap.add(i)
        for i in reversed(range(_len)):
            theSeq[i] = heap.extract()
        return theSeq


    def test_simpleHeapSort():
        """ 用一些测试用例证明实现的堆排序是可以工作的 """
        def _is_sorted(seq):
            for i in range(len(seq)-1):
                if seq[i] > seq[i+1]:
                    return False
            return True

        from random import randint
        assert simpleHeapSort([]) == []
        for i in range(1000):
            _len = randint(1, 100)
            to_sort = []
            for i in range(_len):
                to_sort.append(randint(0, 100))
            simpleHeapSort(to_sort)    # 注意这里用了原地排序，直接更改了数组
            assert _is_sorted(to_sort)


    test_simpleHeapSort()

--------------

14章: Search Trees
---------------------

二叉差找树性质：对每个内部节点V， 1. 所有key小于V.key的存储在V的左子树。
2. 所有key大于V.key的存储在V的右子树
对BST进行中序遍历会得到升序的key序列

::

    class _BSTMapNode:
        __slots__ = ('key', 'value', 'left', 'right')

        def __init__(self, key, value):
            self.key = key
            self.value = value
            self.left = None
            self.right = None

        def __repr__(self):
            return '<{}:{}> left:{}, right:{}'.format(
                self.key, self.value, self.left, self.right)

        __str__ = __repr__


    class BSTMap:
        """ BST，树节点包含key可payload。用BST来实现之前用hash实现过的Map ADT.
        性质：对每个内部节点V，
        1.对于节点V，所有key小于V.key的存储在V的左子树。
        2.所有key大于V.key的存储在V的右子树
        对BST进行中序遍历会得到升序的key序列
        """
        def __init__(self):
            self._root = None
            self._size = 0
            self._rval = None     # 作为remove的返回值

        def __len__(self):
            return self._size

        def __iter__(self):
            return _BSTMapIterator(self._root, self._size)

        def __contains__(self, key):
            return self._bstSearch(self._root, key) is not None

        def valueOf(self, key):
            node = self._bstSearch(self._root, key)
            assert node is not None, 'Invalid map key.'
            return node.value

        def _bstSearch(self, subtree, target):
            if subtree is None:    # 递归出口，遍历到树底没有找到key或是空树
                return None
            elif target < subtree.key:
                return self._bstSearch(subtree.left, target)
            elif target > subtree.key:
                return self._bstSearch(subtree.right, target)
            return subtree    # 返回引用

        def _bstMinumum(self, subtree):
            """ 顺着树一直往左下角递归找就是最小的,向右下角递归就是最大的 """
            if subtree is None:
                return None
            elif subtree.left is None:
                return subtree
            else:
                return subtree._bstMinumum(self, subtree.left)

        def add(self, key, value):
            """ 添加或者替代一个key的value, O(N) """
            node = self._bstSearch(self._root, key)
            if node is not None:    # if key already exists, update value
                node.value = value
                return False
            else:   # insert a new entry
                self._root = self._bstInsert(self._root, key, value)
                self._size += 1
                return True

        def _bstInsert(self, subtree, key, value):
            """ 新的节点总是插入在树的叶子结点上 """
            if subtree is None:
                subtree = _BSTMapNode(key, value)
            elif key < subtree.key:
                subtree.left = self._bstInsert(subtree.left, key, value)
            elif key > subtree.key:
                subtree.right = self._bstInsert(subtree.right, key, value)
            # 注意这里没有else语句了，应为在被调用处add函数里先判断了是否有重复key
            return subtree

        def remove(self, key):
            """ O(N)
            被删除的节点分为三种:
            1.叶子结点:直接把其父亲指向该节点的指针置None
            2.该节点有一个孩子: 删除该节点后，父亲指向一个合适的该节点的孩子
            3.该节点有俩孩子:
                (1)找到要删除节点N和其后继S（中序遍历后该节点下一个）
                (2)复制S的key到N
                (3)从N的右子树中删除后继S（即在N的右子树中最小的）
            """
            assert key in self, 'invalid map key'
            self._root = self._bstRemove(self._root, key)
            self._size -= 1
            return self._rval

        def _bstRemove(self, subtree, target):
            # search for the item in the tree
            if subtree is None:
                return subtree
            elif target < subtree.key:
                subtree.left = self._bstRemove(subtree.left, target)
                return subtree
            elif target > subtree.key:
                subtree.right = self._bstRemove(subtree.right, target)
                return subtree

            else:    # found the node containing the item
                self._rval = subtree.value
                if subtree.left is None and subtree.right is None:
                    # 叶子node
                    return None
                elif subtree.left is None or subtree.right is None:
                    # 有一个孩子节点
                    if subtree.left is not None:
                        return subtree.left
                    else:
                        return subtree.right
                else:   # 有俩孩子节点
                    successor = self._bstMinumum(subtree.right)
                    subtree.key = successor.key
                    subtree.value = successor.value
                    subtree.right = self._bstRemove(subtree.right, successor.key)
                    return subtree

        def __repr__(self):
            return '->'.join([str(i) for i in self])

        def assert_keep_bst_property(self, subtree):
            """ 写这个函数为了验证add和delete操作始终维持了bst的性质 """
            if subtree is None:
                return
            if subtree.left is not None and subtree.right is not None:
                assert subtree.left.value <= subtree.value
                assert subtree.right.value >= subtree.value
                self.assert_keep_bst_property(subtree.left)
                self.assert_keep_bst_property(subtree.right)

            elif subtree.left is None and subtree.right is not None:
                assert subtree.right.value >= subtree.value
                self.assert_keep_bst_property(subtree.right)

            elif subtree.left is not None and subtree.right is None:
                assert subtree.left.value <= subtree.value
                self.assert_keep_bst_property(subtree.left)


    class _BSTMapIterator:
        def __init__(self, root, size):
            self._theKeys = Array(size)
            self._curItem = 0
            self._bstTraversal(root)
            self._curItem = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self._curItem < len(self._theKeys):
                key = self._theKeys[self._curItem]
                self._curItem += 1
                return key
            else:
                raise StopIteration

        def _bstTraversal(self, subtree):
            if subtree is not None:
                self._bstTraversal(subtree.left)
                self._theKeys[self._curItem] = subtree.key
                self._curItem += 1
                self._bstTraversal(subtree.right)


    def test_BSTMap():
        l = [60, 25, 100, 35, 17, 80]
        bst = BSTMap()
        for i in l:
            bst.add(i)

    def test_HashMap():
        """ 之前用来测试用hash实现的map，改为用BST实现的Map测试 """
        # h = HashMap()
        h = BSTMap()
        assert len(h) == 0
        h.add('a', 'a')
        assert h.valueOf('a') == 'a'
        assert len(h) == 1

        a_v = h.remove('a')
        assert a_v == 'a'
        assert len(h) == 0

        h.add('a', 'a')
        h.add('b', 'b')
        assert len(h) == 2
        assert h.valueOf('b') == 'b'
        b_v = h.remove('b')
        assert b_v == 'b'
        assert len(h) == 1
        h.remove('a')

        assert len(h) == 0

        _len = 10
        for i in range(_len):
            h.add(str(i), i)
        assert len(h) == _len
        for i in range(_len):
            assert str(i) in h
        for i in range(_len):
            print(len(h))
            print('bef', h)
            _ = h.remove(str(i))
            assert _ == i
            print('aft', h)
            print(len(h))
        assert len(h) == 0

    test_HashMap()

