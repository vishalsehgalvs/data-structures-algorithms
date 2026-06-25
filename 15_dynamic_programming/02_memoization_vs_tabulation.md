# Memoization vs Tabulation: Dynamic Programming Guide

## Quick Recap

DP solves problems by breaking them into overlapping subproblems and storing results to avoid recomputation. There are two ways to implement it:

- **Memoization** — top-down (recursive + cache)
- **Tabulation** — bottom-up (iterative + table)

Both produce the same answers. They differ in direction, style, and practical tradeoffs.

---

## Memoization (Top-Down)

Start from the big problem → recurse down → **cache** the result the first time each subproblem is solved → return cached result on future calls.

### Fibonacci — Memoization

#### Python

```python
def fib_memo(n, memo={}):
    if n == 0: return 0
    if n == 1: return 1
    if n in memo: return memo[n]        # already computed — return instantly

    memo[n] = fib_memo(n-1, memo) + fib_memo(n-2, memo)
    return memo[n]

print(fib_memo(6))   # 8
print(fib_memo(10))  # 55
print(fib_memo(50))  # 12586269025
```

#### C++ (simple)

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

unordered_map<int, long long> memo;

long long fib_memo(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;
    if (memo.count(n)) return memo[n];  // cached — return instantly

    memo[n] = fib_memo(n - 1) + fib_memo(n - 2);
    return memo[n];
}

int main() {
    cout << fib_memo(10) << "\n";   // 55
    cout << fib_memo(50) << "\n";   // 12586269025
    return 0;
}
// Time: O(n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci (top-down memoization)
    int fib(int n) {
        vector<int> memo(n + 1, -1);
        return solve(n, memo);
    }

private:
    int solve(int n, vector<int>& memo) {
        if (n == 0) return 0;
        if (n == 1) return 1;
        if (memo[n] != -1) return memo[n];  // hit cache

        memo[n] = solve(n-1, memo) + solve(n-2, memo);
        return memo[n];
    }
};
// Time: O(n) | Space: O(n)
```

---

## Tabulation (Bottom-Up)

Start from the base cases → build up iteratively → **fill a table** until the final answer is reached. No recursion at all.

### Fibonacci — Tabulation

#### Python

```python
def fib_tab(n):
    if n == 0: return 0
    if n == 1: return 1

    dp = [0] * (n + 1)
    dp[0] = 0;  dp[1] = 1           # base cases

    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]  # build each answer from the two before it

    return dp[n]

print(fib_tab(6))   # 8
print(fib_tab(10))  # 55
print(fib_tab(50))  # 12586269025
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

long long fib_tab(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    vector<long long> dp(n + 1);
    dp[0] = 0; dp[1] = 1;

    for (int i = 2; i <= n; i++)
        dp[i] = dp[i-1] + dp[i-2];     // fill table bottom-up

    return dp[n];
}

int main() {
    cout << fib_tab(10) << "\n";    // 55
    cout << fib_tab(50) << "\n";    // 12586269025
    return 0;
}
// Time: O(n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci (bottom-up tabulation)
    int fib(int n) {
        if (n <= 1) return n;

        vector<int> dp(n + 1);
        dp[0] = 0; dp[1] = 1;

        for (int i = 2; i <= n; i++)
            dp[i] = dp[i-1] + dp[i-2];

        return dp[n];
    }
};
// Time: O(n) | Space: O(n)
```

---

## Side-by-Side Comparison

| Feature             | Memoization (Top-Down)          | Tabulation (Bottom-Up)          |
| ------------------- | ------------------------------- | ------------------------------- |
| Direction           | Big problem → base case         | Base case → big problem         |
| Technique           | Recursion + cache               | Iteration + table               |
| Subproblems solved  | Only the ones needed            | All subproblems                 |
| Stack overflow risk | Yes (deep recursion)            | No (loop-based)                 |
| Ease of writing     | Easier — add cache to recursion | Requires clear iteration order  |
| Space               | Cache + call stack              | Array (often space-optimizable) |
| Performance         | Slight recursion overhead       | Slightly faster in practice     |

---

## Memoization Applied — 0/1 Knapsack

State key: `(n, capacity)` — each unique combination is computed once.

#### Python

```python
def knapsack_memo(weights, values, capacity, n, memo={}):
    if n == 0 or capacity == 0: return 0

    key = (n, capacity)
    if key in memo: return memo[key]        # return cached result

    if weights[n-1] > capacity:             # item too heavy — skip it
        result = knapsack_memo(weights, values, capacity, n-1, memo)
    else:
        include = values[n-1] + knapsack_memo(weights, values, capacity - weights[n-1], n-1, memo)
        exclude = knapsack_memo(weights, values, capacity, n-1, memo)
        result = max(include, exclude)

    memo[key] = result
    return result


