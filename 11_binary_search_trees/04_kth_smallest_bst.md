# Kth Smallest Element in BST: In-Order Traversal Guide

## What is the Kth Smallest Element in a BST?

Imagine you have a sorted list of numbers and someone asks you: "Give me the 3rd smallest number." Easy, right? You just pick the third item from the left. Now, what if those numbers are stored in a Binary Search Tree (BST) instead of a list? That is exactly the problem we are solving today.

The Kth smallest element problem asks you to find the element that would appear at **position K** if all BST values were listed in sorted order. This is a classic BST problem that shows up in interviews and placement tests regularly.

---

## Quick BST Recap

A Binary Search Tree has one golden rule: for any node, all values in its **left subtree are smaller**, and all values in its **right subtree are larger**.

Because of this rule, if you traverse a BST in **in-order fashion** (Left → Root → Right), you always visit nodes in **ascending sorted order**. That is the insight we will use.

---

## In-Order Traversal Recap

In-order traversal visits nodes in this order: left child first, then the current node, then the right child. For a BST, this produces values from smallest to largest.

#### Python

```python
# In-order traversal of a BST
# Left → Root → Right produces ascending sorted order

def in_order(root, result):
    if root is None:        # base case: empty node
        return
    in_order(root.left, result)     # visit left subtree first
    result.append(root.val)         # visit current node
    in_order(root.right, result)    # visit right subtree last
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// In-order traversal — collects values in sorted order
void inOrder(TreeNode* root, vector<int>& result) {
    if (root == nullptr) return;        // base case: empty node
    inOrder(root->left, result);        // visit left subtree first
    result.push_back(root->val);        // store current node value
    inOrder(root->right, result);       // visit right subtree last
}
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Entry point — runs in-order and returns all values sorted
    vector<int> inOrderTraversal(TreeNode* root) {
        vector<int> result;
        inOrder(root, result);
        return result;
    }

private:
    void inOrder(TreeNode* root, vector<int>& result) {
        if (root == nullptr) return;    // base case
        inOrder(root->left, result);    // left subtree first
        result.push_back(root->val);    // record current value
        inOrder(root->right, result);   // right subtree last
    }
};
```

---

## Example Tree

```
        5
       / \
      3   6
     / \
    2   4
   /
  1
```

In-order traversal of this tree gives: **1, 2, 3, 4, 5, 6**.

- If K = 3 → answer is **3**
- If K = 5 → answer is **5**

---

## Approach 1: Collect All and Return Kth

The simplest approach is to run in-order traversal, store all values in an array, and return the element at index **K - 1**.

### Complexity

|       | Value                             |
| ----- | --------------------------------- |
| Time  | O(N) — visits every node once     |
| Space | O(N) — stores all values in array |

#### Python

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def kth_smallest_collect(root, k):
    result = []

    def in_order(node):
        if node is None:            # base case
            return
        in_order(node.left)         # go left first (smaller values)
        result.append(node.val)     # record this value
        in_order(node.right)        # go right last (larger values)

    in_order(root)
    return result[k - 1]            # k-1 because list is 0-indexed


# Build the BST:    5
#                  / \
#                 3   6
#                / \
#               2   4
#              /
#             1
root = TreeNode(5)
root.left = TreeNode(3)
root.right = TreeNode(6)
root.left.left = TreeNode(2)
root.left.right = TreeNode(4)
root.left.left.left = TreeNode(1)

print(kth_smallest_collect(root, 3))  # Output: 3
print(kth_smallest_collect(root, 1))  # Output: 1
print(kth_smallest_collect(root, 6))  # Output: 6
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// Step 1: collect all values in sorted order via in-order traversal
void inOrder(TreeNode* root, vector<int>& result) {
    if (root == nullptr) return;        // base case: nothing to visit
    inOrder(root->left, result);        // smaller values come first
    result.push_back(root->val);        // record current node
    inOrder(root->right, result);       // larger values come last
}

// Step 2: return value at index k-1 (0-indexed array)
int kthSmallest(TreeNode* root, int k) {
    vector<int> result;
    inOrder(root, result);              // fills result in sorted order
    return result[k - 1];              // k-1 because array is 0-indexed
}

int main() {
    TreeNode* root = new TreeNode(5);
    root->left = new TreeNode(3);
    root->right = new TreeNode(6);
    root->left->left = new TreeNode(2);
    root->left->right = new TreeNode(4);
    root->left->left->left = new TreeNode(1);

    cout << kthSmallest(root, 3) << endl;  // Output: 3
    cout << kthSmallest(root, 1) << endl;  // Output: 1
    cout << kthSmallest(root, 6) << endl;  // Output: 6
    return 0;
}
// Time: O(N) | Space: O(N)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        vector<int> result;
        inOrder(root, result);      // collect all values sorted
        return result[k - 1];      // return kth (0-indexed)
    }

