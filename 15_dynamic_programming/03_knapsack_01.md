# 0/1 Knapsack Problem: Dynamic Programming for Beginners

## What is the 0/1 Knapsack Problem?

Given a knapsack with capacity **W** and **n items** (each with a weight and value), select items to **maximize total value without exceeding the weight limit**.

**0/1 rule:** you either take an item (1) or leave it (0). No fractions allowed (that's Fractional Knapsack).

---

## Why Greedy Doesn't Work Here

| Item | Weight | Value |
|------|--------|-------|
| A | 4 kg | 5 |
| B | 3 kg | 4 |
| C | 2 kg | 3 |

Capacity = 5 kg.

- **Greedy** picks A (best value) → only 1 kg left, nothing fits. Total = **5**.
- **Optimal** picks B + C → 3+2=5 kg, value = **7**. Greedy fails.

DP is required for 0/1 Knapsack.

---

## The Two Choices at Every Item

For every item `i` with remaining capacity `w`:
1. **Exclude it** → best value stays as `dp[i-1][w]`
2. **Include it** (if `weight[i] <= w`) → `value[i] + dp[i-1][w - weight[i]]`

Take the **maximum** of both.

---

## Approach 1 — Naive Recursion

#### Python

```python
def knapsack_recursive(weights, values, n, W):
    if n == 0 or W == 0: return 0           # base case

    if weights[n-1] > W:                    # item too heavy — skip
        return knapsack_recursive(weights, values, n-1, W)

    include = values[n-1] + knapsack_recursive(weights, values, n-1, W - weights[n-1])
    exclude = knapsack_recursive(weights, values, n-1, W)
    return max(include, exclude)


weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
print(knapsack_recursive(weights, values, 4, 7))    # 9
# O(2^n) — recalculates same subproblems many times
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Naive recursion — O(2^n) time
int knapsackRec(vector<int>& weights, vector<int>& values, int n, int W) {
    if (n == 0 || W == 0) return 0;

    if (weights[n-1] > W)                       // too heavy — skip
        return knapsackRec(weights, values, n-1, W);

    int include = values[n-1] + knapsackRec(weights, values, n-1, W - weights[n-1]);
    int exclude = knapsackRec(weights, values, n-1, W);
    return max(include, exclude);
}

int main() {
    vector<int> w = {1,3,4,5}, v = {1,4,5,7};
    cout << knapsackRec(w, v, 4, 7) << "\n";    // 9
    return 0;
}
// Time: O(2^n) | Space: O(n) call stack
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // 0/1 Knapsack — naive recursive (to show overlapping subproblem issue)
    int knapsack(int W, vector<int>& weights, vector<int>& values) {
        return solve(weights, values, weights.size(), W);
    }

private:
    int solve(vector<int>& w, vector<int>& v, int n, int cap) {
        if (n == 0 || cap == 0) return 0;
        if (w[n-1] > cap) return solve(w, v, n-1, cap);
        return max(v[n-1] + solve(w, v, n-1, cap - w[n-1]),
                   solve(w, v, n-1, cap));
    }
};
// Time: O(2^n) — exponential, needs DP
```

---

## Approach 2 — Memoization (Top-Down DP)

Cache `memo[n][W]` so each unique state is computed exactly once.

#### Python

```python
def knapsack_memo(weights, values, n, W, memo={}):
    if n == 0 or W == 0: return 0

    key = (n, W)
    if key in memo: return memo[key]            # cached result

    if weights[n-1] > W:
        result = knapsack_memo(weights, values, n-1, W, memo)
    else:
        include = values[n-1] + knapsack_memo(weights, values, n-1, W - weights[n-1], memo)
        exclude = knapsack_memo(weights, values, n-1, W, memo)
        result = max(include, exclude)

    memo[key] = result
    return result


weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
print(knapsack_memo(weights, values, 4, 7))     # 9
# O(n * W) — each state solved once
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

int knapsackMemo(vector<int>& weights, vector<int>& values,
                 int n, int W, vector<vector<int>>& memo) {
    if (n == 0 || W == 0) return 0;
    if (memo[n][W] != -1) return memo[n][W];    // cached

    if (weights[n-1] > W) {
        memo[n][W] = knapsackMemo(weights, values, n-1, W, memo);
    } else {
        int include = values[n-1] + knapsackMemo(weights, values, n-1, W - weights[n-1], memo);
        int exclude = knapsackMemo(weights, values, n-1, W, memo);
        memo[n][W] = max(include, exclude);
    }
    return memo[n][W];
}

int main() {
    vector<int> w = {1,3,4,5}, v = {1,4,5,7};
    int n = 4, W = 7;
    vector<vector<int>> memo(n+1, vector<int>(W+1, -1));
    cout << knapsackMemo(w, v, n, W, memo) << "\n";    // 9
    return 0;
}
// Time: O(n * W) | Space: O(n * W)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #416 — Partition Equal Subset Sum (same knapsack pattern)
    // Generic 0/1 knapsack with memoization
    int knapsack(int W, vector<int>& weights, vector<int>& values) {
        int n = weights.size();
        vector<vector<int>> memo(n + 1, vector<int>(W + 1, -1));
        return solve(weights, values, n, W, memo);
    }

private:
    int solve(vector<int>& w, vector<int>& v, int n, int cap,
              vector<vector<int>>& memo) {
        if (n == 0 || cap == 0) return 0;
        if (memo[n][cap] != -1) return memo[n][cap];

        if (w[n-1] > cap)
            return memo[n][cap] = solve(w, v, n-1, cap, memo);

        return memo[n][cap] = max(
            v[n-1] + solve(w, v, n-1, cap - w[n-1], memo),
            solve(w, v, n-1, cap, memo)
        );
    }
};
// Time: O(n * W) | Space: O(n * W)
```

---

## Approach 3 — Tabulation (Bottom-Up DP)

`dp[i][w]` = max value using first `i` items with capacity `w`.

### Dry Run (weights=[2,3], values=[3,4], W=5)

| i\w | 0 | 1 | 2 | 3 | 4 | 5 |
|-----|---|---|---|---|---|---|
| 0 (no items) | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 (item1 w=2,v=3) | 0 | 0 | 3 | 3 | 3 | 3 |
| 2 (item2 w=3,v=4) | 0 | 0 | 3 | 4 | 4 | 7 |

`dp[2][5] = 7` — take both items (2+3=5 kg, 3+4=7 value).

#### Python

```python
def knapsack_tab(weights, values, capacity):
    n = len(values)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i-1][w]                          # option 1: exclude item i

            if weights[i-1] <= w:                           # option 2: include item i
                include = values[i-1] + dp[i-1][w - weights[i-1]]
                dp[i][w] = max(dp[i][w], include)

    return dp[n][capacity]                                  # answer in bottom-right cell


weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
print(knapsack_tab(weights, values, 7))     # 9
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

int knapsackTab(vector<int>& weights, vector<int>& values, int capacity) {
    int n = values.size();
    vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= capacity; w++) {
            dp[i][w] = dp[i-1][w];                         // exclude item i

            if (weights[i-1] <= w) {
                int include = values[i-1] + dp[i-1][w - weights[i-1]];
                dp[i][w] = max(dp[i][w], include);          // include if better
            }
        }
    }
    return dp[n][capacity];
}

int main() {
    vector<int> w = {1,3,4,5}, v = {1,4,5,7};
    cout << knapsackTab(w, v, 7) << "\n";   // 9
    return 0;
}
// Time: O(n * W) | Space: O(n * W)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #474 — Ones and Zeroes (2D knapsack variant — same tabulation pattern)
    // Generic 0/1 knapsack tabulation
    int knapsack(int capacity, vector<int>& weights, vector<int>& values) {
        int n = values.size();
        vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));

        for (int i = 1; i <= n; i++)
            for (int w = 0; w <= capacity; w++) {
                dp[i][w] = dp[i-1][w];
                if (weights[i-1] <= w)
                    dp[i][w] = max(dp[i][w], values[i-1] + dp[i-1][w - weights[i-1]]);
            }

        return dp[n][capacity];
    }
};
// Time: O(n * W) | Space: O(n * W)
```

---

## Approach 4 — Space-Optimized 1D Array

We only ever look at the **previous row** — use a single 1D array updated **right to left**.

**Critical:** iterate `w` from `W` down to `weights[i]`. Left-to-right would let the same item be included multiple times (that's Unbounded Knapsack).

#### Python

```python
def knapsack_optimized(weights, values, capacity):
    dp = [0] * (capacity + 1)

    for i in range(len(values)):
        for w in range(capacity, weights[i] - 1, -1):  # RIGHT to LEFT
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]])

    return dp[capacity]


weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
print(knapsack_optimized(weights, values, 7))   # 9
# O(n * W) time, O(W) space
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Space-optimized 0/1 Knapsack — 1D array, right-to-left traversal
int knapsackOpt(vector<int>& weights, vector<int>& values, int capacity) {
    vector<int> dp(capacity + 1, 0);

    for (int i = 0; i < (int)values.size(); i++) {
        // RIGHT to LEFT — prevents reusing same item in same pass
        for (int w = capacity; w >= weights[i]; w--) {
            dp[w] = max(dp[w], values[i] + dp[w - weights[i]]);
        }
    }
    return dp[capacity];
}

int main() {
    vector<int> w = {1,3,4,5}, v = {1,4,5,7};
    cout << knapsackOpt(w, v, 7) << "\n";   // 9
    return 0;
}
// Time: O(n * W) | Space: O(W)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #416 — Partition Equal Subset Sum (1D knapsack)
    bool canPartition(vector<int>& nums) {
        int total = 0;
        for (int x : nums) total += x;
        if (total % 2 != 0) return false;

        int target = total / 2;
        vector<bool> dp(target + 1, false);
        dp[0] = true;

        for (int num : nums) {
            for (int w = target; w >= num; w--)     // right-to-left = 0/1 constraint
                dp[w] = dp[w] || dp[w - num];
        }
        return dp[target];
    }
};
// Time: O(n * W) | Space: O(W)
```

---

## Traceback — Which Items Were Selected?

Walk backwards through the full 2D table: if `dp[i][w] != dp[i-1][w]`, item `i` was included.

#### Python

```python
def trace_items(dp, weights, n, W):
    selected = []
    w = W
    for i in range(n, 0, -1):
        if dp[i][w] != dp[i-1][w]:         # value changed → item i was included
            selected.append(i)              # 1-indexed
            w -= weights[i-1]               # reduce remaining capacity
    return selected


weights = [1, 3, 4, 5]
values  = [1, 4, 5, 7]
n, W = 4, 7

dp = [[0]*(W+1) for _ in range(n+1)]
for i in range(1, n+1):
    for w in range(W+1):
        dp[i][w] = dp[i-1][w]
        if weights[i-1] <= w:
            dp[i][w] = max(dp[i][w], values[i-1] + dp[i-1][w - weights[i-1]])

print("Max value:", dp[n][W])                       # 9
print("Items (1-indexed):", trace_items(dp, weights, n, W))   # [3, 2]
# Item 3 (w=4,v=5) and item 2 (w=3,v=4) → total weight=7, value=9
```

---

## Complexity Summary

| Approach | Time | Space |
|----------|------|-------|
| Naive recursion | O(2ⁿ) | O(n) call stack |
| Memoization | O(n × W) | O(n × W) |
| Tabulation | O(n × W) | O(n × W) |
| Space-optimized | O(n × W) | **O(W)** |

---

## Common Mistakes to Avoid

- **Left-to-right in 1D DP** — always iterate right-to-left to prevent reusing the same item.
- **Wrong base case** — first row and column must be initialized to 0.
- **Off-by-one** — use `weights[i-1]` and `values[i-1]` inside a 1-indexed loop.
- **Confusing 0/1 with Unbounded Knapsack** — Unbounded allows reuse; iterate left-to-right for that.

---

## Key Takeaways

- For every item: **include or exclude, take the max** — that one rule drives the whole solution.
- Memoization: recursion + 2D cache, top-down.
- Tabulation: iterative 2D table, bottom-up, no stack risk.
- Space-optimized: 1D array, **right-to-left traversal** is critical.
- Traceback through full 2D table to recover which items were selected.

---

## FAQ

**Difference between 0/1 and Fractional Knapsack?**

In 0/1, you take an entire item or nothing. In Fractional, you can take a fraction. Fractional is solved greedily; 0/1 requires DP.

**Why right-to-left in the 1D version?**

When `dp[w]` is updated using `dp[w - weight[i]]`, iterating right-to-left ensures `dp[w - weight[i]]` still reflects the **previous item's result** — not the current item's updated value. This enforces "use each item at most once."

**Real-life uses?**

Resource allocation, investment selection within a budget, task scheduling within a time limit, cargo packing — any problem where you maximize value under a capacity constraint without splitting items.
