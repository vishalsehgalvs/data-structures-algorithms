# Dynamic Programming for Beginners: Learn DP Fast

## What is Dynamic Programming?

**Dynamic Programming (DP)** is a problem-solving strategy that:

1. Breaks a big problem into smaller **overlapping subproblems**.
2. Solves each subproblem **exactly once**.
3. **Stores the result** so it is never recalculated.

DP is not a data structure — it is a technique. Without it, naive recursion recalculates the same subproblems hundreds of times, causing exponential time. DP brings that down to linear or polynomial time.

---

## Two Key Properties of DP Problems

### 1. Overlapping Subproblems

The same smaller problems appear **repeatedly** during recursion. Example: computing `fib(5)` requires `fib(3)` twice, `fib(2)` three times, etc. DP eliminates this repeated work.

### 2. Optimal Substructure

The best solution to the full problem can be **built from best solutions to its subproblems**. Example: shortest path from A→C through B = shortest(A→B) + shortest(B→C).

If **both properties exist**, DP is likely the right tool.

---

## Understanding DP with Fibonacci Numbers

Fibonacci: each number = sum of the two before it. `0, 1, 1, 2, 3, 5, 8, 13, 21 ...`

### Approach 1 — Naive Recursion (Slow)

For `fib(40)`, this makes over 300 million recursive calls. Time: **O(2ⁿ)** — exponential.

#### Python

```python
def fib(n):
    if n == 0: return 0         # base case
    if n == 1: return 1         # base case
    return fib(n - 1) + fib(n - 2)  # recalculates same values many times

print(fib(10))  # 55
# fib(40) is extremely slow — same subproblems solved hundreds of times
```

#### C++ (simple)

```cpp
#include <iostream>
using namespace std;

// Naive recursive Fibonacci — O(2^n) time
int fib(int n) {
    if (n == 0) return 0;   // base case
    if (n == 1) return 1;   // base case
    return fib(n - 1) + fib(n - 2);    // repeated subproblems!
}

int main() {
    cout << fib(10) << "\n";    // 55
    // fib(40) will be very slow — exponential calls
    return 0;
}
// Time: O(2^n) | Space: O(n) call stack
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci Number (naive, to show the problem)
    int fib(int n) {
        if (n == 0) return 0;
        if (n == 1) return 1;
        return fib(n - 1) + fib(n - 2);    // overlapping subproblems!
    }
};
// Time: O(2^n) — fib(3) recalculated multiple times for large n
```

---

### Approach 2 — Memoization (Top-Down DP)

Store each result in a cache the first time it is computed. On repeat calls, return the stored answer immediately.

**Top-down:** start from the big problem, break it down recursively, cache on the way back up.

#### Python

```python
def fib(n, memo={}):
    if n in memo: return memo[n]    # already computed — return instantly
    if n == 0: return 0
    if n == 1: return 1

    memo[n] = fib(n - 1, memo) + fib(n - 2, memo)  # store before returning
    return memo[n]

print(fib(10))   # 55
print(fib(50))   # 12586269025  (fast!)
print(fib(100))  # 354224848179261915075  (still fast)
# Each value computed exactly ONCE → O(n) time
```

#### C++ (simple)

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

// Memoization — top-down DP with a cache
unordered_map<int, long long> memo;

long long fib(int n) {
    if (memo.count(n)) return memo[n];  // already computed
    if (n == 0) return 0;
    if (n == 1) return 1;

    memo[n] = fib(n - 1) + fib(n - 2); // store result
    return memo[n];
}

int main() {
    cout << fib(10)  << "\n";   // 55
    cout << fib(50)  << "\n";   // 12586269025
    return 0;
}
// Time: O(n) — each subproblem solved once | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci Number (memoization)
    int fib(int n) {
        vector<int> memo(n + 1, -1);    // -1 = not yet computed
        return helper(n, memo);
    }

