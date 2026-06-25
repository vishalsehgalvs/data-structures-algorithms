# Fractional Knapsack Problem: Greedy Approach Guide

## What is the Fractional Knapsack Problem?

You have a knapsack with maximum weight capacity **W** and **n items**, each with a weight and value. Maximize total value — but unlike the 0/1 version, **you can take any fraction of an item**.

If an item weighs 5 kg but you only have 3 kg of space left, you can take 3/5 of it and get 3/5 of its value.

---

## Why Use a Greedy Approach?

**Greedy rule:** pick the item with the highest **value-per-unit-weight ratio** first.

Think of it like choosing the most expensive ingredient per kilogram at a market. Grab as much of the most valuable one first, then move to the next best. Because we can take fractions, we never skip a high-value item — we just take a piece of it.

**Value per weight ratio = `value / weight`**

---

## Step-by-Step Algorithm

1. Compute `value / weight` ratio for each item.
2. Sort items by ratio in **descending order**.
3. Initialize `total_value = 0`, `current_weight = 0`.
4. For each item:
   - If it fits entirely → take all of it.
   - If not → take the fraction that fills remaining capacity, then stop.

---

## Dry Run Example

**Knapsack capacity = 50 kg, 3 items:**

| Item | Weight (kg) | Value (Rs) | Ratio |
| ---- | ----------- | ---------- | ----- |
| A    | 10          | 60         | 6.0   |
| B    | 20          | 100        | 5.0   |
| C    | 30          | 120        | 4.0   |

```
Sorted by ratio: A(6.0) → B(5.0) → C(4.0)

Take all of A:   weight=10, value=60,  remaining capacity=40
Take all of B:   weight=30, value=160, remaining capacity=20
Item C weighs 30, only 20 left → take 20/30 = 2/3 of C:
  value += 120 * (2/3) = 80 → total value = 240

Maximum value = 240.0
```

---

## Code Implementation

#### Python

```python
def fractional_knapsack(capacity, items):
    # items = list of (weight, value) tuples
    # Step 1: Sort by value/weight ratio descending
    items = sorted(items, key=lambda x: x[1] / x[0], reverse=True)

    total_value = 0.0
    current_weight = 0

    for weight, value in items:
        if current_weight + weight <= capacity:
            # Take the whole item
            current_weight += weight
            total_value += value
        else:
            # Take the fraction that fits
            remaining = capacity - current_weight
            fraction = remaining / weight
            total_value += value * fraction
            break       # knapsack is now full

    return total_value


capacity = 50
items = [(10, 60), (20, 100), (30, 120)]   # (weight, value)

result = fractional_knapsack(capacity, items)
print(f"Maximum value in knapsack: {result}")   # 240.0
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Item {
    int weight, value;
};

// Fractional Knapsack — greedy by value/weight ratio
double fractionalKnapsack(int capacity, vector<Item>& items) {
    // Sort by value/weight ratio descending
    sort(items.begin(), items.end(), [](const Item& a, const Item& b) {
        return (double)a.value / a.weight > (double)b.value / b.weight;
    });

    double totalValue = 0.0;
    int currentWeight = 0;

    for (auto& item : items) {
        if (currentWeight + item.weight <= capacity) {
            // Take the whole item
            currentWeight += item.weight;
            totalValue += item.value;
        } else {
            // Take the fraction that fits in remaining capacity
            int remaining = capacity - currentWeight;
            totalValue += item.value * ((double)remaining / item.weight);
            break;      // knapsack is full
        }
    }
    return totalValue;
}

int main() {
    vector<Item> items = {{10, 60}, {20, 100}, {30, 120}};
    int capacity = 50;

    cout << "Maximum value: " << fractionalKnapsack(capacity, items) << "\n"; // 240
    return 0;
}
// Time: O(n log n) | Space: O(1) extra
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Fractional Knapsack — maximize value for given weight capacity
    // Input: capacity W, items as {weight, value} pairs
    double fractionalKnapsack(int capacity, vector<pair<int,int>>& items) {
        // Sort by value/weight ratio descending (greedy key)
        sort(items.begin(), items.end(), [](const pair<int,int>& a, const pair<int,int>& b) {
            return (double)a.second / a.first > (double)b.second / b.first;
        });

        double totalValue = 0.0;
        int weightUsed = 0;

        for (auto& [weight, value] : items) {
            if (weightUsed + weight <= capacity) {
                weightUsed += weight;           // take whole item
                totalValue += value;
            } else {
                int remaining = capacity - weightUsed;
                totalValue += value * ((double)remaining / weight);  // take fraction
                break;                          // bag is full
            }
        }
        return totalValue;
    }
};
// Time: O(n log n) | Space: O(1) extra
```

---

## Fractional vs 0/1 Knapsack

| Feature             | Fractional Knapsack | 0/1 Knapsack        |
| ------------------- | ------------------- | ------------------- |
| Can split items?    | Yes                 | No                  |
| Approach            | Greedy              | Dynamic Programming |
| Optimal guaranteed? | Yes with greedy     | Needs DP            |
| Time complexity     | O(n log n)          | O(n × W)            |
| Difficulty          | Easier              | Harder              |

**Key insight:** greedy works for fractional because the best-ratio item always leads to the best result when you can split freely. In 0/1 Knapsack, you cannot split, so a greedy pick can miss better combinations.

---

## Time and Space Complexity

| Step            | Operation                  | Complexity     |
| --------------- | -------------------------- | -------------- |
| Sort by ratio   | Sorting n items            | O(n log n)     |
| Fill knapsack   | Single pass                | O(n)           |
| **Total time**  |                            | **O(n log n)** |
| **Extra space** | Sort in place + a few vars | **O(1)**       |

---

## Real-World Applications

- **Budget allocation** — divide a fixed budget among projects by best return per dollar.
- **Cargo loading** — fill a truck with goods that maximize profit per unit weight.
- **Investment portfolios** — allocate capital to assets by best expected return per risk unit.
- **Network bandwidth** — assign limited bandwidth to streams by highest priority per unit.

Anywhere you can **partially select items** and want to maximize value under a capacity constraint.

---

## Common Mistakes to Avoid

- **Sorting by value or weight alone** instead of by the ratio.
- **Forgetting the fractional case** when the last item doesn't fit entirely.
- **Integer division** for the ratio — always use float/double division.
- **Confusing with 0/1 Knapsack** and trying to use DP (not needed here).

---

## Key Takeaways

- Compute `value / weight` ratios → sort descending → greedily fill the bag.
- Take whole items when they fit; take the exact fraction when the bag is nearly full.
- O(n log n) time, O(1) extra space.
- Greedy is **provably optimal** here because fractions are allowed.

---

## FAQ

**Is greedy always optimal for knapsack problems?**

No — only for the **fractional** version. For 0/1 Knapsack (no splitting), greedy can miss better combinations. Use Dynamic Programming for that case.

**What if all items fit within the capacity?**

Take all items. Maximum value = sum of all values. No fractions needed.

**What if two items have the same value-per-weight ratio?**

Order between them doesn't affect the final answer. Either can be taken first — total value remains the same.
