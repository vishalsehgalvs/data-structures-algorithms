# Topological Sorting for Beginners: BFS and DFS Guide

## What is Topological Sorting?

Topological sorting arranges the nodes of a **directed graph** in a linear order such that for every directed edge from node A → node B, **A comes before B** in the result.

Think of it like course prerequisites: you must finish Course A before taking Course B. Topological sort gives you a valid study order.

**Only valid for Directed Acyclic Graphs (DAGs).** If the graph has a cycle, topological sorting is impossible (infinite dependency loop).

---

## Directed Acyclic Graphs (DAGs)

A DAG is a directed graph with **no cycles**. Key properties:

- All edges are directed (one-way).
- No cycles exist.
- Multiple valid topological orderings may exist for the same graph.
- A DAG always has **at least one node with no incoming edges** (called a source node).

---

## Real-World Use Cases

| Use Case             | Example                                    |
| -------------------- | ------------------------------------------ |
| Build systems        | Compile files in dependency order          |
| Course prerequisites | Take Math before Physics                   |
| Task scheduling      | Run pipeline jobs in correct order         |
| Package managers     | Install libraries in right sequence        |
| Spreadsheets         | Evaluate cell formulas in dependency order |

---

## Approach 1 — Kahn's Algorithm (BFS-Based)

**In-degree** = number of incoming edges a node has. A node with **in-degree 0** has no dependencies — it can be processed first.

### Steps

1. Calculate in-degree of every node.
2. Add all nodes with in-degree 0 to a queue.
3. Dequeue a node, add it to result, reduce in-degrees of its neighbors.
4. If any neighbor's in-degree becomes 0, enqueue it.
5. If `len(result) == total nodes` → valid sort. Otherwise → cycle detected.

### Example Graph

```
Nodes 0–5, edges: 5→2, 5→0, 4→0, 4→1, 2→3, 3→1

5 must come before 2 and 0
4 must come before 0 and 1
2 must come before 3
3 must come before 1
```

#### Python

```python
from collections import deque

def topological_sort_bfs(vertices, edges):
    adj = {i: [] for i in range(vertices)}
    in_degree = [0] * vertices

    for u, v in edges:
        adj[u].append(v)
        in_degree[v] += 1          # v has one more incoming edge

    # Start with all nodes that have no dependencies
    queue = deque(i for i in range(vertices) if in_degree[i] == 0)
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)

        for neighbor in adj[node]:
            in_degree[neighbor] -= 1          # remove this edge
            if in_degree[neighbor] == 0:
                queue.append(neighbor)         # now ready to process

    # If result doesn't include all nodes, a cycle exists
    return result if len(result) == vertices else []


vertices = 6
edges = [(5,2),(5,0),(4,0),(4,1),(2,3),(3,1)]
print(topological_sort_bfs(vertices, edges))
# Output: [4, 5, 0, 2, 1, 3]  (or another valid ordering)
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

// Kahn's Algorithm — BFS-based topological sort
vector<int> topoSortBFS(int n, vector<vector<int>>& edges) {
    vector<vector<int>> adj(n);
    vector<int> inDegree(n, 0);

    for (auto& e : edges) {
        adj[e[0]].push_back(e[1]);
        inDegree[e[1]]++;           // e[1] has one more incoming edge
    }

    queue<int> q;
    for (int i = 0; i < n; i++)
        if (inDegree[i] == 0) q.push(i);   // source nodes first

    vector<int> result;

    while (!q.empty()) {
        int node = q.front(); q.pop();
        result.push_back(node);

        for (int neighbor : adj[node]) {
            inDegree[neighbor]--;               // remove this dependency
            if (inDegree[neighbor] == 0)
                q.push(neighbor);               // neighbor is now ready
        }
    }

    // If not all nodes processed, graph has a cycle
    return (result.size() == n) ? result : vector<int>{};
}

int main() {
    int n = 6;
    vector<vector<int>> edges = {{5,2},{5,0},{4,0},{4,1},{2,3},{3,1}};
    vector<int> order = topoSortBFS(n, edges);
    for (int x : order) cout << x << " ";   // e.g. 4 5 0 2 1 3
    cout << "\n";
    return 0;
}
// Time: O(V + E) | Space: O(V + E)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #210 — Course Schedule II (return order using Kahn's BFS)
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> adj(numCourses);
        vector<int> inDegree(numCourses, 0);

        for (auto& p : prerequisites) {
            adj[p[1]].push_back(p[0]);  // p[1] must come before p[0]
            inDegree[p[0]]++;
        }

        queue<int> q;
        for (int i = 0; i < numCourses; i++)
            if (inDegree[i] == 0) q.push(i);   // courses with no prerequisites

        vector<int> order;

        while (!q.empty()) {
            int course = q.front(); q.pop();
            order.push_back(course);

            for (int next : adj[course]) {
                if (--inDegree[next] == 0)      // dependency resolved
                    q.push(next);
            }
        }

        // Cycle check: must include all courses
        return (order.size() == numCourses) ? order : vector<int>{};
    }
};
// Time: O(V + E) | Space: O(V + E)
```

