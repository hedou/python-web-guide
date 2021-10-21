.. _go_algorithms:

Go 算法和数据结构
=====================================================================

刷题过程中会遇到一些通用数据结构的封装，在这里记录一下。注意因为是刷算法题用的，没有考虑 goroutine 安全，
不要直接用在并发环境，如果是在生产环境中使用请加锁改造。(没有泛型写起来挺繁琐的)

Stack
--------------------------------------------------

.. code-block:: go

    type Stack struct {
        st []int
    }

    func NewStack() *Stack {
        return &Stack{st:make([]int,0)}
    }
    func (s *Stack) Push(x int) {
        s.st = append(s.st, x)
    }

    func (s *Stack) Peek() int{
        return s.st[len(s.st)-1]
    }

    func (s *Stack) Pop() int{
        n := len(s.st)
        top := s.st[n-1]
        s.st = s.st[:n-1]
        return top
    }

    func (s *Stack) Empty() bool{
        return len(s.st) == 0
    }

Queue
--------------------------------------------------

.. code-block:: go

    type Item struct {
        Num   int
        Order int
    }
    type Queue struct {
        items []Item
    }

    func NewQueue() *Queue {
        return &Queue{
            items: make([]Item, 0),
        }
    }
    func (q *Queue) Push(x Item) {
        q.items = append(q.items, x)
    }

    func (q *Queue) Pop() Item {
        x := q.items[0]
        q.items = q.items[1:]
        return x
    }

    func (q *Queue) Front() Item {
        return q.items[0]
    }

    func (q *Queue) End() Item {
        return q.items[len(q.items)-1]
    }

    func (q *Queue) Empty() bool {
        return len(q.items) == 0
    }

Deque 双端队列
--------------------------------------------------

.. code-block:: go

    import (
        "container/list"
        "fmt"
    )

    // 滑动窗口最大值

    type Deque struct {
        ll *list.List
    }

    func NewDeque() *Deque {
        return &Deque{ll: list.New()}
    }

    func (dq *Deque) PushFront(x int) {
        dq.ll.PushFront(x)
    }

    func (dq *Deque) PushBack(x int) {
        dq.ll.PushBack(x)
    }

    func (dq *Deque) Pop() { // remove back
        dq.ll.Remove(dq.ll.Back())
    }

    func (dq *Deque) PopFront() { // remove first
        dq.ll.Remove(dq.ll.Front())
    }

    func (dq *Deque) Front() int {
        return dq.ll.Front().Value.(int)
    }

    func (dq *Deque) Back() int {
        return dq.ll.Back().Value.(int)
    }

    func (dq *Deque) Len() int {
        return dq.ll.Len()
    }


Linked List
--------------------------------------------------

.. code-block:: go

    package main

    import "fmt"

    // 测试链表。在 redigo 里边使用到了链表作为 pool 的实现
    type IntList struct {
        count int
        // front,back 分别指向第一个和最后一个 node，或者是 nil。front.prev back.next 都是空
        front, back *Node
    }

    // 链表节点
    type Node struct {
        next, prev *Node
    }

    func (l *IntList) Count() int {
        return l.count
    }

    func (l *IntList) pushFront(node *Node) {
        node.next = l.front
        node.prev = nil
        if l.count == 0 { // note when list is empty
            l.back = node
        } else {
            l.front.prev = node
        }
        l.front = node
        l.count++
    }

    func (l *IntList) popFront() {
        first := l.front
        l.count--
        if l.count == 0 {
            l.front, l.back = nil, nil
        } else {
            first.next.prev = nil
            l.front = first.next
        }
        first.next, first.prev = nil, nil // clear first
    }

    func (l *IntList) popBack() {
        last := l.back
        l.count--
        if l.count == 0 {
            l.front, l.back = nil, nil
        } else {
            last.prev.next = nil
            l.back = last.prev
        }
        last.prev, last.next = nil, nil
    }

    func (l *IntList) Print() {
        cur := l.front
        for cur != l.back {
            fmt.Println(cur)
            cur = cur.next
        }
        if l.back != nil {
            fmt.Println(l.back)
        }
    }


Trie 字典树
--------------------------------------------------

