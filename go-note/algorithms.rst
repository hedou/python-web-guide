.. _go_algorithms:

Go 算法和数据结构
=====================================================================

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
