# Cycle Detection in Graphs: DFS and Union-Find Guide

## What is a Cycle in a Graph?

A **cycle** exists when you can start at a node, follow a series of edges, and return to the same node.

Real-world uses:

- Detecting **deadlocks** in operating systems (resource allocation graphs)
- Finding **circular dependencies** in build tools (Maven, npm)
- Validating **routing** in networks (prevent infinite packet loops)

Cycle detection works **differently** for undirected and directed graphs — we cover both below.

---

## Types of Graphs: Quick Recap

| Graph Type | Edges           | Cycle Detection Method               |
| ---------- | --------------- | ------------------------------------ |
| Undirected | Bidirectional   | DFS with parent check, or Union-Find |
| Directed   | One-directional | DFS with recursion stack             |

---

## Cycle Detection in Undirected Graphs

### Approach 1 — DFS with Parent Check

**Idea:** During DFS, if we reach a neighbor that is already visited **and it is not our parent**, we have found a cycle (we went around a loop).

**Example graph (5 nodes):**

```
0 - 1 - 2 - 3 - 4
        |       |
        +-------+   ← edge 4→1 creates a cycle
```

Edges: `0-1, 1-2, 2-3, 3-4, 4-1`

#### Python

```python
def has_cycle_dfs_undirected(graph, node, visited, parent):
    visited[node] = True                        # mark current node visited

    for neighbor in graph[node]:
        if not visited[neighbor]:               # unvisited neighbor — go deeper
            if has_cycle_dfs_undirected(graph, neighbor, visited, node):
                return True
        elif neighbor != parent:                # visited AND not our parent = cycle
            return True
    return False


def detect_cycle_undirected(n, edges):
    graph = [[] for _ in range(n)]              # build adjacency list
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)                      # undirected: both directions

    visited = [False] * n

    for i in range(n):
        if not visited[i]:                      # handle disconnected components
            if has_cycle_dfs_undirected(graph, i, visited, -1):
                return True                     # -1 = no parent for start node
    return False


edges = [[0,1],[1,2],[2,3],[3,4],[4,1]]
print(detect_cycle_undirected(5, edges))    # Output: True
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// DFS helper — returns true if cycle found from 'node' with given 'parent'
bool hasCycleDFS(vector<vector<int>>& graph, int node, vector<bool>& visited, int parent) {
    visited[node] = true;                           // mark current as visited

    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {                   // unvisited — recurse
            if (hasCycleDFS(graph, neighbor, visited, node))
                return true;
        } else if (neighbor != parent) {            // visited & not parent = cycle
            return true;
        }
    }
    return false;
}

bool detectCycleUndirected(int n, vector<vector<int>>& edges) {
    vector<vector<int>> graph(n);
    for (auto& e : edges) {
        graph[e[0]].push_back(e[1]);               // build undirected adj list
        graph[e[1]].push_back(e[0]);
    }

    vector<bool> visited(n, false);

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            if (hasCycleDFS(graph, i, visited, -1))    // -1 = no parent
                return true;
        }
    }
    return false;
}

int main() {
    vector<vector<int>> edges = {{0,1},{1,2},{2,3},{3,4},{4,1}};
    cout << detectCycleUndirected(5, edges) << "\n";    // Output: 1 (true)
    return 0;
}
// Time: O(V + E) | Space: O(V)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #684 — Redundant Connection (undirected cycle via DFS)
    bool hasCycle(int n, vector<vector<int>>& edges) {
        vector<vector<int>> graph(n + 1);           // 1-indexed nodes
        for (auto& e : edges) {
            graph[e[0]].push_back(e[1]);
            graph[e[1]].push_back(e[0]);
        }

        vector<bool> visited(n + 1, false);

        for (int i = 1; i <= n; i++) {
            if (!visited[i]) {
                if (dfs(graph, i, visited, -1))
                    return true;
            }
        }
        return false;
    }

private:
    bool dfs(vector<vector<int>>& graph, int node, vector<bool>& visited, int parent) {
        visited[node] = true;

        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                if (dfs(graph, neighbor, visited, node)) return true;
            } else if (neighbor != parent) {        // back edge found = cycle
                return true;
            }
        }
        return false;
    }
};
// Time: O(V + E) | Space: O(V)
```

---

### Approach 2 — Union-Find (Disjoint Set Union)

**Idea:** For each edge `(u, v)`, check if `u` and `v` already belong to the **same connected component**. If they do, adding this edge would create a cycle.

Path compression + union by rank makes each operation nearly O(1).

#### Python

