# Queue and Circular Queue — Beginner Guide with Examples

> **One-line summary:**
> A queue is a FIFO (First In, First Out) structure where enqueue, dequeue, and peek all run in $O(1)$ time; a circular queue fixes the wasted-space problem of a simple array queue by wrapping the rear pointer around using the modulo operator, reusing freed slots without extra memory.

---

## Table of Contents

1. [What is a Queue?](#1-what-is-a-queue)
2. [Queue Terminology](#2-queue-terminology)
3. [Queue Operations](#3-queue-operations)
4. [Queue Using Array — Manual Implementation](#4-queue-using-array--manual-implementation)
5. [The Problem with a Simple Queue](#5-the-problem-with-a-simple-queue)
6. [What is a Circular Queue?](#6-what-is-a-circular-queue)
7. [Circular Queue Implementation](#7-circular-queue-implementation)
8. [Simple Queue vs Circular Queue](#8-simple-queue-vs-circular-queue)
9. [Queue Using Python `collections.deque` / C++ `std::queue`](#9-queue-using-python-collectionsdeque--c-stdqueue)
10. [Real-World Applications](#10-real-world-applications)
11. [Time and Space Complexity](#11-time-and-space-complexity)
12. [Common Mistakes to Avoid](#12-common-mistakes-to-avoid)
13. [Key Takeaways](#13-key-takeaways)
14. [FAQs](#14-faqs)

---

## 1. What is a Queue?

Imagine standing in line at a coffee shop. The first person to arrive gets served first, and new customers join at the back. That is exactly how a queue works in programming.

A **queue** is a linear data structure that follows the **FIFO** principle: **First In, First Out**. The element that enters first is the one that leaves first.

```
Enqueue (rear) →          ← Dequeue (front)

rear                              front
  ↓                                 ↓
[ 30 | 20 | 10 ]   →   dequeue   →  10 leaves first
```

A stack removes the _most recent_ element; a queue removes the _oldest_ element — the two are opposite ideas.

---

## 2. Queue Terminology

| Term          | Meaning                                            |
| ------------- | -------------------------------------------------- |
| **Enqueue**   | Add an element to the **rear** (back) of the queue |
| **Dequeue**   | Remove an element from the **front** of the queue  |
| **Front**     | Pointer/index to the first element                 |
| **Rear**      | Pointer/index to the last element                  |
| **Overflow**  | Attempting to enqueue when the queue is full       |
| **Underflow** | Attempting to dequeue when the queue is empty      |

Think of `front` as the **exit door** and `rear` as the **entrance door**. Elements always enter through the rear and leave through the front.

---

## 3. Queue Operations

### Enqueue

Place an element at the rear and advance the rear pointer.

### Dequeue

Remove and return the element at the front and advance the front pointer.

### Peek and isEmpty

Look at the front element without removing it; check whether the queue has any elements.

#### Python (simple list-based)

```python
# Python — Basic queue operations using a list
queue = []
MAX_SIZE = 5

def enqueue(queue, item):
    if len(queue) == MAX_SIZE:
        print("Queue is full. Cannot enqueue.")
    else:
        queue.append(item)       # Add to the rear
        print(f"Enqueued: {item}")

def dequeue(queue):
    if len(queue) == 0:
        print("Queue is empty. Cannot dequeue.")
        return None
    return queue.pop(0)          # Remove from the front (O(n) — see note below)

def peek(queue):
    if len(queue) == 0:
        print("Queue is empty.")
        return None
    return queue[0]

def is_empty(queue):
    return len(queue) == 0


enqueue(queue, 10)
enqueue(queue, 20)
enqueue(queue, 30)
print("Queue:", queue)           # [10, 20, 30]

dequeue(queue)
print("After dequeue:", queue)   # [20, 30]
print("Front:", peek(queue))     # 20
```

> **Note:** `list.pop(0)` is $O(n)$ because it shifts all elements. Use `collections.deque` for production code (see Section 9).

**C++ (simple):**

```cpp
// C++ — Basic queue operations using standalone functions and std::queue
#include <iostream>
#include <queue>
using namespace std;

const int MAX_SIZE = 5;

void enqueue(queue<int>& q, int item) {
    if ((int)q.size() == MAX_SIZE) {        // check if queue is full
        cout << "Queue is full. Cannot enqueue.\n";
        return;
    }
    q.push(item);                           // add item to the rear — O(1)
    cout << "Enqueued: " << item << "\n";
}

int dequeue(queue<int>& q) {
    if (q.empty()) {                        // check if queue is empty
        cout << "Queue is empty. Cannot dequeue.\n";
        return -1;
    }
    int item = q.front();                   // read the front element
    q.pop();                                // remove from the front — O(1)
    return item;
}

int peek(queue<int>& q) {
    if (q.empty()) { cout << "Queue is empty.\n"; return -1; }
    return q.front();                       // view front without removing
}

int main() {
    queue<int> q;
    enqueue(q, 10); enqueue(q, 20); enqueue(q, 30);
    cout << "Queue: [10, 20, 30]\n";
    dequeue(q);
    cout << "After dequeue, front: " << peek(q) << "\n";  // 20
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — queue operations using std::queue<int>
#include <iostream>
#include <queue>
using namespace std;

class Solution {
public:
    void demonstrateQueue() {
        queue<int> q;              // declare a FIFO queue of integers

        q.push(10);                // enqueue 10 at the rear
        q.push(20);                // enqueue 20 at the rear
        q.push(30);                // enqueue 30 at the rear

        cout << "Front: " << q.front() << "\n";        // peek front: 10
        q.pop();                                        // dequeue from front (10)
        cout << "After dequeue, front: " << q.front() << "\n"; // 20
        cout << "Size: "  << q.size()  << "\n";        // 2
        cout << "Empty: " << q.empty() << "\n";        // 0 (false)
    }
};
```

---

## 4. Queue Using Array — Manual Implementation

A pointer-based approach eliminates the $O(n)$ shift cost by moving the `front` pointer forward instead of shifting data.

### Python

```python
# Python — Queue with front/rear pointers (O(1) dequeue)
class Queue:
    def __init__(self, size):
        self.queue = [None] * size
        self.front = -1
        self.rear  = -1
        self.size  = size

    def is_empty(self):
        return self.front == -1

    def is_full(self):
        return self.rear == self.size - 1

    def enqueue(self, item):
        if self.is_full():
            print("Queue is full!")
            return
        if self.front == -1:     # First element being added
            self.front = 0
        self.rear += 1
        self.queue[self.rear] = item
        print(f"Enqueued: {item}")

    def dequeue(self):
        if self.is_empty():
            print("Queue is empty!")
            return None
        item = self.queue[self.front]
        if self.front == self.rear:  # Last element removed
            self.front = -1
            self.rear  = -1
        else:
            self.front += 1
        print(f"Dequeued: {item}")
        return item

    def display(self):
        if self.is_empty():
            print("Queue is empty.")
        else:
            print("Queue:", self.queue[self.front : self.rear + 1])


q = Queue(5)
q.enqueue(1); q.enqueue(2); q.enqueue(3)
q.display()    # Queue: [1, 2, 3]
q.dequeue()    # Dequeued: 1
q.display()    # Queue: [2, 3]
```

**C++ (simple):**

```cpp
// C++ — Queue with front/rear pointers (O(1) dequeue)
#include <iostream>
#include <vector>

class Queue {
    std::vector<int> data;
    int front_idx, rear_idx, capacity;
public:
    Queue(int size) : data(size, -1), front_idx(-1), rear_idx(-1), capacity(size) {}

    bool isEmpty() const { return front_idx == -1; }
    bool isFull()  const { return rear_idx == capacity - 1; }

    void enqueue(int item) {
        if (isFull()) { std::cout << "Queue is full!\n"; return; }
        if (front_idx == -1) front_idx = 0;   // First element
        data[++rear_idx] = item;
        std::cout << "Enqueued: " << item << "\n";
    }

    int dequeue() {
        if (isEmpty()) { std::cout << "Queue is empty!\n"; return -1; }
        int item = data[front_idx];
        if (front_idx == rear_idx)            // Last element removed
            front_idx = rear_idx = -1;
        else
            front_idx++;                      // advance front pointer
        std::cout << "Dequeued: " << item << "\n";
        return item;
    }

    void display() const {
        if (isEmpty()) { std::cout << "Queue is empty.\n"; return; }
        std::cout << "Queue: ";
        for (int i = front_idx; i <= rear_idx; i++)
            std::cout << data[i] << " ";
        std::cout << "\n";
    }
};

int main() {
    Queue q(5);
    q.enqueue(1); q.enqueue(2); q.enqueue(3);
    q.display();   // Queue: 1 2 3
    q.dequeue();   // Dequeued: 1
    q.display();   // Queue: 2 3
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — queue with pointer tracking using std::queue<int>
#include <iostream>
#include <queue>
using namespace std;

class Solution {
public:
    void demonstratePointerQueue() {
        queue<int> q;              // std::queue handles front/rear tracking internally

        q.push(1);                 // enqueue 1 at the rear
        q.push(2);                 // enqueue 2 at the rear
        q.push(3);                 // enqueue 3 at the rear

        cout << "Front: " << q.front() << "\n"; // peek front without removing: 1
        q.pop();                                 // dequeue from front (1) — O(1)
        cout << "After dequeue: " << q.front() << "\n"; // 2
        cout << "Size: " << q.size() << "\n";   // 2
    }
};
```

---

## 5. The Problem with a Simple Queue

After several enqueue and dequeue operations, the `front` pointer moves forward. Even if empty slots exist at the beginning of the array, they cannot be reused because `rear` has hit the end.

```
After 3 enqueues and 2 dequeues:

index:   0     1     2     3     4
       [   ] [   ] [ 30] [ 40] [ 50]
              ↑             ↑
           front=2        rear=4

rear == size-1 → "full", but slots 0 and 1 are wasted!
```

This wastes memory. The fix is a **circular queue**.

---

## 6. What is a Circular Queue?

A circular queue connects the end of the array back to the beginning. When `rear` reaches the last index, it wraps around to index `0` if space is available at the front.

Think of it like a **merry-go-round** — once you reach the last seat, you loop back to the first. No space is wasted.

```
Circular layout (size = 5):

      0
   4     1
   3     2

rear wraps: (rear + 1) % size
```

Key formulas:

- Advance rear: `rear = (rear + 1) % size`
- Advance front: `front = (front + 1) % size`
- Full condition: `(rear + 1) % size == front`
- Empty condition: `front == -1`

This is also called a **Ring Buffer** and is used in operating systems, audio buffers, and task scheduling.

---

## 7. Circular Queue Implementation

### Python

```python
# Python — Circular Queue using modulo
class CircularQueue:
    def __init__(self, size):
        self.queue = [None] * size
        self.front = -1
        self.rear  = -1
        self.size  = size

    def is_empty(self):
        return self.front == -1

    def is_full(self):
        return (self.rear + 1) % self.size == self.front

    def enqueue(self, item):
        if self.is_full():
            print("Circular Queue is full!")
            return
        if self.front == -1:              # First element
            self.front = 0
        self.rear = (self.rear + 1) % self.size   # Wrap around
        self.queue[self.rear] = item
        print(f"Enqueued: {item}")

    def dequeue(self):
        if self.is_empty():
            print("Circular Queue is empty!")
            return None
        item = self.queue[self.front]
        if self.front == self.rear:       # Last element removed
            self.front = -1
            self.rear  = -1
        else:
            self.front = (self.front + 1) % self.size  # Wrap around
        print(f"Dequeued: {item}")
        return item

    def display(self):
        if self.is_empty():
            print("Circular Queue is empty.")
            return
        i = self.front
        print("Circular Queue:", end=" ")
        while True:
            print(self.queue[i], end=" ")
            if i == self.rear:
                break
            i = (i + 1) % self.size
        print()


cq = CircularQueue(5)
cq.enqueue(10); cq.enqueue(20); cq.enqueue(30); cq.enqueue(40)
cq.display()    # 10 20 30 40
cq.dequeue()    # Dequeued: 10
cq.dequeue()    # Dequeued: 20
cq.enqueue(50)  # Reuses freed slot at index 0
cq.enqueue(60)  # Reuses freed slot at index 1
cq.display()    # 30 40 50 60
```

**C++ (simple):**

```cpp
// C++ — Circular Queue using modulo
#include <iostream>
#include <vector>

class CircularQueue {
    std::vector<int> data;
    int front_idx, rear_idx, capacity;
public:
    CircularQueue(int size)
        : data(size, 0), front_idx(-1), rear_idx(-1), capacity(size) {}

    bool isEmpty() const { return front_idx == -1; }
    bool isFull()  const { return (rear_idx + 1) % capacity == front_idx; }

    void enqueue(int item) {
        if (isFull()) { std::cout << "Circular Queue is full!\n"; return; }
        if (front_idx == -1) front_idx = 0;           // First element
        rear_idx = (rear_idx + 1) % capacity;         // Wrap around
        data[rear_idx] = item;
        std::cout << "Enqueued: " << item << "\n";
    }

    int dequeue() {
        if (isEmpty()) { std::cout << "Circular Queue is empty!\n"; return -1; }
        int item = data[front_idx];
        if (front_idx == rear_idx)                    // Last element removed
            front_idx = rear_idx = -1;
        else
            front_idx = (front_idx + 1) % capacity;  // Wrap around
        std::cout << "Dequeued: " << item << "\n";
        return item;
    }

    void display() const {
        if (isEmpty()) { std::cout << "Circular Queue is empty.\n"; return; }
        std::cout << "Circular Queue: ";
        int i = front_idx;
        while (true) {
            std::cout << data[i] << " ";
            if (i == rear_idx) break;
            i = (i + 1) % capacity;
        }
        std::cout << "\n";
    }
};

int main() {
    CircularQueue cq(5);
    cq.enqueue(10); cq.enqueue(20); cq.enqueue(30); cq.enqueue(40);
    cq.display();   // 10 20 30 40
    cq.dequeue();   // Dequeued: 10
    cq.dequeue();   // Dequeued: 20
    cq.enqueue(50); // Reuses freed slot
    cq.enqueue(60);
    cq.display();   // 30 40 50 60
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — LeetCode #622: Design Circular Queue
#include <vector>
using namespace std;

class MyCircularQueue {
    vector<int> data;
    int front_idx, rear_idx, capacity, count;
public:
    MyCircularQueue(int k)
        : data(k, 0), front_idx(0), rear_idx(-1), capacity(k), count(0) {}

    bool enQueue(int value) {
        if (isFull()) return false;                      // no space available
        rear_idx = (rear_idx + 1) % capacity;           // advance rear with wrap
        data[rear_idx] = value;                          // store value at new rear
        count++;
        return true;
    }

    bool deQueue() {
        if (isEmpty()) return false;                     // nothing to remove
        front_idx = (front_idx + 1) % capacity;         // advance front with wrap
        count--;
        return true;
    }

    int Front() {
        return isEmpty() ? -1 : data[front_idx];        // front element or -1
    }

    int Rear() {
        return isEmpty() ? -1 : data[rear_idx];         // rear element or -1
    }

    bool isEmpty() { return count == 0; }               // true if no elements
    bool isFull()  { return count == capacity; }        // true if all slots used
};
```

---

## 8. Simple Queue vs Circular Queue

| Feature           | Simple Queue               | Circular Queue                    |
| ----------------- | -------------------------- | --------------------------------- |
| Memory usage      | Wastes freed slots         | Reuses all slots                  |
| Rear wraps around | No                         | Yes — using `% size`              |
| Full condition    | `rear == size - 1`         | `(rear + 1) % size == front`      |
| Empty condition   | `front == -1`              | `front == -1`                     |
| Best for          | Simple, short-lived queues | Buffers, schedulers, ring buffers |
| Space efficiency  | Lower                      | Higher                            |

---

## 9. Queue Using Python `collections.deque` / C++ `std::queue`

Both languages offer built-in queue abstractions that handle all the pointer bookkeeping for you.

### Python — `collections.deque`

Python's `deque` supports $O(1)$ appends and pops from **both ends**, making it the recommended production queue.

```python
# Python — Efficient queue using collections.deque
from collections import deque

q = deque()

# Enqueue — add to the right (rear)
q.append(1)
q.append(2)
q.append(3)
print("Queue:", q)            # deque([1, 2, 3])

# Dequeue — remove from the left (front)  O(1)
q.popleft()
print("After dequeue:", q)    # deque([2, 3])

# Peek
print("Front element:", q[0]) # 2
```

**C++ (simple):**

```cpp
// C++ — Efficient queue using std::queue
#include <iostream>
#include <queue>

int main() {
    std::queue<int> q;

    // Enqueue
    q.push(1);                                             // add 1 to the rear
    q.push(2);                                             // add 2 to the rear
    q.push(3);                                             // add 3 to the rear

    // Peek front
    std::cout << "Front: " << q.front() << "\n";   // 1

    // Dequeue
    q.pop();                                               // remove from front
    std::cout << "After dequeue, front: " << q.front() << "\n";  // 2

    std::cout << "Size: " << q.size() << "\n";     // 2
    std::cout << "Empty: " << q.empty() << "\n";   // 0 (false)
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — std::queue<int> operations in class Solution
#include <iostream>
#include <queue>
using namespace std;

class Solution {
public:
    void demonstrateBuiltinQueue() {
        queue<int> q;              // built-in FIFO queue backed by std::deque

        q.push(1);                 // enqueue at rear — O(1)
        q.push(2);                 // enqueue at rear — O(1)
        q.push(3);                 // enqueue at rear — O(1)

        cout << "Front: " << q.front() << "\n";   // peek front: 1
        q.pop();                                   // dequeue from front — O(1)
        cout << "After dequeue: " << q.front() << "\n"; // 2
        cout << "Size: "  << q.size()  << "\n";   // 2
        cout << "Empty: " << q.empty() << "\n";   // 0 (false)
    }
};
```

Use `collections.deque` / `std::queue` for all production code. Roll your own implementation only when you need to understand internals or when practising for interviews.

---

## 10. Real-World Applications

| Use Case                | Why a Queue?                                                       |
| ----------------------- | ------------------------------------------------------------------ |
| Print spooler           | Documents printed in the order they were submitted                 |
| CPU task scheduling     | Processes wait in a queue for CPU time                             |
| BFS graph traversal     | Nodes are processed level by level (covered in the Graphs section) |
| Keyboard input buffer   | Keystrokes stored and processed in arrival order                   |
| Network packet handling | Packets queued and forwarded in sequence                           |
| Audio / video streaming | Ring buffer (circular queue) feeds the playback engine             |

Every time **order matters** and you need **fair processing**, a queue is likely the right tool.

---

## 11. Time and Space Complexity

| Operation | Simple Queue (array) | Circular Queue | `deque` / `std::queue` |
| --------- | -------------------- | -------------- | ---------------------- |
| Enqueue   | $O(1)$               | $O(1)$         | $O(1)$                 |
| Dequeue   | $O(1)^*$             | $O(1)$         | $O(1)$                 |
| Peek      | $O(1)$               | $O(1)$         | $O(1)$                 |
| Space     | $O(n)$               | $O(n)$         | $O(n)$                 |

$^*$ $O(1)$ with pointer-based implementation; $O(n)$ if using `list.pop(0)` in Python.

The circular queue improves **space utilisation** without changing time complexity.

---

## 12. Common Mistakes to Avoid

**1. Confusing stack (LIFO) with queue (FIFO)**

They have opposite removal behaviour. A stack is a pile of plates; a queue is a line at a counter.

**2. Forgetting to reset `front` and `rear` to `-1` on last dequeue**

When the queue transitions from one element to empty, both pointers must reset:

```python
if self.front == self.rear:   # last element
    self.front = -1
    self.rear  = -1
```

**3. Omitting the modulo in a circular queue**

Without `% size`, the index goes out of bounds on wrap-around:

```python
# Wrong — no wrap
self.rear = self.rear + 1

# Correct — wrap around
self.rear = (self.rear + 1) % self.size
```

**4. Using `list.pop(0)` in Python for large queues**

`list.pop(0)` is $O(n)$ — it shifts every element one position. Use `collections.deque.popleft()` which is $O(1)$.

---

## 13. Key Takeaways

- A queue follows **FIFO** — the first element added is always the first removed.
- The three core operations — enqueue, dequeue, and peek — all run in $O(1)$ time with a proper pointer-based implementation.
- A **simple array queue** wastes memory because freed front slots cannot be reused.
- A **circular queue** fixes this by wrapping the rear pointer using `(rear + 1) % size`, reusing every slot.
- The full condition in a circular queue is `(rear + 1) % size == front`; the empty condition is `front == -1`.
- In Python, use `collections.deque` for $O(1)$ enqueue and dequeue in production code.
- In C++, use `std::queue<T>` for a ready-made FIFO queue backed by `std::deque`.
- Circular queues are the foundation of ring buffers used in OS schedulers, audio engines, and network packet handling.

---

## 14. FAQs

**What is the difference between a stack and a queue?**

A stack follows LIFO — the most recently added element is removed first (pile of plates). A queue follows FIFO — the oldest element is removed first (line at a counter). They are opposite removal strategies.

**When should I use a circular queue instead of a simple queue?**

Use a circular queue when you have a fixed-size buffer and need to continuously reuse freed positions — audio streaming, CPU scheduling, and network buffers are classic examples.

**Is a deque the same as a queue?**

A deque (double-ended queue) allows insertion and removal from **both ends**. A regular queue only allows insertion at the rear and removal from the front. Python's `collections.deque` can be used as an efficient regular queue by sticking to `append` (rear) and `popleft` (front).

**Why does the circular queue full condition use `(rear + 1) % size == front` and not `rear == front`?**

If we used `rear == front` as the full condition it would be identical to the empty condition (`front == -1` or when they meet after wrapping). The standard fix is to sacrifice one slot: the queue is considered full when the next position of `rear` would equal `front`, keeping one slot always empty as a sentinel.

**Can a queue be implemented using two stacks?**

Yes — this is a classic interview problem covered in a later post. You use one stack for enqueue and a second for dequeue, amortising the reversal cost to $O(1)$ per operation on average.