.. code-block:: go

    // Package main provides ...
    package main

    import "fmt"

    // https://golangbyexample.com/trie-implementation-in-go/

    const (
        ALBHABET_SIZE = 26
    )

    type node struct {
        childrens [ALBHABET_SIZE]*node
        isWordEnd bool
    }

    type trie struct {
        root *node
    }

    func newTrie() *trie {
        return &trie{
            root: &node{},
        }
    }

    func (t *trie) insert(word string) {
        wordLength := len(word)
        current := t.root
        for i := 0; i < wordLength; i++ {
            idx := word[i] - 'a'
            if current.childrens[idx] == nil {
                current.childrens[idx] = &node{}
            }
            current = current.childrens[idx]
        }
        current.isWordEnd = true
    }
    func (t *trie) find(word string) bool {
        wordLength := len(word)
        current := t.root
        for i := 0; i < wordLength; i++ {
            idx := word[i] - 'a'
            if current.childrens[idx] == nil {
                return false
            }
            current = current.childrens[idx]
        }
        if current.isWordEnd {
            return true
        }
        return false
    }

    func main() {
        trie := newTrie()
        words := []string{"zhang", "wang", "li", "zhao"}
        for i := 0; i < len(words); i++ {
            trie.insert(words[i])
        }
        toFind := []string{"zhang", "wang", "li", "zhao", "gong"}
        for i := 0; i < len(toFind); i++ {
            c := toFind[i]
            if trie.find(c) {
                fmt.Printf("word[%s] found in trie.\n", c)
            } else {
                fmt.Printf("word[%s] not found in trie\n", c)
            }
        }
    }

OrderedMap (类似 python collections.OrderedDict)
--------------------------------------------------
模拟 python collections.OrderedDict 写的，可以方便的实现 lru 等。注意这里的 order 指的是 key 插入的顺序，不是指 key 字典序。

.. code-block:: go

    package main

    import (
        "container/list"
        "fmt"
    )

    // 按照 key 插入顺序遍历 map，类似 python collections.OrderedDict。注意不是 key 的字典序，而是插入顺序
    type OrderedMap struct {
        m  map[string]int
        me map[string]*list.Element
        ll *list.List // 记录 key order
    }

    func NewOrderedMap() *OrderedMap {
        return &OrderedMap{
            m:  make(map[string]int),
            me: make(map[string]*list.Element),
            ll: list.New(),
        }
    }

    func (o *OrderedMap) Set(k string, v int) {
        if _, found := o.m[k]; !found {
            e := o.ll.PushBack(k)
            o.me[k] = e
        }
        o.m[k] = v
    }

    func (o *OrderedMap) Exist(k string) bool {
        _, found := o.m[k]
        return found
    }

    func (o *OrderedMap) Get(k string) int {
        return o.m[k]
    }

    func (o *OrderedMap) Delete(k string) {
        delete(o.m, k)

        node := o.me[k]
        o.ll.Remove(node)
        delete(o.me, k)
    }

    func (o *OrderedMap) Len() int {
        return len(o.m)
    }

    // 按照 key 进入顺序返回
    func (o *OrderedMap) Keys() []string {
        keys := make([]string, o.ll.Len())
        i := 0
        for e := o.ll.Front(); e != nil; e = e.Next() {
            keys[i] = e.Value.(string)
            i++
        }
        return keys
    }

Heap 堆
--------------------------------------------------
go 自带了一个 `container/heap` 模块可以用来实现堆。

.. code-block:: go

    // This example demonstrates an integer heap built using the heap interface.
    // A heap is a tree with the property that each node is the minimum-valued node in its subtree.
    // 可以用来实现优先级队列 priority queue
    package main

    import (
        "container/heap"
        "fmt"
    )

    // An IntHeap is a min-heap of ints.
    type IntHeap []int

    func (h IntHeap) Len() int           { return len(h) }
    func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
    func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

    // 最后追加一个元素
    func (h *IntHeap) Push(x interface{}) {
        // Push and Pop use pointer receivers because they modify the slice's length,
        // not just its contents.
        *h = append(*h, x.(int))
    }

    // 移除并且返回最后一个元素
    func (h *IntHeap) Pop() interface{} {
        old := *h
        n := len(old)
        x := old[n-1]
        *h = old[0 : n-1]
        return x
    }

    // This example inserts several ints into an IntHeap, checks the minimum,
    // and removes them in order of priority.
    func main() {
        h := &IntHeap{2, 1, 5}
        heap.Init(h)
        fmt.Println(h)
        heap.Push(h, 3)
        fmt.Println(h)
        fmt.Printf("minimum: %d\n", (*h)[0]) // h[0] 最小的元素
        for h.Len() > 0 {
            fmt.Printf("%d ", heap.Pop(h))
        }
    }
