# Space Complexity — Hidden Memory Costs in Algorithms

> **One-line summary:**
> Time complexity tells you how fast your code runs. Space complexity tells you how much memory it eats up. Both matter.

---

## Table of Contents

1. [What is Space Complexity?](#1-what-is-space-complexity)
2. [Why It Matters](#2-why-it-matters)
3. [Input Space vs Auxiliary Space](#3-input-space-vs-auxiliary-space)
4. [Common Space Complexities Explained](#4-common-space-complexities-explained)
5. [Hidden Memory Costs You Should Know](#5-hidden-memory-costs-you-should-know)
6. [Space vs Time Tradeoff](#6-space-vs-time-tradeoff)
7. [How to Calculate Space Complexity](#7-how-to-calculate-space-complexity)
8. [Quick Reference Table](#8-quick-reference-table)
9. [Key Takeaways](#9-key-takeaways)
10. [FAQs](#10-faqs)

---

## 1. What is Space Complexity?

Imagine packing for a trip. You care about how long it takes to pack (time), but also how much luggage space you need (memory).

- **Time complexity** → how fast your algorithm runs
- **Space complexity** → how much memory your algorithm uses

Just like time complexity, we express it using Big-O: O(1), O(n), O(n²), etc.

---

## 2. Why It Matters

Think of RAM as your desk. You work faster on a big desk, but most real systems have limited space. An algorithm that uses too much memory can:

- Crash your program
- Slow everything down due to memory swapping

In interviews, both time **and** space efficiency are evaluated. A solution that runs fast but eats gigabytes of memory is not a good solution.

---

## 3. Input Space vs Auxiliary Space

The total memory used by an algorithm has two parts:

| Type                | What it is                                                                                | Do we count it?                                  |
| ------------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------ |
| **Input Space**     | Memory taken by the input itself (the array you pass in)                                  | Usually no — it's given to us, not created by us |
| **Auxiliary Space** | Extra memory the algorithm creates on its own (temp variables, helper arrays, call stack) | **Yes — this is what we measure**                |

> When someone in an interview asks about space complexity, they almost always mean **auxiliary space**.

---

## 4. Common Space Complexities Explained

### O(1) — Constant Space

The algorithm uses a fixed number of variables no matter how large the input is.

#### Python

```python
def sum_array(arr):
    total = 0   # only this one variable is ever created

    for num in arr:
        total += num

    return total


print(sum_array([1, 2, 3, 4, 5]))   # Output: 15
# Whether arr has 5 or 5 million elements, we still only use 'total'
# Space complexity: O(1)
```

#### C++

```cpp
#include <iostream>
#include <vector>
using namespace std;

int sumArray(vector<int> arr) {
    int total = 0;   // only this one variable is ever created

    for (int i = 0; i < arr.size(); i++) {
        total += arr[i];
    }

    return total;
}

int main() {
    vector<int> arr = {1, 2, 3, 4, 5};
    cout << sumArray(arr) << endl;   // Output: 15
    return 0;
}
```

No matter how big the input, we only ever have `total`. Memory stays flat → **O(1)**.

---

### O(n) — Linear Space

Memory grows proportionally with the input size. Creating a new array that mirrors the input is the classic example.

#### Python

```python
def double_values(arr):
    result = []   # this new list grows with the input

    for num in arr:
        result.append(num * 2)

    return result


print(double_values([1, 2, 3]))   # Output: [2, 4, 6]
# arr has 3 items → result has 3 items
# arr has 1000 items → result has 1000 items
# Space complexity: O(n)
```

#### C++

```cpp
vector<int> doubleValues(vector<int> arr) {
    vector<int> result;   // this new vector grows with the input

    for (int i = 0; i < arr.size(); i++) {
        result.push_back(arr[i] * 2);
    }

    return result;
}
```

Double the input → double the memory used → **O(n)**.

---

### O(n²) — Quadratic Space

Creating a 2D structure (like a matrix) that grows in both dimensions.

#### Python

```python
def create_grid(n):
    # Creates an n × n grid filled with zeros
    grid = []

    for i in range(n):
        row = [0] * n   # each row has n elements
        grid.append(row)

    return grid


grid = create_grid(3)
# Output: [[0,0,0],[0,0,0],[0,0,0]]
# n=3 → 9 cells. n=100 → 10,000 cells. n=1000 → 1,000,000 cells
# Space complexity: O(n²)
```

#### C++

```cpp
vector<vector<int>> createGrid(int n) {
    // Creates an n × n grid filled with zeros
    vector<vector<int>> grid(n, vector<int>(n, 0));
    return grid;
}

// n=3 → 9 cells. n=100 → 10,000 cells. Grows very fast.
```

---

## 5. Hidden Memory Costs You Should Know

These are the ones that catch beginners off guard.

### The Call Stack and Recursion

Every recursive call stores information on the **call stack**. Think of it like a stack of plates — each call adds one plate, each return removes one.

```
countdown(5) calls countdown(4)
  countdown(4) calls countdown(3)
    countdown(3) calls countdown(2)
      countdown(2) calls countdown(1)
        countdown(1) calls countdown(0)
          countdown(0) → base case, returns
        countdown(1) returns
      countdown(2) returns
    ...

5 plates stacked at peak → O(n) space
```

#### Python

```python
def countdown(n):
    if n == 0:
        return          # base case — stack starts unwinding
    print(n)
    countdown(n - 1)   # each call adds a frame to the call stack


countdown(5)
# Outputs: 5, 4, 3, 2, 1
# Space complexity: O(n) — call stack goes n levels deep
```

#### C++

```cpp
void countdown(int n) {
    if (n == 0) return;   // base case
    cout << n << endl;
    countdown(n - 1);     // each call adds a stack frame
}

// countdown(5) → 5 stack frames in memory at peak
// Space complexity: O(n)
```

> Calling a deep recursion with n = 1,000,000 can crash your program with a **stack overflow**. Even simple-looking recursive functions carry a hidden O(n) space cost.

---

### Temporary Variables Inside Loops

A variable **reused** each iteration is still O(1) — it's not accumulating, it's being overwritten.

#### Python

```python
def print_squares(n):
    for i in range(1, n + 1):
        temp = i * i   # created and overwritten each loop, not stored
        print(temp)


print_squares(4)
# Output: 1, 4, 9, 16
# 'temp' is overwritten each time → Space: O(1)
```

The key: `temp` is overwritten, not added to a list. So memory stays flat.

---

### String Concatenation — A Sneaky O(n²) Trap

In Python and many languages, strings are **immutable**. Concatenating in a loop creates a brand new string each time — not a modification.

#### Python

```python
# BAD: creates a new string on every iteration → O(n²) hidden memory
def build_string_slow(n):
    result = ""
    for i in range(n):
        result = result + "x"   # new string created every time!
    return result


# GOOD: collect parts, join once → O(n)
def build_string_fast(n):
    parts = []
    for i in range(n):
        parts.append("x")       # just storing small references
    return "".join(parts)       # one final string created at the end
```

Always prefer `"".join(list)` over `+=` string concatenation in a loop.

---

## 6. Space vs Time Tradeoff

In algorithm design, you can often **trade memory for speed** or **speed for memory**. Neither is always better — it depends on your constraints.

**Real-life analogy:** You need chopped onions 3 times while cooking.

- **Option A:** Chop fresh each time → less memory, more work
- **Option B:** Chop all at once, store in a bowl → more memory, less work

| Approach                      | Time            | Space | Use When                                   |
| ----------------------------- | --------------- | ----- | ------------------------------------------ |
| Store results (cache/memoize) | O(1) lookup     | O(n)  | Memory available, speed matters            |
| Recompute each time           | O(n) per call   | O(1)  | Memory is tight                            |
| Use helper array              | Faster overall  | O(n)  | Need fast access to precomputed values     |
| In-place modification         | Slightly slower | O(1)  | Space is a priority, input can be modified |

---

## 7. How to Calculate Space Complexity

### Step 1 — List all variables and data structures

Go through your code and note every variable or structure created. Ask: does its size depend on `n`, or is it always fixed?

### Step 2 — Check for recursion

If the function calls itself, count the maximum depth it can reach. That depth = the call stack space cost.

### Step 3 — Take the dominant term

Same rule as time complexity — keep only the biggest term, drop the rest.

#### Python

```python
def analyze_me(arr):
    count = 0          # O(1) — single variable
    doubled = []       # O(n) — grows with input  ← dominant
    max_val = arr[0]   # O(1) — single variable

    for num in arr:
        doubled.append(num * 2)
        if num > max_val:
            max_val = num
        count += 1

    return doubled, max_val, count

# Space breakdown:
# count   → O(1)
# doubled → O(n)   ← dominates
# max_val → O(1)
# Total Space Complexity: O(n)
```

#### C++

```cpp
pair<vector<int>, int> analyzeMe(vector<int> arr) {
    int count = 0;          // O(1)
    vector<int> doubled;    // O(n) ← dominant
    int maxVal = arr[0];    // O(1)

    for (int i = 0; i < arr.size(); i++) {
        doubled.push_back(arr[i] * 2);
        if (arr[i] > maxVal) maxVal = arr[i];
        count++;
    }

    return {doubled, maxVal};
}
// Space complexity: O(n)
```

---

## 8. Quick Reference Table

| Space Complexity | Name         | Example                            | n = 1,000        |
| ---------------- | ------------ | ---------------------------------- | ---------------- |
| **O(1)**         | Constant     | Swap two variables                 | Fixed bytes      |
| **O(log n)**     | Logarithmic  | Recursive binary search call stack | ~10 stack frames |
| **O(n)**         | Linear       | Copy of input array                | 1,000 units      |
| **O(n log n)**   | Linearithmic | Merge sort auxiliary space         | ~10,000 units    |
| **O(n²)**        | Quadratic    | 2D matrix n × n                    | 1,000,000 units  |

Always aim for **O(1) or O(n)**. Be very careful before creating quadratic structures.

---

## 9. Key Takeaways

- Space complexity = how much **extra (auxiliary) memory** your algorithm creates
- Expressed in Big-O, same rules: keep the dominant term, drop constants
- **O(1)** → fixed memory, doesn't grow with input (best)
- **O(n)** → memory grows linearly with input (acceptable)
- **O(n²)** → memory explodes fast (avoid when possible)
- **Recursion has a hidden O(depth) space cost** from the call stack
- String concatenation in a loop is a sneaky memory trap — use `join()` instead
- There's always a **space vs time tradeoff** — know both so you can choose wisely

---

## 10. FAQs

**Is space complexity the same as auxiliary space?**
Not exactly. Total space = input space + auxiliary space. In interviews, "space complexity" almost always means auxiliary space — the extra memory your algorithm creates beyond the input. Clarify if unsure.

**Does every recursive function use O(n) space?**
No. It depends on the recursion depth. If a function recurses `n` levels deep → O(n). If it recurses `log n` levels deep (like binary search) → O(log n). Always trace the deepest possible call path.

**How do I reduce space complexity in my code?**
Three main techniques:

1. Modify the input **in-place** instead of creating a copy
2. Avoid unnecessary helper arrays — ask "do I really need to store this?"
3. Convert recursion to an **iterative loop** to eliminate call stack overhead
