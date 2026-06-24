# Heap Data Structure Explained: Beginner Guide

## What is a Heap?

Imagine a hospital emergency room. Patients are not treated in the order they arrive. The most critical patient always gets seen first. That is exactly how a heap works — it always keeps the most important element at the top.

A heap is a special tree-based data structure that satisfies the **heap property**. It is one of the most useful structures in DSA, and it powers something called a **Priority Queue**.

---

## Heap as a Complete Binary Tree

A heap is a **complete binary tree**. This means all levels of the tree are fully filled except possibly the last level, and the last level is filled **from left to right**.

Think of filling seats in a cinema row by row, always from the left. You never skip a seat and move to the next row until the current row is full. That is exactly how a complete binary tree is filled.

```
Valid complete binary tree (heap shape):
          90
        /    \
      75      80
     /  \    /  \
   55   60  65   70

NOT complete (gap on the left):
          90
        /    \
      75      80
        \    /
        60  65
```

### Why Must It Be a Complete Binary Tree?

The complete structure allows the heap to be stored as a **plain array** — no pointers needed. This makes heaps memory-efficient and fast to implement.

For any element at index `i`:
- **Left child** → index `2 * i + 1`
- **Right child** → index `2 * i + 2`
- **Parent** → index `(i - 1) / 2`

---

## The Heap Property

Not every complete binary tree is a heap. A heap must also follow the **heap property**. There are two types:

### Max Heap Property

Every parent node is **greater than or equal to** its children. The **largest** element is always at the root.

```
Max Heap example:
          90        ← largest always at root
        /    \
      75      80
     /  \    /  \
   55   60  65   70
```

### Min Heap Property

Every parent node is **less than or equal to** its children. The **smallest** element is always at the root.

```
Min Heap example:
          10        ← smallest always at root
        /    \
      20      15
     /  \    /  \
   30   25  40   50
```

### Comparison Table

| Feature | Max Heap | Min Heap |
|---------|----------|----------|
| Root contains | Largest element | Smallest element |
| Parent vs Child | Parent ≥ children | Parent ≤ children |
| Use case | Find maximum quickly | Find minimum quickly |
| Priority Queue type | Highest priority first | Lowest value first |

---

## Heap Represented as an Array

One of the most powerful features of a heap is that it can be stored as a plain array. Since the tree is complete, there are no gaps.

Max Heap with values: **90, 75, 80, 55, 60, 65, 70**

```
Tree view:
          90
        /    \
      75      80
     /  \    /  \
   55   60  65   70

Array view:
 Index:  0   1   2   3   4   5   6
 Value: [90, 75, 80, 55, 60, 65, 70]

For element at index 1 (value = 75):
  Left child  → index 3 (value = 55)  → 2*1+1 = 3
  Right child → index 4 (value = 60)  → 2*1+2 = 4
  Parent      → index 0 (value = 90)  → (1-1)/2 = 0
```

This index-based relationship means we can navigate the heap without any pointer-based tree nodes.

---

## How to Check If an Array is a Valid Heap

To verify a Max Heap: every parent must be ≥ its children.
To verify a Min Heap: every parent must be ≤ its children.

Let us check `[90, 75, 80, 55, 60, 65, 70]` for Max Heap validity:

```
Index 0 (90): children at 1 (75) and 2 (80) → 90 ≥ 75 and 90 ≥ 80  [VALID]
Index 1 (75): children at 3 (55) and 4 (60) → 75 ≥ 55 and 75 ≥ 60  [VALID]
Index 2 (80): children at 5 (65) and 6 (70) → 80 ≥ 65 and 80 ≥ 70  [VALID]
Index 3 (55): no children                                             [VALID]
Index 4 (60): no children                                             [VALID]

Result: This is a valid Max Heap!
```

#### Python

```python
def is_max_heap(arr):
    n = len(arr)
    # Only internal nodes (indices 0 to n//2 - 1) have children
    for i in range(n // 2):
        left = 2 * i + 1        # left child index
        right = 2 * i + 2       # right child index

        # Check left child: parent must be >= left child
        if left < n and arr[i] < arr[left]:
            return False

        # Check right child: parent must be >= right child
        if right < n and arr[i] < arr[right]:
            return False

    return True     # all parent-child pairs passed the check


print(is_max_heap([90, 75, 80, 55, 60, 65, 70]))  # Output: True
print(is_max_heap([90, 75, 80, 95, 60, 65, 70]))  # Output: False (95 > 75)
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Check if array represents a valid Max Heap
bool isMaxHeap(vector<int>& arr) {
    int n = arr.size();

    // Only internal nodes (0 to n/2-1) have children to check
    for (int i = 0; i < n / 2; i++) {
        int left  = 2 * i + 1;     // left child index
        int right = 2 * i + 2;     // right child index

        // Parent must be >= left child
        if (left < n && arr[i] < arr[left])
            return false;

        // Parent must be >= right child
        if (right < n && arr[i] < arr[right])
            return false;
    }
    return true;    // all checks passed
}

int main() {
    vector<int> v1 = {90, 75, 80, 55, 60, 65, 70};
    vector<int> v2 = {90, 75, 80, 95, 60, 65, 70};

    cout << isMaxHeap(v1) << endl;  // Output: 1 (true)
    cout << isMaxHeap(v2) << endl;  // Output: 0 (false)
    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Returns true if arr is a valid Max Heap
    bool isMaxHeap(vector<int>& arr) {
        int n = arr.size();

        // Iterate over all internal nodes
        for (int i = 0; i < n / 2; i++) {
            int left  = 2 * i + 1;     // left child index
            int right = 2 * i + 2;     // right child index

            if (left < n && arr[i] < arr[left])
                return false;           // parent < left child — invalid

            if (right < n && arr[i] < arr[right])
                return false;           // parent < right child — invalid
        }
        return true;
    }
};
```

