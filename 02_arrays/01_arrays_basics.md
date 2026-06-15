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

#### C++

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // Fixed-size array
    int numbers[] = {10, 20, 30, 40, 50};

    cout << numbers[0] << endl;   // Output: 10
    cout << numbers[4] << endl;   // Output: 50

    // Dynamic array using vector (preferred in C++ DSA)
    vector<int> nums = {10, 20, 30, 40, 50};
    cout << nums[0] << endl;      // Output: 10
    cout << nums[4] << endl;      // Output: 50

    return 0;
}
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

#### C++

```cpp
vector<int> arr = {5, 10, 15, 20};

cout << arr.size() << endl;          // Output: 4
cout << arr[arr.size() - 1] << endl; // Output: 20

// arr[4] → undefined behavior (no bounds check in raw arrays)
// Use arr.at(4) for safe bounds-checked access
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

#### C++

```cpp
vector<int> arr = {3, 7, 1, 9, 4};

for (int i = 0; i < arr.size(); i++) {
    cout << "Index " << i << ": " << arr[i] << endl;
}
// Output:
// Index 0: 3
// Index 1: 7
// Index 2: 1
// Index 3: 9
// Index 4: 4
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

#### C++

```cpp
vector<int> arr = {3, 7, 1, 9, 4};

for (int i = arr.size() - 1; i >= 0; i--) {
    cout << arr[i] << endl;
}
// Output: 4, 9, 1, 7, 3
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

#### C++

```cpp
vector<int> arr = {12, 3, 45, 7, 23};

int maxVal = arr[0];   // assume first is max
int minVal = arr[0];   // assume first is min

for (int i = 1; i < arr.size(); i++) {
    if (arr[i] > maxVal) maxVal = arr[i];
    if (arr[i] < minVal) minVal = arr[i];
}

cout << "Max: " << maxVal << endl;   // Output: Max: 45
cout << "Min: " << minVal << endl;   // Output: Min: 3
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

#### C++

```cpp
vector<int> arr = {15, 6, 22, 8, 30};
int target = 22;
int result = -1;

for (int i = 0; i < arr.size(); i++) {
    if (arr[i] == target) {
        result = i;
        break;
    }
}

if (result != -1)
    cout << "Found at index " << result << endl;   // Output: Found at index 2
else
    cout << "Not found" << endl;
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

#### C++

```cpp
vector<int> arr = {1, 2, 3, 4, 5};
int left = 0;
int right = arr.size() - 1;

while (left < right) {
    swap(arr[left], arr[right]);   // built-in swap
    left++;
    right--;
}

for (int x : arr) cout << x << " ";   // Output: 5 4 3 2 1
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

#### C++

```cpp
#include <unordered_map>
vector<int> arr = {1, 3, 2, 3, 1, 3, 2, 1};

unordered_map<int, int> freq;

for (int num : arr) {
    freq[num]++;   // auto-initializes to 0 if key doesn't exist
}

for (auto& p : freq) {
    cout << p.first << ": " << p.second << endl;
}
// Output: 1: 3, 2: 2, 3: 3
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

#### C++

```cpp
vector<int> arr = {2, 4, 1, 6, 3};
vector<int> prefix(arr.size());
prefix[0] = arr[0];

for (int i = 1; i < arr.size(); i++) {
    prefix[i] = prefix[i - 1] + arr[i];
}

// Sum from index 1 to 3
cout << prefix[3] - prefix[0] << endl;   // Output: 11
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

#### C++

```cpp
vector<int> arr = {-2, 1, -3, 4, -1, 2, 1, -5, 4};

int maxSum = arr[0];
int currentSum = arr[0];

for (int i = 1; i < arr.size(); i++) {
    currentSum = max(arr[i], currentSum + arr[i]);
    maxSum = max(maxSum, currentSum);
}

cout << maxSum << endl;   // Output: 6
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