---

## Approach 2 — DFS-Based Topological Sort

**Key insight:** finish processing a node and **all its dependencies first**, then push it onto a stack. Popping the full stack gives a valid topological order.

Think of it like packing a suitcase: items needed last go at the bottom (pushed first), items needed first go on top (popped first).

### Steps

1. For each unvisited node, call recursive DFS.
2. After all neighbors of a node are visited, **push the current node to a stack**.
3. After all nodes are processed, reverse the stack for the topological order.

#### Python

```python
def topological_sort_dfs(vertices, edges):
    adj = {i: [] for i in range(vertices)}
    for u, v in edges:
        adj[u].append(v)

    visited = [False] * vertices
    stack = []

    def dfs(node):
        visited[node] = True
        for neighbor in adj[node]:
            if not visited[neighbor]:
                dfs(neighbor)           # visit all reachable nodes first
        stack.append(node)              # push AFTER all neighbors are done

    for i in range(vertices):
        if not visited[i]:
            dfs(i)

    return stack[::-1]                  # reverse stack = topological order


vertices = 6
edges = [(5,2),(5,0),(4,0),(4,1),(2,3),(3,1)]
print(topological_sort_dfs(vertices, edges))
# Output: [5, 4, 2, 3, 1, 0]  (or another valid ordering)
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

void dfs(int node, vector<vector<int>>& adj, vector<bool>& visited, stack<int>& st) {
    visited[node] = true;
    for (int neighbor : adj[node]) {
        if (!visited[neighbor])
            dfs(neighbor, adj, visited, st);    // recurse until no unvisited neighbors
    }
    st.push(node);      // push AFTER all descendants are done
}

vector<int> topoSortDFS(int n, vector<vector<int>>& edges) {
    vector<vector<int>> adj(n);
    for (auto& e : edges) adj[e[0]].push_back(e[1]);

    vector<bool> visited(n, false);
    stack<int> st;

    for (int i = 0; i < n; i++)
        if (!visited[i]) dfs(i, adj, visited, st);

    vector<int> result;
    while (!st.empty()) {
        result.push_back(st.top());     // pop stack = topological order
        st.pop();
    }
    return result;
}

int main() {
    int n = 6;
    vector<vector<int>> edges = {{5,2},{5,0},{4,0},{4,1},{2,3},{3,1}};
    vector<int> order = topoSortDFS(n, edges);
    for (int x : order) cout << x << " ";   // e.g. 5 4 2 3 1 0
    cout << "\n";
    return 0;
}
// Time: O(V + E) | Space: O(V + E)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #210 — Course Schedule II (DFS approach)
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> adj(numCourses);
        for (auto& p : prerequisites)
            adj[p[1]].push_back(p[0]);  // p[1] must come before p[0]

        // 0 = unvisited, 1 = in current path, 2 = fully processed
        vector<int> state(numCourses, 0);
        vector<int> order;
        bool hasCycle = false;

        for (int i = 0; i < numCourses; i++) {
            if (state[i] == 0)
                dfs(i, adj, state, order, hasCycle);
        }

        if (hasCycle) return {};
        reverse(order.begin(), order.end());    // reverse stack order
        return order;
    }

private:
    void dfs(int node, vector<vector<int>>& adj, vector<int>& state,
             vector<int>& order, bool& hasCycle) {
        state[node] = 1;    // on current DFS path

        for (int next : adj[node]) {
            if (state[next] == 1) { hasCycle = true; return; }  // back edge
            if (state[next] == 0)
                dfs(next, adj, state, order, hasCycle);
        }

        state[node] = 2;        // fully processed
        order.push_back(node);  // push after all descendants done
    }
};
// Time: O(V + E) | Space: O(V + E)
```