```python
def find(parent, x):
    if parent[x] != x:
        parent[x] = find(parent, parent[x])    # path compression
    return parent[x]


def union(parent, rank, x, y):
    rx, ry = find(parent, x), find(parent, y)
    if rx == ry:                                # same component = cycle!
        return False
    # union by rank
    if rank[rx] < rank[ry]:
        parent[rx] = ry
    elif rank[rx] > rank[ry]:
        parent[ry] = rx
    else:
        parent[ry] = rx
        rank[rx] += 1
    return True


def detect_cycle_union_find(n, edges):
    parent = list(range(n))                     # each node is its own parent
    rank = [0] * n

    for u, v in edges:
        if not union(parent, rank, u, v):       # union returns False = cycle
            return True
    return False


edges = [[0,1],[1,2],[2,3],[3,4],[4,1]]
print(detect_cycle_union_find(5, edges))    # Output: True
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

int find(vector<int>& parent, int x) {
    if (parent[x] != x)
        parent[x] = find(parent, parent[x]);   // path compression
    return parent[x];
}

// Returns false if x and y are already in the same set (= cycle)
bool unite(vector<int>& parent, vector<int>& rank, int x, int y) {
    int rx = find(parent, x), ry = find(parent, y);
    if (rx == ry) return false;                 // same root = adding edge = cycle

    if (rank[rx] < rank[ry])       parent[rx] = ry;
    else if (rank[rx] > rank[ry])  parent[ry] = rx;
    else { parent[ry] = rx; rank[rx]++; }
    return true;
}

bool detectCycleUnionFind(int n, vector<vector<int>>& edges) {
    vector<int> parent(n), rank(n, 0);
    for (int i = 0; i < n; i++) parent[i] = i; // initialize each node as own parent

    for (auto& e : edges) {
        if (!unite(parent, rank, e[0], e[1]))   // cycle detected
            return true;
    }
    return false;
}

int main() {
    vector<vector<int>> edges = {{0,1},{1,2},{2,3},{3,4},{4,1}};
    cout << detectCycleUnionFind(5, edges) << "\n";    // Output: 1 (true)
    return 0;
}
// Time: O(E * α(V)) ≈ O(E) | Space: O(V)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #684 — Redundant Connection (Union-Find approach)
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        vector<int> parent(n + 1), rank(n + 1, 0);
        for (int i = 1; i <= n; i++) parent[i] = i;    // init DSU

        for (auto& e : edges) {
            if (!unite(parent, rank, e[0], e[1]))
                return e;   // this edge creates the cycle
        }
        return {};
    }

private:
    int find(vector<int>& parent, int x) {
        if (parent[x] != x)
            parent[x] = find(parent, parent[x]);   // path compression
        return parent[x];
    }

    bool unite(vector<int>& parent, vector<int>& rank, int x, int y) {
        int rx = find(parent, x), ry = find(parent, y);
        if (rx == ry) return false;                 // already connected = cycle

        if (rank[rx] < rank[ry])       parent[rx] = ry;
        else if (rank[rx] > rank[ry])  parent[ry] = rx;
        else { parent[ry] = rx; rank[rx]++; }
        return true;
    }
};
// Time: O(E * α(V)) | Space: O(V)
```

---

## Cycle Detection in Directed Graphs

### Why DFS with Parent Check Doesn't Work Here

In a **directed** graph, edges have direction. A node can be visited in a previous DFS path that has nothing to do with the current path. Simply checking "visited and not parent" gives **false positives**.

We need to track the **current active DFS path** using a **recursion stack**.

### DFS with Recursion Stack

**Idea:** Maintain two arrays:

- `visited` — all nodes fully explored
- `in_stack` — nodes on the **current active DFS path**

If we reach a neighbor that is **in the current stack**, we have found a back edge → cycle.

```
Example 1 (cycle):     0 → 1 → 2 → 0   (cycle!)
Example 2 (no cycle):  0 → 1 → 3
                        ↓       ↑
                        2 ------+       (DAG — no cycle)
```

#### Python

```python
def has_cycle_directed(graph, node, visited, in_stack):
    visited[node] = True
    in_stack[node] = True               # add to current DFS path

    for neighbor in graph[node]:
        if not visited[neighbor]:
            if has_cycle_directed(graph, neighbor, visited, in_stack):
                return True
        elif in_stack[neighbor]:        # neighbor on current path = cycle!
            return True

    in_stack[node] = False              # remove from path when backtracking
    return False


def detect_cycle_directed(n, edges):
    graph = [[] for _ in range(n)]
    for u, v in edges:
        graph[u].append(v)              # directed: only u → v

    visited = [False] * n
    in_stack = [False] * n

    for i in range(n):
        if not visited[i]:
            if has_cycle_directed(graph, i, visited, in_stack):
                return True
    return False


# Test 1: cycle 0→1→2→0
edges1 = [[0,1],[1,2],[2,0]]
print(detect_cycle_directed(3, edges1))     # Output: True

# Test 2: DAG 0→1, 0→2, 1→3, 2→3
edges2 = [[0,1],[0,2],[1,3],[2,3]]
print(detect_cycle_directed(4, edges2))     # Output: False
```

