# Heap Operations: Insert, Delete, Heapify and Peek

## Quick Recap: Heap Properties

Two rules every heap must follow:

1. **Structure Property** — A heap must be a complete binary tree (all levels filled left to right).
2. **Heap Order Property** — Min Heap: every parent ≤ children. Max Heap: every parent ≥ children.

### Node Index Formulas (0-based array)

| Node        | Formula       | Example (i = 2) |
| ----------- | ------------- | --------------- |
| Left Child  | `2*i + 1`     | 5               |
| Right Child | `2*i + 2`     | 6               |
| Parent      | `(i - 1) / 2` | 0               |

---

## Heap Array Representation

```
Min Heap Tree:
      10
     /  \
   20    15
  /  \
40   50

Array: [10, 20, 15, 40, 50]
Index:   0   1   2   3   4

Parent of index 3  = (3-1)/2 = 1  → value 20
Left child of index 1  = 2*1+1 = 3 → value 40
Right child of index 1 = 2*1+2 = 4 → value 50
```

---

## Heap Operations Overview

| Operation            | Time     | Description                          |
| -------------------- | -------- | ------------------------------------ |
| Insert               | O(log n) | Add element at end, sift up          |
| Extract Min/Max      | O(log n) | Remove root, sift down               |
| Peek                 | O(1)     | Read root without removing           |
| Build Heap (Heapify) | O(n)     | Convert unsorted array to valid heap |

---

## Insert Operation

Place the new element at the **end** of the array (maintains complete binary tree shape), then **sift it up** by comparing with its parent and swapping until the heap property is restored.

### Insert Example

Inserting 5 into Min Heap `[10, 20, 15, 40, 50]`:

```
Step 1: Add 5 at end
  Array: [10, 20, 15, 40, 50, 5]
  Index of 5 = 5 → parent = (5-1)/2 = 2 → value 15

Step 2: 5 < 15, swap
  Array: [10, 20, 5, 40, 50, 15]
  5 is at index 2 → parent = (2-1)/2 = 0 → value 10

Step 3: 5 < 10, swap
  Array: [5, 20, 10, 40, 50, 15]
  5 is at index 0 (root). No parent. Stop.

Final Min Heap: [5, 20, 10, 40, 50, 15]
```

#### Python

```python
class MinHeap:
    def __init__(self):
        self.heap = []          # internal array to store the heap

    def insert(self, val):
        self.heap.append(val)               # Step 1: add at end of array
        self._sift_up(len(self.heap) - 1)   # Step 2: bubble it up to correct position

    def _sift_up(self, i):
        while i > 0:
            parent = (i - 1) // 2           # parent index formula
            if self.heap[i] < self.heap[parent]:    # child smaller than parent → swap
                self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
                i = parent                  # move up to parent's position
            else:
                break                       # heap property satisfied, stop

# Usage
heap = MinHeap()
heap.insert(10)
heap.insert(20)
heap.insert(5)
print(heap.heap)    # Output: [5, 20, 10]  — 5 is at root (minimum)
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Sift element at index i upward to restore min heap property
void siftUp(vector<int>& heap, int i) {
    while (i > 0) {
        int parent = (i - 1) / 2;              // parent index formula

        if (heap[i] < heap[parent]) {           // child smaller than parent → swap
            swap(heap[i], heap[parent]);
            i = parent;                         // move up to parent's position
        } else {
            break;                              // heap property satisfied, stop
        }
    }
}

// Insert a value into the min heap
void insert(vector<int>& heap, int val) {
    heap.push_back(val);                        // Step 1: place at end
    siftUp(heap, heap.size() - 1);              // Step 2: bubble up
}

int main() {
    vector<int> heap;
    insert(heap, 10);
    insert(heap, 20);
    insert(heap, 5);

    for (int x : heap) cout << x << " ";   // Output: 5 20 10
    cout << endl;
    return 0;
}
// Time: O(log n) per insert
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    // Insert a new value — O(log n)
    void insert(int val) {
        heap.push_back(val);                    // add at end of array
        siftUp(heap.size() - 1);                // restore heap by bubbling up
    }

    vector<int> getHeap() { return heap; }

private:
    vector<int> heap;

    void siftUp(int i) {
        while (i > 0) {
            int parent = (i - 1) / 2;           // parent index
            if (heap[i] < heap[parent]) {       // min heap: child < parent → swap
                swap(heap[i], heap[parent]);
                i = parent;
            } else break;
        }
    }
};
```

