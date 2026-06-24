# Top K Elements with Heaps: Fast K Largest/Smallest

## What Are Top K Elements Problems?

Have you ever seen a leaderboard that shows only the top 10 players? Or a music app that shows your top 5 most played songs? That is exactly the kind of problem we solve with Top K Elements techniques.

You are given a list of numbers and asked to find the **K largest** or **K smallest** values efficiently, especially when the list is very large.

---

## Why Not Just Sort the Array?

Sorting works but costs **O(n log n)**. Using a heap we can solve most Top K problems in **O(n log k)**, which is much faster when K is small.

Think of it like this: instead of arranging all 1000 students by grade to find the top 10, you just maintain a small rolling list of the best 10 as you go.

| Approach | Time | Space |
|----------|------|-------|
| Sort and slice | O(n log n) | O(1) or O(n) |
| Max Heap (all elements) | O(n + k log n) | O(n) |
| **Min Heap of size K** | **O(n log k)** | **O(k)** |

---

## Min Heap vs Max Heap for Top K

| Goal | Heap to Use | Why |
|------|------------|-----|
| Top K **Largest** | **Min Heap** of size K | Root = weakest candidate; replace it when a larger value comes |
| Top K **Smallest** | **Max Heap** of size K | Root = largest among K; replace it when a smaller value comes |

---

## Top K Largest Elements

Maintain a **min heap of size K**. If a new element is larger than the heap's minimum (root), pop the root and push the new element. At the end, the heap holds the K largest.

### Step-by-Step Example

Find top 3 largest in `[3, 1, 5, 12, 2, 11]`:

| Step | Element | Heap State (Min Heap) | Action |
|------|---------|----------------------|--------|
| 1 | 3 | [3] | Push (size < k) |
| 2 | 1 | [1, 3] | Push (size < k) |
| 3 | 5 | [1, 3, 5] | Push (size = k) |
| 4 | 12 | [3, 12, 5] | 12 > min(1) → replace 1 |
| 5 | 2 | [3, 12, 5] | 2 < min(3) → skip |
| 6 | 11 | [5, 12, 11] | 11 > min(3) → replace 3 |

Result: `[5, 11, 12]` — the 3 largest elements.

#### Python

```python
import heapq

def top_k_largest(nums, k):
    # Step 1: build a min heap from the first k elements
    min_heap = nums[:k]
    heapq.heapify(min_heap)             # O(k) build time

    # Step 2: scan remaining elements
    for num in nums[k:]:
        if num > min_heap[0]:           # new element beats the current weakest
            heapq.heapreplace(min_heap, num)  # pop min, push num — O(log k)

    return min_heap                     # heap now holds the k largest elements


nums = [3, 1, 5, 12, 2, 11]
result = top_k_largest(nums, 3)
print(sorted(result, reverse=True))     # Output: [12, 11, 5]
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

// Find top k largest using a min heap of size k
vector<int> topKLargest(vector<int>& nums, int k) {
    // Min heap — smallest element sits at top
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int num : nums) {
        minHeap.push(num);              // push every element

        if ((int)minHeap.size() > k)    // heap exceeds size k
            minHeap.pop();              // remove the smallest (weakest candidate)
    }

    // Collect remaining k elements — these are the k largest
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top());
        minHeap.pop();
    }
    return result;
}

int main() {
    vector<int> nums = {3, 1, 5, 12, 2, 11};
    vector<int> result = topKLargest(nums, 3);
    sort(result.rbegin(), result.rend());           // sort for display
    for (int x : result) cout << x << " ";         // Output: 12 11 5
    cout << endl;
    return 0;
}
// Time: O(n log k) | Space: O(k)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #215 variant — returns vector of k largest
    vector<int> topKLargest(vector<int>& nums, int k) {
        // Min heap keeps the k largest — root is always the weakest of the k
        priority_queue<int, vector<int>, greater<int>> minHeap;

        for (int num : nums) {
            minHeap.push(num);              // consider every element

            if ((int)minHeap.size() > k)    // exceeded k — remove weakest
                minHeap.pop();
        }

        // Transfer heap contents to result vector
        vector<int> result;
        while (!minHeap.empty()) {
            result.push_back(minHeap.top());
            minHeap.pop();
        }
        return result;
    }
};
// Time: O(n log k) | Space: O(k)
```

---

## Top K Smallest Elements

Maintain a **max heap of size K**. If a new element is smaller than the heap's maximum (root), pop the root and push the new element.

#### Python

```python
import heapq

def top_k_smallest(nums, k):
    # Simulate max heap by negating values (Python heapq is min heap only)
    max_heap = [-num for num in nums[:k]]
    heapq.heapify(max_heap)             # O(k)

    for num in nums[k:]:
        if num < -max_heap[0]:          # new element beats the current largest
            heapq.heapreplace(max_heap, -num)   # pop largest, push new

    return [-x for x in max_heap]       # negate back to get real values


nums = [7, 10, 4, 3, 20, 15]
result = top_k_smallest(nums, 3)
print(sorted(result))       # Output: [3, 4, 7]
```

#### C++ (simple)

