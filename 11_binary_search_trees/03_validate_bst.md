# How to Validate a Binary Search Tree (BST)

## What is a Valid Binary Search Tree?

Imagine you are organizing a bookshelf. Books on the left side must have smaller numbers, and books on the right side must have bigger numbers. That is exactly how a Binary Search Tree (BST) works.

A BST is valid only when every node follows a strict rule: all values in the left subtree must be less than the node, and all values in the right subtree must be greater than the node.

This rule must hold for **every single node** in the tree, not just the root. That is where most beginners get confused.

---

## BST Rules Recap

A Binary Search Tree has three key properties:

- The left child is always **smaller** than its parent node.
- The right child is always **greater** than its parent node.
- Both the left and right subtrees must also be valid BSTs.

Validation means checking all three of these rules for every node in the entire tree.

---

## The Common Mistake Beginners Make

Look at this tree below. At first glance, it looks like a valid BST. But is it really?

```
        10
       /  \
      5    15
     / \
    3   12
```

The node `12` is in the left subtree of `10`. But `12 > 10`. That breaks the BST rule. This tree is **NOT a valid BST**.

Just checking that each node is greater than its left child and smaller than its right child is **not enough**. You must track the allowed range for every node as you go deeper.

---

## The Min-Max Range Approach

The best way to validate a BST is to pass a **minimum and maximum allowed value** to each node as you traverse the tree.

Think of it like setting boundaries:

- When you go **left** from a node, the current node value becomes the new **maximum**.
- When you go **right**, the current node value becomes the new **minimum**.

Every node's value must stay within its allowed range. If it goes out of range, the tree is invalid.

### How the Range Updates

| Direction | What changes                               |
| --------- | ------------------------------------------ |
| Go left   | Current node value becomes the new **max** |
| Go right  | Current node value becomes the new **min** |

---

## Step-by-Step Dry Run

Let us validate this BST using the range method:

```
        10
       /  \
      5    15
     / \
    3   7
```

| Node | Range        | Result       |
| ---- | ------------ | ------------ |
| 10   | (-inf, +inf) | Valid        |
| 5    | (-inf, 10)   | Valid        |
| 3    | (-inf, 5)    | Valid (leaf) |
| 7    | (5, 10)      | Valid (leaf) |
| 15   | (10, +inf)   | Valid (leaf) |

All nodes passed the range check. This tree is a **valid BST**.

---

## Code Implementation

### Min-Max Range Approach

```python
# Definition of a Binary Tree Node
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def isValidBST(self, root):
        # Helper function with min and max boundaries
        def validate(node, min_val, max_val):
            # An empty node is always valid
            if node is None:
                return True

            # Check if current node's value is within the allowed range
            if node.val <= min_val or node.val >= max_val:
                return False

            # Recurse left: update max to current node value
            # Recurse right: update min to current node value
            return (validate(node.left, min_val, node.val) and
                    validate(node.right, node.val, max_val))

        # Start with the widest possible range
        return validate(root, float('-inf'), float('inf'))
```

The function `validate` checks each node against its allowed range. If any node fails, the entire function returns `False`.

The base case handles empty nodes, which are always considered valid. This is how recursion bottoms out cleanly.

**C++ (simple):**

```cpp
#include <climits>

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
};

// Plain function — no class needed
bool validate(TreeNode* node, long long minVal, long long maxVal) {
    if (node == nullptr) return true;
    if (node->val <= minVal || node->val >= maxVal) return false;
    return validate(node->left, minVal, node->val) &&
           validate(node->right, node->val, maxVal);
}

bool isValidBST(TreeNode* root) {
    return validate(root, LLONG_MIN, LLONG_MAX);
}
// Time: O(n) | Space: O(h) for recursion stack
```

> **Note:** We use `long long` for boundaries (not `int`) to safely handle nodes with values equal to `INT_MIN` or `INT_MAX`.

**C++ (LeetCode class style):**

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        return validate(root, LLONG_MIN, LLONG_MAX);
    }

private:
    bool validate(TreeNode* node, long long minVal, long long maxVal) {
        if (node == nullptr) return true;
        if (node->val <= minVal || node->val >= maxVal) return false;
        return validate(node->left, minVal, node->val) &&
               validate(node->right, node->val, maxVal);
    }
};
```

---

## Example Walkthrough: Invalid BST

```python
# Building an invalid BST manually
#
#        5
#       / \
#      4   6
#         / \
#        3   7
#
# Node 3 is in the right subtree of 5, but 3 < 5. Invalid!

root = TreeNode(5)
root.left = TreeNode(4)
root.right = TreeNode(6)
root.right.left = TreeNode(3)  # This violates the BST property
root.right.right = TreeNode(7)

sol = Solution()
print(sol.isValidBST(root))  # Output: False
```

The output is `False` because node `3` falls in the right subtree of `5`, but its value is less than `5`. The range check at node `3` would be `(5, 6)`, and `3` is not within that range.

**C++:**

```cpp
// Building an invalid BST manually
//
//        5
//       / \
//      4   6
//         / \
//        3   7
//
// Node 3 is in the right subtree of 5, but 3 < 5. Invalid!

TreeNode* root = new TreeNode(5);
root->left = new TreeNode(4);
root->right = new TreeNode(6);
root->right->left = new TreeNode(3);  // This violates the BST property
root->right->right = new TreeNode(7);

