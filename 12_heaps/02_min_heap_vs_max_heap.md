# Min Heap vs Max Heap: Differences Explained for Beginners

## What is a Heap?

A heap is a special tree-based data structure that satisfies one important rule called the **heap property**. Every heap is a **complete binary tree** — all levels are filled from left to right.

There are two types of heaps: the **min heap** and the **max heap**. Both follow the same structure rules but differ in which element stays at the top.

---

## Min Heap Explained

In a **min heap**, the smallest element is always at the root. Every parent node holds a value that is **less than or equal to** its children.

Think of it like a leaderboard where the lowest score is always shown first. No matter how many elements are inside, the root is always the smallest.

### Min Heap Example

Inserting: 10, 5, 3, 8, 1

```
Min Heap after inserting [10, 5, 3, 8, 1]:

        1         ← root (smallest)
       / \
      5   3
     / \
    8   10

Rule: every parent is SMALLER than its children.
```

---

## Max Heap Explained

In a **max heap**, the largest element is always at the root. Every parent node holds a value that is **greater than or equal to** its children.

Think of a scoreboard where the highest score is always shown at the top. If you peek at the top of a max heap, you always get the maximum value instantly.

### Max Heap Example

Inserting the same numbers: 10, 5, 3, 8, 1

```
Max Heap after inserting [10, 5, 3, 8, 1]:

        10        ← root (largest)
       /  \
      8    3
     / \
    5   1

Rule: every parent is LARGER than its children.
```

---

## Side-by-Side Comparison

| Feature | Min Heap | Max Heap |
|---------|----------|----------|
| Root Element | Smallest value | Largest value |
| Parent vs Child | Parent ≤ children | Parent ≥ children |
| Quick Access To | Minimum element | Maximum element |
| Common Use Case | Find smallest items quickly | Find largest items quickly |
| Tree Type | Complete binary tree | Complete binary tree |
| Insert Time | O(log n) | O(log n) |
| Delete Min/Max Time | O(log n) | O(log n) |
| Peek Min/Max Time | O(1) | O(1) |

Both heaps share the same time complexities. The difference is purely about what gets prioritized — the smallest or the largest.

---

## Heap Array Representation

Since a heap is a complete binary tree, we store it in a plain array. Index formulas (0-based):

```
Parent of node at index i  →  (i - 1) / 2
Left child of node at i    →  2 * i + 1
Right child of node at i   →  2 * i + 2

----------------------------------------------
Min Heap as array: [1, 5, 3, 8, 10]

  Index:  0  1  2  3   4
  Value:  1  5  3  8  10

  Parent of index 1 (value 5):  (1-1)/2 = 0 → value 1  ✓ (1 < 5)
  Left child of index 0 (1):    2*0+1 = 1   → value 5
  Right child of index 0 (1):   2*0+2 = 2   → value 3

----------------------------------------------
Max Heap as array: [10, 8, 3, 5, 1]

  Index:  0   1  2  3  4
  Value: 10   8  3  5  1

  Parent of index 1 (value 8): (1-1)/2 = 0 → value 10  ✓ (10 > 8)
  Left child of index 0 (10):  2*0+1 = 1   → value 8
  Right child of index 0 (10): 2*0+2 = 2   → value 3
```

---

## The Heapify Operation

When we insert or remove an element, the heap property might break temporarily. **Heapify** is the process of fixing this by comparing a node with its parent or children and swapping when needed.

### Heapify Up — After Insertion

When inserting, place the new element at the end of the array, then **bubble it up** until the heap property is satisfied.

```
Insert 2 into Min Heap [1, 5, 3, 8, 10]:

Step 1: Place 2 at end
  Array: [1, 5, 3, 8, 10, 2]
  Index of 2 = 5 → parent = (5-1)/2 = 2 → value 3

Step 2: 2 < 3, so swap
  Array: [1, 5, 2, 8, 10, 3]
  2 is now at index 2 → parent = (2-1)/2 = 0 → value 1

Step 3: 2 > 1, stop — heap property satisfied
  Final: [1, 5, 2, 8, 10, 3]
```

#### Python

