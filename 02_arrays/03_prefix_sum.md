# Prefix Sum Technique — Fast Range Sum Queries

> **One-line summary:**
> Precompute a running total array once in O(n), then answer any range sum query in O(1) — no matter how many queries you get.

---

## Table of Contents

1. [What is the Prefix Sum Technique?](#1-what-is-the-prefix-sum-technique)
2. [Building the Prefix Sum Array](#2-building-the-prefix-sum-array)
3. [Answering Range Queries in O(1)](#3-answering-range-queries-in-o1)
4. [Code Implementation](#4-code-implementation)
5. [Cleaner Version — n+1 Size Array](#5-cleaner-version--n1-size-array)
6. [Time and Space Complexity](#6-time-and-space-complexity)
7. [Real Applications](#7-real-applications)
8. [When to Use Prefix Sum](#8-when-to-use-prefix-sum)
9. [Common Mistakes](#9-common-mistakes)
10. [Key Takeaways](#10-key-takeaways)
11. [FAQs](#11-faqs)

---

## 1. What is the Prefix Sum Technique?

Imagine you track daily expenses for a month. Every time someone asks "how much did you spend between day 5 and day 20?", you add all the values from scratch. That gets slow fast.

**Prefix sum solves this.** Instead of re-adding for every query, you precompute a running total once and answer any range query in constant time.

Think of it like a **bank statement** — each row shows the total balance so far. You never re-add all transactions from the beginning; you just read the current row.

```
Expenses: [3, 1, 4, 1, 5, 9, 2, 6]
Running:  [3, 4, 8, 9, 14, 23, 25, 31]
           ↑  ↑  ↑  ↑   ↑   ↑   ↑   ↑
         Day1 D2 D3 D4  D5  D6  D7  D8  (cumulative totals)
```

---

## 2. Building the Prefix Sum Array

**Formula:**

```
prefix[0] = arr[0]
prefix[i] = prefix[i-1] + arr[i]   for i from 1 to n-1
```

**Step-by-step for `arr = [3, 1, 4, 1, 5, 9, 2, 6]`:**

| i   | arr[i] | prefix[i] = prefix[i-1] + arr[i] |
| --- | ------ | -------------------------------- |
| 0   | 3      | 3                                |
| 1   | 1      | 3 + 1 = **4**                    |
| 2   | 4      | 4 + 4 = **8**                    |
| 3   | 1      | 8 + 1 = **9**                    |
| 4   | 5      | 9 + 5 = **14**                   |
| 5   | 9      | 14 + 9 = **23**                  |
| 6   | 2      | 23 + 2 = **25**                  |
| 7   | 6      | 25 + 6 = **31**                  |

`prefix = [3, 4, 8, 9, 14, 23, 25, 31]`

---

## 3. Answering Range Queries in O(1)

**Formula:**

```
rangeSum(l, r) = prefix[r] - prefix[l - 1]

Special case: if l == 0 → rangeSum(0, r) = prefix[r]
```

**Why does this work?**

`prefix[r]` = sum of everything from index 0 to r.
`prefix[l-1]` = sum of everything from index 0 to l-1.

Subtract → you're left with exactly the sum from l to r.

```
arr    = [3, 1, 4, 1, 5, 9,  2,  6]
prefix = [3, 4, 8, 9, 14, 23, 25, 31]
index:    0  1  2  3   4   5   6   7
```

**Query 1 — Sum from index 2 to 5:**

```
rangeSum(2, 5) = prefix[5] - prefix[1]
              = 23 - 4 = 19
Check: 4 + 1 + 5 + 9 = 19 ✓
```

**Query 2 — Sum from index 0 to 4:**

```
rangeSum(0, 4) = prefix[4]   (l=0, no subtraction)
              = 14
Check: 3 + 1 + 4 + 1 + 5 = 14 ✓
```

**Query 3 — Sum from index 5 to 7:**

```
rangeSum(5, 7) = prefix[7] - prefix[4]
              = 31 - 14 = 17
Check: 9 + 2 + 6 = 17 ✓
```

Each query = **one subtraction**. Whether the array has 10 elements or 10 million, it's always O(1).

---

## 4. Code Implementation

#### Python

```python
def build_prefix(arr):
    n = len(arr)
    prefix = [0] * n
    prefix[0] = arr[0]

    for i in range(1, n):
        prefix[i] = prefix[i - 1] + arr[i]

    return prefix


def range_sum(prefix, l, r):
    if l == 0:
        return prefix[r]              # no subtraction needed
    return prefix[r] - prefix[l - 1]


# Example
arr = [3, 1, 4, 1, 5, 9, 2, 6]
prefix = build_prefix(arr)

print(range_sum(prefix, 2, 5))   # Output: 19
print(range_sum(prefix, 0, 4))   # Output: 14
print(range_sum(prefix, 5, 7))   # Output: 17
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
    prefix[0] = arr[0];   // base case: first element

    for (int i = 1; i < n; i++) {
        prefix[i] = prefix[i - 1] + arr[i];   // add current to running total
    }

    return prefix;
}

// Returns sum of arr[l..r] in O(1) using precomputed prefix array
int rangeSum(vector<int> prefix, int l, int r) {
    if (l == 0) return prefix[r];             // no subtraction needed for l=0
    return prefix[r] - prefix[l - 1];        // subtract everything before index l
}

int main() {
    vector<int> arr = {3, 1, 4, 1, 5, 9, 2, 6};
    vector<int> prefix = buildPrefix(arr);

    cout << rangeSum(prefix, 2, 5) << endl;   // Output: 19
    cout << rangeSum(prefix, 0, 4) << endl;   // Output: 14
    cout << rangeSum(prefix, 5, 7) << endl;   // Output: 17

    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Builds prefix sum array in O(n); answers each range query in O(1)
    vector<int> buildPrefix(vector<int>& arr) {
        int n = arr.size();
        vector<int> prefix(n);
        prefix[0] = arr[0];   // first prefix equals first element

        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i - 1] + arr[i];   // cumulative sum up to index i
        }
        return prefix;
    }

    // Sum of arr[l..r] using prefix array — O(1)
    int rangeSum(vector<int>& prefix, int l, int r) {
        if (l == 0) return prefix[r];         // no subtraction when l is 0
        return prefix[r] - prefix[l - 1];    // cancel out prefix before l
    }
};
```

Build once in O(n), answer each query in O(1).

---

## 5. Cleaner Version — n+1 Size Array

Many competitive programmers use a `prefix` array of size `n+1` with `prefix[0] = 0`. This eliminates the special case when `l = 0`.

#### Python

```python
def build_prefix_clean(arr):
    n = len(arr)
    prefix = [0] * (n + 1)   # one extra slot at the start, always 0

    for i in range(n):
        prefix[i + 1] = prefix[i] + arr[i]

    return prefix


def range_sum_clean(prefix, l, r):
    # No special case needed — prefix[0] = 0 handles it naturally
    return prefix[r + 1] - prefix[l]


arr = [3, 1, 4, 1, 5, 9, 2, 6]
prefix = build_prefix_clean(arr)

print(range_sum_clean(prefix, 2, 5))   # Output: 19
print(range_sum_clean(prefix, 0, 4))   # Output: 14
```

#### C++ (simple):

```cpp
#include <vector>
using namespace std;

// Cleaner version: prefix[0] = 0, so no special case needed for l=0
vector<int> buildPrefixClean(vector<int> arr) {
    int n = arr.size();
    vector<int> prefix(n + 1, 0);   // size n+1; prefix[0] = 0 always

    for (int i = 0; i < n; i++) {
        prefix[i + 1] = prefix[i] + arr[i];   // shift by one compared to the basic version
    }
    return prefix;
}

// No edge case needed — prefix[0] = 0 handles l=0 naturally
int rangeSumClean(vector<int> prefix, int l, int r) {
    return prefix[r + 1] - prefix[l];   // consistent formula for all l values
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // n+1 size prefix array — eliminates special-case handling for l=0
    vector<int> buildPrefixClean(vector<int>& arr) {
        int n = arr.size();
        vector<int> prefix(n + 1, 0);   // extra slot at front; prefix[0] is sentinel 0

        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + arr[i];   // accumulate running total
        }
        return prefix;
    }

    // Uniform formula — works for any l without branching
    int rangeSumClean(vector<int>& prefix, int l, int r) {
        return prefix[r + 1] - prefix[l];   // prefix[0]=0 makes l=0 work naturally
    }
};
```

> Both versions work. The n+1 version is cleaner for contests. Pick one style and stay consistent.

---

## 6. Time and Space Complexity

| Operation          | Without Prefix Sum | With Prefix Sum |
| ------------------ | ------------------ | --------------- |
| Build / preprocess | O(1) — no setup    | O(n)            |
| Single range query | O(n)               | O(1)            |
| Q queries total    | O(n × Q)           | O(n + Q)        |
| Extra space        | O(1)               | O(n)            |

**Concrete example:** 10,000 queries on an array of 10,000 elements:

- Brute force: 100,000,000 operations
- Prefix sum: 10,000 + 10,000 = **20,000 operations** (5,000× faster)

Classic **space-time tradeoff** — a small amount of extra memory buys a massive speed gain.

---

## 7. Real Applications

### Count Subarrays with Sum Equal to K

A very common interview problem. Uses prefix sum + a frequency map.

#### Python

```python
def subarray_sum(arr, k):
    count = 0
    current_sum = 0
    freq = {0: 1}   # base case: empty prefix has sum 0

    for num in arr:
        current_sum += num

        # if (current_sum - k) was seen before,
        # a subarray ending here has sum exactly k
        if (current_sum - k) in freq:
            count += freq[current_sum - k]

        freq[current_sum] = freq.get(current_sum, 0) + 1

    return count


print(subarray_sum([1, 1, 1], 2))        # Output: 2  ([1,1] at pos 0 and pos 1)
print(subarray_sum([1, 2, 3, -2, 5], 5)) # Output: 2  ([2,3] and [5])
```

#### C++ (simple):

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

// Counts subarrays whose elements sum exactly to k — O(n) time, O(n) space
int subarraySum(vector<int> arr, int k) {
    int count = 0;
    int currentSum = 0;
    unordered_map<int, int> freq;
    freq[0] = 1;   // base case: empty prefix has sum 0, seen once

    for (int num : arr) {
        currentSum += num;

        // if (currentSum - k) was seen before, a subarray ending here sums to k
        if (freq.count(currentSum - k))
            count += freq[currentSum - k];   // add all such subarrays

        freq[currentSum]++;   // record this prefix sum
    }

    return count;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    // LeetCode 560: Subarray Sum Equals K — O(n) time, O(n) space
    int subarraySum(vector<int>& nums, int k) {
        int count = 0;
        int currentSum = 0;
        unordered_map<int, int> freq;
        freq[0] = 1;   // prefix sum of 0 has appeared once (empty prefix)

        for (int num : nums) {
            currentSum += num;   // running prefix sum

            // check if a previous prefix sum makes current subarray sum = k
            if (freq.count(currentSum - k))
                count += freq[currentSum - k];

            freq[currentSum]++;   // store this prefix sum for future lookups
        }

        return count;
    }
};
```

---

### Equilibrium Index

Find the index where left sum equals right sum.

#### Python

```python
def find_equilibrium(arr):
    total = sum(arr)
    left_sum = 0

    for i in range(len(arr)):
        right_sum = total - left_sum - arr[i]   # what's left after removing arr[i]

        if left_sum == right_sum:
            return i   # found equilibrium

        left_sum += arr[i]

    return -1   # no equilibrium


arr = [1, 7, 3, 6, 5, 6]
print(find_equilibrium(arr))   # Output: 3
# At index 3 (value 6): left = 1+7+3 = 11, right = 5+6 = 11 ✓
```

#### C++ (simple):

```cpp
#include <vector>
using namespace std;

// Returns the index where left sum equals right sum, or -1 if none
int findEquilibrium(vector<int> arr) {
    int total = 0;
    for (int x : arr) total += x;   // compute total sum in one pass

    int leftSum = 0;
    for (int i = 0; i < (int)arr.size(); i++) {
        int rightSum = total - leftSum - arr[i];   // right = everything not on left and not arr[i]
        if (leftSum == rightSum) return i;         // found equilibrium index
        leftSum += arr[i];                         // include arr[i] in left sum for next iteration
    }

    return -1;   // no equilibrium exists
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Returns equilibrium index where leftSum == rightSum — O(n) time, O(1) space
    int findEquilibrium(vector<int>& arr) {
        int total = 0;
        for (int x : arr) total += x;   // first pass: get total sum

        int leftSum = 0;
        for (int i = 0; i < (int)arr.size(); i++) {
            // right sum = total minus left part minus current element
            int rightSum = total - leftSum - arr[i];
            if (leftSum == rightSum) return i;   // pivot found
            leftSum += arr[i];   // grow left sum by including current element
        }

        return -1;
    }
};
```

---

### Count Evens in a Range

Prefix sum works for counts too — not just sums.

#### Python

```python
def build_even_prefix(arr):
    n = len(arr)
    prefix = [0] * (n + 1)

    for i in range(n):
        prefix[i + 1] = prefix[i] + (1 if arr[i] % 2 == 0 else 0)

    return prefix


def count_even_in_range(prefix, l, r):
    return prefix[r + 1] - prefix[l]


arr = [2, 3, 4, 7, 8, 10, 1]
prefix = build_even_prefix(arr)
print(count_even_in_range(prefix, 1, 5))   # Output: 3  (4, 8, 10)
```

> **Key insight:** Prefix sum is really a **prefix aggregate** pattern. It works for sums, counts, and more — any operation that can be "undone" by subtraction.

#### C++ (simple):

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Builds a prefix count of even numbers: prefix[i] = # of evens in arr[0..i-1]
vector<int> buildEvenPrefix(vector<int> arr) {
    int n = arr.size();
    vector<int> prefix(n + 1, 0);   // n+1 size for clean range queries

    for (int i = 0; i < n; i++) {
        // Add 1 if current element is even, else carry forward the previous count
        prefix[i + 1] = prefix[i] + (arr[i] % 2 == 0 ? 1 : 0);
    }
    return prefix;
}

// Returns count of even numbers in arr[l..r] — O(1)
int countEvenInRange(vector<int> prefix, int l, int r) {
    return prefix[r + 1] - prefix[l];   // same formula as range sum
}

int main() {
    vector<int> arr = {2, 3, 4, 7, 8, 10, 1};
    vector<int> prefix = buildEvenPrefix(arr);
    cout << countEvenInRange(prefix, 1, 5) << endl;   // Output: 3  (4, 8, 10)
    return 0;
}
```

#### C++ (LeetCode class style):

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Builds prefix count of even numbers — O(n) build, O(1) per query
    vector<int> buildEvenPrefix(vector<int>& arr) {
        int n = arr.size();
        vector<int> prefix(n + 1, 0);

        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + (arr[i] % 2 == 0 ? 1 : 0);   // count evens
        }
        return prefix;
    }

    // Count of even numbers in arr[l..r] — O(1)
    int countEvenInRange(vector<int>& prefix, int l, int r) {
        return prefix[r + 1] - prefix[l];   // prefix aggregate subtraction
    }
};
```

---

## 8. When to Use Prefix Sum

Ask yourself these questions when reading a problem:

| Question                                            | If Yes →       |
| --------------------------------------------------- | -------------- |
| Multiple range queries on the same static array?    | Use prefix sum |
| Need the sum of any subarray quickly?               | Use prefix sum |
| Count elements satisfying a condition in a range?   | Use prefix sum |
| Brute force has a nested loop with a running total? | Use prefix sum |

```mermaid
flowchart TD
    A[Multiple range queries?] -- Yes --> B[Static array?]
    B -- Yes --> C[Build prefix sum → O(n) once, O(1) per query]
    B -- No --> D[Array changes? → Fenwick Tree / Segment Tree]
    A -- No --> E[Single query? → Just loop O(n)]
```

---

## 9. Common Mistakes

| Mistake                               | Fix                                                      |
| ------------------------------------- | -------------------------------------------------------- |
| `prefix[-1]` when l=0                 | Handle `l == 0` separately, or use the n+1 size version  |
| Mixing 0-indexed and 1-indexed styles | Pick one and stay consistent throughout                  |
| Overwriting the original array        | Keep `arr` and `prefix` as separate arrays               |
| Querying after array updates          | Standard prefix sum is static — rebuild if array changes |

---

## 10. Key Takeaways

- Prefix sum = **precompute running totals once, answer range queries in O(1)**
- `rangeSum(l, r) = prefix[r] - prefix[l-1]` (handle l=0 separately)
- The **n+1 size version** (`prefix[0] = 0`) is cleaner — no edge case for l=0
- Space-time tradeoff: O(n) extra space for O(1) queries
- Works for **sums, counts, or any aggregate** that can be reversed by subtraction
- For **multiple queries on the same array**, prefix sum is almost always the right choice
- For arrays that change frequently, use a **Fenwick Tree** (covered in a later section)

---

## 11. FAQs

**Can prefix sum handle negative numbers?**
Yes. The formula works correctly regardless of sign. Negative values reduce the cumulative sum, but the math is the same.

**What if the array changes after I build the prefix array?**
A standard prefix sum is static — you must rebuild after changes. If the array updates frequently, use a Fenwick Tree (Binary Indexed Tree), covered later in this series.

**Is prefix sum the same as sliding window?**
Related but different. Prefix sum precomputes all cumulative totals for any range query. Sliding window maintains a moving window. Prefix sum handles arbitrary range queries; sliding window is better for contiguous windows of fixed or variable size.