```cpp
// Find top k smallest using a max heap of size k
vector<int> topKSmallest(vector<int>& nums, int k) {
    // Max heap — largest element sits at top (default priority_queue)
    priority_queue<int> maxHeap;

    for (int num : nums) {
        maxHeap.push(num);              // push every element

        if ((int)maxHeap.size() > k)    // heap exceeds size k
            maxHeap.pop();              // remove the largest (weakest candidate for smallest)
    }

    // Collect remaining k elements — these are the k smallest
    vector<int> result;
    while (!maxHeap.empty()) {
        result.push_back(maxHeap.top());
        maxHeap.pop();
    }
    return result;
}

int main() {
    vector<int> nums = {7, 10, 4, 3, 20, 15};
    vector<int> result = topKSmallest(nums, 3);
    sort(result.begin(), result.end());
    for (int x : result) cout << x << " ";     // Output: 3 4 7
    cout << endl;
    return 0;
}
// Time: O(n log k) | Space: O(k)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    vector<int> topKSmallest(vector<int>& nums, int k) {
        // Max heap — root is always the largest among current k candidates
        priority_queue<int> maxHeap;

        for (int num : nums) {
            maxHeap.push(num);              // add element

            if ((int)maxHeap.size() > k)    // exceeded k — discard largest
                maxHeap.pop();
        }

        vector<int> result;
        while (!maxHeap.empty()) {
            result.push_back(maxHeap.top());
            maxHeap.pop();
        }
        return result;
    }
};
// Time: O(n log k) | Space: O(k)
```

---

## Kth Largest Element

Use a **min heap of size K**. After processing all elements, the root of the heap is exactly the Kth largest.

#### Python

```python
import heapq

def kth_largest(nums, k):
    min_heap = []

    for num in nums:
        heapq.heappush(min_heap, num)       # push every element

        if len(min_heap) > k:               # heap grew beyond k
            heapq.heappop(min_heap)         # remove smallest — not in top k

    return min_heap[0]                      # root = Kth largest


nums = [3, 1, 5, 12, 2, 11]
print(kth_largest(nums, 2))     # Output: 11  (2nd largest)
```

#### C++ (simple)

```cpp
// Find Kth largest using a min heap of size k
int kthLargest(vector<int>& nums, int k) {
    // Min heap — root is always the smallest of the top k candidates
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int num : nums) {
        minHeap.push(num);              // push every element

        if ((int)minHeap.size() > k)    // heap exceeds k → remove weakest
            minHeap.pop();
    }

    return minHeap.top();               // root = Kth largest
}

int main() {
    vector<int> nums = {3, 1, 5, 12, 2, 11};
    cout << kthLargest(nums, 2) << endl;    // Output: 11
    return 0;
}
// Time: O(n log k) | Space: O(k)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #215 — Kth Largest Element in an Array
    int findKthLargest(vector<int>& nums, int k) {
        // Min heap of size k — root will be the Kth largest at the end
        priority_queue<int, vector<int>, greater<int>> minHeap;

        for (int num : nums) {
            minHeap.push(num);              // consider every number

            if ((int)minHeap.size() > k)    // keep heap at size k
                minHeap.pop();              // discard the current minimum
        }

        return minHeap.top();               // Kth largest = root of min heap
    }
};
// Time: O(n log k) | Space: O(k)
```

---

## Kth Smallest Element

Use a **max heap of size K**. After scanning all elements, the root is the Kth smallest.

#### Python

```python
import heapq

def kth_smallest(nums, k):
    max_heap = []

    for num in nums:
        heapq.heappush(max_heap, -num)      # negate to simulate max heap

        if len(max_heap) > k:               # heap grew beyond k
            heapq.heappop(max_heap)         # remove largest — not in bottom k

    return -max_heap[0]                     # negate back to get real value


nums = [7, 10, 4, 3, 20, 15]
print(kth_smallest(nums, 3))    # Output: 7  (3rd smallest: sorted = [3,4,7,...])
```

#### C++ (simple)

```cpp
// Find Kth smallest using a max heap of size k
int kthSmallest(vector<int>& nums, int k) {
    // Max heap — root is always the largest of the current k candidates
    priority_queue<int> maxHeap;

    for (int num : nums) {
        maxHeap.push(num);              // push every element

        if ((int)maxHeap.size() > k)    // heap exceeds k → remove largest
            maxHeap.pop();
    }

    return maxHeap.top();               // root = Kth smallest
}

int main() {
    vector<int> nums = {7, 10, 4, 3, 20, 15};
    cout << kthSmallest(nums, 3) << endl;   // Output: 7
    return 0;
}
// Time: O(n log k) | Space: O(k)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Find Kth smallest — max heap of size k, root = Kth smallest
    int findKthSmallest(vector<int>& nums, int k) {
        priority_queue<int> maxHeap;        // max heap by default

        for (int num : nums) {
            maxHeap.push(num);              // add every number

            if ((int)maxHeap.size() > k)    // keep heap at size k
                maxHeap.pop();              // discard current maximum
        }

        return maxHeap.top();               // Kth smallest = root of max heap
    }
};
// Time: O(n log k) | Space: O(k)
```