---

## Peek Operation

Returns the root element without removing it. Always O(1) because the answer is always at index 0.

#### Python

```python
def peek(self):
    if not self.heap:           # guard: empty heap
        return None
    return self.heap[0]         # root is always at index 0 — O(1)
```

#### C++ (simple)

```cpp
// Peek at the minimum without removing it — O(1)
int peek(vector<int>& heap) {
    if (heap.empty()) return -1;    // guard: empty heap
    return heap[0];                  // root is always the minimum
}
```

#### C++ (LeetCode class style)

```cpp
// Returns minimum element without removing — O(1)
int peek() {
    if (heap.empty()) return -1;
    return heap[0];     // root is always at index 0
}
```

---

## Delete Operation (Extract Min)

Three steps:

1. **Swap** root with the last element.
2. **Remove** the last element (the old root/answer).
3. **Sift down** the new root until the heap property is restored.

### Delete Example

Extract min from `[5, 20, 10, 40, 50, 15]`:

```
Step 1: Swap root (5) with last element (15)
  Array: [15, 20, 10, 40, 50, 5]

Step 2: Remove last element (5 — the original min)
  Array: [15, 20, 10, 40, 50]

Step 3: Sift-down 15 from index 0
  Left = index 1 (value 20), Right = index 2 (value 10)
  Smallest child = 10 at index 2 → 15 > 10, swap
  Array: [10, 20, 15, 40, 50]

Step 4: 15 is at index 2. Children at index 5, 6 — out of bounds. Stop.
  Final Min Heap: [10, 20, 15, 40, 50]
  Extracted: 5
```

#### Python

```python
def extract_min(self):
    if not self.heap:
        return None
    if len(self.heap) == 1:
        return self.heap.pop()          # single element — just remove it

    root = self.heap[0]                 # save the minimum value
    self.heap[0] = self.heap.pop()      # move last element to root position
    self._sift_down(0)                  # push new root down to correct position
    return root

def _sift_down(self, i):
    n = len(self.heap)
    while True:
        smallest = i                    # assume current is smallest
        left  = 2 * i + 1              # left child index
        right = 2 * i + 2              # right child index

        # check if left child is smaller than current
        if left < n and self.heap[left] < self.heap[smallest]:
            smallest = left

        # check if right child is even smaller
        if right < n and self.heap[right] < self.heap[smallest]:
            smallest = right

        if smallest != i:               # a child is smaller → swap and continue down
            self.heap[i], self.heap[smallest] = self.heap[smallest], self.heap[i]
            i = smallest
        else:
            break                       # heap property restored, stop

# Usage
heap = MinHeap()
heap.insert(10)
heap.insert(5)
heap.insert(20)
print(heap.extract_min())   # Output: 5   (minimum removed)
print(heap.heap)            # Output: [10, 20]
```

#### C++ (simple)

```cpp
// Sift element at index i downward to restore min heap property
void siftDown(vector<int>& heap, int i) {
    int n = heap.size();
    while (true) {
        int smallest = i;               // assume current node is smallest
        int left  = 2 * i + 1;         // left child index
        int right = 2 * i + 2;         // right child index

        // if left child exists and is smaller, update smallest
        if (left < n && heap[left] < heap[smallest])
            smallest = left;

        // if right child exists and is even smaller, update smallest
        if (right < n && heap[right] < heap[smallest])
            smallest = right;

        if (smallest != i) {            // need to swap with smallest child
            swap(heap[i], heap[smallest]);
            i = smallest;               // continue from swapped position
        } else {
            break;                      // current is already the smallest
        }
    }
}

// Remove and return minimum element (root) from min heap
int extractMin(vector<int>& heap) {
    if (heap.empty()) return -1;

    int root = heap[0];                 // save minimum
    heap[0] = heap.back();             // move last element to root
    heap.pop_back();                    // remove last element
    siftDown(heap, 0);                  // restore heap from root down
    return root;
}

int main() {
    vector<int> heap;
    insert(heap, 10);
    insert(heap, 5);
    insert(heap, 20);

    cout << extractMin(heap) << endl;   // Output: 5
    for (int x : heap) cout << x << " ";  // Output: 10 20
    cout << endl;
    return 0;
}
// Time: O(log n)
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    void insert(int val) {
        heap.push_back(val);
        siftUp(heap.size() - 1);
    }

    int peek() {
        return heap.empty() ? -1 : heap[0];
    }

    // Remove and return minimum — O(log n)
    int extractMin() {
        if (heap.empty()) return -1;
        if (heap.size() == 1) {
            int val = heap[0];
            heap.pop_back();
            return val;
        }

        int root = heap[0];             // save minimum value
        heap[0] = heap.back();          // bring last element to root
        heap.pop_back();                // remove last
        siftDown(0);                    // restore heap property
        return root;
    }

private:
    vector<int> heap;

    void siftUp(int i) {
        while (i > 0) {
            int parent = (i - 1) / 2;
            if (heap[i] < heap[parent]) { swap(heap[i], heap[parent]); i = parent; }
            else break;
        }
    }

    void siftDown(int i) {
        int n = heap.size();
        while (true) {
            int smallest = i;
            int left = 2*i+1, right = 2*i+2;
            if (left  < n && heap[left]  < heap[smallest]) smallest = left;
            if (right < n && heap[right] < heap[smallest]) smallest = right;
            if (smallest != i) { swap(heap[i], heap[smallest]); i = smallest; }
            else break;
        }
    }
};
```