private:
    void inOrder(TreeNode* root, vector<int>& result) {
        if (root == nullptr) return;
        inOrder(root->left, result);        // left subtree (smaller)
        result.push_back(root->val);        // current node
        inOrder(root->right, result);       // right subtree (larger)
    }
};
// Time: O(N) | Space: O(N)
```

---

## Approach 2: Stop Early Using a Counter

Instead of collecting all values, we use a **counter** and stop as soon as we reach the Kth node during in-order traversal. This saves space because we never store all values.

Think of it like counting people entering a room — you stop and note who walked in as soon as the count reaches K. No need to wait for everyone to come in.

### Complexity

|       | Value                                         |
| ----- | --------------------------------------------- |
| Time  | O(H + K) — H is tree height, stop at Kth node |
| Space | O(H) — only the recursion call stack          |

#### Python

```python
def kth_smallest_early_stop(root, k):
    # Use a list to simulate pass-by-reference for count and result
    count = [0]     # number of nodes visited so far
    result = [-1]   # will hold the answer

    def in_order(node):
        if node is None or count[0] == k:   # stop if found already
            return

        in_order(node.left)         # go left first (smaller values)

        count[0] += 1               # visiting current node, increment counter
        if count[0] == k:           # is this exactly the Kth node?
            result[0] = node.val    # store answer
            return

        in_order(node.right)        # go right (larger values)

    in_order(root)
    return result[0]


root = TreeNode(5)
root.left = TreeNode(3)
root.right = TreeNode(6)
root.left.left = TreeNode(2)
root.left.right = TreeNode(4)
root.left.left.left = TreeNode(1)

print(kth_smallest_early_stop(root, 2))  # Output: 2
print(kth_smallest_early_stop(root, 4))  # Output: 4
```

#### C++ (simple)

```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// count tracks how many nodes visited; result stores answer when count == k
void inOrderHelper(TreeNode* root, int k, int& count, int& result) {
    if (root == nullptr) return;    // base case: nothing to visit

    inOrderHelper(root->left, k, count, result);    // go left first

    count++;                        // we visited one more node
    if (count == k) {               // is this exactly the Kth node?
        result = root->val;         // store the answer
        return;                     // stop — no need to go further
    }

    inOrderHelper(root->right, k, count, result);   // go right
}

int kthSmallest(TreeNode* root, int k) {
    int count = 0;      // nodes visited so far
    int result = -1;    // answer (-1 means not found yet)
    inOrderHelper(root, k, count, result);
    return result;
}

int main() {
    TreeNode* root = new TreeNode(5);
    root->left = new TreeNode(3);
    root->right = new TreeNode(6);
    root->left->left = new TreeNode(2);
    root->left->right = new TreeNode(4);
    root->left->left->left = new TreeNode(1);

    cout << kthSmallest(root, 2) << endl;  // Output: 2
    cout << kthSmallest(root, 4) << endl;  // Output: 4
    return 0;
}
// Time: O(H + K) | Space: O(H)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        count = 0;      // reset counter
        result = -1;    // reset result
        inOrder(root, k);
        return result;
    }

private:
    int count;          // shared across recursive calls
    int result;         // stores the answer

    void inOrder(TreeNode* root, int k) {
        if (root == nullptr) return;    // base case

        inOrder(root->left, k);         // visit left subtree first

        count++;                        // increment visited count
        if (count == k) {               // found the Kth node
            result = root->val;
            return;                     // early exit
        }

        inOrder(root->right, k);        // visit right subtree
    }
};
// Time: O(H + K) | Space: O(H)
```

---

## Dry Run Walkthrough — Approach 2 with K = 3

```
        5
       / \
      3   6
     / \
    2   4
   /
  1
```

| Step | Node visited | count after visit | count == K?          |
| ---- | ------------ | ----------------- | -------------------- |
| 1    | 1            | 1                 | No                   |
| 2    | 2            | 2                 | No                   |
| 3    | 3            | 3                 | **Yes → result = 3** |

We stopped after visiting only **3 nodes** instead of all 6. For large trees where K is small, this is a major saving.

---

## Comparison: Approach 1 vs Approach 2

| Feature           | Approach 1 (Collect All) | Approach 2 (Early Stop)    |
| ----------------- | ------------------------ | -------------------------- |
| Extra Space       | O(N) for the array       | O(H) for call stack only   |
| Time (worst case) | O(N)                     | O(H + K)                   |
| Simplicity        | Very easy to implement   | Slightly more logic        |
| Best for          | Small trees or learning  | Large trees, optimized use |
| Stops early       | No                       | Yes                        |

For interviews, **Approach 2** is preferred — it is more efficient and shows better problem-solving thinking.

---

## Edge Cases to Handle

| Edge Case        | Behaviour                                            |
| ---------------- | ---------------------------------------------------- |
| K = 1            | Return the leftmost node (minimum value)             |
| K = N            | Return the rightmost node (maximum value)            |
| Single node tree | Valid only if K = 1                                  |
| K > N            | Invalid input — return -1 or handle with guard check |

---

## Key Takeaways

- **In-order traversal of a BST always gives sorted output** — this is the foundation of both approaches.
- **Approach 1** (collect all) is simpler and good for beginners.
- **Approach 2** (early stop with counter) is optimized and preferred in interviews.
- Both run in-order traversal; Approach 2 just stops sooner.

---

## Frequently Asked Questions

**Why does in-order traversal give sorted output in a BST?**

Because of the BST property: the left subtree always has smaller values and the right subtree always has larger values. In-order traversal visits left subtree first, then current node, then right subtree. This naturally produces values in ascending order.

**What is the time complexity of finding the Kth smallest element?**

With the early stop approach, time complexity is **O(H + K)** where H is the height of the tree. In a balanced BST, H is O(log N). In the worst case (skewed tree), H is O(N), making it O(N) overall.

**Can this problem be solved iteratively without recursion?**

Yes. You can use an explicit stack to simulate in-order traversal iteratively. Push all left children onto the stack first, then pop and count, and push right children. The logic is the same, just without recursive calls.
