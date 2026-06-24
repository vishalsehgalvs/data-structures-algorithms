# Singly vs Doubly Linked Lists — Differences and Use Cases

> **One-line summary:**
> A singly linked list uses one `next` pointer per node (forward traversal only, less memory); a doubly linked list adds a `prev` pointer (bidirectional traversal, $O(1)$ deletion of a known node) — pick singly for simplicity, doubly when you need to move backward or delete nodes efficiently.

---

## Table of Contents

1. [Quick Recap](#1-quick-recap)
2. [Singly Linked List](#2-singly-linked-list)
3. [Node Structure — Singly](#3-node-structure--singly)
4. [Building a Singly Linked List](#4-building-a-singly-linked-list)
5. [Doubly Linked List](#5-doubly-linked-list)
6. [Node Structure — Doubly](#6-node-structure--doubly)
7. [Building a Doubly Linked List](#7-building-a-doubly-linked-list)
8. [Key Differences at a Glance](#8-key-differences-at-a-glance)
9. [Inserting a Node — Side-by-Side](#9-inserting-a-node--side-by-side)
10. [Deleting a Node — Where Doubly Shines](#10-deleting-a-node--where-doubly-shines)
11. [Real-World Use Cases](#11-real-world-use-cases)
12. [Time Complexity Summary](#12-time-complexity-summary)
13. [Common Mistakes Beginners Make](#13-common-mistakes-beginners-make)
14. [Key Takeaways](#14-key-takeaways)
15. [FAQs](#15-faqs)

---

## 1. Quick Recap

We covered the fundamentals of linked lists in the previous post — nodes, head, tail, traversal, insertion, deletion. Now we go one level deeper and compare the two most common variants: **singly** and **doubly** linked lists.

Both store data in nodes. The difference is entirely in **how those nodes connect to each other**.

```
Singly:   [10|●]──→[20|●]──→[30|╳]
           one pointer per node (next only)

Doubly:   [╳|10|●]⟷[●|20|●]⟷[●|30|╳]
           two pointers per node (prev + next)
```

---

## 2. Singly Linked List

A singly linked list is the simpler version. Each node has **two parts**: the data and a single pointer called `next`. That pointer points to the next node. Think of it as a **one-way street** — you can only move forward.

The last node's `next` pointer is `None` / `nullptr`, marking the end of the chain. You always start from the **head** and walk forward one step at a time.

```
head
  ↓
[10 | ●]──→[20 | ●]──→[30 | ╳]
             ↑ next           ↑ next = None (tail)
```

---

## 3. Node Structure — Singly

### Python

```python
# Python — Node for a singly linked list
class Node:
    def __init__(self, data):
        self.data = data   # The value stored in this node
        self.next = None   # Pointer to the next node (default None)
```

### C++ (simple)

```cpp
// C++ — Node for a singly linked list
struct Node {
    int data;
    Node* next;

    Node(int val) : data(val), next(nullptr) {}
};
```

### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — LeetCode's singly linked list node
struct ListNode {
    int val;          // value stored in this node
    ListNode* next;   // pointer to the next node (null at the tail)
    ListNode(int x) : val(x), next(nullptr) {}
};

// In LeetCode problems, ListNode is defined globally.
// A Solution method receives a ListNode* head as its parameter.
class Solution {
public:
    // Example: count the number of nodes in the list
    int length(ListNode* head) {
        int count = 0;
        ListNode* current = head;      // start at head
        while (current != nullptr) {
            count++;                    // count this node
            current = current->next;   // advance to next
        }
        return count;
    }
};
```

---

## 4. Building a Singly Linked List

### Python

```python
# Python — Build and traverse a singly linked list
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None


# Create three nodes
node1 = Node(10)
node2 = Node(20)
node3 = Node(30)

# Connect the nodes
node1.next = node2
node2.next = node3

# Traverse forward from head
current = node1
while current is not None:
    print(current.data)
    current = current.next
# Output: 10  20  30
```

### C++ (simple)

```cpp
// C++ — Build and traverse a singly linked list
#include <iostream>

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

int main() {
    Node* node1 = new Node(10);
    Node* node2 = new Node(20);
    Node* node3 = new Node(30);

    // Connect the nodes
    node1->next = node2;
    node2->next = node3;

    // Traverse forward from head
    Node* current = node1;
    while (current != nullptr) {
        std::cout << current->data << "\n";
        current = current->next;
    }
    // Output: 10  20  30

    delete node1; delete node2; delete node3;
}
```

### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Build and traverse using ListNode
#include <iostream>

struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    // Build list 10 → 20 → 30 and print each node value
    void buildAndTraverse() {
        ListNode* node1 = new ListNode(10);   // first node
        ListNode* node2 = new ListNode(20);   // second node
        ListNode* node3 = new ListNode(30);   // third node

        node1->next = node2;   // node1 → node2
        node2->next = node3;   // node2 → node3

        // Walk forward from head to tail
        ListNode* current = node1;
        while (current != nullptr) {
            std::cout << current->val << "\n";   // print node value
            current = current->next;              // advance to next node
        }
        // Output: 10  20  30

        delete node1; delete node2; delete node3;
    }
};
```

We start at `node1` and keep moving forward using `next` until we hit `None` / `nullptr`. That is traversal in a singly linked list.

---

## 5. Doubly Linked List

A doubly linked list gives each node **two pointers**: `next` to move forward and `prev` to move backward. Think of it as a **two-way street**.

This extra pointer makes some operations easier — traversing backward, or deleting a node when you only have a reference to that node. The trade-off is a little extra memory per node.

```
head                                          tail
  ↓                                             ↓
[╳|10|●]⟷[●|20|●]⟷[●|30|╳]
 prev=None       prev    next   next=None
```

---

## 6. Node Structure — Doubly

### Python

```python
# Python — Node for a doubly linked list
class Node:
    def __init__(self, data):
        self.data = data   # The value stored
        self.prev = None   # Pointer to the previous node
        self.next = None   # Pointer to the next node
```

### C++ (simple)

```cpp
// C++ — Node for a doubly linked list
struct Node {
    int data;
    Node* prev;
    Node* next;

    Node(int val) : data(val), prev(nullptr), next(nullptr) {}
};
```

### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Doubly linked list node using ListNode
// Note: standard LeetCode uses singly ListNode; doubly is shown here for completeness
struct ListNode {
    int val;          // value stored in this node
    ListNode* prev;   // pointer to the previous node (null at the head)
    ListNode* next;   // pointer to the next node (null at the tail)
    ListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class Solution {
public:
    // Example: traverse the list forward
    void traverseForward(ListNode* head) {
        ListNode* current = head;              // start at the head
        while (current != nullptr) {
            std::cout << current->val << "\n";  // print current node value
            current = current->next;            // advance to next node
        }
    }
};
```

---

## 7. Building a Doubly Linked List

### Python

```python
# Python — Build and traverse a doubly linked list (forward + backward)
class Node:
    def __init__(self, data):
        self.data = data
        self.prev = None
        self.next = None


node1 = Node(10)
node2 = Node(20)
node3 = Node(30)

# Forward links
node1.next = node2
node2.next = node3

# Backward links
node2.prev = node1
node3.prev = node2

# Forward traversal
print("Forward:")
current = node1
while current is not None:
    print(current.data)
    current = current.next
# Output: 10  20  30

# Backward traversal (start from tail)
print("Backward:")
current = node3
while current is not None:
    print(current.data)
    current = current.prev
# Output: 30  20  10
```

### C++ (simple)

```cpp
// C++ — Build and traverse a doubly linked list (forward + backward)
#include <iostream>

struct Node {
    int data;
    Node* prev;
    Node* next;
    Node(int val) : data(val), prev(nullptr), next(nullptr) {}
};

int main() {
    Node* node1 = new Node(10);
    Node* node2 = new Node(20);
    Node* node3 = new Node(30);

    // Forward links
    node1->next = node2;
    node2->next = node3;

    // Backward links
    node2->prev = node1;
    node3->prev = node2;

    // Forward traversal
    std::cout << "Forward:\n";
    Node* current = node1;
    while (current != nullptr) {
        std::cout << current->data << "\n";
        current = current->next;
    }
    // Output: 10  20  30

    // Backward traversal (start from tail)
    std::cout << "Backward:\n";
    current = node3;
    while (current != nullptr) {
        std::cout << current->data << "\n";
        current = current->prev;
    }
    // Output: 30  20  10

    delete node1; delete node2; delete node3;
}
```

### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Build and traverse a doubly linked list
#include <iostream>

struct ListNode {
    int val;
    ListNode* prev;
    ListNode* next;
    ListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class Solution {
public:
    void buildAndTraverse() {
        ListNode* node1 = new ListNode(10);   // first node
        ListNode* node2 = new ListNode(20);   // second node
        ListNode* node3 = new ListNode(30);   // third node

        // Forward links (next pointers)
        node1->next = node2;   // node1 → node2
        node2->next = node3;   // node2 → node3

        // Backward links (prev pointers)
        node2->prev = node1;   // node2 ← node1
        node3->prev = node2;   // node3 ← node2

        // Forward traversal using next
        std::cout << "Forward:\n";
        ListNode* current = node1;
        while (current != nullptr) {
            std::cout << current->val << "\n";   // print node value
            current = current->next;              // advance forward
        }

        // Backward traversal using prev (start from tail)
        std::cout << "Backward:\n";
        current = node3;
        while (current != nullptr) {
            std::cout << current->val << "\n";   // print node value
            current = current->prev;              // step backward
        }

        delete node1; delete node2; delete node3;
    }
};
```

Notice how we can walk in both directions — something a singly linked list cannot do without extra tricks.

---

## 8. Key Differences at a Glance

| Feature                   | Singly Linked List            | Doubly Linked List                |
| ------------------------- | ----------------------------- | --------------------------------- |
| Pointers per node         | 1 (`next` only)               | 2 (`next` and `prev`)             |
| Traversal direction       | Forward only                  | Forward and backward              |
| Memory per node           | Less                          | More (extra pointer ~8 bytes)     |
| Delete a known node       | Need previous node reference  | $O(1)$ — `prev` is already stored |
| Insert / delete at tail   | $O(n)$ without tail pointer   | $O(1)$ with tail pointer          |
| Implementation complexity | Simpler                       | Slightly more complex             |
| Common use cases          | Stacks, simple queues, chains | Browser history, undo-redo, deque |

The extra `prev` pointer in a doubly linked list is very helpful in practice. But if you do not need backward traversal, a singly linked list saves memory and keeps things simple.

---

## 9. Inserting a Node — Side-by-Side

### Insert at Head — Singly

Only one pointer update needed: point the new node's `next` to the old head, then reassign head.

#### Python

```python
# Python — Insert at head (singly)
def insert_at_head_singly(head, data):
    new_node = Node(data)
    new_node.next = head   # New node points to current head
    return new_node        # New node becomes the new head

# Before: head -> [10] -> [20] -> None
# After:  head -> [5]  -> [10] -> [20] -> None
```

#### C++ (simple)

```cpp
// C++ — Insert at head (singly)
Node* insertAtHeadSingly(Node* head, int data) {
    Node* new_node = new Node(data);
    new_node->next = head;   // New node points to current head
    return new_node;         // New node becomes the new head
}

// Before: head -> [10] -> [20] -> nullptr
// After:  head -> [5]  -> [10] -> [20] -> nullptr
```

#### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Prepend a node to a singly linked list
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* insertAtHeadSingly(ListNode* head, int val) {
        ListNode* new_node = new ListNode(val);   // create the new node
        new_node->next = head;                     // point new node to current head
        return new_node;                           // new node is the new head
    }
};

// Before: head -> [10] -> [20] -> nullptr
// After:  head -> [5]  -> [10] -> [20] -> nullptr
```

---

### Insert at Head — Doubly

One extra step: update the old head's `prev` pointer to point back at the new node.

#### Python

```python
# Python — Insert at head (doubly)
def insert_at_head_doubly(head, data):
    new_node = Node(data)
    new_node.next = head         # New node's next → old head
    if head is not None:
        head.prev = new_node     # Old head's prev ← new node
    return new_node              # New node becomes the new head

# Before: None <-> [10] <-> [20] <-> None
# After:  None <-> [5]  <-> [10] <-> [20] <-> None
```

#### C++ (simple)

```cpp
// C++ — Insert at head (doubly)
Node* insertAtHeadDoubly(Node* head, int data) {
    Node* new_node = new Node(data);
    new_node->next = head;           // New node's next → old head
    if (head != nullptr)
        head->prev = new_node;       // Old head's prev ← new node
    return new_node;                 // New node becomes the new head
}

// Before: nullptr <-> [10] <-> [20] <-> nullptr
// After:  nullptr <-> [5]  <-> [10] <-> [20] <-> nullptr
```

#### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Prepend a node to a doubly linked list
struct ListNode {
    int val;
    ListNode* prev;
    ListNode* next;
    ListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class Solution {
public:
    ListNode* insertAtHeadDoubly(ListNode* head, int val) {
        ListNode* new_node = new ListNode(val);   // create the new node
        new_node->next = head;                     // new node's next → old head
        if (head != nullptr)
            head->prev = new_node;                 // old head's prev ← new node
        return new_node;                           // new node is the new head
    }
};

// Before: nullptr <-> [10] <-> [20] <-> nullptr
// After:  nullptr <-> [5]  <-> [10] <-> [20] <-> nullptr
```

> **Rule of thumb for doubly lists:** every insertion and deletion must update **both** `next` and `prev`. Missing one breaks the chain.

---

## 10. Deleting a Node — Where Doubly Shines

Deleting a node is where doubly linked lists prove their value. In a singly list you need to find the **previous** node first. In a doubly list that reference is already stored inside the node.

### Deletion in a Singly Linked List — $O(n)$

#### Python

```python
# Python — Delete node with value 20 (singly)
# Must traverse from head to find the node BEFORE 20
current = head
while current.next is not None:
    if current.next.data == 20:
        current.next = current.next.next   # Bypass the node
        break
    current = current.next
```

#### C++ (simple)

```cpp
// C++ — Delete node with value 20 (singly)
// Must traverse from head to find the node BEFORE 20
Node* current = head;
while (current->next != nullptr) {
    if (current->next->data == 20) {
        Node* to_del = current->next;
        current->next = current->next->next;   // Bypass the node
        delete to_del;
        break;
    }
    current = current->next;
}
```

#### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Delete first node with given value (singly)
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* deleteByValue(ListNode* head, int value) {
        if (head == nullptr) return nullptr;         // empty list
        if (head->val == value) {                    // head is the target node
            ListNode* to_del = head;
            head = head->next;                       // advance head past target
            delete to_del;
            return head;
        }
        ListNode* current = head;
        while (current->next != nullptr) {           // walk from head
            if (current->next->val == value) {
                ListNode* to_del = current->next;
                current->next = current->next->next; // bypass the target node
                delete to_del;
                return head;
            }
            current = current->next;                 // keep walking forward
        }
        return head;
    }
};
```

We had to walk from the head — worst case $O(n)$.

---

### Deletion in a Doubly Linked List — $O(1)$

If you already have a pointer to the node, you can delete it immediately — no traversal needed.

#### Python

```python
# Python — Delete a node given a direct reference (doubly)
def delete_node(node):
    if node.prev is not None:
        node.prev.next = node.next    # Previous node skips over current
    if node.next is not None:
        node.next.prev = node.prev    # Next node links back to previous
    # node is now disconnected — Python GC will free it
```

#### C++ (simple)

```cpp
// C++ — Delete a node given a direct reference (doubly)
void deleteNode(Node* node) {
    if (node->prev != nullptr)
        node->prev->next = node->next;   // Previous node skips over current
    if (node->next != nullptr)
        node->next->prev = node->prev;   // Next node links back to previous
    delete node;                          // Free the memory
}
```

#### C++ (LeetCode class style)

```cpp
// C++ (LeetCode class style) — Delete a node by direct pointer (doubly)
struct ListNode {
    int val;
    ListNode* prev;
    ListNode* next;
    ListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class Solution {
public:
    void deleteNode(ListNode* node) {
        if (node->prev != nullptr)
            node->prev->next = node->next;   // previous node skips over current
        if (node->next != nullptr)
            node->next->prev = node->prev;   // next node links back to previous
        delete node;                          // free the memory
    }
};
```

This $O(1)$ deletion of a known node is the standout advantage of a doubly linked list. In many algorithms and system designs — LRU caches, text editor buffers — this makes a real difference.

---

## 11. Real-World Use Cases

### When to Use a Singly Linked List

- Memory is tight and you want a lightweight structure.
- You only need to traverse in one direction (e.g., processing a task queue).
- Implementing a **stack** (push/pop from the head).
- Simple chains where backward traversal is never needed.

### When to Use a Doubly Linked List

- Moving both forward and backward — media player previous/next buttons.
- **Browser history** (back and forward navigation).
- **Undo-redo** in a text editor.
- **Deque** (double-ended queue) — insert and delete from both ends efficiently.
- **LRU cache** — requires $O(1)$ removal of any node.

> A good analogy: a singly linked list is like reading a book page by page from front to back. A doubly linked list is like using a bookmark that lets you flip both forward and backward from any page.

---

## 12. Time Complexity Summary

Assumes access to both `head` and `tail` pointers.

| Operation               | Singly Linked List                   | Doubly Linked List        |
| ----------------------- | ------------------------------------ | ------------------------- |
| Access by index         | $O(n)$                               | $O(n)$                    |
| Insert at head          | $O(1)$                               | $O(1)$                    |
| Insert at tail          | $O(n)$ or $O(1)$ with tail pointer   | $O(1)$ with tail pointer  |
| Delete at head          | $O(1)$                               | $O(1)$                    |
| Delete a **known** node | $O(n)$ — need to find previous first | $O(1)$ — `prev` is stored |
| Backward traversal      | Not possible without reversal        | $O(n)$                    |

The $O(1)$ deletion of a known node is the headline advantage. In most other operations the two types perform identically.

---

## 13. Common Mistakes Beginners Make

**1. Forgetting to update `prev` in doubly lists**

Every insertion and deletion must update **both** `next` and `prev`. Missing one silently corrupts the list.

```python
# Wrong — only updates next, prev is stale
new_node.next = head

# Correct — update both directions
new_node.next = head
if head is not None:
    head.prev = new_node
```

**2. Not handling the empty list (`None` head) case**

Always check `if head is None` / `if head == nullptr` before accessing `head.data` or `head.next`. Skipping this causes a crash.

**3. Losing the head reference**

If you reassign `head` without saving the old value first, you lose access to the entire list and leak memory in C++.

**4. Confusing tail checks**

- Singly tail: `next == None`
- Doubly head: `prev == None`
- Doubly tail: `next == None`

All three are distinct; mixing them up causes infinite loops or off-by-one errors.

---

## 14. Key Takeaways

- A **singly linked list** has one pointer per node (`next`). Traversal is forward-only. Simpler to implement, uses less memory.
- A **doubly linked list** has two pointers per node (`prev` + `next`). Supports bidirectional traversal and $O(1)$ deletion of a known node.
- Every doubly list operation must update **both** pointers — missing one breaks the chain.
- Use singly when you only traverse forward and want simplicity. Use doubly when you need backward movement or efficient arbitrary deletion.
- The extra `prev` pointer costs ~8 bytes per node. For a million nodes that is ~8 MB — worth considering in memory-constrained environments.
- In C++, always `delete` removed nodes; in Python, the garbage collector handles cleanup.
- Doubly linked lists underpin practical data structures like LRU caches, browser history, and undo-redo systems.

---

## 15. FAQs

**Does a doubly linked list always use more memory than a singly linked list?**

Yes. Each node stores an extra `prev` pointer (~8 bytes on a 64-bit system). For a million nodes that is an extra 8 MB. It is usually worth the trade-off for the flexibility, but in memory-limited environments the singly list is the leaner choice.

**Can I traverse a singly linked list backward?**

Not directly. You would need to reverse the list first, or use recursion to simulate backward traversal by unwinding the call stack. If you frequently need backward traversal, always choose a doubly linked list.

**Which type should beginners learn first?**

Start with singly linked lists — they teach the core idea of nodes and pointers without the added complexity of two directions. Once insertions, deletions, and traversals feel natural, doubly linked lists are a logical extension.

**Is there a circular variant of these lists?**

Yes. A **circular singly linked list** has the tail's `next` pointing back to the head instead of `None`. A **circular doubly linked list** does the same for both `next` and `prev`. They are useful for round-robin schedulers and cyclic buffers. Cycle detection is covered in the next post in this series.

**When would I choose a doubly list over a hash map for $O(1)$ deletion?**

A hash map gives $O(1)$ average-case lookup and deletion by key, but it does not preserve insertion order in the same way and has higher constant-factor overhead. A doubly linked list gives $O(1)$ deletion by pointer reference and preserves order naturally — which is exactly why LRU caches combine both: a hash map for $O(1)$ lookup and a doubly linked list for $O(1)$ ordered deletion.
