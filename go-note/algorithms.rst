.. _go_algorithms:

Go 算法和数据结构
=====================================================================

刷题过程中会遇到一些通用数据结构的封装，在这里记录一下。注意没有考虑 goroutine 安全，生产环境用需要加锁改造。

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

Deque
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


Trie
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
