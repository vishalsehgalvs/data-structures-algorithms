# Hashing vs Sorting: When to Use What

> **One-line summary:** Use hashing for $O(1)$ lookups, counting, and membership checks; use sorting when order, rank, or range matters — and combine both when a problem has a count-then-rank shape.

---

## Table of Contents

1. [Hashing or Sorting: Which One to Pick?](#1-hashing-or-sorting-which-one-to-pick)
2. [Quick Recap](#2-quick-recap)
3. [Time Complexity Comparison](#3-time-complexity-comparison)
4. [When to Use Hashing](#4-when-to-use-hashing)
   - [Use Case 1 — Counting Frequencies](#use-case-1--counting-frequencies)
   - [Use Case 2 — Checking for Duplicates](#use-case-2--checking-for-duplicates)
   - [Use Case 3 — Two Sum Problem](#use-case-3--two-sum-problem)
5. [When to Use Sorting](#5-when-to-use-sorting)
   - [Use Case 1 — K-th Smallest Element](#use-case-1--k-th-smallest-element)
   - [Use Case 2 — Group Anagrams](#use-case-2--group-anagrams)
   - [Use Case 3 — Remove Duplicates in Sorted Order](#use-case-3--remove-duplicates-in-sorted-order)
6. [Side-by-Side Decision Guide](#6-side-by-side-decision-guide)
7. [When to Combine Both](#7-when-to-combine-both)
8. [Common Pitfalls](#8-common-pitfalls)
9. [Key Takeaways](#9-key-takeaways)
10. [FAQs](#10-faqs)

---

## 1. Hashing or Sorting: Which One to Pick?

Imagine a bag of coloured marbles and two questions:

1. _"Do you have any red marbles?"_
2. _"List all the marbles from lightest to darkest colour."_

Would you use the same method for both? Probably not. That is exactly how hashing and sorting work — they solve different shapes of problem.

```
Hashing:  bag → notebook (key → count)  →  instant answer per key
Sorting:  bag → arranged sequence       →  ordered view of all items
```

---

## 2. Quick Recap

**Hashing** stores and retrieves data via key-value pairs using a hash function. Insert, delete, and search all run in $O(1)$ average time.

**Sorting** arranges elements in a defined order ($O(n \log n)$ for the best general-purpose algorithms). Once sorted, patterns and comparisons become easy — but the initial sort has an upfront cost.

---

## 3. Time Complexity Comparison

| Operation         | Hashing (HashMap/Set) | Sorting                                 |
| ----------------- | --------------------- | --------------------------------------- |
| Search an element | $O(1)$ average        | $O(\log n)$ after $O(n \log n)$ sort    |
| Insert an element | $O(1)$ average        | $O(n \log n)$ to re-sort                |
| Count frequency   | $O(n)$                | $O(n \log n)$                           |
| Find duplicates   | $O(n)$                | $O(n \log n)$                           |
| K-th smallest     | Not straightforward   | $O(n \log n)$ then index                |
| Range queries     | Not natural           | $O(\log n)$ with binary search          |
| Space used        | $O(n)$ extra          | $O(1)$ to $O(n)$ depending on algorithm |

In most **lookup and counting** tasks, hashing wins on speed. Sorting unlocks capabilities hashing cannot provide: order, rank, and range.

---

## 4. When to Use Hashing

Hashing excels at answering: **"Have I seen this before?"** or **"How many times?"**

### Use Case 1 — Counting Frequencies

**Python:**

```python
nums = [3, 1, 2, 3, 1, 3]
freq = {}

for num in nums:
    freq[num] = freq.get(num, 0) + 1

print(freq)
# Output: {3: 3, 1: 2, 2: 1}
# One O(n) pass — no sorting needed
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

int main() {
    vector<int> nums = {3, 1, 2, 3, 1, 3};
    unordered_map<int,int> freq;

    for (int n : nums) freq[n]++;

    for (auto& [key, count] : freq)
        cout << key << " -> " << count << "\n";
    // Output: {1->2, 2->1, 3->3}
    return 0;
}
```

Time: $O(n)$. Sorting would cost $O(n \log n)$ just to count.

---

### Use Case 2 — Checking for Duplicates

A hash set finds the first duplicate in a **single pass** — no sort needed.

**Python:**

```python
def has_duplicate(nums):
    seen = set()
    for num in nums:
        if num in seen:
            print(f"Duplicate found: {num}")
            return True
        seen.add(num)
    print("No duplicates")
    return False

has_duplicate([4, 7, 2, 7, 1])  # Output: Duplicate found: 7
has_duplicate([1, 2, 3, 4])     # Output: No duplicates
```

**C++:**

```cpp
#include <iostream>
#include <unordered_set>
#include <vector>
using namespace std;

bool hasDuplicate(vector<int>& nums) {
    unordered_set<int> seen;
    for (int n : nums) {
        if (seen.count(n)) {
            cout << "Duplicate found: " << n << "\n";
            return true;
        }
        seen.insert(n);
    }
    cout << "No duplicates\n";
    return false;
}

int main() {
    vector<int> a = {4, 7, 2, 7, 1};
    vector<int> b = {1, 2, 3, 4};
    hasDuplicate(a);  // Duplicate found: 7
    hasDuplicate(b);  // No duplicates
    return 0;
}
```

$O(n)$ — far faster than sort-then-scan at $O(n \log n)$.

---

### Use Case 3 — Two Sum Problem

Find two numbers that add up to a target. With hashing: $O(n)$. With sorting + two pointers: $O(n \log n)$.

**Python:**

```python
def two_sum(nums, target):
    seen = {}   # value → index

    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            print(f"Indices: {seen[complement]} and {i}")
            return [seen[complement], i]
        seen[num] = i
    return []

two_sum([2, 7, 11, 15], 9)
# Output: Indices: 0 and 1  (2 + 7 = 9)
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int,int> seen;  // value → index

    for (int i = 0; i < (int)nums.size(); i++) {
        int complement = target - nums[i];
        if (seen.count(complement)) {
            cout << "Indices: " << seen[complement] << " and " << i << "\n";
            return {seen[complement], i};
        }
        seen[nums[i]] = i;
    }
    return {};
}

int main() {
    vector<int> nums = {2, 7, 11, 15};
    twoSum(nums, 9);
    // Output: Indices: 0 and 1
    return 0;
}
```

---

## 5. When to Use Sorting

Sorting is powerful when **order matters** or when you need elements ranked or compared relative to each other.

### Use Case 1 — K-th Smallest Element

After sorting, the $k$-th smallest is at index $k-1$. Hashing cannot do this directly.

**Python:**

```python
def kth_smallest(nums, k):
    nums.sort()               # O(n log n)
    return nums[k - 1]

print(kth_smallest([7, 2, 5, 1, 9], 3))
# Sorted: [1, 2, 5, 7, 9]
# Output: 5
```

**C++:**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int kthSmallest(vector<int> nums, int k) {
    sort(nums.begin(), nums.end());  // O(n log n)
    return nums[k - 1];
}

int main() {
    vector<int> nums = {7, 2, 5, 1, 9};
    cout << kthSmallest(nums, 3) << "\n";
    // Sorted: [1, 2, 5, 7, 9]  → Output: 5
    return 0;
}
```

---

### Use Case 2 — Group Anagrams

Sort each word's characters to produce a canonical key, then group with a hash map. This is a classic example of **combining both techniques**.

**Python:**

```python
from collections import defaultdict

def group_anagrams(words):
    groups = defaultdict(list)
    for word in words:
        key = "".join(sorted(word))   # "eat" → "aet"
        groups[key].append(word)
    return list(groups.values())

words = ["eat", "tea", "tan", "ate", "nat", "bat"]
for group in group_anagrams(words):
    print(group)
# Output:
# ['eat', 'tea', 'ate']
# ['tan', 'nat']
# ['bat']
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

vector<vector<string>> groupAnagrams(vector<string>& words) {
    unordered_map<string, vector<string>> groups;

    for (auto& word : words) {
        string key = word;
        sort(key.begin(), key.end());   // "eat" → "aet"
        groups[key].push_back(word);
    }

    vector<vector<string>> result;
    for (auto& [_, group] : groups)
        result.push_back(group);
    return result;
}

int main() {
    vector<string> words = {"eat","tea","tan","ate","nat","bat"};
    for (auto& group : groupAnagrams(words)) {
        for (auto& w : group) cout << w << " ";
        cout << "\n";
    }
    // Output: eat tea ate / tan nat / bat
    return 0;
}
```

Sorting creates a consistent key; hashing provides fast grouping. Neither alone is as clean.

---

### Use Case 3 — Remove Duplicates in Sorted Order

After sorting, duplicates are adjacent — a single pass removes them and yields a sorted unique sequence.

**Python:**

```python
def unique_sorted(nums):
    nums.sort()         # [1, 1, 2, 3, 3, 4, 5]
    result = []
    for num in nums:
        if not result or result[-1] != num:
            result.append(num)
    return result

print(unique_sorted([3, 1, 4, 1, 5, 3, 2]))
# Output: [1, 2, 3, 4, 5]
```

**C++:**

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

vector<int> uniqueSorted(vector<int> nums) {
    sort(nums.begin(), nums.end());

    vector<int> result;
    for (int n : nums)
        if (result.empty() || result.back() != n)
            result.push_back(n);
    return result;
}

int main() {
    vector<int> nums = {3, 1, 4, 1, 5, 3, 2};
    for (int x : uniqueSorted(nums)) cout << x << " ";
    // Output: 1 2 3 4 5
    return 0;
}
```

Hashing could remove duplicates too, but it would not give you the results in sorted order without an extra step.

---

## 6. Side-by-Side Decision Guide

| Problem Type             | Use Hashing       | Use Sorting                |
| ------------------------ | ----------------- | -------------------------- |
| Count element frequency  | ✓ $O(n)$          | Possible but $O(n \log n)$ |
| Check if element exists  | ✓ $O(1)$          | $O(\log n)$ after sorting  |
| Find duplicates          | ✓ $O(n)$          | $O(n \log n)$              |
| Two Sum / Pair Sum       | ✓ $O(n)$          | Two pointers $O(n \log n)$ |
| K-th smallest or largest | Not direct        | ✓ Sort then index          |
| Range queries            | Not efficient     | ✓ Binary search            |
| Anagram detection        | ✓ Frequency map   | ✓ Sort characters          |
| Order matters in output  | ✗                 | ✓                          |
| Space is very tight      | Uses $O(n)$ extra | In-place $O(1)$ extra      |

> **Rule of thumb:** hashing = speed tool; sorting = order tool.

---

## 7. When to Combine Both

Many real problems benefit from both techniques together: **hash to count or group, sort to rank or order**.

**Top K Frequent Elements** — hash to count in $O(n)$, then sort to rank in $O(n \log n)$:

**Python:**

```python
def top_k_frequent(nums, k):
    freq = {}
    for n in nums:
        freq[n] = freq.get(n, 0) + 1

    # Sort by frequency descending
    sorted_keys = sorted(freq, key=freq.get, reverse=True)
    return sorted_keys[:k]

print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))
# Output: [1, 2]
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int,int> freq;
    for (int n : nums) freq[n]++;

    vector<pair<int,int>> entries(freq.begin(), freq.end());

    // Sort by count descending
    sort(entries.begin(), entries.end(), [](auto& a, auto& b){
        return a.second > b.second;
    });

    vector<int> result;
    for (int i = 0; i < k; i++)
        result.push_back(entries[i].first);
    return result;
}

int main() {
    vector<int> nums = {1, 1, 1, 2, 2, 3};
    for (int x : topKFrequent(nums, 2)) cout << x << " ";
    // Output: 1 2
    return 0;
}
```

The group anagrams problem from Section 5 is another canonical example: sort each word to form the key, hash to group.

---

## 8. Common Pitfalls

1. **Sorting just to check membership** — if you only need "does this value exist?", a hash set is $O(1)$ vs $O(n \log n)$ sort + $O(\log n)$ binary search.
2. **Ignoring space constraints** — hash maps use $O(n)$ extra memory. If memory is very tight, in-place sorting ($O(1)$ extra) is the better trade-off even at $O(n \log n)$ time.
3. **Assuming HashMap preserves order** — `dict` in Python 3.7+ preserves insertion order, but C++ `unordered_map` does not. Use `std::map` (sorted by key) or a `vector` of pairs when order matters.
4. **Using a hash map when a sort + two-pointer is cleaner** — for problems like finding all pairs that sum to a target in a sorted array, two pointers on the sorted array is $O(1)$ space and equally fast in practice.

---

## 9. Key Takeaways

- Hashing gives $O(1)$ average for insert, delete, and lookup — best for counting, membership, and frequency problems.
- Sorting costs $O(n \log n)$ upfront but enables $O(\log n)$ binary search, ranked access, and range queries.
- **Default choice:** if order does not matter and you need speed → hashing. If the result must be ordered or ranked → sorting.
- Many interview problems (top-K, group anagrams, two-sum variants) use **both** — hash first, sort second.
- Hash maps use $O(n)$ extra space; in-place sorting uses $O(1)$ extra — consider memory when choosing.
- Python: `dict` / `set` for hashing, `list.sort()` / `sorted()` for sorting. C++: `unordered_map` / `unordered_set` for hashing, `std::sort` for sorting.

---

## 10. FAQs

**Is hashing always faster than sorting?**  
For individual lookups and counting, yes — $O(1)$ per operation vs $O(n \log n)$ upfront. However, once sorted, binary search gives $O(\log n)$ per query with $O(1)$ extra space. The right choice depends on which operations dominate your problem.

**Can I use a HashMap to find the k-th smallest element?**  
Not directly — a HashMap does not maintain order. You can collect all keys into a vector and sort them ($O(k \log k)$), or use a `std::set` / Python `SortedList` which keeps keys ordered. Sorting the original array is the simplest approach.

**When should I use both hashing and sorting together?**  
When the problem has two phases: (1) count or group efficiently — hashing; (2) rank or order the results — sorting. Top-K frequent elements and group anagrams are the canonical examples.

**Does Python's `dict` maintain insertion order?**  
Yes, since Python 3.7 `dict` guarantees insertion order. C++ `std::unordered_map` does **not** — use `std::map` for sorted keys or store results in a `vector` if order matters.

**What about using a sorted set instead of sorting?**  
A sorted set (`std::set` in C++, `sortedcontainers.SortedList` in Python) keeps elements in order with $O(\log n)$ insert and $O(\log n)$ lookup. It is useful when you need both fast lookups and sorted order simultaneously, at the cost of $O(\log n)$ per operation instead of $O(1)$.
