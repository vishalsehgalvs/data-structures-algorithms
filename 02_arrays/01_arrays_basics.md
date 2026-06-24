# Arrays — Basics, Traversal, and Patterns

> **One-line summary:**
> An array is a fixed-size shelf of elements stored side by side in memory — fast to access by index, the building block of almost every algorithm.

---

## Table of Contents

1. [What is an Array?](#1-what-is-an-array)
2. [Array Basics — Declaring and Indexing](#2-array-basics--declaring-and-indexing)
3. [Traversing an Array](#3-traversing-an-array)
4. [Common Array Operations](#4-common-array-operations)
5. [Static vs Dynamic Arrays](#5-static-vs-dynamic-arrays)
6. [Important Array Patterns](#6-important-array-patterns)
7. [Time and Space Complexity of Arrays](#7-time-and-space-complexity-of-arrays)
8. [Tips for Solving Array Problems](#8-tips-for-solving-array-problems)
9. [Key Takeaways](#9-key-takeaways)
10. [FAQs](#10-faqs)

---

## 1. What is an Array?

Imagine 10 books lined up on a shelf in order — book 1, book 2, book 3, and so on. You can reach any book instantly because you know its position.

An array works exactly like that shelf:

- Elements stored **side by side** in memory (contiguous)
- Every element has an **index** — its position number
- Indexing starts at **0** in most languages

```
Index:   0    1    2    3    4
       +----+----+----+----+----+
arr =  | 10 | 20 | 30 | 40 | 50 |
       +----+----+----+----+----+
         ↑                   ↑
      arr[0]=10           arr[4]=50
```

Arrays are one of the most fundamental data structures. Almost every algorithm in this series uses them in some form.

---

## 2. Array Basics — Declaring and Indexing

### Declaring an Array

Think of it like booking a row of seats — you decide positions upfront.

#### Python

```python
# Python uses lists as dynamic arrays
numbers = [10, 20, 30, 40, 50]

# Access by index
print(numbers[0])    # Output: 10  (first element)
print(numbers[4])    # Output: 50  (last element)
print(numbers[-1])   # Output: 50  (last element using negative index)
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // Fixed-size array — size must be declared upfront
    int numbers[] = {10, 20, 30, 40, 50};
    cout << numbers[0] << endl;   // Output: 10 (first element)
    cout << numbers[4] << endl;   // Output: 50 (last element)

    // Dynamic array using vector — preferred in C++ DSA (resizes automatically)
    vector<int> nums = {10, 20, 30, 40, 50};
    cout << nums[0] << endl;      // Output: 10
    cout << nums[4] << endl;      // Output: 50

    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Returns the element at a given index — O(1) direct access
    int getElement(vector<int>& nums, int index) {
        return nums[index];   // constant time — memory address computed directly
    }
};
```

---

### Array Indexing and Length

**Critical rule:** For an array of size `n`, the last valid index is `n - 1`. Going beyond causes an error.

#### Python

```python
arr = [5, 10, 15, 20]

print(len(arr))           # Output: 4  (size)
print(arr[len(arr) - 1])  # Output: 20 (last element, index 3)

# arr[4] → IndexError: list index out of range
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> arr = {5, 10, 15, 20};

    cout << arr.size() << endl;           // Output: 4 — number of elements
    cout << arr[arr.size() - 1] << endl;  // Output: 20 — last index is always size-1

    // arr[4] → undefined behavior — C++ does NOT check bounds automatically
    // Use arr.at(4) to get a bounds-checked std::out_of_range exception
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Returns the last element of the array — O(1)
    int getLastElement(vector<int>& arr) {
        return arr[arr.size() - 1];   // last valid index = size - 1
    }
};
```

---

## 3. Traversing an Array

Traversal = visiting every element one by one. This is the foundation of almost all array algorithms.

### Forward Traversal

#### Python

```python
arr = [3, 7, 1, 9, 4]

for i in range(len(arr)):
    print(f"Index {i}: {arr[i]}")

# Output:
# Index 0: 3
# Index 1: 7
# Index 2: 1
# Index 3: 9
# Index 4: 4
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> arr = {3, 7, 1, 9, 4};

    // Visit every element from left to right using index
    for (int i = 0; i < (int)arr.size(); i++) {
        cout << "Index " << i << ": " << arr[i] << endl;
    }
    // Output:
    // Index 0: 3
    // Index 1: 7
    // Index 2: 1
    // Index 3: 9
    // Index 4: 4
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
#include <iostream>
using namespace std;

class Solution {
public:
    // Prints each element alongside its index (forward traversal)
    void traverseArray(vector<int>& arr) {
        for (int i = 0; i < (int)arr.size(); i++) {   // cast avoids signed/unsigned warning
            cout << "Index " << i << ": " << arr[i] << endl;
        }
    }
};
```

Using the index `i` gives you access to both position and value — often needed in problems.

---

### Reverse Traversal

#### Python

```python
arr = [3, 7, 1, 9, 4]

for i in range(len(arr) - 1, -1, -1):
    print(arr[i])

# Output: 4, 9, 1, 7, 3
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> arr = {3, 7, 1, 9, 4};

    // Start from the last index and move backward to 0
    for (int i = (int)arr.size() - 1; i >= 0; i--) {
        cout << arr[i] << endl;
    }
    // Output: 4, 9, 1, 7, 3
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
#include <iostream>
using namespace std;

class Solution {
public:
    // Prints array elements in reverse order
    void reverseTraverse(vector<int>& arr) {
        for (int i = (int)arr.size() - 1; i >= 0; i--) {   // start at last index
            cout << arr[i] << endl;
        }
    }
};
```

Useful for: reversing an array, finding last occurrence of a value, or scanning from the end.

---

## 4. Common Array Operations

### Find Maximum and Minimum

Scan through, keep track of the best so far. Like scanning prices on a shelf and remembering the cheapest one.

#### Python

```python
arr = [12, 3, 45, 7, 23]

max_val = arr[0]   # assume first is max
min_val = arr[0]   # assume first is min

for num in arr:
    if num > max_val:
        max_val = num
    if num < min_val:
        min_val = num

print("Max:", max_val)   # Output: Max: 45
print("Min:", min_val)   # Output: Min: 3

# Time: O(n) — one pass through the array
# Space: O(1) — only two extra variables
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Scans the array once and returns the max value — O(n)
int findMax(vector<int> arr) {
    int maxVal = arr[0];   // assume first element is the maximum
    for (int i = 1; i < (int)arr.size(); i++) {
        if (arr[i] > maxVal) maxVal = arr[i];   // update if larger
    }
    return maxVal;
}

// Scans the array once and returns the min value — O(n)
int findMin(vector<int> arr) {
    int minVal = arr[0];   // assume first element is the minimum
    for (int i = 1; i < (int)arr.size(); i++) {
        if (arr[i] < minVal) minVal = arr[i];   // update if smaller
    }
    return minVal;
}

int main() {
    vector<int> arr = {12, 3, 45, 7, 23};
    cout << "Max: " << findMax(arr) << endl;   // Output: Max: 45
    cout << "Min: " << findMin(arr) << endl;   // Output: Min: 3
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Returns the maximum value — O(n) time, O(1) space
    int findMax(vector<int>& arr) {
        int maxVal = arr[0];   // initialize with first element
        for (int i = 1; i < (int)arr.size(); i++) {
            if (arr[i] > maxVal) maxVal = arr[i];   // keep updating best
        }
        return maxVal;
    }

    // Returns the minimum value — O(n) time, O(1) space
    int findMin(vector<int>& arr) {
        int minVal = arr[0];   // initialize with first element
        for (int i = 1; i < (int)arr.size(); i++) {
            if (arr[i] < minVal) minVal = arr[i];   // keep updating best
        }
        return minVal;
    }
};
```

---

### Linear Search

#### Python

```python
arr = [15, 6, 22, 8, 30]
target = 22

found = False
for i in range(len(arr)):
    if arr[i] == target:
        print(f"Found at index {i}")   # Output: Found at index 2
        found = True
        break

if not found:
    print("Not found")

# Time: O(n) worst case — target is last or not present
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Returns index of target in arr, or -1 if not found — O(n)
int linearSearch(vector<int> arr, int target) {
    for (int i = 0; i < (int)arr.size(); i++) {
        if (arr[i] == target)
            return i;   // return as soon as target is found
    }
    return -1;   // scanned all elements, not found
}

int main() {
    vector<int> arr = {15, 6, 22, 8, 30};
    int result = linearSearch(arr, 22);
    if (result != -1)
        cout << "Found at index " << result << endl;   // Output: Found at index 2
    else
        cout << "Not found" << endl;
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Returns the first index where target appears, or -1 if absent
    // Time: O(n) — worst case scans all elements
    int search(vector<int>& nums, int target) {
        for (int i = 0; i < (int)nums.size(); i++) {
            if (nums[i] == target)
                return i;   // found — return index immediately
        }
        return -1;   // not present in the array
    }
};
```

We'll cover Binary Search (O(log n)) in Section 6. Linear search is O(n) — fine for unsorted data.

---

### Reversing an Array In-Place

Two pointers start at opposite ends and swap inward. No extra array needed.

```
Start:  [1, 2, 3, 4, 5]
         ↑           ↑
        left        right

Step 1: swap 1,5 → [5, 2, 3, 4, 1]
Step 2: swap 2,4 → [5, 4, 3, 2, 1]
Step 3: left >= right → done
```

#### Python

```python
arr = [1, 2, 3, 4, 5]
left = 0
right = len(arr) - 1

while left < right:
    arr[left], arr[right] = arr[right], arr[left]   # swap
    left += 1
    right -= 1

print(arr)   # Output: [5, 4, 3, 2, 1]

# Time: O(n)  Space: O(1) — in-place, no new array created
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Reverses the array in-place — O(n) time, O(1) space
void reverseArray(vector<int>& arr) {
    int left = 0;
    int right = (int)arr.size() - 1;

    while (left < right) {
        swap(arr[left], arr[right]);   // built-in swap — no temp variable needed
        left++;    // move left pointer inward
        right--;   // move right pointer inward
    }
}

int main() {
    vector<int> arr = {1, 2, 3, 4, 5};
    reverseArray(arr);
    for (int x : arr) cout << x << " ";   // Output: 5 4 3 2 1
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Reverses the array in-place using two pointers — O(n/2) = O(n)
    void reverseArray(vector<int>& arr) {
        int left = 0;
        int right = (int)arr.size() - 1;

        while (left < right) {
            swap(arr[left], arr[right]);   // no temp needed — std::swap handles it
            left++;    // shrink from left
            right--;   // shrink from right
        }
    }
};
```

This two-pointer idea is a preview of a very powerful technique covered in detail later in this section.

---

## 5. Static vs Dynamic Arrays

| Feature              | Static Array (C++ `int[]`)  | Dynamic Array (Python list / C++ `vector`) |
| -------------------- | --------------------------- | ------------------------------------------ |
| **Size**             | Fixed at declaration        | Grows and shrinks automatically            |
| **Memory**           | Allocated once              | Reallocated when needed                    |
| **Access time**      | O(1)                        | O(1)                                       |
| **Insert at end**    | Not possible after creation | O(1) amortized                             |
| **Insert at middle** | Not applicable              | O(n) — elements shift                      |
| **Use case**         | When size is known upfront  | When size is unknown                       |

> In interviews and DSA problems, you'll mostly use **dynamic arrays** (Python lists or C++ vectors). Understanding static arrays helps you reason about space complexity.

---

## 6. Important Array Patterns

These four patterns solve the majority of array interview problems. Each gets its own dedicated file in this section.

### Pattern 1 — Frequency Counting

Count how many times each value appears. Like counting how many students got each grade.

#### Python

```python
arr = [1, 3, 2, 3, 1, 3, 2, 1]

freq = {}   # dictionary as a frequency map

for num in arr:
    if num in freq:
        freq[num] += 1
    else:
        freq[num] = 1

print(freq)   # Output: {1: 3, 2: 2, 3: 3}

# Time: O(n)   Space: O(n)
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

// Returns frequency map: each element → how many times it appears
unordered_map<int, int> frequencyCount(vector<int> arr) {
    unordered_map<int, int> freq;
    for (int num : arr) {
        freq[num]++;   // default value is 0; operator++ increments it
    }
    return freq;
}

int main() {
    vector<int> arr = {1, 3, 2, 3, 1, 3, 2, 1};
    auto freq = frequencyCount(arr);
    for (auto& p : freq) {
        cout << p.first << ": " << p.second << endl;
    }
    // Output: 1: 3, 2: 2, 3: 3
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    // Counts occurrences of each element — O(n) time, O(n) space
    unordered_map<int, int> frequencyCount(vector<int>& arr) {
        unordered_map<int, int> freq;
        for (int num : arr) {
            freq[num]++;   // unordered_map auto-initializes missing keys to 0
        }
        return freq;
    }
};
```

---

### Pattern 2 — Prefix Sum (Preview)

Build a running total array so any range sum can be answered in O(1).

#### Python

```python
arr = [2, 4, 1, 6, 3]
prefix = [0] * len(arr)
prefix[0] = arr[0]

for i in range(1, len(arr)):
    prefix[i] = prefix[i - 1] + arr[i]

print(prefix)   # Output: [2, 6, 7, 13, 16]

# Sum of elements from index 1 to 3:
# prefix[3] - prefix[0] = 13 - 2 = 11
print(prefix[3] - prefix[0])   # Output: 11

# Build: O(n)  |  Each query: O(1)
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Builds prefix sum array: prefix[i] = sum of arr[0..i]
vector<int> buildPrefix(vector<int> arr) {
    int n = arr.size();
    vector<int> prefix(n);
    prefix[0] = arr[0];   // first entry equals first element
    for (int i = 1; i < n; i++) {
        prefix[i] = prefix[i - 1] + arr[i];   // running cumulative total
    }
    return prefix;
}

int main() {
    vector<int> arr = {2, 4, 1, 6, 3};
    vector<int> prefix = buildPrefix(arr);
    // Sum from index 1 to 3: prefix[3] - prefix[0]
    cout << prefix[3] - prefix[0] << endl;   // Output: 11
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Builds prefix sum array in O(n); each range query is then O(1)
    vector<int> buildPrefix(vector<int>& arr) {
        int n = arr.size();
        vector<int> prefix(n);
        prefix[0] = arr[0];   // base case: prefix of one element
        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] + arr[i];   // add current element to running sum
        }
        return prefix;
    }

    // Returns sum of arr[l..r] — O(1) using precomputed prefix array
    int rangeSum(vector<int>& prefix, int l, int r) {
        if (l == 0) return prefix[r];         // no subtraction needed when l is 0
        return prefix[r] - prefix[l - 1];    // subtract prefix before l
    }
};
```

Covered in detail in the next file.

---

### Pattern 3 — Kadane's Algorithm (Preview)

Track the maximum subarray sum. Keep a running total; reset when it drops below zero.

#### Python

```python
arr = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

max_sum = arr[0]
current_sum = arr[0]

for i in range(1, len(arr)):
    # Extend current subarray or start fresh from here?
    current_sum = max(arr[i], current_sum + arr[i])
    max_sum = max(max_sum, current_sum)

print(max_sum)   # Output: 6  (subarray [4, -1, 2, 1])

# Time: O(n)   Space: O(1)
```

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Returns the maximum subarray sum using Kadane's algorithm — O(n)
int maxSubarraySum(vector<int> arr) {
    int maxSum = arr[0];       // best sum found anywhere so far
    int currentSum = arr[0];   // best sum of subarray ending at current index

    for (int i = 1; i < (int)arr.size(); i++) {
        // Extend existing subarray or start fresh — pick whichever is larger
        currentSum = max(arr[i], currentSum + arr[i]);
        maxSum = max(maxSum, currentSum);   // update global best
    }
    return maxSum;
}

int main() {
    vector<int> arr = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubarraySum(arr) << endl;   // Output: 6
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    // Maximum subarray sum — Kadane's Algorithm
    // Time: O(n), Space: O(1)
    int maxSubArray(vector<int>& nums) {
        int maxSum = nums[0];       // tracks the best sum seen globally
        int currentSum = nums[0];   // tracks best sum ending at this index

        for (int i = 1; i < (int)nums.size(); i++) {
            // If running sum is dragging us down, reset to current element
            currentSum = max(nums[i], currentSum + nums[i]);
            maxSum = max(maxSum, currentSum);   // never let global max drop
        }
        return maxSum;
    }
};
```

Covered in detail in a dedicated file later in this section.

---

## 7. Time and Space Complexity of Arrays

| Operation               | Time           | Notes                                  |
| ----------------------- | -------------- | -------------------------------------- |
| **Access by index**     | O(1)           | Direct memory address calculation      |
| **Search (unsorted)**   | O(n)           | Must scan all elements                 |
| **Search (sorted)**     | O(log n)       | Binary search — covered in Section 6   |
| **Insert at end**       | O(1) amortized | Dynamic arrays may resize occasionally |
| **Insert at beginning** | O(n)           | All elements shift right               |
| **Delete at end**       | O(1)           | Simply reduce size                     |
| **Delete at middle**    | O(n)           | Elements must shift left               |
| **Traversal**           | O(n)           | Visit every element once               |

> If a problem requires frequent insertions at the beginning or middle, an array is not the best tool. A linked list (Section 7) handles that better.

---

## 8. Tips for Solving Array Problems

1. **Clarify the input** — size, value range, sorted or unsorted, duplicates allowed?
2. **Dry run on paper** — draw the array and trace your logic manually before coding
3. **Think about edge cases** — empty array, single element, all same values, already sorted
4. **Start with brute force** — get O(n²) working first, then optimize
5. **Ask what you're scanning** — pairs? subarrays? prefixes? That guides the right pattern:

```
Need every pair?       → Two nested loops     → O(n²)
Need running total?    → Prefix sum            → O(n) build, O(1) query
Need a window?         → Sliding window        → O(n)
Need to compare ends?  → Two pointers          → O(n)
```

---

## 9. Key Takeaways

- An array stores elements **contiguously** in memory with **O(1) index access**
- Indexing starts at **0**. Last valid index = `n - 1`. Always check bounds.
- Traversal is **O(n)** — the base of almost every array algorithm
- **Static arrays** have fixed size. **Dynamic arrays** (Python list, C++ vector) grow automatically.
- The four core patterns — **frequency counting, prefix sum, sliding window, two pointers** — solve the majority of array interview problems
- Arrays are fast at **random access** but slow at **insert/delete in the middle** (O(n))

---

## 10. FAQs

**Why do arrays start at index 0 and not 1?**
The index represents the _offset_ from the starting memory address. The first element has zero offset, so its index is 0. This is a low-level design decision kept for performance reasons.

**Is a Python list the same as an array?**
Conceptually yes — Python lists behave like dynamic arrays. Under the hood they store references to objects rather than values directly. For numeric-heavy work, NumPy arrays give you true contiguous memory with better performance.

**When should I NOT use an array?**
Avoid arrays when you need frequent insertions or deletions at the beginning or middle. In those cases, a linked list (Section 7) is a better fit. Arrays shine when you need fast random access by index.
