# Greedy Approach in Algorithms: Beginner-Friendly Guide

## What is the Greedy Approach?

The **Greedy Approach** means making the **locally optimal choice at each step**, hoping it leads to the globally optimal solution. It never looks back or reconsiders past decisions.

Think of always moving to the checkout line that looks shortest — that's greedy thinking. Fast and simple, but only guaranteed correct for the right problems.

---

## How the Greedy Approach Works

1. Break the problem into a series of steps or choices.
2. At each step, pick the option that seems best **right now**.
3. Never undo or revisit a past decision.
4. Repeat until the problem is solved.

This makes greedy algorithms very fast — often **O(n log n)** or even **O(n)** — compared to Dynamic Programming or Backtracking.

---

## Greedy vs Other Approaches

| Approach | Strategy | Looks Back? | Speed |
|----------|----------|------------|-------|
| **Greedy** | Pick locally best at each step | No | Very fast |
| Backtracking | Try all options, undo bad choices | Yes | Slow |
| Dynamic Programming | Store and reuse subproblem results | Yes | Moderate |
| Brute Force | Try every possible combination | Yes | Slowest |

---

## When Does Greedy Work?

Greedy is only reliable when a problem has **both** of these properties:

### Greedy Choice Property
A globally optimal solution can always be built by making locally optimal choices. The best local pick never closes the door on the best global answer.

### Optimal Substructure
The optimal solution to the full problem contains optimal solutions to its subproblems. (Same idea as DP, but greedy solves each subproblem once without storing results.)

---

## A Simple Greedy Example — Coin Change

**Problem:** Give change of 41 using fewest coins. Coins available: 25, 10, 5, 2, 1.

**Greedy rule:** always pick the largest coin that fits the remaining amount.

```
Amount = 41, Coins = [25, 10, 5, 2, 1] (sorted descending)

Step 1: Pick 25 → Remaining = 41 - 25 = 16
Step 2: Pick 10 → Remaining = 16 - 10 = 6
Step 3: Pick  5 → Remaining =  6 -  5 = 1
Step 4: Pick  1 → Remaining =  1 -  1 = 0

Coins used: [25, 10, 5, 1] = 4 coins total
```

**Note:** Greedy works for standard currency systems (designed with the greedy choice property). For arbitrary coin sets it can fail — e.g. coins [1, 3, 4] and target 6: greedy picks 4+1+1 = 3 coins, but optimal is 3+3 = 2 coins. Use DP for the general case.

---

## Greedy Approach in Code — Maximum Non-Overlapping Activities

**Problem:** Given tasks with start and end times, select the **maximum number of non-overlapping tasks**.

**Greedy rule:** always pick the task that **ends earliest** — this leaves the most room for future tasks.

#### Python

```python
def max_activities(activities):
    # Sort by end time — the greedy key insight
    activities.sort(key=lambda x: x[1])

    selected = []
    last_end = -1               # when the last selected activity ended

    for start, end in activities:
        if start >= last_end:   # no overlap with last selected
            selected.append((start, end))
            last_end = end      # move the window forward

    return selected


activities = [(1, 3), (2, 5), (3, 9), (6, 8), (5, 7)]
result = max_activities(activities)
print("Selected:", result)          # Selected: [(1, 3), (5, 7)]
print("Total:", len(result))        # Total: 2

# Sorted by end: [(1,3), (2,5), (5,7), (6,8), (3,9)]
# Pick (1,3) → last_end=3
# Skip (2,5): start=2 < 3
# Pick (5,7) → last_end=7
# Skip (6,8): start=6 < 7
# Skip (3,9): start=3 < 7
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Greedy activity selection: maximize non-overlapping tasks
vector<pair<int,int>> maxActivities(vector<pair<int,int>>& activities) {
    // Sort by end time — greedy choice property
    sort(activities.begin(), activities.end(),
         [](auto& a, auto& b) { return a.second < b.second; });

    vector<pair<int,int>> selected;
    int lastEnd = -1;

    for (auto& [start, end] : activities) {
        if (start >= lastEnd) {         // no overlap
            selected.push_back({start, end});
            lastEnd = end;              // move window forward
        }
    }
    return selected;
}

int main() {
    vector<pair<int,int>> activities = {{1,3},{2,5},{3,9},{6,8},{5,7}};
    auto result = maxActivities(activities);

    cout << "Selected activities:\n";
    for (auto [s, e] : result)
        cout << "(" << s << ", " << e << ")\n";
    // Output: (1, 3) and (5, 7)
    return 0;
}
// Time: O(n log n) for sort | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #435 — Non-overlapping Intervals (greedy — sort by end time)
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        // Sort intervals by end time
        sort(intervals.begin(), intervals.end(),
             [](auto& a, auto& b) { return a[1] < b[1]; });

        int count = 0;      // intervals removed
        int lastEnd = INT_MIN;

        for (auto& interval : intervals) {
            if (interval[0] >= lastEnd) {   // no overlap — keep it
                lastEnd = interval[1];
            } else {
                count++;                    // overlap — remove this one
            }
        }
        return count;
    }
};
// Time: O(n log n) | Space: O(1)
```

---

## When Greedy Fails — Counter-Example

```
Tree structure:
        10
       /  \
      5    15
     / \
    1   100

Greedy path: picks 15 (larger child at root)
Greedy total: 10 + 15 = 25

Optimal path: picks 5, then 100
Optimal total: 10 + 5 + 100 = 115

Greedy FAILS here — it picked 15 over 5 locally,
but missed the hidden 100 deeper in the 5-branch.
```

Problems like this require **Dynamic Programming** or **recursion with memoization**.

---

## How to Identify a Greedy Problem

Look for these signals:
- Problem asks to **maximize or minimize** something (count, cost, distance).
- Making a local best choice does **not hurt** future choices.
- Involves **selecting items, intervals, or tasks** one at a time.
- **Sorting the input** gives a natural order for decision-making.
- No dependencies between subproblems (unlike DP).

If you find a counter-example where the greedy choice fails, switch to DP or Backtracking.

---

## Greedy vs Dynamic Programming

| Feature | Greedy | Dynamic Programming |
|---------|--------|-------------------|
| Decision style | Pick best now, no undo | Explore all, store results |
| Memory usage | Very low | Moderate to high |
| Speed | Very fast | Moderate |
| Correctness | Only for specific problems | General and reliable |
| Examples | Activity Selection, Fractional Knapsack | 0/1 Knapsack, LCS |

**Useful rule of thumb:** if splitting an item is allowed (fractional), greedy often works. If you must take whole items (0/1), you likely need DP.

---

## Key Takeaways

- Greedy makes the **locally optimal choice** at each step without looking back.
- Only correct when the problem has the **greedy choice property** and **optimal substructure**.
- Most greedy algorithms run in **O(n log n)** (sort first, then one linear pass).
- Always test with a small counter-example before committing to a greedy approach.

---

## FAQ

**Is the greedy approach always correct?**

No. It only gives the optimal answer when the problem has the greedy choice property and optimal substructure. Otherwise use DP or Backtracking.

**When should I use Greedy instead of DP?**

Use Greedy when local choices lead to a global optimum and you don't need to revisit decisions. Use DP when subproblems overlap and you need to compare multiple choices at each step. Fractional Knapsack → greedy. 0/1 Knapsack → DP.

**What is the typical time complexity of greedy algorithms?**

Usually O(n log n) because they sort the input first. The decision loop is often O(n). Much faster than DP or Backtracking in practice.