---

## Using Heaps in Python and C++

Most languages provide built-in heap or priority queue implementations. You do not always need to build one from scratch.

### Python — `heapq` module (Min Heap by default)

To simulate a **Max Heap**, insert negative values.

#### Python

```python
import heapq

# --- Min Heap ---
min_heap = []
heapq.heappush(min_heap, 30)    # insert 30
heapq.heappush(min_heap, 10)    # insert 10
heapq.heappush(min_heap, 20)    # insert 20

print(min_heap[0])                      # Output: 10  (peek at smallest)
print(heapq.heappop(min_heap))          # Output: 10  (remove and return smallest)
print(min_heap[0])                      # Output: 20  (new smallest is now at root)

# --- Max Heap (negate values) ---
max_heap = []
heapq.heappush(max_heap, -30)   # store -30 to simulate max heap
heapq.heappush(max_heap, -10)
heapq.heappush(max_heap, -20)

print(-max_heap[0])                     # Output: 30  (negate back to get true max)
print(-heapq.heappop(max_heap))         # Output: 30  (remove and return true max)
```

### C++ — `priority_queue` (Max Heap by default)

#### C++ (simple)

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

int main() {
    // --- Max Heap (default in C++) ---
    priority_queue<int> maxHeap;

    maxHeap.push(30);           // insert 30
    maxHeap.push(10);           // insert 10
    maxHeap.push(20);           // insert 20

    cout << maxHeap.top() << endl;  // Output: 30  (peek at largest)
    maxHeap.pop();                  // remove largest
    cout << maxHeap.top() << endl;  // Output: 20  (new largest)

    // --- Min Heap (use greater<int> comparator) ---
    priority_queue<int, vector<int>, greater<int>> minHeap;

    minHeap.push(30);
    minHeap.push(10);
    minHeap.push(20);

    cout << minHeap.top() << endl;  // Output: 10  (peek at smallest)
    minHeap.pop();                  // remove smallest
    cout << minHeap.top() << endl;  // Output: 20  (new smallest)

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
    // Demo: find the maximum element using a max heap
    int findMax(vector<int>& nums) {
        priority_queue<int> maxHeap(nums.begin(), nums.end());  // build heap from array
        return maxHeap.top();   // largest element is always at top — O(1)
    }

    // Demo: find the minimum element using a min heap
    int findMin(vector<int>& nums) {
        // greater<int> makes it a min heap
        priority_queue<int, vector<int>, greater<int>> minHeap(nums.begin(), nums.end());
        return minHeap.top();   // smallest element is always at top — O(1)
    }
};
```

---

## Heap vs Sorted Array

| Operation | Sorted Array | Heap |
|-----------|-------------|------|
| Find min or max | O(1) | O(1) |
| Insert new element | O(n) | O(log n) |
| Delete min or max | O(n) | O(log n) |
| Build from n elements | O(n log n) | O(n) |

A heap wins in **dynamic** scenarios where elements are being added and removed frequently. If your data is static and you only need the top element once, a sorted array might be simpler.

---

## Real-World Use Cases of Heaps

| Use Case | Why a Heap? |
|----------|------------|
| **Priority Queue** | Process tasks by priority, not arrival order |
| **Dijkstra's Shortest Path** | Always process the nearest unvisited node first |
| **Heap Sort** | Sorting algorithm built on heap operations — O(n log n) |
| **Top K Elements** | Find K largest/smallest in a dataset efficiently |
| **OS Scheduling** | Operating systems manage process scheduling with heaps |

Every time you see "find the K largest" or "always process the minimum next," a heap is likely the right tool.

---

## Key Takeaways

- A heap is a **complete binary tree** that follows the heap property (max or min).
- It is stored as a **plain array** using index-based parent-child formulas.
- **Finding the top element** is O(1). **Inserting or removing** takes O(log n).
- Python uses `heapq` (min heap by default). C++ uses `priority_queue` (max heap by default).

---

## Frequently Asked Questions

**Is a heap the same as a binary search tree?**

No. A BST keeps left children smaller and right children larger than the parent. A heap only cares about parent-child order (max or min), not left vs right ordering. Heaps are better for priority access; BSTs are better for searching sorted data.

**Is a heap a sorted data structure?**

Not exactly. A heap guarantees the root is the maximum or minimum, but the rest of the elements are not fully sorted. It is **partially ordered**, which is enough for most priority-based problems.

**When should I use a heap instead of a sorted array?**

Use a heap when you need to **repeatedly insert elements** and always access the minimum or maximum quickly. If your data is static and you only need the top element once, a sorted array or simple scan might be enough.
