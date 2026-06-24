# Binary Search Variations — First, Last, and Boundaries

> **One-line summary:**
> All binary search variations share the same $O(\log n)$ loop — the only change is what you do when `arr[mid]` matches the target: instead of returning immediately, you save the answer and keep narrowing the search in the direction that might give a better result.

---

## Table of Contents

1. [What Are Binary Search Variations?](#1-what-are-binary-search-variations)
2. [Quick Recap of Standard Binary Search](#2-quick-recap-of-standard-binary-search)
3. [Variation 1 — First Occurrence](#3-variation-1--first-occurrence)
4. [Variation 2 — Last Occurrence](#4-variation-2--last-occurrence)
5. [Variation 3 — Count of Occurrences](#5-variation-3--count-of-occurrences)
6. [Variation 4 — Floor of a Target](#6-variation-4--floor-of-a-target)
7. [Variation 5 — Ceiling of a Target](#7-variation-5--ceiling-of-a-target)
8. [Variation 6 — Strict Upper Bound](#8-variation-6--strict-upper-bound)
9. [Comparison Table of All Variations](#9-comparison-table-of-all-variations)
10. [Common Mistakes to Avoid](#10-common-mistakes-to-avoid)
11. [Practice Problems](#11-practice-problems)
12. [Key Takeaways](#12-key-takeaways)
13. [FAQs](#13-faqs)

---

## 1. What Are Binary Search Variations?

Standard binary search finds **any** occurrence of a target. But real problems are often more specific:

- What is the **first** index where `4` appears?
- What is the **last** index?
- What is the **largest value ≤ target** (floor)?
- What is the **smallest value ≥ target** (ceiling)?

All these variations use the same core loop. The only thing that changes is **what you do when `arr[mid]` matches** — instead of returning immediately, you save the answer and keep searching in the direction that might give a better result.

> Think of it like using the same recipe but changing one ingredient to get a different dish.

---

## 2. Quick Recap of Standard Binary Search

```python
# Python — Standard Binary Search
def binary_search(arr, target):
    left, right = 0, len(arr) - 1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] == target:
            return mid           # Found — return immediately
        elif arr[mid] < target:
            left = mid + 1       # Search right half
        else:
            right = mid - 1      # Search left half

    return -1                    # Not found
```

**C++ (simple):**

```cpp
// C++ (simple) — Standard Binary Search
#include <vector>

int binarySearch(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;  // Safe midpoint

        if      (arr[mid] == target) return mid;       // Found — return immediately
        else if (arr[mid] < target)  left  = mid + 1;  // Search right half
        else                         right = mid - 1;  // Search left half
    }
    return -1;  // Not found
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Standard Binary Search
#include <vector>

class Solution {
public:
    int search(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;  // Safe midpoint

            if      (arr[mid] == target) return mid;       // Found — return immediately
            else if (arr[mid] < target)  left  = mid + 1;  // Search right half
            else                         right = mid - 1;  // Search left half
        }
        return -1;  // Not found
    }
};
```

For `arr = [1, 3, 5, 7, 9, 11]` and `target = 7`, this returns index `3`. Time complexity: $O(\log n)$.

---

## 3. Variation 1 — First Occurrence

**Problem:** find the index of the **leftmost** occurrence of a repeated element.

`arr = [2, 4, 4, 4, 7, 9]`, target = `4` → expected answer: index `1`

Standard binary search might land on index `2` (the middle `4`). The fix: when you find the target, **save the answer** and keep searching **left**.

```python
# Python — First Occurrence
def first_occurrence(arr, target):
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] == target:
            result = mid         # Save — but keep searching left
            right = mid - 1      # ← move left
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return result
```

**C++ (simple):**

```cpp
// C++ (simple) — First Occurrence
#include <vector>

int firstOccurrence(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1, result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            result = mid;         // Save — but keep searching left
            right = mid - 1;      // Move left to find earlier occurrence
        } else if (arr[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return result;
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — First Occurrence
#include <vector>

class Solution {
public:
    int searchFirst(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (arr[mid] == target) {
                result = mid;         // Save — but keep searching left
                right = mid - 1;      // Move left to find earlier occurrence
            } else if (arr[mid] < target)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return result;
    }
};
```

**Dry run** on `[2, 4, 4, 4, 7, 9]`, target = `4`:

```
left=0, right=5 → mid=2, arr[2]=4 → save result=2, right=1
left=0, right=1 → mid=0, arr[0]=2 → 2 < 4, left=1
left=1, right=1 → mid=1, arr[1]=4 → save result=1, right=0
left=1 > right=0 → stop

Return: 1 ✓
```

---

## 4. Variation 2 — Last Occurrence

**Problem:** find the index of the **rightmost** occurrence.

Mirror of first occurrence — when you find the target, **save the answer** and keep searching **right**.

```python
# Python — Last Occurrence
def last_occurrence(arr, target):
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] == target:
            result = mid         # Save — but keep searching right
            left = mid + 1       # → move right
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return result
```

**C++ (simple):**

```cpp
// C++ (simple) — Last Occurrence
#include <vector>

int lastOccurrence(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1, result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            result = mid;         // Save — but keep searching right
            left = mid + 1;       // Move right to find later occurrence
        } else if (arr[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return result;
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Last Occurrence
#include <vector>

class Solution {
public:
    int searchLast(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (arr[mid] == target) {
                result = mid;         // Save — but keep searching right
                left = mid + 1;       // Move right to find later occurrence
            } else if (arr[mid] < target)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return result;
    }
};
```

For `[2, 4, 4, 4, 7, 9]`, target = `4` → returns index `3`.

---

## 5. Variation 3 — Count of Occurrences

Combine first and last occurrence to count duplicates in $O(\log n)$ — no need to scan the full array.

```python
# Python — Count Occurrences
def count_occurrences(arr, target):
    first = first_occurrence(arr, target)
    if first == -1:
        return 0                         # Target not present
    last = last_occurrence(arr, target)
    return last - first + 1             # count = last index − first index + 1
```

**C++ (simple):**

```cpp
// C++ (simple) — Count Occurrences
// (requires firstOccurrence and lastOccurrence defined above)
int countOccurrences(const std::vector<int>& arr, int target) {
    int first = firstOccurrence(arr, target);  // Find leftmost index
    if (first == -1) return 0;                 // Target not present
    int last = lastOccurrence(arr, target);    // Find rightmost index
    return last - first + 1;                   // count = last - first + 1
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Count Occurrences
#include <vector>

class Solution {
    int findFirst(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == target) { result = mid; right = mid - 1; }  // Save, search left
            else if (arr[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return result;
    }

    int findLast(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == target) { result = mid; left = mid + 1; }   // Save, search right
            else if (arr[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return result;
    }

public:
    int countOccurrences(vector<int>& arr, int target) {
        int first = findFirst(arr, target);  // Leftmost index
        if (first == -1) return 0;           // Target not present
        int last = findLast(arr, target);    // Rightmost index
        return last - first + 1;             // count = last - first + 1
    }
};
```

**Examples on `[2, 4, 4, 4, 7, 9]`:**

| Target | First | Last | Count |
| ------ | ----- | ---- | ----- |
| `4`    | 1     | 3    | **3** |
| `7`    | 4     | 4    | **1** |
| `5`    | -1    | —    | **0** |

---

## 6. Variation 4 — Floor of a Target

**Floor:** the **largest element ≤ target** in the array.

For `target = 6` in `[1, 3, 5, 7, 9]` → floor is `5` (rounding down).

```python
# Python — Floor Value
def floor_value(arr, target):
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] <= target:
            result = arr[mid]    # This could be the floor — search right for a closer value
            left = mid + 1       # → move right
        else:
            right = mid - 1      # arr[mid] > target — go left

    return result
```

**C++ (simple):**

```cpp
// C++ (simple) — Floor Value
#include <vector>

int floorValue(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1, result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] <= target) {
            result = arr[mid];    // This could be the floor — search right for closer value
            left = mid + 1;       // Move right
        } else
            right = mid - 1;      // arr[mid] > target — go left
    }
    return result;
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Floor Value (largest element ≤ target)
#include <vector>

class Solution {
public:
    int floorValue(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (arr[mid] <= target) {
                result = arr[mid];    // Candidate floor — search right for closer value
                left = mid + 1;       // Move right
            } else
                right = mid - 1;      // arr[mid] > target — go left
        }
        return result;
    }
};
```

| Array             | Target | Floor |
| ----------------- | ------ | ----- |
| `[1, 3, 5, 7, 9]` | `6`    | `5`   |
| `[1, 3, 5, 7, 9]` | `5`    | `5`   |
| `[1, 3, 5, 7, 9]` | `0`    | `-1`  |

---

## 7. Variation 5 — Ceiling of a Target

**Ceiling:** the **smallest element ≥ target** in the array.

For `target = 6` in `[1, 3, 5, 7, 9]` → ceiling is `7` (rounding up).

```python
# Python — Ceiling Value
def ceiling_value(arr, target):
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] >= target:
            result = arr[mid]    # This could be the ceiling — search left for a smaller value
            right = mid - 1      # ← move left
        else:
            left = mid + 1       # arr[mid] < target — go right

    return result
```

**C++ (simple):**

```cpp
// C++ (simple) — Ceiling Value
#include <vector>

int ceilingValue(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1, result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] >= target) {
            result = arr[mid];    // Candidate ceiling — search left for smaller value
            right = mid - 1;      // Move left
        } else
            left = mid + 1;       // arr[mid] < target — go right
    }
    return result;
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Ceiling Value (smallest element ≥ target)
#include <vector>

class Solution {
public:
    int ceilingValue(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (arr[mid] >= target) {
                result = arr[mid];    // Candidate ceiling — search left for smaller value
                right = mid - 1;      // Move left
            } else
                left = mid + 1;       // arr[mid] < target — go right
        }
        return result;
    }
};
```

| Array             | Target | Ceiling |
| ----------------- | ------ | ------- |
| `[1, 3, 5, 7, 9]` | `6`    | `7`     |
| `[1, 3, 5, 7, 9]` | `9`    | `9`     |
| `[1, 3, 5, 7, 9]` | `10`   | `-1`    |

---

## 8. Variation 6 — Strict Upper Bound

**Strict upper bound:** the **smallest element > target** (excludes the target itself).

For `target = 5` in `[1, 3, 5, 7, 9]` → result is `7` (not `5`).

The only difference from ceiling: condition is `arr[mid] > target` instead of `arr[mid] >= target`.

```python
# Python — Strict Upper Bound
def strict_upper_bound(arr, target):
    left, right = 0, len(arr) - 1
    result = -1

    while left <= right:
        mid = left + (right - left) // 2

        if arr[mid] > target:          # Strictly greater — not equal
            result = arr[mid]
            right = mid - 1
        else:
            left = mid + 1             # arr[mid] <= target — go right

    return result
```

**C++ (simple):**

```cpp
// C++ (simple) — Strict Upper Bound (smallest element > target)
#include <vector>

int strictUpperBound(const std::vector<int>& arr, int target) {
    int left = 0, right = (int)arr.size() - 1, result = -1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] > target) {      // Strictly greater — not equal
            result = arr[mid];
            right = mid - 1;          // Search left for smaller qualifying value
        } else
            left = mid + 1;           // arr[mid] <= target — go right
    }
    return result;
}
```

**C++ (LeetCode class style):**

```cpp
// C++ (LeetCode class style) — Strict Upper Bound
#include <vector>

class Solution {
public:
    int upperBound(vector<int>& arr, int target) {
        int left = 0, right = (int)arr.size() - 1, result = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;

            if (arr[mid] > target) {      // Strictly greater — not equal
                result = arr[mid];
                right = mid - 1;          // Search left for smaller qualifying value
            } else
                left = mid + 1;           // arr[mid] <= target — go right
        }
        return result;
    }
};
```

| Array             | Target | Strict upper bound |
| ----------------- | ------ | ------------------ |
| `[1, 3, 5, 7, 9]` | `5`    | `7`                |
| `[1, 3, 5, 7, 9]` | `9`    | `-1`               |
| `[1, 3, 5, 7, 9]` | `0`    | `1`                |

---

## 9. Comparison Table of All Variations

| Variation          | Condition when `arr[mid]` matches / qualifies | Direction after saving | Use case                             |
| ------------------ | --------------------------------------------- | ---------------------- | ------------------------------------ |
| Standard           | `arr[mid] == target`                          | Return immediately     | Check if element exists              |
| First occurrence   | `arr[mid] == target`                          | Move `right` left      | Find first index of duplicate        |
| Last occurrence    | `arr[mid] == target`                          | Move `left` right      | Find last index of duplicate         |
| Count occurrences  | Combine first + last                          | —                      | Count duplicates in $O(\log n)$      |
| Floor              | `arr[mid] <= target`                          | Move `left` right      | Largest value not exceeding target   |
| Ceiling            | `arr[mid] >= target`                          | Move `right` left      | Smallest value at least target       |
| Strict upper bound | `arr[mid] > target`                           | Move `right` left      | Smallest value strictly above target |

---

## 10. Common Mistakes to Avoid

**1. Forgetting the `result` variable**

In first/last occurrence, if you do not store the answer before moving the pointer you lose it. Always initialize `result = -1` and update it inside the `if` block.

**2. Wrong loop condition**

Always use `left <= right`. Using `left < right` skips the last valid element and produces wrong answers.

**3. Integer overflow in the midpoint**

Use `mid = left + (right - left) // 2` instead of `(left + right) // 2` to prevent overflow in languages with fixed-size integers.

**4. Applying on unsorted arrays**

All binary search variations require a sorted array. Verify this before applying any of these patterns.

**5. Confusing ceiling with strict upper bound**

Ceiling includes the target itself (`>=`). Strict upper bound excludes it (`>`). One character difference, completely different results.

---

## 11. Practice Problems

Try these on paper first, then code them up:

| Problem            | Array                         | Target | Expected Answer           |
| ------------------ | ----------------------------- | ------ | ------------------------- |
| Count occurrences  | `[1, 1, 2, 2, 3, 4, 4, 4, 5]` | `4`    | `3`                       |
| Floor              | `[10, 20, 30, 40, 50]`        | `35`   | `30`                      |
| Ceiling            | `[10, 20, 30, 40, 50]`        | `35`   | `40`                      |
| First + Last       | `[2, 2, 2, 2, 2]`             | `2`    | first=`0`, last=`4`       |
| Strict upper bound | `[1, 3, 5, 7]`                | `7`    | `-1` (no element greater) |

---

## 12. Key Takeaways

- All binary search variations run in $O(\log n)$ — the same as standard binary search.
- The **core insight**: instead of returning the moment you find the target, save the answer and keep narrowing the search in the direction that might give a better result.
- **First occurrence** → save and move `right` left.
- **Last occurrence** → save and move `left` right.
- **Floor** → save when `arr[mid] <= target`, move `left` right.
- **Ceiling** → save when `arr[mid] >= target`, move `right` left.
- **Strict upper bound** → save when `arr[mid] > target`, move `right` left.
- Count occurrences = `last − first + 1`, computed in $O(\log n)$.

---

## 13. FAQs

**Do binary search variations only work on sorted arrays?**

Yes. All binary search variations require a sorted array. The entire logic depends on knowing which direction to search after each comparison. On an unsorted array the results are unpredictable.

**What is the difference between floor and ceiling?**

Floor gives the **largest element ≤ target** (round down). Ceiling gives the **smallest element ≥ target** (round up). They bracket the position where the target would sit in the array.

**What is the time complexity of these variations?**

All variations run in $O(\log n)$, identical to standard binary search. The extra logic inside the loop (saving the result, adjusting only one pointer) does not change the number of iterations.

**When would I use count of occurrences instead of just searching?**

When you need the frequency of an element in a large sorted array. Using `first` and `last` occurrence gives you the count in $O(\log n)$ instead of $O(n)$ for a linear scan — a significant advantage for large datasets.