values  = [60, 100, 120]
weights = [10, 20, 30]
capacity = 50
print(knapsack_memo(weights, values, capacity, len(values)))    # 220
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;

map<pair<int,int>, int> memo;

int knapsackMemo(vector<int>& weights, vector<int>& values,
                 int capacity, int n) {
    if (n == 0 || capacity == 0) return 0;

    auto key = make_pair(n, capacity);
    if (memo.count(key)) return memo[key];  // cached state

    int result;
    if (weights[n-1] > capacity) {
        result = knapsackMemo(weights, values, capacity, n-1);  // skip
    } else {
        int include = values[n-1] + knapsackMemo(weights, values, capacity - weights[n-1], n-1);
        int exclude = knapsackMemo(weights, values, capacity, n-1);
        result = max(include, exclude);
    }

    memo[key] = result;
    return result;
}

int main() {
    vector<int> values  = {60, 100, 120};
    vector<int> weights = {10, 20, 30};
    cout << knapsackMemo(weights, values, 50, 3) << "\n";  // 220
    return 0;
}
// Time: O(n * W) | Space: O(n * W)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // 0/1 Knapsack — top-down memoization
    int knapsack(int capacity, vector<int>& weights, vector<int>& values) {
        int n = values.size();
        vector<vector<int>> memo(n + 1, vector<int>(capacity + 1, -1));
        return solve(weights, values, capacity, n, memo);
    }

private:
    int solve(vector<int>& weights, vector<int>& values,
              int cap, int n, vector<vector<int>>& memo) {
        if (n == 0 || cap == 0) return 0;
        if (memo[n][cap] != -1) return memo[n][cap];    // cached

        int result;
        if (weights[n-1] > cap) {
            result = solve(weights, values, cap, n-1, memo);    // skip
        } else {
            int include = values[n-1] + solve(weights, values, cap - weights[n-1], n-1, memo);
            int exclude = solve(weights, values, cap, n-1, memo);
            result = max(include, exclude);
        }
        return memo[n][cap] = result;
    }
};
// Time: O(n * W) | Space: O(n * W)
```

---

## Tabulation Applied — 0/1 Knapsack

`dp[i][w]` = max value using the first `i` items with weight capacity `w`.

#### Python

```python
def knapsack_tab(weights, values, capacity):
    n = len(values)
    # dp[i][w] = max value with first i items and capacity w
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i-1][w]                  # option 1: exclude item i

            if weights[i-1] <= w:                   # option 2: include item i (if it fits)
                include = values[i-1] + dp[i-1][w - weights[i-1]]
                dp[i][w] = max(dp[i][w], include)

    return dp[n][capacity]                          # answer in bottom-right cell


values  = [60, 100, 120]
weights = [10, 20, 30]
print(knapsack_tab(weights, values, 50))    # 220
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 0/1 Knapsack — bottom-up tabulation
int knapsackTab(vector<int>& weights, vector<int>& values, int capacity) {
    int n = values.size();
    // dp[i][w] = max value using first i items with capacity w
    vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));

    for (int i = 1; i <= n; i++) {
        for (int w = 0; w <= capacity; w++) {
            dp[i][w] = dp[i-1][w];                     // exclude item i

            if (weights[i-1] <= w) {                    // include item i if it fits
                int include = values[i-1] + dp[i-1][w - weights[i-1]];
                dp[i][w] = max(dp[i][w], include);
            }
        }
    }
    return dp[n][capacity];                             // bottom-right = answer
}

