# Activity Selection Problem: Greedy Algorithm Guide

## What is the Activity Selection Problem?

Given a list of activities each with a **start time** and **end time**, select the **maximum number of non-overlapping activities** that can be performed by a single person.

Two activities are compatible if one finishes before or exactly when the other starts: `f[i] <= s[j]`.

**Real-life analogy:** Booking gym classes — you want to attend as many as possible in a day. Always pick the class that ends earliest so you have maximum free time left for the next one.

---

## The Greedy Strategy

**Rule: always pick the activity that finishes earliest among remaining compatible activities.**

Why? Finishing early leaves the most room for future activities. A long activity might block two or three shorter ones.

---

## Step-by-Step Logic

1. Sort all activities by **finish time** (ascending).
2. Select the first activity (always safe).
3. For each remaining activity, if `start >= last_finish` → select it and update `last_finish`.
4. Repeat until all activities are checked.

---

## Dry Run Example

| Activity | Start | Finish |
| -------- | ----- | ------ |
| A1       | 1     | 3      |
| A2       | 2     | 5      |
| A3       | 4     | 6      |
| A4       | 6     | 8      |
| A5       | 5     | 7      |
| A6       | 8     | 9      |

```
Step 1: Sort by finish time → A1(3), A2(5), A3(6), A5(7), A4(8), A6(9)

Step 2: Select A1 (finish=3).  last_end = 3
Step 3: A2 start=2 < 3 → skip
        A3 start=4 >= 3 → SELECT A3. last_end = 6
Step 4: A5 start=5 < 6 → skip
        A4 start=6 >= 6 → SELECT A4. last_end = 8
Step 5: A6 start=8 >= 8 → SELECT A6. last_end = 9

Result: A1, A3, A4, A6 → 4 activities (maximum possible)
```

---

## Code Implementation

### Basic Version (by index)

#### Python

```python
def activity_selection(start, finish):
    # Pair (finish, start) and sort by finish time
    activities = sorted(zip(finish, start))

    selected = []
    last_finish = 0             # when the last selected activity ended

    for i, (f, s) in enumerate(activities):
        if s >= last_finish:    # compatible: starts after last one ends
            selected.append(i + 1)  # 1-indexed activity number
            last_finish = f

    return selected


start  = [1, 2, 4, 6, 5, 8]
finish = [3, 5, 6, 8, 7, 9]

result = activity_selection(start, finish)
print("Selected activities:", result)           # [1, 3, 4, 6]
print("Total activities selected:", len(result))  # 4
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// Activity Selection — returns count of max non-overlapping activities
int activitySelection(vector<int>& start, vector<int>& finish) {
    int n = start.size();

    // Pair (finish, start) and sort by finish time
    vector<pair<int,int>> activities(n);
    for (int i = 0; i < n; i++)
        activities[i] = {finish[i], start[i]};

    sort(activities.begin(), activities.end());     // sort by finish time

    int count = 1;                      // always select the first activity
    int lastFinish = activities[0].first;

    for (int i = 1; i < n; i++) {
        int s = activities[i].second;
        int f = activities[i].first;

        if (s >= lastFinish) {          // compatible activity
            count++;
            lastFinish = f;             // update last finish time
        }
    }
    return count;
}

int main() {
    vector<int> start  = {1, 2, 4, 6, 5, 8};
    vector<int> finish = {3, 5, 6, 8, 7, 9};

    cout << "Max activities: " << activitySelection(start, finish) << "\n"; // 4
    return 0;
}
// Time: O(n log n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #435 — Non-overlapping Intervals
    // (remove minimum intervals to make rest non-overlapping = same greedy)
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        // Sort by end time — greedy key
        sort(intervals.begin(), intervals.end(),
             [](auto& a, auto& b) { return a[1] < b[1]; });

        int removed = 0;
        int lastEnd = INT_MIN;

        for (auto& interval : intervals) {
            if (interval[0] >= lastEnd) {   // compatible — keep it
                lastEnd = interval[1];
            } else {
                removed++;                  // overlap — remove it
            }
        }
        return removed;
    }
};
// Time: O(n log n) | Space: O(1)
```

---

### Named Activities Version

When activities have labels instead of just indices.

#### Python