---

## Top K Frequent Elements

Two steps:
1. Count frequency of each element using a hash map.
2. Use a **min heap of size K** on `(frequency, element)` pairs.

#### Python

```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    # Step 1: count how often each element appears
    freq_map = Counter(nums)            # {element: frequency}

    # Step 2: min heap of size k on (frequency, element) pairs
    min_heap = []

    for num, freq in freq_map.items():
        heapq.heappush(min_heap, (freq, num))   # push (freq, value) pair

        if len(min_heap) > k:                   # exceeded k → remove least frequent
            heapq.heappop(min_heap)

    return [num for freq, num in min_heap]      # extract elements


nums = [1, 1, 1, 2, 2, 3]
print(top_k_frequent(nums, 2))      # Output: [2, 1]  (freq: 1→3, 2→2, 3→1)
```

#### C++ (simple)

```cpp
#include <unordered_map>
using namespace std;

// Find top k most frequent elements
vector<int> topKFrequent(vector<int>& nums, int k) {
    // Step 1: build frequency map
    unordered_map<int, int> freqMap;
    for (int num : nums)
        freqMap[num]++;                 // count occurrences of each element

    // Step 2: min heap on (frequency, value) — keeps k most frequent
    // pair<freq, value>: min heap compares by first element (freq)
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> minHeap;

    for (auto& [num, freq] : freqMap) {
        minHeap.push({freq, num});      // push frequency-element pair

        if ((int)minHeap.size() > k)    // exceeded k — remove least frequent
            minHeap.pop();
    }

    // Collect the k most frequent elements
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().second);
        minHeap.pop();
    }
    return result;
}

int main() {
    vector<int> nums = {1, 1, 1, 2, 2, 3};
    vector<int> result = topKFrequent(nums, 2);
    for (int x : result) cout << x << " ";     // Output: 2 1 (or 1 2)
    cout << endl;
    return 0;
}
// Time: O(n log k) | Space: O(n + k)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #347 — Top K Frequent Elements
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // Step 1: count frequency of each number
        unordered_map<int, int> freqMap;
        for (int num : nums)
            freqMap[num]++;

        // Step 2: min heap of size k on (frequency, element)
        // root = least frequent among top k → easy to evict
        priority_queue<pair<int,int>,
                       vector<pair<int,int>>,
                       greater<pair<int,int>>> minHeap;

        for (auto& [num, freq] : freqMap) {
            minHeap.push({freq, num});          // (frequency, value)

            if ((int)minHeap.size() > k)        // evict least frequent
                minHeap.pop();
        }

        // Extract the k most frequent elements
        vector<int> result;
        while (!minHeap.empty()) {
            result.push_back(minHeap.top().second);
            minHeap.pop();
        }
        return result;
    }
};
// Time: O(n log k) | Space: O(n + k)
```

---

## Dry Run: Top 3 Largest in `[3, 1, 5, 12, 2, 11]`

| Step | Element | Heap (Min Heap size ≤ 3) | Action |
|------|---------|--------------------------|--------|
| 1 | 3 | [3] | Push (size < 3) |
| 2 | 1 | [1, 3] | Push (size < 3) |
| 3 | 5 | [1, 3, 5] | Push (size = 3) |
| 4 | 12 | [3, 5, 12] | 12 > min(1) → pop 1, push 12 |
| 5 | 2 | [3, 5, 12] | 2 < min(3) → skip |
| 6 | 11 | [5, 11, 12] | 11 > min(3) → pop 3, push 11 |

Final heap: `[5, 11, 12]` — the 3 largest. ✓

---

## Time and Space Complexity

| Problem | Time | Space |
|---------|------|-------|
| Top K Largest | O(n log k) | O(k) |
| Top K Smallest | O(n log k) | O(k) |
| Kth Largest | O(n log k) | O(k) |
| Kth Smallest | O(n log k) | O(k) |
| Top K Frequent | O(n log k) | O(n + k) |

For each of n elements, we do at most one push + one pop on a heap of size k. Each heap operation costs O(log k). Total = **O(n log k)**.

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Using max heap for top K largest | Use **min heap** — root is the weakest candidate |
| Letting heap grow beyond K | Always pop when `size > k` |
| Forgetting to negate back in Python max heap | `-max_heap[0]` to get real value |
| Confusing Kth largest vs top K largest | Kth largest = single value at heap root; top K = all K values |

---

## Frequently Asked Questions

**Why use a min heap to find the K largest elements?**

The min heap keeps its smallest element at the top. We use it as a size-K filter. Any new element larger than the current minimum earns its place, replacing the minimum. At the end, only the K largest survive.

**What is the difference between Kth largest and Top K largest?**

Top K largest returns a list of K elements. Kth largest returns exactly one element — the one at position K in descending sorted order. For Kth largest, just return `heap.top()` at the end.

**When is the heap approach not the best choice?**

If K is very close to n, sorting might be simpler and equally efficient. The heap approach shines when K is much smaller than n. For very small arrays, a simple sort is fine.