int main() {
    vector<int> values  = {60, 100, 120};
    vector<int> weights = {10, 20, 30};
    cout << knapsackTab(weights, values, 50) << "\n";   // 220
    return 0;
}
// Time: O(n * W) | Space: O(n * W)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // 0/1 Knapsack — bottom-up tabulation
    // (LeetCode #416 — Partition Equal Subset Sum uses same pattern)
    int knapsack(int capacity, vector<int>& weights, vector<int>& values) {
        int n = values.size();
        vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));

        for (int i = 1; i <= n; i++) {
            for (int w = 0; w <= capacity; w++) {
                dp[i][w] = dp[i-1][w];                  // default: skip item i

                if (weights[i-1] <= w)
                    dp[i][w] = max(dp[i][w],
                                   values[i-1] + dp[i-1][w - weights[i-1]]);
            }
        }
        return dp[n][capacity];
    }
};
// Time: O(n * W) | Space: O(n * W)
```

---

## Space Optimization in Tabulation

For Fibonacci, only the last **two values** are ever needed — shrink the array to two variables.

#### Python

```python
def fib_optimized(n):
    if n == 0: return 0
    if n == 1: return 1

    prev2, prev1 = 0, 1         # fib(0), fib(1)

    for _ in range(2, n + 1):
        current = prev1 + prev2
        prev2 = prev1           # slide window forward
        prev1 = current

    return prev1

print(fib_optimized(10))  # 55
print(fib_optimized(50))  # 12586269025
# O(n) time, O(1) space — optimal
```

#### C++ (simple)

```cpp
// Space-optimized Fibonacci — O(1) extra space
long long fib_optimized(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    long long prev2 = 0, prev1 = 1;
    for (int i = 2; i <= n; i++) {
        long long cur = prev1 + prev2;
        prev2 = prev1;
        prev1 = cur;
    }
    return prev1;
}
// Time: O(n) | Space: O(1)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci (space-optimized)
    int fib(int n) {
        if (n <= 1) return n;
        int prev2 = 0, prev1 = 1;
        for (int i = 2; i <= n; i++) {
            int cur = prev1 + prev2;
            prev2 = prev1;
            prev1 = cur;
        }
        return prev1;
    }
};
// Time: O(n) | Space: O(1)
```

---

## Recursion Depth and Stack Overflow

Memoization uses the call stack. Python's default recursion limit is ~1000 calls. For large `n`, deep recursion causes `RecursionError`.

**Solutions:**

- Convert to tabulation (no call stack, always safe).
- Use `sys.setrecursionlimit(10000)` (temporary workaround, not recommended for production).

Tabulation is always safe because it uses a loop.

---

## When to Use Which

| Situation                                                         | Use               |
| ----------------------------------------------------------------- | ----------------- |
| You have a working recursive solution and want quick optimization | Memoization       |
| Large inputs where recursion depth could overflow                 | Tabulation        |
| You want better performance and cleaner iteration                 | Tabulation        |
| Only a subset of subproblems are ever needed                      | Memoization       |
| Interview — showing awareness of stack issues                     | Tabulation        |
| Contest — speed of writing matters                                | Memoization first |

---

## Key Takeaways

- Both approaches achieve the same time complexity — O(n) or O(n×W) — just via different paths.
- **Memoization:** recursion + cache, only computes needed states, risks stack overflow.
- **Tabulation:** iteration + table, computes all states, safe for large inputs, space-optimizable.
- Good habit: prototype with memoization → rewrite as tabulation for production/interviews.

---

## FAQ

**Is memoization always faster than tabulation?**

Not always. Memoization skips unneeded states (good when many are skipped). Tabulation avoids function call overhead (good when all states are needed). In most interview problems, both are effectively equal.

**Can I mix both in one solution?**

Yes — both produce correct answers. You can prototype with memoization, verify, then convert to tabulation.

**What should beginners start with?**

Start with memoization — it mirrors natural recursive thinking. Once you understand why caching works, tabulation becomes intuitive.