```python
def activity_selection_named(activities):
    # activities = list of (name, start, finish)
    activities.sort(key=lambda x: x[2])    # sort by finish time

    selected = []
    last_finish = 0

    for name, start, finish in activities:
        if start >= last_finish:            # no overlap
            selected.append(name)
            last_finish = finish

    return selected


activities = [
    ("Meeting",  1, 3),
    ("Workshop", 2, 5),
    ("Seminar",  4, 6),
    ("Lunch",    6, 8),
    ("Training", 5, 7),
    ("Review",   8, 9),
]

result = activity_selection_named(activities)
print("Selected:", result)  # ['Meeting', 'Seminar', 'Lunch', 'Review']
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

struct Activity {
    string name;
    int start, finish;
};

// Activity selection with named activities
vector<string> activitySelectionNamed(vector<Activity>& activities) {
    // Sort by finish time
    sort(activities.begin(), activities.end(),
         [](auto& a, auto& b) { return a.finish < b.finish; });

    vector<string> selected;
    int lastFinish = 0;

    for (auto& act : activities) {
        if (act.start >= lastFinish) {      // compatible
            selected.push_back(act.name);
            lastFinish = act.finish;
        }
    }
    return selected;
}

int main() {
    vector<Activity> activities = {
        {"Meeting",  1, 3}, {"Workshop", 2, 5}, {"Seminar",  4, 6},
        {"Lunch",    6, 8}, {"Training", 5, 7}, {"Review",   8, 9}
    };

    auto result = activitySelectionNamed(activities);
    cout << "Selected: ";
    for (auto& name : result) cout << name << " ";
    cout << "\n";
    // Output: Meeting Seminar Lunch Review
    return 0;
}
// Time: O(n log n) | Space: O(n)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #452 — Minimum Number of Arrows to Burst Balloons
    // (same greedy: sort by end, greedily select non-overlapping groups)
    int findMinArrowShots(vector<vector<int>>& points) {
        sort(points.begin(), points.end(),
             [](auto& a, auto& b) { return a[1] < b[1]; });   // sort by end

        int arrows = 1;
        int lastEnd = points[0][1];

        for (int i = 1; i < (int)points.size(); i++) {
            if (points[i][0] > lastEnd) {   // balloon starts after current arrow
                arrows++;
                lastEnd = points[i][1];     // new arrow at this balloon's end
            }
        }
        return arrows;
    }
};
// Time: O(n log n) | Space: O(1)
```

---

## Why Sorting by Finish Time Works

| Strategy                      | Always optimal? | Reason                                                         |
| ----------------------------- | --------------- | -------------------------------------------------------------- |
| Sort by **finish time** ✓     | Yes             | Finishing early leaves the most room for future activities     |
| Sort by start time            | No              | An activity may start early but run very long, blocking others |
| Sort by duration              | No              | A short activity may still conflict with two others            |
| Sort by start time descending | No              | Late starters may block earlier slots                          |

Sorting by finish time is the **only provably optimal** strategy for this problem.

---

## Time and Space Complexity

| Step           | Complexity     |
| -------------- | -------------- |
| Sorting        | O(n log n)     |
| Selection loop | O(n)           |
| **Total time** | **O(n log n)** |
| **Space**      | **O(n)**       |

---

## Common Mistakes to Avoid

- **Forgetting to sort** — the algorithm is incorrect without sorting by finish time first.
- **Using `>` instead of `>=`** — use `start >= last_finish`; an activity can start exactly when the previous one ends.
- **Sorting by start time** — the most common beginner mistake. Always sort by **finish time**.
- **Sorting by duration** — shorter duration does not mean earlier finish.

---

## Key Takeaways

- Greedy rule: **always pick the activity that ends earliest and is compatible** with the last selected.
- Sort by finish time → single linear scan → O(n log n) total.
- This is a perfect example of greedy being provably optimal due to the exchange argument.

---

## FAQ

**Is the Greedy approach always optimal for scheduling problems?**

Not always. For Activity Selection it is optimal. For 0/1 Knapsack (where you must take whole items), greedy fails — use Dynamic Programming instead.

**What if two activities have the same finish time?**

Either can be picked — the result remains optimal. Ties in sorting don't affect correctness.

**How is this different from the Fractional Knapsack Problem?**

Activity Selection maximizes the **number** of non-overlapping activities. Fractional Knapsack maximizes **total value** within a weight limit. Both use greedy, but the criteria and structure differ.
