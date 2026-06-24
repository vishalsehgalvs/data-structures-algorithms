# BFS vs DFS Tree Traversal: A Beginner-Friendly Guide

## What is Tree Traversal?

In programming, tree traversal means visiting every node in a tree **exactly once**. The order in which you visit nodes changes how you process and understand the data.

There are two main ways to traverse a tree: **BFS** (Breadth-First Search) and **DFS** (Depth-First Search).

---

## Understanding BFS — Breadth-First Search

Think of BFS like reading a bookshelf row by row. You finish all books on the top shelf before moving to the next shelf down.

BFS visits all nodes at the **current level** before going deeper. It uses a **queue** (FIFO) to track which node to visit next. This is also called **Level Order Traversal**.

### BFS Visual Example

```
        1
       / \
      2   3
     / \ / \
    4  5 6  7

BFS order: 1 → 2 → 3 → 4 → 5 → 6 → 7
```

### BFS Step-by-Step

1. Put the root into the queue.
2. Dequeue the front node and process it.
3. Enqueue its left and right children (if they exist).
4. Repeat until the queue is empty.

#### Python

```python
from collections import deque

class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None

def bfs(root):
    if root is None:
        return

    queue = deque()         # FIFO queue for level-order processing
    queue.append(root)      # start with the root

    while queue:
        node = queue.popleft()          # process the front node
        print(node.val, end=" ")

        if node.left:                   # enqueue left child
            queue.append(node.left)
        if node.right:                  # enqueue right child
            queue.append(node.right)


# Build tree
root = TreeNode(1)
root.left = TreeNode(2);   root.right = TreeNode(3)
root.left.left = TreeNode(4);  root.left.right = TreeNode(5)
root.right.left = TreeNode(6); root.right.right = TreeNode(7)

bfs(root)   # Output: 1 2 3 4 5 6 7
```

#### C++ (simple)

```cpp
#include <iostream>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// BFS — level order traversal using a queue
void bfs(TreeNode* root) {
    if (root == nullptr) return;

    queue<TreeNode*> q;
    q.push(root);               // start with root in queue

    while (!q.empty()) {
        TreeNode* node = q.front();
        q.pop();                            // dequeue front node
        cout << node->val << " ";           // process it

        if (node->left)  q.push(node->left);    // enqueue left child
        if (node->right) q.push(node->right);   // enqueue right child
    }
}

int main() {
    TreeNode* root = new TreeNode(1);
    root->left = new TreeNode(2);   root->right = new TreeNode(3);
    root->left->left = new TreeNode(4);  root->left->right = new TreeNode(5);
    root->right->left = new TreeNode(6); root->right->right = new TreeNode(7);

    bfs(root);  // Output: 1 2 3 4 5 6 7
    return 0;
}
// Time: O(n) | Space: O(n) — queue holds up to one full level
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #102 — Binary Tree Level Order Traversal
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (root == nullptr) return result;

        queue<TreeNode*> q;
        q.push(root);                       // start BFS from root

        while (!q.empty()) {
            int levelSize = q.size();       // number of nodes at this level
            vector<int> level;

            for (int i = 0; i < levelSize; i++) {
                TreeNode* node = q.front();
                q.pop();
                level.push_back(node->val); // collect this level's values

                if (node->left)  q.push(node->left);
                if (node->right) q.push(node->right);
            }
            result.push_back(level);        // add completed level to result
        }
        return result;
    }
};
// Time: O(n) | Space: O(n)
```

---

## Understanding DFS — Depth-First Search

Think of DFS like exploring a maze. You follow one path all the way to a dead end before backing up and trying another path.

DFS goes as **deep as possible** down one branch before backtracking. It uses the call stack (recursion) or an explicit stack.

DFS has **three variations** based on when you process the current node:

---

### DFS PreOrder — Root → Left → Right

Process the current node **first**, then explore left, then right. Output for the example tree: `1 2 4 5 3 6 7`

#### Python

```python
def preorder(node):
    if node is None:
        return
    print(node.val, end=" ")    # process current node FIRST
    preorder(node.left)          # then go left
    preorder(node.right)         # then go right

preorder(root)  # Output: 1 2 4 5 3 6 7
```

#### C++ (simple)