```python
import heapq

def heapify_up_demo():
    min_heap = [1, 5, 3, 8, 10]
    heapq.heappush(min_heap, 2)     # insert 2 — heapq handles bubbling up internally
    print(min_heap)                 # Output: [1, 5, 2, 8, 10, 3]
    print("Root (min):", min_heap[0])  # Output: 1

heapify_up_demo()
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Bubble a newly inserted element up to restore min heap property
void heapifyUp(vector<int>& heap, int index) {
    while (index > 0) {
        int parent = (index - 1) / 2;           // find parent index

        if (heap[index] < heap[parent]) {        // child smaller than parent → swap
            swap(heap[index], heap[parent]);
            index = parent;                      // move up to parent position
        } else {
            break;                               // heap property satisfied, stop
        }
    }
}

// Insert a value into a min heap
void insert(vector<int>& heap, int val) {
    heap.push_back(val);                         // place at end
    heapifyUp(heap, heap.size() - 1);            // bubble it up
}

int main() {
    vector<int> minHeap = {1, 5, 3, 8, 10};
    insert(minHeap, 2);                          // insert 2

    for (int x : minHeap)
        cout << x << " ";    // Output: 1 5 2 8 10 3
    cout << endl;
    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    // Insert a new value and restore heap property
    void insert(int val) {
        heap.push_back(val);                    // place at end of array
        heapifyUp(heap.size() - 1);             // bubble up from last index
    }

    // Peek at minimum without removing
    int peek() {
        return heap[0];                         // root is always the minimum
    }

    vector<int> getHeap() { return heap; }

private:
    vector<int> heap;

    void heapifyUp(int index) {
        while (index > 0) {
            int parent = (index - 1) / 2;       // parent index formula

            if (heap[index] < heap[parent]) {   // child < parent → swap up
                swap(heap[index], heap[parent]);
                index = parent;
            } else {
                break;                          // heap property restored
            }
        }
    }
};
```

---

### Heapify Down — After Deletion

When removing the root, replace it with the last element, then **push it down** until the heap property is satisfied.

```
Remove root (1) from Min Heap [1, 5, 2, 8, 10, 3]:

Step 1: Replace root with last element
  Array: [3, 5, 2, 8, 10]

Step 2: Compare 3 with children: left=5 (idx 1), right=2 (idx 2)
  Smallest child is 2 at index 2 → 3 > 2, swap
  Array: [2, 5, 3, 8, 10]

Step 3: 3 is at index 2. Children would be at index 5, 6 — out of bounds. Stop.

Final Min Heap: [2, 5, 3, 8, 10]
```

#### Python

```python
import heapq

def heapify_down_demo():
    min_heap = [1, 5, 2, 8, 10, 3]
    removed = heapq.heappop(min_heap)   # removes root (min), heapq handles sift-down
    print("Removed:", removed)          # Output: Removed: 1
    print("Heap after pop:", min_heap)  # Output: [2, 5, 3, 8, 10]

heapify_down_demo()
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Push element at index down to restore min heap property
void heapifyDown(vector<int>& heap, int index) {
    int n = heap.size();

    while (true) {
        int smallest = index;               // assume current is smallest
        int left  = 2 * index + 1;         // left child index
        int right = 2 * index + 2;         // right child index

        // Check if left child is smaller
        if (left < n && heap[left] < heap[smallest])
            smallest = left;

        // Check if right child is even smaller
        if (right < n && heap[right] < heap[smallest])
            smallest = right;

        if (smallest != index) {            // a child is smaller → swap and continue
            swap(heap[index], heap[smallest]);
            index = smallest;
        } else {
            break;                          // current is already the smallest
        }
    }
}

// Remove and return the minimum (root) from the heap
int removeMin(vector<int>& heap) {
    int root = heap[0];                    // save the root value
    heap[0] = heap.back();                 // move last element to root
    heap.pop_back();                       // remove last element
    heapifyDown(heap, 0);                  // push new root down
    return root;
}

int main() {
    vector<int> minHeap = {1, 5, 2, 8, 10, 3};
    cout << "Removed: " << removeMin(minHeap) << endl;  // Output: Removed: 1

    for (int x : minHeap) cout << x << " ";             // Output: 2 5 3 8 10
    cout << endl;
    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    // Remove and return the minimum element (root)
    int removeMin() {
        int root = heap[0];             // save root (minimum value)
        heap[0] = heap.back();          // replace root with last element
        heap.pop_back();                // shrink the array
        heapifyDown(0);                 // restore heap property by sifting down
        return root;
    }

    void insert(int val) {
        heap.push_back(val);
        heapifyUp(heap.size() - 1);
    }

    int peek() { return heap[0]; }

private:
    vector<int> heap;

    void heapifyUp(int index) {
        while (index > 0) {
            int parent = (index - 1) / 2;
            if (heap[index] < heap[parent]) {
                swap(heap[index], heap[parent]);
                index = parent;
            } else break;
        }
    }

    void heapifyDown(int index) {
        int n = heap.size();
        while (true) {
            int smallest = index;
            int left  = 2 * index + 1;
            int right = 2 * index + 2;

            if (left < n  && heap[left]  < heap[smallest]) smallest = left;
            if (right < n && heap[right] < heap[smallest]) smallest = right;

            if (smallest != index) {
                swap(heap[index], heap[smallest]);
                index = smallest;
            } else break;
        }
    }
};
```