private:
    int helper(int n, vector<int>& memo) {
        if (n == 0) return 0;
        if (n == 1) return 1;
        if (memo[n] != -1) return memo[n];  // cached result

        memo[n] = helper(n - 1, memo) + helper(n - 2, memo);
        return memo[n];
    }
};
// Time: O(n) | Space: O(n)
```

---

### Approach 3 — Tabulation (Bottom-Up DP)

Build the answer **iteratively from the smallest subproblems upward**. No recursion — fills a table row by row where each cell depends on already-filled cells.

**Bottom-up:** start from base cases, work up to the final answer.

#### Python

```python
def fib(n):
    if n == 0: return 0
    if n == 1: return 1

    dp = [0] * (n + 1)  # table to store all results
    dp[0] = 0           # base case
    dp[1] = 1           # base case

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]  # use already-computed values

    return dp[n]

print(fib(10))  # 55
print(fib(50))  # 12586269025
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Tabulation — bottom-up DP, no recursion
long long fib(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    vector<long long> dp(n + 1);
    dp[0] = 0;  dp[1] = 1;     // base cases

    for (int i = 2; i <= n; i++)
        dp[i] = dp[i - 1] + dp[i - 2]; // build up from small to large

    return dp[n];
}

int main() {
    cout << fib(10) << "\n";    // 55
    cout << fib(50) << "\n";    // 12586269025
    return 0;
}
// Time: O(n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci Number (tabulation)
    int fib(int n) {
        if (n == 0) return 0;
        if (n == 1) return 1;

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

### Approach 4 — Space-Optimized Tabulation

We only ever need the **last two values** — no need to keep the full array.

#### Python

```python
def fib(n):
    if n == 0: return 0
    if n == 1: return 1

    prev2, prev1 = 0, 1     # fib(0) and fib(1)

    for _ in range(2, n + 1):
        current = prev1 + prev2
        prev2 = prev1       # slide window forward
        prev1 = current

    return prev1

print(fib(10))  # 55
# O(n) time, O(1) space — most efficient version
```

#### C++ (simple)

```cpp
#include <iostream>
using namespace std;

// Space-optimized bottom-up DP — O(1) extra space
long long fib(int n) {
    if (n == 0) return 0;
    if (n == 1) return 1;

    long long prev2 = 0, prev1 = 1;

    for (int i = 2; i <= n; i++) {
        long long current = prev1 + prev2;
        prev2 = prev1;          // slide window
        prev1 = current;
    }
    return prev1;
}

int main() {
    cout << fib(10) << "\n";    // 55
    return 0;
}
// Time: O(n) | Space: O(1)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #509 — Fibonacci Number (space-optimized)
    int fib(int n) {
        if (n == 0) return 0;
        if (n == 1) return 1;

        int prev2 = 0, prev1 = 1;

        for (int i = 2; i <= n; i++) {
            int cur = prev1 + prev2;
            prev2 = prev1;
            prev1 = cur;
        }
        return prev1;
    }
};
// Time: O(n) | Space: O(1) — optimal
```

---

## Memoization vs Tabulation

| Feature                         | Memoization (Top-Down)                   | Tabulation (Bottom-Up)         |
| ------------------------------- | ---------------------------------------- | ------------------------------ |
| Approach                        | Recursive + cache                        | Iterative table                |
| Direction                       | Big problem → base case                  | Base case → big problem        |
| Ease of coding                  | Easier (start with recursion, add cache) | Requires clear iteration order |
| Stack overflow risk             | Yes, for very deep recursion             | No — uses a loop               |
| Space                           | O(n) memo + call stack                   | O(n), or O(1) if optimizable   |
| Solves only needed subproblems? | Yes                                      | No — solves all                |

For beginners: **start with memoization** (add cache to working recursive solution). Switch to tabulation once you see the pattern.

---

## Second DP Example — Climbing Stairs

**Problem:** A staircase with `n` steps. Each time you can climb 1 or 2 steps. How many distinct ways to reach the top?

**Recurrence:** `ways(n) = ways(n-1) + ways(n-2)` — identical to Fibonacci!

If you are at step `n`, you arrived from step `n-1` (took 1 step) or step `n-2` (took 2 steps).

#### Python

```python
def climb_stairs(n):
    if n == 1: return 1     # only one way: (1)
    if n == 2: return 2     # two ways: (1,1) or (2)

    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2

    for i in range(3, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]  # came from step i-1 or i-2

    return dp[n]

print(climb_stairs(3))   # 3  → (1,1,1), (1,2), (2,1)
print(climb_stairs(5))   # 8
print(climb_stairs(10))  # 89
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Climbing stairs — bottom-up DP (same recurrence as Fibonacci)
int climbStairs(int n) {
    if (n == 1) return 1;
    if (n == 2) return 2;

    vector<int> dp(n + 1);
    dp[1] = 1; dp[2] = 2;

    for (int i = 3; i <= n; i++)
        dp[i] = dp[i-1] + dp[i-2];     // from step i-1 or i-2

    return dp[n];
}

int main() {
    cout << climbStairs(3)  << "\n";    // 3
    cout << climbStairs(5)  << "\n";    // 8
    cout << climbStairs(10) << "\n";    // 89
    return 0;
}
// Time: O(n) | Space: O(n) — reducible to O(1) using two variables
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #70 — Climbing Stairs
    int climbStairs(int n) {
        if (n <= 2) return n;

        int prev2 = 1, prev1 = 2;   // dp[1]=1, dp[2]=2

        for (int i = 3; i <= n; i++) {
            int cur = prev1 + prev2;    // ways to reach step i
            prev2 = prev1;
            prev1 = cur;
        }
        return prev1;
    }
};
// Time: O(n) | Space: O(1)
```

---

## How to Identify a DP Problem

Ask these questions:

- Does the problem ask for **maximum, minimum, count, or true/false**?
- Does it involve **choices at each step** that affect future choices?
- Can you define the answer in terms of **smaller versions of the same problem**?
- Does a recursive solution have **repeated subproblems** in its recursion tree?

If yes to most → likely a DP candidate.

**Classic DP problems:** coin change, longest increasing subsequence, 0/1 knapsack, grid path counting, edit distance.

---

## General Steps to Solve Any DP Problem

1. **Define the state** — what does `dp[i]` represent? Write it in plain English first.
2. **Write the recurrence relation** — how does `dp[i]` depend on previous states?
3. **Identify base cases** — what are the smallest subproblems you can answer directly?
4. **Choose top-down or bottom-up** — memoization or tabulation.
5. **Check for space optimization** — can you reduce the table to a few variables?

The hardest step is always step 1. Defining the state clearly makes everything else follow naturally.

---

## Complexity Summary

| Approach                   | Time  | Space           |
| -------------------------- | ----- | --------------- |
| Naive recursion            | O(2ⁿ) | O(n) call stack |
| Memoization (top-down)     | O(n)  | O(n)            |
| Tabulation (bottom-up)     | O(n)  | O(n)            |
| Space-optimized tabulation | O(n)  | O(1)            |

---

## Key Takeaways

- DP = solve each subproblem once and store the result.
- Two properties needed: **overlapping subproblems** + **optimal substructure**.
- **Memoization** = recursion + cache (top-down). Easy to write, risk of stack overflow.
- **Tabulation** = iterative table (bottom-up). No stack risk, often space-optimizable.
- Fibonacci and Climbing Stairs are the same recurrence — practice both until the pattern is automatic.

---

## FAQ

**Is DP just recursion with caching?**

Memoization is recursion with caching. But tabulation is purely iterative. DP is the broader strategy; both memoization and tabulation are implementations of it.

**When should I NOT use DP?**

If the problem has no overlapping subproblems, DP adds unnecessary complexity. Greedy or divide-and-conquer are better for problems where subproblems don't repeat.

**Why does DP feel hard at first?**

Because defining the state and recurrence requires pattern recognition built through practice. Start with Fibonacci → climbing stairs → coin change → knapsack. The patterns become familiar quickly.
