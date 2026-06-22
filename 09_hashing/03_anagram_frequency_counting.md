# Frequency Map Pattern: Solve Coding Problems Faster

> **One-line summary:** A frequency map is a hash map from element → count; building it in one $O(n)$ pass unlocks a family of patterns — duplicates, anagrams, top-K, first unique — that would otherwise require $O(n^2)$ or $O(n \log n)$ brute force.

---

## Table of Contents

1. [What is a Frequency Map?](#1-what-is-a-frequency-map)
2. [Why Frequency Maps Are Powerful](#2-why-frequency-maps-are-powerful)
3. [Building a Frequency Map](#3-building-a-frequency-map)
4. [Pattern 1 — Find Duplicates](#4-pattern-1--find-duplicates)
5. [Pattern 2 — First Non-Repeating Element](#5-pattern-2--first-non-repeating-element)
6. [Pattern 3 — Anagram Check](#6-pattern-3--anagram-check)
7. [Pattern 4 — Element with Highest Frequency](#7-pattern-4--element-with-highest-frequency)
8. [Pattern 5 — Top K Frequent Elements](#8-pattern-5--top-k-frequent-elements)
9. [Pattern 6 — Count Elements with a Given Frequency](#9-pattern-6--count-elements-with-a-given-frequency)
10. [Frequency of Frequency Pattern](#10-frequency-of-frequency-pattern)
11. [When to Use Frequency Map vs Other Approaches](#11-when-to-use-frequency-map-vs-other-approaches)
12. [Tips for Using Frequency Maps Effectively](#12-tips-for-using-frequency-maps-effectively)
13. [Key Takeaways](#13-key-takeaways)
14. [FAQs](#14-faqs)

---

## 1. What is a Frequency Map?

Imagine a bag of coloured marbles. You grab a notebook and write each colour with its count. That notebook is a **frequency map**.

In programming, a frequency map is a `HashMap` where:
- **Key** = an element from your input
- **Value** = how many times that element appears

```
Input: [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]

Frequency map:
  3 → 2
  1 → 2
  4 → 1
  5 → 3   ← appears most
  9 → 1
  2 → 1
  6 → 1
```

---

## 2. Why Frequency Maps Are Powerful

Without a frequency map, counting occurrences typically requires nested loops — $O(n^2)$ time. With a hash map, you count everything in a **single pass** — $O(n)$ time.

| Approach | Time | Notes |
|---|---|---|
| Brute force (nested loops) | $O(n^2)$ | Check every pair |
| Sort then scan | $O(n \log n)$ | Useful when order matters |
| Frequency map | $O(n)$ | Single pass, hash map lookup |

Think of it like a cashier scanning items. Instead of re-scanning the whole cart to count each item, they scan once and the register tracks counts automatically.

---

## 3. Building a Frequency Map

The core logic is always the same: loop through each element and increment its count.

**Python:**

```python
nums = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]

freq = {}
for num in nums:
    freq[num] = freq.get(num, 0) + 1   # get(key, 0) avoids KeyError

print(freq)
# Output: {3: 2, 1: 2, 4: 1, 5: 3, 9: 1, 2: 1, 6: 1}
```

Python's `collections.Counter` is a shortcut for the same thing:

```python
from collections import Counter

freq = Counter([3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5])
print(freq)
# Output: Counter({5: 3, 3: 2, 1: 2, 4: 1, 9: 1, 2: 1, 6: 1})
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

int main() {
    vector<int> nums = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5};
    unordered_map<int, int> freq;

    for (int num : nums)
        freq[num]++;   // operator[] inserts 0 if missing, then increments

    for (auto& [key, count] : freq)
        cout << key << " -> " << count << "\n";
    // Output (order may vary):
    // 5 -> 3, 3 -> 2, 1 -> 2, 4 -> 1, ...

    return 0;
}
```

Both build the map in $O(n)$ time and $O(n)$ space.

---

## 4. Pattern 1 — Find Duplicates

Check whether any element appears more than once. Return `True` as soon as any count exceeds 1 — no need to finish the entire array.

**Python:**

```python
def has_duplicate(nums):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1
        if freq[num] > 1:
            return True
    return False

print(has_duplicate([1, 2, 3, 4]))  # Output: False
print(has_duplicate([1, 2, 3, 1]))  # Output: True
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

bool hasDuplicate(vector<int>& nums) {
    unordered_map<int, int> freq;
    for (int num : nums) {
        if (++freq[num] > 1) return true;
    }
    return false;
}

int main() {
    vector<int> a = {1, 2, 3, 4};
    vector<int> b = {1, 2, 3, 1};
    cout << hasDuplicate(a) << "\n";  // 0 (false)
    cout << hasDuplicate(b) << "\n";  // 1 (true)
    return 0;
}
```

Early termination means we stop the moment a duplicate is found — $O(1)$ best case, $O(n)$ worst case.

---

## 5. Pattern 2 — First Non-Repeating Element

Find the first character (or number) that appears **exactly once**. Always two passes: count first, then find.

**Python:**

```python
def first_unique_char(s):
    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1

    for i, char in enumerate(s):
        if freq[char] == 1:
            return i   # index of first unique character
    return -1

print(first_unique_char("leetcode"))  # Output: 0 ('l')
print(first_unique_char("aabb"))      # Output: -1
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <string>
using namespace std;

int firstUniqueChar(const string& s) {
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;

    for (int i = 0; i < (int)s.size(); i++)
        if (freq[s[i]] == 1) return i;
    return -1;
}

int main() {
    cout << firstUniqueChar("leetcode") << "\n";  // 0
    cout << firstUniqueChar("aabb")     << "\n";  // -1
    return 0;
}
```

Time: $O(n)$ — two passes. Space: $O(1)$ for lowercase-only strings (at most 26 keys).

---

## 6. Pattern 3 — Anagram Check

Two strings are anagrams if they use the same characters with the same counts. Use one map: **increment** for the first string, **decrement** for the second.

**Python:**

```python
def is_anagram(s, t):
    if len(s) != len(t):
        return False

    freq = {}
    for char in s:
        freq[char] = freq.get(char, 0) + 1

    for char in t:
        freq[char] = freq.get(char, 0) - 1
        if freq[char] < 0:   # t has a character s doesn't have enough of
            return False

    return True

print(is_anagram("anagram", "nagaram"))  # Output: True
print(is_anagram("rat",     "car"))      # Output: False
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <string>
using namespace std;

bool isAnagram(const string& s, const string& t) {
    if (s.size() != t.size()) return false;

    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    for (char c : t) {
        if (--freq[c] < 0) return false;
    }
    return true;
}

int main() {
    cout << isAnagram("anagram", "nagaram") << "\n";  // 1 (true)
    cout << isAnagram("rat",     "car")     << "\n";  // 0 (false)
    return 0;
}
```

If any count goes negative, `t` has a character `s` lacks — not an anagram.

---

## 7. Pattern 4 — Element with Highest Frequency

Find the most common element. Build the map, then scan values for the maximum count.

**Python:**

```python
def most_frequent(nums):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1
    return max(freq, key=freq.get)   # key with highest value

print(most_frequent([1, 3, 2, 1, 4, 1]))  # Output: 1
print(most_frequent([7, 7, 5, 5, 5]))     # Output: 5
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

int mostFrequent(vector<int>& nums) {
    unordered_map<int, int> freq;
    for (int n : nums) freq[n]++;

    int best = -1, bestCount = 0;
    for (auto& [key, count] : freq) {
        if (count > bestCount) { bestCount = count; best = key; }
    }
    return best;
}

int main() {
    vector<int> a = {1, 3, 2, 1, 4, 1};
    vector<int> b = {7, 7, 5, 5, 5};
    cout << mostFrequent(a) << "\n";  // 1
    cout << mostFrequent(b) << "\n";  // 5
    return 0;
}
```

---

## 8. Pattern 5 — Top K Frequent Elements

Return the $k$ elements that appear most often. Build the frequency map, then sort keys by count descending.

**Python:**

```python
def top_k_frequent(nums, k):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    sorted_keys = sorted(freq, key=freq.get, reverse=True)
    return sorted_keys[:k]

print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))  # Output: [1, 2]
print(top_k_frequent([4, 4, 4, 7, 7, 9], 1))  # Output: [4]
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int n : nums) freq[n]++;

    // Collect unique keys
    vector<int> keys;
    for (auto& [key, _] : freq) keys.push_back(key);

    // Sort by frequency descending
    sort(keys.begin(), keys.end(), [&](int a, int b) {
        return freq[a] > freq[b];
    });

    keys.resize(k);
    return keys;
}

int main() {
    vector<int> nums = {1, 1, 1, 2, 2, 3};
    for (int x : topKFrequent(nums, 2)) cout << x << " ";
    // Output: 1 2
    return 0;
}
```

Time: $O(n \log n)$ due to sorting. A heap-based approach can achieve $O(n \log k)$ once you have covered heaps.

---

## 9. Pattern 6 — Count Elements with a Given Frequency

How many elements appear exactly $k$ times? Build the map, then count values that equal $k$.

**Python:**

```python
def count_with_frequency(nums, k):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    return sum(1 for count in freq.values() if count == k)

print(count_with_frequency([1,2,2,3,3,3], 2))  # Output: 1 (only 2 appears twice)
print(count_with_frequency([5,5,6,6,7,7], 2))  # Output: 3 (5, 6, 7 each appear twice)
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

int countWithFrequency(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int n : nums) freq[n]++;

    int result = 0;
    for (auto& [_, count] : freq)
        if (count == k) result++;
    return result;
}

int main() {
    vector<int> a = {1,2,2,3,3,3};
    vector<int> b = {5,5,6,6,7,7};
    cout << countWithFrequency(a, 2) << "\n";  // 1
    cout << countWithFrequency(b, 2) << "\n";  // 3
    return 0;
}
```

---

## 10. Frequency of Frequency Pattern

A two-level summary: count **how many elements share the same frequency**. Useful for distribution problems.

**Python:**

```python
def freq_of_freq(nums):
    freq = {}
    for num in nums:
        freq[num] = freq.get(num, 0) + 1   # element → count

    freq_freq = {}
    for count in freq.values():
        freq_freq[count] = freq_freq.get(count, 0) + 1  # count → how many elements

    return freq_freq

result = freq_of_freq([1, 1, 2, 2, 3])
print(result)
# freq: {1:2, 2:2, 3:1}
# Output: {2: 2, 1: 1}
# Meaning: 2 elements appear 2 times; 1 element appears 1 time
```

**C++:**

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
using namespace std;

unordered_map<int,int> freqOfFreq(vector<int>& nums) {
    unordered_map<int,int> freq;
    for (int n : nums) freq[n]++;

    unordered_map<int,int> ff;
    for (auto& [_, count] : freq) ff[count]++;
    return ff;
}

int main() {
    vector<int> nums = {1, 1, 2, 2, 3};
    for (auto& [count, howMany] : freqOfFreq(nums))
        cout << howMany << " element(s) appear " << count << " time(s)\n";
    // Output: 2 element(s) appear 2 time(s)
    //         1 element(s) appear 1 time(s)
    return 0;
}
```

---

## 11. When to Use Frequency Map vs Other Approaches

| Problem Type | Naive Approach | With Frequency Map | Improvement |
|---|---|---|---|
| Count occurrences | Nested loops $O(n^2)$ | Single pass $O(n)$ | High |
| Find duplicates | Sort then scan $O(n \log n)$ | HashMap $O(n)$ | Medium |
| Anagram check | Sort both strings $O(n \log n)$ | Two-pass HashMap $O(n)$ | Medium |
| Top K frequent | Count and sort $O(n \log n)$ | Freq map + sort $O(n \log n)$ | Cleaner code |
| First unique | Two nested loops $O(n^2)$ | Two-pass $O(n)$ | High |

**Rule of thumb:** reach for a frequency map whenever the problem involves counting, grouping, or querying element occurrences.

---

## 12. Tips for Using Frequency Maps Effectively

1. **Build the full map first**, then query it — unless the problem benefits from early termination (duplicate check).
2. Use `dict.get(key, 0)` in Python or `map[key]++` in C++ to avoid key-not-found errors.
3. Frequency maps cost $O(n)$ extra space. If space is very tight, sorting is an alternative at the cost of $O(n \log n)$ time.
4. For **lowercase-only character** problems, a fixed array of size 26 can replace a hash map for slightly better constant-factor performance.
5. In Python, two `Counter` objects can be compared directly with `==` to check identical frequencies.
6. When iterating over frequencies to find max/min, iterate over `.values()`, not `.keys()`.

---

## 13. Key Takeaways

- A frequency map is a hash map of element → count, built in a single $O(n)$ pass.
- The universal recipe: **count first, then answer questions**.
- Six core patterns: find duplicates, first unique, anagram check, most frequent, top-K frequent, count by frequency.
- The **frequency of frequency** pattern adds a second level to summarise distributions.
- In most cases, frequency maps reduce time complexity from $O(n^2)$ or $O(n \log n)$ to $O(n)$.
- Space is always $O(k)$ where $k$ = number of unique elements ($O(1)$ for fixed alphabets).
- Python `Counter` and C++ `unordered_map` are the go-to built-ins.

---

## 14. FAQs

**When should I use a frequency map instead of sorting?**  
Use a frequency map when you need $O(n)$ time and the problem involves counting, checking duplicates, or looking up occurrences. Use sorting when you need elements in order or when extra memory is very limited.

**What is the space complexity of a frequency map?**  
$O(k)$ where $k$ is the number of unique elements. Worst case (all elements unique) is $O(n)$. For lowercase-letter-only problems the map has at most 26 entries — effectively $O(1)$.

**Can frequency maps work with strings as keys?**  
Yes. Hash maps support any hashable type — strings, characters, tuples. A common interview trick is to use a **sorted string** as a key to group all anagrams together in a single map.

**Should I use `Counter` or a plain `dict` in Python?**  
`Counter` is more concise and adds helpful methods like `most_common(k)`. A plain `dict` is slightly faster to construct and more explicit. Either works; prefer `Counter` for readability in competitive settings.

**What is the difference between `freq.values()` and `freq.keys()` when finding the maximum?**  
`freq.keys()` gives the elements; `freq.values()` gives the counts. To find the **most frequent element** use `max(freq, key=freq.get)` (iterates keys, compares by value). To find the **highest count** use `max(freq.values())`.
