# Kadane's Algorithm — Maximum Subarray Sum in O(n)

> **One-line summary:**
> At every index, decide: extend the current subarray or start fresh? Keep tracking the best result. One pass, O(n) time, O(1) space.

---

## Table of Contents

1. [What is Kadane's Algorithm?](#1-what-is-kadanes-algorithm)
2. [Problem Statement](#2-problem-statement)
3. [Brute Force Approach — Why It's Slow](#3-brute-force-approach--why-its-slow)
4. [The Core Idea](#4-the-core-idea)
5. [The Two Key Variables](#5-the-two-key-variables)
6. [Kadane's Algorithm Implementation](#6-kadanes-algorithm-implementation)
7. [Step-by-Step Dry Run](#7-step-by-step-dry-run)
8. [Handling All-Negative Arrays](#8-handling-all-negative-arrays)
9. [Tracking the Actual Subarray](#9-tracking-the-actual-subarray)
10. [Brute Force vs Kadane's](#10-brute-force-vs-kadanes)
11. [Common Mistakes](#11-common-mistakes)
12. [Practice Variations](#12-practice-variations)
13. [Key Takeaways](#13-key-takeaways)
14. [FAQs](#14-faqs)

---

## 1. What is Kadane's Algorithm?

Imagine you track daily profits and losses from a small shop. You want to find the best stretch of days where your total earnings were the highest.

That's exactly what Kadane's Algorithm solves — it finds the **maximum sum contiguous subarray** in a single pass through the array.

- **Time complexity:** O(n)
- **Space complexity:** O(1)
- One of the most common interview problems involving arrays

---

## 2. Problem Statement

Given an array of integers (can include negatives), find the contiguous subarray with the largest sum and return that sum.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6
Reason: subarray [4, -1, 2, 1] = 4 + (-1) + 2 + 1 = 6
```

---

## 3. Brute Force Approach — Why It's Slow

Check every possible subarray, sum it, track the max. Two nested loops.

#### Python (Brute Force — don't use this)

```python
def max_subarray_brute(arr):
    max_sum = arr[0]

    for i in range(len(arr)):
        current_sum = 0
        for j in range(i, len(arr)):       # extend subarray from i to j
            current_sum += arr[j]
            if current_sum > max_sum:
                max_sum = current_sum

    return max_sum


print(max_subarray_brute([-2, 1, -3, 4, -1, 2, 1, -5, 4]))   # Output: 6
# Time: O(n²) — 10,000 elements = ~100 million operations
```

#### C++ (Brute Force)

```cpp
int maxSubarrayBrute(vector<int> arr) {
    int maxSum = arr[0];

    for (int i = 0; i < arr.size(); i++) {
        int currentSum = 0;
        for (int j = i; j < arr.size(); j++) {
            currentSum += arr[j];
            if (currentSum > maxSum)
                maxSum = currentSum;
        }
    }

    return maxSum;
}
```

Works correctly but is way too slow for large inputs. Kadane's does the same in O(n).

---

## 4. The Core Idea

At every position, you have **two choices**:

1. **Extend** the current subarray — add `arr[i]` to the running sum
2. **Start fresh** — begin a new subarray starting at `arr[i]`

You pick whichever gives the **larger value**.

> **Analogy:** A road trip where at each city you decide — keep driving with your current group, or ditch them and go solo? If your group is dragging you down (negative sum), leave them behind.

**Key insight:** If the running sum goes negative, it hurts every future element. Drop it and start fresh.

```
arr = [-2, 1, -3, 4, ...]

At index 1 (value=1):
  Option A: extend → -2 + 1 = -1   (worse)
  Option B: start fresh → 1         (better)
  → Choose 1, start fresh

At index 2 (value=-3):
  Option A: extend → 1 + (-3) = -2  (worse than -3 alone? No, -2 > -3)
  Option B: start fresh → -3
  → Choose -2, extend
```

---

## 5. The Two Key Variables

| Variable      | What it tracks                                   |
| ------------- | ------------------------------------------------ |
| `current_sum` | Best sum of subarray **ending at current index** |
| `max_sum`     | Best sum found **anywhere so far**               |

At each step:

```
current_sum = max(arr[i], current_sum + arr[i])
max_sum     = max(max_sum, current_sum)
```

---

## 6. Kadane's Algorithm Implementation

#### Python

```python
def max_subarray(arr):
    # Initialize both with first element, NOT zero
    # (handles all-negative arrays correctly)
    max_sum = arr[0]
    current_sum = arr[0]

    for i in range(1, len(arr)):
        # Extend current subarray or start fresh — whichever is bigger
        current_sum = max(arr[i], current_sum + arr[i])

        # Update global maximum
        max_sum = max(max_sum, current_sum)

    return max_sum


print(max_subarray([-2, 1, -3, 4, -1, 2, 1, -5, 4]))   # Output: 6
print(max_subarray([-3, -1, -4, -2]))                    # Output: -1
print(max_subarray([5]))                                  # Output: 5
```

#### C++

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int maxSubarray(vector<int> arr) {
    // Initialize with first element, NOT zero
    int maxSum = arr[0];
    int currentSum = arr[0];

    for (int i = 1; i < arr.size(); i++) {
        // Extend or start fresh — whichever is bigger
        currentSum = max(arr[i], currentSum + arr[i]);

        // Update global maximum
        maxSum = max(maxSum, currentSum);
    }

    return maxSum;
}

int main() {
    vector<int> arr = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubarray(arr) << endl;   // Output: 6

    vector<int> neg = {-3, -1, -4, -2};
    cout << maxSubarray(neg) << endl;   // Output: -1

    return 0;
}
```

---

## 7. Step-by-Step Dry Run

**Input:** `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`

| i   | arr[i] | current_sum           | max_sum | Decision             |
| --- | ------ | --------------------- | ------- | -------------------- |
| 0   | -2     | -2                    | -2      | Start here           |
| 1   | 1      | max(1, -2+1) = **1**  | 1       | Start fresh (1 > -1) |
| 2   | -3     | max(-3, 1-3) = **-2** | 1       | Extend (-2 > -3)     |
| 3   | 4      | max(4, -2+4) = **4**  | 4       | Start fresh (4 > 2)  |
| 4   | -1     | max(-1, 4-1) = **3**  | 4       | Extend (3 > -1)      |
| 5   | 2      | max(2, 3+2) = **5**   | 5       | Extend (5 > 2)       |
| 6   | 1      | max(1, 5+1) = **6**   | 6       | Extend (6 > 1)       |
| 7   | -5     | max(-5, 6-5) = **1**  | 6       | Extend (1 > -5)      |
| 8   | 4      | max(4, 1+4) = **5**   | 6       | Extend (5 > 4)       |

**Answer: 6** — the subarray `[4, -1, 2, 1]` starting at index 3.

Notice index 1: the algorithm dropped the -2 running sum and started fresh at 1. That's the core decision happening at every step.

---

## 8. Handling All-Negative Arrays

If every element is negative, the answer should be the **least negative** single element — not 0.

```
Input:  [-3, -1, -4, -2]
Output: -1   ← the best we can do
```

This works correctly **only because** we initialize `max_sum = arr[0]` (not 0). Initializing with 0 would wrongly return 0 for all-negative arrays.

#### Python

```python
print(max_subarray([-3, -1, -4, -2]))   # Output: -1  ✓
# If you had initialized max_sum = 0, you'd get 0 — WRONG
```

---

## 9. Tracking the Actual Subarray

Sometimes the interview asks for the subarray itself, not just the sum. Track start and end indices.

#### Python

```python
def max_subarray_with_indices(arr):
    max_sum = arr[0]
    current_sum = arr[0]
    start = 0
    end = 0
    temp_start = 0   # potential new start when we reset

    for i in range(1, len(arr)):
        if arr[i] > current_sum + arr[i]:
            # Starting fresh is better
            current_sum = arr[i]
            temp_start = i
        else:
            # Extending is better
            current_sum = current_sum + arr[i]

        if current_sum > max_sum:
            max_sum = current_sum
            start = temp_start   # commit the new start
            end = i

    return max_sum, arr[start:end + 1]


result, subarray = max_subarray_with_indices([-2, 1, -3, 4, -1, 2, 1, -5, 4])
print(result)    # Output: 6
print(subarray)  # Output: [4, -1, 2, 1]
```

#### C++

```cpp
pair<int, vector<int>> maxSubarrayWithIndices(vector<int> arr) {
    int maxSum = arr[0], currentSum = arr[0];
    int start = 0, end = 0, tempStart = 0;

    for (int i = 1; i < arr.size(); i++) {
        if (arr[i] > currentSum + arr[i]) {
            currentSum = arr[i];
            tempStart = i;
        } else {
            currentSum += arr[i];
        }

        if (currentSum > maxSum) {
            maxSum = currentSum;
            start = tempStart;
            end = i;
        }
    }

    vector<int> subarray(arr.begin() + start, arr.begin() + end + 1);
    return {maxSum, subarray};
}
```

---

## 10. Brute Force vs Kadane's

| Approach     | Time     | Space    | n = 10,000       |
| ------------ | -------- | -------- | ---------------- |
| Brute Force  | O(n²)    | O(1)     | ~100 million ops |
| **Kadane's** | **O(n)** | **O(1)** | **~10,000 ops**  |

Same space, 10,000× faster at n=10,000. This gap grows with input size.

---

## 11. Common Mistakes

| Mistake                                | Why it breaks                     | Fix                              |
| -------------------------------------- | --------------------------------- | -------------------------------- |
| Initializing `max_sum = 0`             | Returns 0 for all-negative arrays | Always init with `arr[0]`        |
| Starting loop from index 0             | Double-counts the first element   | Start loop from index **1**      |
| Not updating `max_sum` inside the loop | Misses the actual global max      | Update `max_sum` every iteration |
| Forgetting to handle empty array       | `arr[0]` crashes on empty input   | Validate input before calling    |

---

## 12. Practice Variations

Once you can write Kadane's from memory, try these:

| Variation                               | Key twist                                               |
| --------------------------------------- | ------------------------------------------------------- |
| Maximum product subarray                | Track both max and min (negatives flip sign)            |
| Maximum sum circular subarray           | Answer is either normal max or total_sum - min_subarray |
| Count subarrays with max sum            | Track how many times `max_sum` is matched               |
| Maximum sum subarray of size at least k | Combine prefix sum + Kadane's                           |

Start with the basic version until you can write it without looking. Then tackle the variations one at a time.

---

## 13. Key Takeaways

- Kadane's solves **maximum subarray sum** in **O(n) time, O(1) space**
- Core decision at every index: `current_sum = max(arr[i], current_sum + arr[i])`
- Always initialize with `arr[0]`, **never 0** — this handles all-negative arrays
- Start the loop from index **1** since index 0 is already used for initialization
- If the running sum goes negative, it's always better to start fresh
- This pattern — running result + global best — is a stepping stone to **dynamic programming**

---

## 14. FAQs

**What if the input array is empty?**
Always validate first. Kadane's assumes at least one element. Return 0 or raise an error for empty input based on your requirements.

**What if multiple subarrays share the same maximum sum?**
The standard algorithm returns just the sum value. It will naturally stop at the first subarray that achieves the max. Modify the index-tracking version to collect all if needed.

**Does Kadane's work on an array of all zeros?**
Yes. Both `current_sum` and `max_sum` stay at 0 throughout, and the function correctly returns 0.

**Is this dynamic programming?**
Yes — it's a simple form of DP. The value `current_sum` at each index is computed from the previous index's result. We'll revisit this connection in Section 15 (Dynamic Programming).