Solution sol;
cout << sol.isValidBST(root);  // Output: 0 (false)
```

---

## Valid BST Code Example

```python
# Building a valid BST
#
#        8
#       / \
#      3   10
#     / \    \
#    1   6    14
#       / \
#      4   7

root = TreeNode(8)
root.left = TreeNode(3)
root.right = TreeNode(10)
root.left.left = TreeNode(1)
root.left.right = TreeNode(6)
root.right.right = TreeNode(14)
root.left.right.left = TreeNode(4)
root.left.right.right = TreeNode(7)

sol = Solution()
print(sol.isValidBST(root))  # Output: True
```

Every node stays within its valid range. The function correctly returns `True`.

**C++:**

```cpp
// Building a valid BST
//
//        8
//       / \
//      3   10
//     / \    \
//    1   6    14
//       / \
//      4   7

TreeNode* root = new TreeNode(8);
root->left = new TreeNode(3);
root->right = new TreeNode(10);
root->left->left = new TreeNode(1);
root->left->right = new TreeNode(6);
root->right->right = new TreeNode(14);
root->left->right->left = new TreeNode(4);
root->left->right->right = new TreeNode(7);

Solution sol;
cout << sol.isValidBST(root);  // Output: 1 (true)
```

---

## Inorder Traversal Approach

There is another popular way to validate a BST. If you do an **inorder traversal** (left → root → right) of a valid BST, the values must always come out in **strictly increasing order**.

Think of it like reading the bookshelf from left to right. In a valid BST, that order is always sorted from smallest to largest.

```python
class Solution:
    def isValidBST(self, root):
        self.prev = float('-inf')  # Track the last visited value

        def inorder(node):
            if node is None:
                return True

            # Traverse left subtree first
            if not inorder(node.left):
                return False

            # Current value must be greater than previous value
            if node.val <= self.prev:
                return False

            # Update previous value
            self.prev = node.val

            # Traverse right subtree
            return inorder(node.right)

        return inorder(root)
```

This approach visits each node once and checks that values are always increasing. It is clean and easy to understand once you are comfortable with tree traversals.

**C++ (simple):**

```cpp
// Plain function — pass prev by reference so all recursive calls share it
bool inorder(TreeNode* node, long long& prev) {
    if (node == nullptr) return true;
    if (!inorder(node->left, prev)) return false;
    if (node->val <= prev) return false;
    prev = node->val;
    return inorder(node->right, prev);
}

bool isValidBST(TreeNode* root) {
    long long prev = LLONG_MIN;
    return inorder(root, prev);
}
// Time: O(n) | Space: O(h) for recursion stack
```

**C++ (LeetCode class style):**

```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        long long prev = LLONG_MIN;
        return inorder(root, prev);
    }

private:
    bool inorder(TreeNode* node, long long& prev) {
        if (node == nullptr) return true;
        if (!inorder(node->left, prev)) return false;
        if (node->val <= prev) return false;
        prev = node->val;
        return inorder(node->right, prev);
    }
};
```

---

## Comparison of Both Approaches

| Feature               | Min-Max Range Approach              | Inorder Traversal Approach           |
| --------------------- | ----------------------------------- | ------------------------------------ |
| Core Idea             | Pass valid range to every node      | Check if inorder values are sorted   |
| Time Complexity       | O(n)                                | O(n)                                 |
| Space Complexity      | O(h) for recursion stack            | O(h) for recursion stack             |
| Handles duplicates    | Yes, with strict less/greater check | Yes, same check                      |
| Beginner friendliness | Slightly harder to visualize        | Easier if you know inorder traversal |

Both approaches run in **O(n)** time where `n` is the number of nodes. Either one is acceptable in interviews. Choose whichever you find easier to explain.

---

## Edge Cases to Remember

- A tree with **only one node** is always a valid BST.
- An **empty tree** (null root) is also considered valid.
- BST rules use **strict inequality**: a node's value must be strictly less than its right child and strictly greater than its left child. Equal values are not allowed.
- Negative numbers and very large numbers should not cause issues if you use `float('-inf')` and `float('inf')` as initial bounds.

---

## Conclusion

Validating a BST is a classic problem that tests your understanding of tree properties and recursion. The key insight is that **checking only the immediate parent is not enough**. You need to track the full allowed range for every node.

The **min-max range approach** is the most reliable method. The **inorder traversal approach** is a great alternative if you are comfortable with tree traversals.

Practice both approaches on paper with sample trees before coding. Drawing the range values next to each node really helps you understand what the algorithm is doing at every step.

---

## Frequently Asked Questions

**Why can I not just compare each node with its parent?**

Checking only the immediate parent is not enough. A node can be correctly placed relative to its parent but still violate the global BST rule. For example, a node deep in the left subtree could be larger than the root. The min-max range approach ensures the rule holds across the entire tree.

**Are duplicate values allowed in a BST?**

In a standard BST definition, duplicate values are not allowed. The validation code uses strict less than and greater than checks, so if a node has the same value as its boundary, it will return false. Some BST variants allow duplicates, but for most interview problems, duplicates are not permitted.

**What is the time complexity of BST validation?**

Both the min-max and inorder traversal approaches visit every node exactly once. So the time complexity is **O(n)** where `n` is the total number of nodes. The space complexity is **O(h)** where `h` is the height of the tree, due to the recursion call stack.