---

## Dry Run — Kahn's Algorithm Step by Step

```
Graph: 0→1, 0→2, 1→3, 2→3

Initial in-degrees:
  Node 0: 0   ← no incoming edges (source)
  Node 1: 1   ← from 0
  Node 2: 1   ← from 0
  Node 3: 2   ← from 1 and 2

Step 1: queue = [0]    (only node with in-degree 0)

Process 0: result = [0]
  Reduce in-degree of 1 → 0, enqueue
  Reduce in-degree of 2 → 0, enqueue
  queue = [1, 2]

Process 1: result = [0, 1]
  Reduce in-degree of 3 → 1
  queue = [2]

Process 2: result = [0, 1, 2]
  Reduce in-degree of 3 → 0, enqueue
  queue = [3]

Process 3: result = [0, 1, 2, 3]
  queue = []

Final result: [0, 1, 2, 3]
len(result) == 4 == vertices → no cycle ✓
```

Node 0 always comes first (no dependencies). Node 3 comes last (depends on both 1 and 2).

---

## Comparison of Approaches

| Feature           | Kahn's Algorithm (BFS)         | DFS-Based                  |
| ----------------- | ------------------------------ | -------------------------- |
| Traversal type    | Breadth-First                  | Depth-First                |
| Cycle detection   | Built-in (check result length) | Needs extra state tracking |
| Data structure    | Queue + in-degree array        | Recursion stack            |
| Time complexity   | O(V + E)                       | O(V + E)                   |
| Space complexity  | O(V + E)                       | O(V + E)                   |
| Beginner-friendly | Yes — intuitive                | Moderate                   |

Kahn's algorithm is often preferred in interviews because **cycle detection is a natural byproduct** of the algorithm.

---

## Key Takeaways

- Topological sort only works on **DAGs** — directed acyclic graphs.
- **Kahn's BFS:** use in-degree; process nodes with in-degree 0 first; built-in cycle detection.
- **DFS:** push nodes onto a stack after all descendants are processed; reverse for final order.
- Multiple valid orderings can exist for the same graph.
- Both approaches run in **O(V + E)** time and space.

---

## Common Mistakes to Avoid

- Using topological sort on an **undirected** graph (only valid for directed).
- **Forgetting cycle detection**: always check if the result includes all nodes.
- Not initializing in-degrees correctly (every edge must increment the destination's count).
- Assuming there is only one unique valid answer.

---

## FAQ

**What happens if the graph has a cycle?**

Topological sorting is not possible. In Kahn's algorithm, the result will have fewer nodes than the total vertex count, signaling a cycle.

**Is the topological sort result always unique?**

No. If two nodes have no dependency on each other, either can come first and both orderings are valid.

**Which approach should I use in interviews?**

Both are accepted. Kahn's BFS is easier to explain and has built-in cycle detection — safer for beginners. Use DFS if you are comfortable with recursive graph traversal.