```cpp
// PreOrder: Root → Left → Right
void preorder(TreeNode* node) {
    if (node == nullptr) return;
    cout << node->val << " ";   // process BEFORE children
    preorder(node->left);        // recurse left
    preorder(node->right);       // recurse right
}
// Output: 1 2 4 5 3 6 7 | Time: O(n) | Space: O(h)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #144 — Binary Tree Preorder Traversal
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        preorder(root, result);
        return result;
    }

private:
    void preorder(TreeNode* node, vector<int>& result) {
        if (node == nullptr) return;
        result.push_back(node->val);    // visit root first
        preorder(node->left, result);   // then left subtree
        preorder(node->right, result);  // then right subtree
    }
};
```

---

### DFS InOrder — Left → Root → Right

Go left first, process the current node, then go right. For a **BST**, InOrder always gives values in **sorted ascending order**.

Output: `4 2 5 1 6 3 7`

#### Python

```python
def inorder(node):
    if node is None:
        return
    inorder(node.left)           # go left first
    print(node.val, end=" ")    # process current node IN the middle
    inorder(node.right)          # then go right

inorder(root)   # Output: 4 2 5 1 6 3 7
```

#### C++ (simple)

```cpp
// InOrder: Left → Root → Right
void inorder(TreeNode* node) {
    if (node == nullptr) return;
    inorder(node->left);         // visit left subtree first
    cout << node->val << " ";   // process BETWEEN children
    inorder(node->right);        // visit right subtree last
}
// For BST → outputs values in sorted order
// Time: O(n) | Space: O(h)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #94 — Binary Tree Inorder Traversal
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        inorder(root, result);
        return result;
    }

private:
    void inorder(TreeNode* node, vector<int>& result) {
        if (node == nullptr) return;
        inorder(node->left, result);            // left subtree first
        result.push_back(node->val);            // then current node
        inorder(node->right, result);           // then right subtree
    }
};
```

---

### DFS PostOrder — Left → Right → Root

Process children first, then the current node. Useful for **deleting a tree** or **evaluating expression trees**.

Output: `4 5 2 6 7 3 1`

#### Python

```python
def postorder(node):
    if node is None:
        return
    postorder(node.left)         # go left
    postorder(node.right)        # go right
    print(node.val, end=" ")    # process current node LAST

postorder(root) # Output: 4 5 2 6 7 3 1
```

#### C++ (simple)

```cpp
// PostOrder: Left → Right → Root
void postorder(TreeNode* node) {
    if (node == nullptr) return;
    postorder(node->left);       // visit left subtree first
    postorder(node->right);      // visit right subtree second
    cout << node->val << " ";   // process AFTER both children
}
// Time: O(n) | Space: O(h)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #145 — Binary Tree Postorder Traversal
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        postorder(root, result);
        return result;
    }

private:
    void postorder(TreeNode* node, vector<int>& result) {
        if (node == nullptr) return;
        postorder(node->left, result);          // left subtree
        postorder(node->right, result);         // right subtree
        result.push_back(node->val);            // root last
    }
};
```

---

## BFS vs DFS Comparison

| Feature | BFS | DFS |
|---------|-----|-----|
| Order of visit | Level by level | Branch by branch |
| Data structure | Queue | Stack or Recursion |
| Memory usage | Higher (stores a full level) | Lower (stores one path) |
| Finds shortest path | Yes, in unweighted graphs | Not guaranteed |
| Best for | Closest node, level problems | Deep paths, subtree problems |
| Variations | Level Order | PreOrder, InOrder, PostOrder |

---

## Iterative DFS Using a Stack

You can do DFS without recursion using an explicit stack — useful for very deep trees to avoid stack overflow.

#### Python

```python
def dfs_iterative(root):
    if root is None:
        return

    stack = [root]              # initialize stack with root

    while stack:
        node = stack.pop()              # pop top node and process it
        print(node.val, end=" ")

        # Push right BEFORE left so left is processed first (LIFO)
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)

dfs_iterative(root)     # Output: 1 2 4 5 3 6 7  (same as PreOrder)
```

#### C++ (simple)