---

## Python Implementation — Full Min and Max Heap

#### Python

```python
import heapq

# ---- MIN HEAP ----
min_heap = []
heapq.heappush(min_heap, 10)    # insert values one by one
heapq.heappush(min_heap, 5)
heapq.heappush(min_heap, 3)
heapq.heappush(min_heap, 8)

print("Min Heap array:", min_heap)      # [3, 8, 5, 10]
print("Smallest element:", min_heap[0]) # 3 — O(1) peek

smallest = heapq.heappop(min_heap)      # remove and return minimum
print("Popped:", smallest)              # 3
print("Heap after pop:", min_heap)      # [5, 8, 10]

# ---- MAX HEAP (negate values trick) ----
max_heap = []
heapq.heappush(max_heap, -10)   # store negated values
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -8)

print("Largest element:", -max_heap[0])         # 10 (negate back to get true max)
largest = -heapq.heappop(max_heap)              # pop and negate to get true value
print("Popped:", largest)                       # 10
```

#### C++ (simple)

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

int main() {
    // ---- MIN HEAP ----
    // greater<int> makes priority_queue a min heap
    priority_queue<int, vector<int>, greater<int>> minHeap;

    minHeap.push(10);   // insert values
    minHeap.push(5);
    minHeap.push(3);
    minHeap.push(8);

    cout << "Smallest: " << minHeap.top() << endl;  // 3 — O(1) peek
    minHeap.pop();                                  // remove minimum
    cout << "After pop: " << minHeap.top() << endl; // 5

    // ---- MAX HEAP (default in C++) ----
    priority_queue<int> maxHeap;    // max heap by default

    maxHeap.push(10);
    maxHeap.push(5);
    maxHeap.push(3);
    maxHeap.push(8);

    cout << "Largest: " << maxHeap.top() << endl;   // 10 — O(1) peek
    maxHeap.pop();                                  // remove maximum
    cout << "After pop: " << maxHeap.top() << endl; // 8

    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
#include <queue>
#include <vector>
using namespace std;

class Solution {
public:
    // Find the minimum element using a min heap
    int findMin(vector<int>& nums) {
        // Build min heap from all elements — O(n)
        priority_queue<int, vector<int>, greater<int>> minHeap(nums.begin(), nums.end());
        return minHeap.top();   // root is always the minimum — O(1)
    }

    // Find the maximum element using a max heap
    int findMax(vector<int>& nums) {
        // Build max heap from all elements — O(n)
        priority_queue<int> maxHeap(nums.begin(), nums.end());
        return maxHeap.top();   // root is always the maximum — O(1)
    }
};
```

---

## When to Use Min Heap vs Max Heap

| Situation | Use |
|-----------|-----|
| Find/remove smallest repeatedly | Min Heap |
| Shortest path (Dijkstra's) | Min Heap |
| Task scheduling by earliest deadline | Min Heap |
| Find/remove largest repeatedly | Max Heap |
| Track highest priority task | Max Heap |
| Top K smallest elements | Max Heap of size K |
| Top K largest elements | Min Heap of size K |

**Min Heap trick for Top K Largest:** Keep a min heap of size K. If it grows beyond K, remove the root. At the end, all K largest elements remain in the heap.

**Max Heap trick for Top K Smallest:** Keep a max heap of size K and do the opposite.

---

## Real-World Analogies

| Analogy | Heap Type |
|---------|-----------|
| Hospital triage — most critical patient treated first | Min Heap (lowest priority number = most urgent) |
| Hiring scoreboard — highest score candidate at top | Max Heap |
| Task list sorted by deadline — earliest deadline first | Min Heap |

---

## Key Takeaways

- **Min heap** → root is always the **smallest**. Parent ≤ children.
- **Max heap** → root is always the **largest**. Parent ≥ children.
- Both support insert and delete in **O(log n)** and peek in **O(1)**.
- **Heapify Up** restores the heap after insertion (bubble up).
- **Heapify Down** restores the heap after deletion (sift down).
- Python `heapq` = min heap. C++ `priority_queue` = max heap by default.

---

## Frequently Asked Questions

**What is the main difference between a min heap and a max heap?**

In a min heap, the smallest element is always at the root. In a max heap, the largest element is always at the root. Both are complete binary trees and share the same time complexities for insert and delete.

**When should I use a min heap instead of a max heap?**

Use a min heap when you need fast access to the smallest element — task scheduling by deadline or shortest path algorithms. Use a max heap when you need fast access to the largest element — highest priority tasks, top scores.

**Why not just sort an array instead of using a heap?**

Sorting takes O(n log n) and must be repeated every time new elements arrive. A heap gives you the min or max in O(1), and insert or delete in O(log n). For dynamic data, a heap is far more efficient.