#### C++ (simple)

```cpp
#include <iostream>
#include <vector>
using namespace std;

// Returns true if a cycle is found starting from 'node'
bool hasCycleDirected(vector<vector<int>>& graph, int node,
                      vector<bool>& visited, vector<bool>& inStack) {
    visited[node] = true;
    inStack[node] = true;           // add to current active DFS path

    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            if (hasCycleDirected(graph, neighbor, visited, inStack))
                return true;
        } else if (inStack[neighbor]) { // back edge to current path = cycle
            return true;
        }
    }

    inStack[node] = false;          // remove from path on backtrack
    return false;
}

bool detectCycleDirected(int n, vector<vector<int>>& edges) {
    vector<vector<int>> graph(n);
    for (auto& e : edges)
        graph[e[0]].push_back(e[1]);    // directed edge only

    vector<bool> visited(n, false), inStack(n, false);

    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            if (hasCycleDirected(graph, i, visited, inStack))
                return true;
        }
    }
    return false;
}

int main() {
    vector<vector<int>> e1 = {{0,1},{1,2},{2,0}};
    cout << detectCycleDirected(3, e1) << "\n";     // Output: 1 (true)

    vector<vector<int>> e2 = {{0,1},{0,2},{1,3},{2,3}};
    cout << detectCycleDirected(4, e2) << "\n";     // Output: 0 (false)
    return 0;
}
// Time: O(V + E) | Space: O(V)
```

#### C++ (LeetCode class style)

```cpp
class Solution {
public:
    // LeetCode #207 — Course Schedule (directed cycle detection)
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        for (auto& p : prerequisites)
            graph[p[1]].push_back(p[0]);    // p[1] must come before p[0]

        vector<bool> visited(numCourses, false);
        vector<bool> inStack(numCourses, false);

        for (int i = 0; i < numCourses; i++) {
            if (!visited[i]) {
                if (hasCycle(graph, i, visited, inStack))
                    return false;           // cycle = impossible to finish
            }
        }
        return true;
    }

private:
    bool hasCycle(vector<vector<int>>& graph, int node,
                  vector<bool>& visited, vector<bool>& inStack) {
        visited[node] = true;
        inStack[node] = true;               // on current DFS path

        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                if (hasCycle(graph, neighbor, visited, inStack)) return true;
            } else if (inStack[neighbor]) { // back edge = cycle detected
                return true;
            }
        }

        inStack[node] = false;              // backtrack: remove from path
        return false;
    }
};
// Time: O(V + E) | Space: O(V)
```

---

## Comparison of Approaches

| Approach              | Graph Type | Time               | Space | Best For                       |
| --------------------- | ---------- | ------------------ | ----- | ------------------------------ |
| DFS + parent check    | Undirected | O(V + E)           | O(V)  | Simple undirected cycle check  |
| Union-Find            | Undirected | O(E · α(V)) ≈ O(E) | O(V)  | Edge-list input, disjoint sets |
| DFS + recursion stack | Directed   | O(V + E)           | O(V)  | Directed graph cycle detection |

α(V) = inverse Ackermann function — practically constant (≤ 5 for any real input).

---

## Key Takeaways

- **Undirected cycle (DFS):** a visited neighbor that is **not the parent** = cycle.
- **Undirected cycle (Union-Find):** two nodes already in the **same component** = cycle.
- **Directed cycle (DFS + stack):** a neighbor that is **on the current path** = back edge = cycle.
- Don't use the parent-check trick on directed graphs — it gives false positives.
- Self-loops count as cycles (caught naturally by both approaches).

---

## FAQ

**Can I use Union-Find for directed graphs?**

Union-Find doesn't handle edge direction, so it can give wrong answers for directed graphs. Use the DFS recursion stack approach instead.

**What is the time complexity of cycle detection?**

Both DFS approaches run in O(V + E). Union-Find with path compression and union by rank is O(E · α(V)), which is practically O(E).

**Does a self-loop count as a cycle?**

Yes. A self-loop is the simplest cycle. The DFS parent-check catches it because the neighbor equals the current node and is already visited.