```cpp
#include <stack>
using namespace std;

// Iterative DFS (PreOrder style) using an explicit stack
void dfsIterative(TreeNode* root) {
    if (root == nullptr) return;

    stack<TreeNode*> st;
    st.push(root);                  // start with root

    while (!st.empty()) {
        TreeNode* node = st.top();
        st.pop();                           // pop and process
        cout << node->val << " ";

        // Push right FIRST so left is processed first (stack is LIFO)
        if (node->right) st.push(node->right);
        if (node->left)  st.push(node->left);
    }
}
// Output: 1 2 4 5 3 6 7 | Time: O(n) | Space: O(h)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // Iterative preorder traversal — avoids recursion stack overflow
    vector<int> preorderIterative(TreeNode* root) {
        vector<int> result;
        if (root == nullptr) return result;

        stack<TreeNode*> st;
        st.push(root);

        while (!st.empty()) {
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);        // process current

            if (node->right) st.push(node->right);  // right pushed first
            if (node->left)  st.push(node->left);   // left processed first
        }
        return result;
    }
};
```

---

## Level Order BFS with Level Numbers

Track which level each node belongs to — useful for minimum depth, zigzag traversal, etc.

#### Python

```python
from collections import deque

def bfs_with_levels(root):
    if root is None:
        return

    queue = deque([root])
    level = 0

    while queue:
        level_size = len(queue)         # all nodes currently in queue = this level
        print(f"Level {level}: ", end="")

        for _ in range(level_size):     # process exactly this level's nodes
            node = queue.popleft()
            print(node.val, end=" ")

            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)

        print()     # new line after each level
        level += 1

bfs_with_levels(root)
# Output:
# Level 0: 1
# Level 1: 2 3
# Level 2: 4 5 6 7
```

#### C++ (simple)

```cpp
// BFS with level tracking — processes nodes group by group
void bfsWithLevels(TreeNode* root) {
    if (root == nullptr) return;

    queue<TreeNode*> q;
    q.push(root);
    int level = 0;

    while (!q.empty()) {
        int levelSize = q.size();       // nodes at this level
        cout << "Level " << level << ": ";

        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            cout << node->val << " ";   // process this level's node

            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        cout << "\n";
        level++;
    }
}
// Output:
// Level 0: 1
// Level 1: 2 3
// Level 2: 4 5 6 7
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #102 — returns list of lists, one per level
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (root == nullptr) return result;

        queue<TreeNode*> q;
        q.push(root);

        while (!q.empty()) {
            int levelSize = q.size();       // snapshot of current level size
            vector<int> level;

            for (int i = 0; i < levelSize; i++) {
                TreeNode* node = q.front();
                q.pop();
                level.push_back(node->val);     // collect current level

                if (node->left)  q.push(node->left);
                if (node->right) q.push(node->right);
            }
            result.push_back(level);            // store completed level
        }
        return result;
    }
};
// Time: O(n) | Space: O(n)
```

---

## Quick Summary of DFS Orders

| DFS Type | Order | Output (example tree) | Memory trick |
|----------|-------|----------------------|-------------|
| PreOrder | Root → Left → Right | 1 2 4 5 3 6 7 | **Pre** = root **before** children |
| InOrder | Left → Root → Right | 4 2 5 1 6 3 7 | **In** = root **between** children |
| PostOrder | Left → Right → Root | 4 5 2 6 7 3 1 | **Post** = root **after** children |

---

## Key Takeaways

- **BFS** uses a queue, visits level by level. Best for shortest path, level-based problems.
- **DFS** uses recursion or a stack, goes depth-first. Best for path exploration, subtree work.
- **InOrder** on a BST always gives sorted values.
- **PostOrder** is used for tree deletion and expression evaluation.
- Iterative DFS avoids recursion stack overflow on very deep trees.

---

## Frequently Asked Questions

**When should I use BFS over DFS?**

Use BFS when you want the shortest path or need to process nodes level by level. Use DFS when you want to explore a full branch, work with subtrees, or solve recursion-based tree problems.

**Why is InOrder traversal special for BSTs?**

InOrder visits nodes in Left → Root → Right order. For a BST, this produces values in sorted ascending order — extremely useful for BST-related problems.

**What is the difference between recursive DFS and iterative DFS?**

Both produce the same result but use different memory. Recursive DFS uses the program call stack automatically. Iterative DFS uses an explicit stack you control. Iterative DFS is better for very deep trees to avoid stack overflow errors.