---

## Heapify — Build a Heap from an Unsorted Array

Inserting n elements one by one costs **O(n log n)**. **Heapify** does it in **O(n)** by starting from the last non-leaf node and sifting each node down toward the root.

The last non-leaf index = `(n / 2) - 1`.

### Heapify Example

Input: `[40, 10, 30, 5, 20]` → build Min Heap

```
Last non-leaf = (5/2) - 1 = 1

Step 1: Sift-down index 1 (value 10)
  Left = index 3 (5), Right = index 4 (20) → smallest = 5 at index 3
  10 > 5 → swap
  Array: [40, 5, 30, 10, 20]

Step 2: Sift-down index 0 (value 40)
  Left = index 1 (5), Right = index 2 (30) → smallest = 5 at index 1
  40 > 5 → swap
  Array: [5, 40, 30, 10, 20]

  40 is now at index 1. Children: index 3 (10), index 4 (20) → smallest = 10
  40 > 10 → swap
  Array: [5, 10, 30, 40, 20]

Final Min Heap: [5, 10, 30, 40, 20]
```

#### Python

```python
def build_heap(self, arr):
    self.heap = arr[:]                              # copy the input array
    n = len(self.heap)
    # start from last non-leaf node and sift down each node up to root
    for i in range((n // 2) - 1, -1, -1):
        self._sift_down(i)                          # reuse the same sift-down logic

# Usage
heap = MinHeap()
heap.build_heap([40, 10, 30, 5, 20])
print(heap.heap)    # Output: [5, 10, 30, 40, 20]
```

#### C++ (simple)

```cpp
// Convert an unsorted array into a valid min heap — O(n)
void buildHeap(vector<int>& heap) {
    int n = heap.size();
    // start at last non-leaf node, sift each node down
    for (int i = (n / 2) - 1; i >= 0; i--) {
        siftDown(heap, i);      // reuses siftDown defined earlier
    }
}

int main() {
    vector<int> heap = {40, 10, 30, 5, 20};
    buildHeap(heap);
    for (int x : heap) cout << x << " ";    // Output: 5 10 30 40 20
    cout << endl;
    return 0;
}
// Time: O(n)
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    // Build a min heap from an existing array — O(n)
    void buildHeap(vector<int>& arr) {
        heap = arr;                                 // copy input
        int n = heap.size();
        // sift down every internal node from bottom up
        for (int i = (n / 2) - 1; i >= 0; i--) {
            siftDown(i);
        }
    }

    // ... insert, peek, extractMin, siftUp, siftDown same as above
private:
    vector<int> heap;

    void siftDown(int i) {
        int n = heap.size();
        while (true) {
            int smallest = i;
            int left = 2*i+1, right = 2*i+2;
            if (left  < n && heap[left]  < heap[smallest]) smallest = left;
            if (right < n && heap[right] < heap[smallest]) smallest = right;
            if (smallest != i) { swap(heap[i], heap[smallest]); i = smallest; }
            else break;
        }
    }
};
```

---

## Complete Min Heap Implementation

#### Python

