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