```python
class MinHeap:
    def __init__(self):
        self.heap = []              # array-based heap storage

    # O(log n) — insert value and restore heap
    def insert(self, val):
        self.heap.append(val)
        self._sift_up(len(self.heap) - 1)

    # O(1) — return minimum without removing
    def peek(self):
        return self.heap[0] if self.heap else None

    # O(log n) — remove and return minimum
    def extract_min(self):
        if not self.heap: return None
        if len(self.heap) == 1: return self.heap.pop()
        root = self.heap[0]
        self.heap[0] = self.heap.pop()  # move last to root
        self._sift_down(0)
        return root

    # O(n) — convert unsorted array to heap
    def build_heap(self, arr):
        self.heap = arr[:]
        for i in range((len(self.heap) // 2) - 1, -1, -1):
            self._sift_down(i)

    def _sift_up(self, i):
        while i > 0:
            parent = (i - 1) // 2
            if self.heap[i] < self.heap[parent]:
                self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
                i = parent
            else:
                break

    def _sift_down(self, i):
        n = len(self.heap)
        while True:
            smallest = i
            left, right = 2*i+1, 2*i+2
            if left  < n and self.heap[left]  < self.heap[smallest]: smallest = left
            if right < n and self.heap[right] < self.heap[smallest]: smallest = right
            if smallest != i:
                self.heap[i], self.heap[smallest] = self.heap[smallest], self.heap[i]
                i = smallest
            else:
                break


# Test
h = MinHeap()
h.build_heap([9, 4, 7, 1, 2])
print("Heap:", h.heap)                  # [1, 2, 7, 4, 9]
print("Min:", h.peek())                  # 1
print("Extracted:", h.extract_min())    # 1
print("After extract:", h.heap)         # [2, 4, 7, 9]
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// ---- siftUp: bubble element at index i upward ----
void siftUp(vector<int>& heap, int i) {
    while (i > 0) {
        int parent = (i - 1) / 2;
        if (heap[i] < heap[parent]) { swap(heap[i], heap[parent]); i = parent; }
        else break;
    }
}

// ---- siftDown: push element at index i downward ----
void siftDown(vector<int>& heap, int i) {
    int n = heap.size();
    while (true) {
        int smallest = i;
        int left = 2*i+1, right = 2*i+2;
        if (left  < n && heap[left]  < heap[smallest]) smallest = left;
        if (right < n && heap[right] < heap[smallest]) smallest = right;
        if (smallest != i) { swap(heap[i], heap[smallest]); i = smallest; }
        else break;
    }
}

void insert(vector<int>& heap, int val)    { heap.push_back(val); siftUp(heap, heap.size()-1); }
int  peek(vector<int>& heap)               { return heap.empty() ? -1 : heap[0]; }
int  extractMin(vector<int>& heap)         { int r=heap[0]; heap[0]=heap.back(); heap.pop_back(); siftDown(heap,0); return r; }
void buildHeap(vector<int>& heap)          { for (int i=(heap.size()/2)-1; i>=0; i--) siftDown(heap, i); }

int main() {
    vector<int> h = {9, 4, 7, 1, 2};
    buildHeap(h);

    cout << "Min: " << peek(h) << endl;             // 1
    cout << "Extracted: " << extractMin(h) << endl; // 1
    for (int x : h) cout << x << " ";              // 2 4 7 9
    cout << endl;
    return 0;
}
```

#### C++ (LeetCode class style)

```cpp
class MinHeap {
public:
    // O(log n) — insert and restore heap
    void insert(int val) {
        heap.push_back(val);
        siftUp(heap.size() - 1);
    }

    // O(1) — return minimum without removing
    int peek() {
        return heap.empty() ? -1 : heap[0];
    }

    // O(log n) — remove and return minimum
    int extractMin() {
        if (heap.empty()) return -1;
        if (heap.size() == 1) { int v = heap[0]; heap.pop_back(); return v; }
        int root = heap[0];
        heap[0] = heap.back();
        heap.pop_back();
        siftDown(0);
        return root;
    }

    // O(n) — build heap from existing array
    void buildHeap(vector<int>& arr) {
        heap = arr;
        for (int i = (heap.size() / 2) - 1; i >= 0; i--)
            siftDown(i);
    }

    vector<int> getHeap() { return heap; }

private:
    vector<int> heap;

    void siftUp(int i) {
        while (i > 0) {
            int p = (i - 1) / 2;
            if (heap[i] < heap[p]) { swap(heap[i], heap[p]); i = p; }
            else break;
        }
    }

    void siftDown(int i) {
        int n = heap.size();
        while (true) {
            int s = i, l = 2*i+1, r = 2*i+2;
            if (l < n && heap[l] < heap[s]) s = l;
            if (r < n && heap[r] < heap[s]) s = r;
            if (s != i) { swap(heap[i], heap[s]); i = s; }
            else break;
        }
    }
};
```

---

## Converting to Max Heap

Only **one sign change** is needed. Flip `<` to `>` in both sift operations:

#### Python

```python
# Max Heap sift-up: swap if child is LARGER than parent
def _sift_up(self, i):
    while i > 0:
        parent = (i - 1) // 2
        if self.heap[i] > self.heap[parent]:    # only change: > instead of <
            self.heap[i], self.heap[parent] = self.heap[parent], self.heap[i]
            i = parent
        else:
            break

# Max Heap sift-down: find LARGEST child instead of smallest
def _sift_down(self, i):
    n = len(self.heap)
    while True:
        largest = i                             # only change: largest instead of smallest
        left, right = 2*i+1, 2*i+2
        if left  < n and self.heap[left]  > self.heap[largest]: largest = left
        if right < n and self.heap[right] > self.heap[largest]: largest = right
        if largest != i:
            self.heap[i], self.heap[largest] = self.heap[largest], self.heap[i]
            i = largest
        else:
            break
```

#### C++ (simple)

```cpp
// Max Heap siftUp — swap if child is LARGER than parent
void siftUpMax(vector<int>& heap, int i) {
    while (i > 0) {
        int parent = (i - 1) / 2;
        if (heap[i] > heap[parent]) {       // > instead of <
            swap(heap[i], heap[parent]);
            i = parent;
        } else break;
    }
}

// Max Heap siftDown — find LARGEST child
void siftDownMax(vector<int>& heap, int i) {
    int n = heap.size();
    while (true) {
        int largest = i;                    // track largest, not smallest
        int left = 2*i+1, right = 2*i+2;
        if (left  < n && heap[left]  > heap[largest]) largest = left;
        if (right < n && heap[right] > heap[largest]) largest = right;
        if (largest != i) { swap(heap[i], heap[largest]); i = largest; }
        else break;
    }
}
```

#### C++ (LeetCode class style)

```cpp
class MaxHeap {
public:
    void insert(int val) { heap.push_back(val); siftUp(heap.size()-1); }
    int peek()           { return heap.empty() ? -1 : heap[0]; }
    int extractMax() {
        int root = heap[0];
        heap[0] = heap.back(); heap.pop_back();
        siftDown(0);
        return root;
    }

private:
    vector<int> heap;

    void siftUp(int i) {
        while (i > 0) {
            int p = (i-1)/2;
            if (heap[i] > heap[p]) { swap(heap[i], heap[p]); i = p; }  // > for max
            else break;
        }
    }

    void siftDown(int i) {
        int n = heap.size();
        while (true) {
            int largest = i, l = 2*i+1, r = 2*i+2;
            if (l < n && heap[l] > heap[largest]) largest = l;     // > for max
            if (r < n && heap[r] > heap[largest]) largest = r;
            if (largest != i) { swap(heap[i], heap[largest]); i = largest; }
            else break;
        }
    }
};
```

---

## Time Complexity Summary

| Operation            | Time     | Reason                                   |
| -------------------- | -------- | ---------------------------------------- |
| Insert               | O(log n) | Sift-up traverses tree height            |
| Extract Min/Max      | O(log n) | Sift-down traverses tree height          |
| Peek                 | O(1)     | Always at index 0                        |
| Build Heap (Heapify) | O(n)     | Amortized analysis of sift-downs         |
| Search               | O(n)     | No order guarantee for non-root elements |

---

## Frequently Asked Questions

**Why is heapify O(n) but inserting n elements one by one is O(n log n)?**

When heapifying, nodes near the bottom do very little work — most are leaf nodes and do zero work. Inserting one by one treats every element as needing a full sift-up from the bottom, which is slower overall.

**Can you search for a specific value in a heap efficiently?**

No. Heaps only guarantee the root is the min or max. Other elements have no strict left-right ordering, so you would need to scan the entire array in O(n) to find a specific value.

**Does Python have a built-in heap?**

Yes. Python's `heapq` module provides a Min Heap. Use `heapq.heappush()` to insert and `heapq.heappop()` to extract the minimum. It operates directly on a plain Python list.
